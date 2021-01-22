<!-- markdownlint-disable MD002 MD041 -->

この演習では、前の演習のアプリケーションを拡張して、Azure AD での認証をサポートします。 これは、Microsoft Graph を呼び出すのに必要な OAuth アクセス トークンを取得するために必要です。 これを行うには [、iOS 用 Microsoft Authentication Library (MSAL) を](https://github.com/AzureAD/microsoft-authentication-library-for-objc) アプリケーションに統合します。

1. **AuthSettings.plist** という名前の **GraphTu graphl** プロジェクトに新しいプロパティ リスト ファイルを作成します。 
1. ルート ディクショナリ内のファイルに次の項目 **を** 追加します。

    | キー | 型 | 値 |
    |-----|------|-------|
    | `AppId` | String | Azure Portal からのアプリケーション ID |
    | `GraphScopes` | 配列 | 3 つの文字列値: `User.Read` `MailboxSettings.Read` 、 、 `Calendars.ReadWrite` |

    ![Xcode の AuthSettings.plist ファイルのスクリーンショット](./images/auth-settings.png)

> [!IMPORTANT]
> git などのソース管理を使用している場合は **、AuthSettings.plist** ファイルをソース管理から除外して、アプリ ID が誤って漏洩しないようにする良い時期です。

## <a name="implement-sign-in"></a>サインインの実装

このセクションでは、MSAL のプロジェクトを構成し、認証マネージャー クラスを作成し、サインインしてサインアウトするアプリを更新します。

### <a name="configure-project-for-msal"></a>MSAL 用にプロジェクトを構成する

1. プロジェクトの機能に新しいキーチェーン グループを追加します。
    1. **GraphTu読み込みプロジェクトを** 選択し、次に **[&機能] を選択します**。
    1. [+ **機能] を** 選択し、[キーチェーンの共有] **をダブルクリックします**。
    1. 値を持つキーチェーン グループを追加します `com.microsoft.adalcache` 。

1. **[Info.plist] をクリックし、[** ファイル名を指定して **開く]、次** に [ソース コード]**の順に選択します**。
1. 要素内に次を追加 `<dict>` します。

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        </array>
      </dict>
    </array>
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauthv2</string>
        <string>msauthv3</string>
    </array>
    ```

1. **AppDelegate.m** を開き、ファイルの上部に次の import ステートメントを追加します。

    ```objc
    #import <MSAL/MSAL.h>
    ```

1. 次の関数を `AppDelegate` クラスに追加します。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AppDelegate.m" id="HandleMsalResponseSnippet":::

### <a name="create-authentication-manager"></a>認証マネージャーを作成する

1. AuthenticationManager という **名前の GraphTu** **touchl** プロジェクトに新しい Cocoa Touch クラス **を作成します**。 フィールド **のサブクラスで NSObject** **を選択** します。
1. **AuthenticationManager.h を開き**、その内容を次のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.h" id="AuthManagerSnippet":::

1. **AuthenticationManager.m を開** き、その内容を次のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.m" id="AuthManagerSnippet":::

### <a name="add-sign-in-and-sign-out"></a>サインインとサインアウトを追加する

1. **SignInViewController.m ファイル** を開き、その内容を次のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/SignInViewController.m" id="SignInViewSnippet":::

1. **WelcomeViewController.m** を開き、ファイルの一番上に次 `import` のステートメントを追加します。

    ```objc
    #import "AuthenticationManager.h"
    ```

1. 既存の `signOut` 関数を、以下の関数で置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.m" id="SignOutSnippet":::

1. 変更を保存し、シミュレーターでアプリケーションを再起動します。

アプリにサインインすると、Xcode の出力ウィンドウにアクセス トークンが表示されます。

![アクセス トークンを示す Xcode の出力ウィンドウのスクリーンショット](./images/access-token-output.png)

## <a name="get-user-details"></a>ユーザーの詳細情報を取得する

このセクションでは、Microsoft Graph へのすべての呼び出しを保持するヘルパー クラスを作成し、この新しいクラスを使用してログイン ユーザーを取得するために更新 `WelcomeViewController` します。

1. GraphManager という **名前の GraphTu** **touchl** プロジェクトに新しい Cocoa Touch クラス **を作成します**。 フィールド **のサブクラスで NSObject** **を選択** します。
1. **GraphManager.h を開** き、その内容を次のコードに置き換えます。

    ```objc
    #import <Foundation/Foundation.h>
    #import <MSGraphClientSDK/MSGraphClientSDK.h>
    #import <MSGraphClientModels/MSGraphClientModels.h>
    #import <MSGraphClientModels/MSCollection.h>
    #import "AuthenticationManager.h"

    NS_ASSUME_NONNULL_BEGIN

    typedef void (^GetMeCompletionBlock)(MSGraphUser* _Nullable user,
                                         NSError* _Nullable error);

    @interface GraphManager : NSObject

    + (id) instance;
    - (void) getMeWithCompletionBlock: (GetMeCompletionBlock) completion;

    @end

    NS_ASSUME_NONNULL_END
    ```

1. **GraphManager.m を開** き、その内容を次のコードに置き換えます。

    ```objc
    #import "GraphManager.h"

    @interface GraphManager()

    @property MSHTTPClient* graphClient;

    @end

    @implementation GraphManager

    + (id) instance {
        static GraphManager *singleInstance = nil;
        static dispatch_once_t onceToken;
        dispatch_once(&onceToken, ^ {
            singleInstance = [[self alloc] init];
        });

        return singleInstance;
    }

    - (id) init {
        if (self = [super init]) {
            // Create the Graph client
            self.graphClient = [MSClientFactory
                                createHTTPClientWithAuthenticationProvider:AuthenticationManager.instance];
        }

        return self;
    }

    - (void) getMeWithCompletionBlock: (GetMeCompletionBlock) completion {
        // GET /me
        NSString* meUrlString = [NSString stringWithFormat:@"%@/me?%@",
                                 MSGraphBaseURL,
                                 @"$select=displayName,mail,mailboxSettings,userPrincipalName"];
        NSURL* meUrl = [[NSURL alloc] initWithString:meUrlString];
        NSMutableURLRequest* meRequest = [[NSMutableURLRequest alloc] initWithURL:meUrl];

        MSURLSessionDataTask* meDataTask =
        [[MSURLSessionDataTask alloc]
            initWithRequest:meRequest
            client:self.graphClient
            completion:^(NSData *data, NSURLResponse *response, NSError *error) {
                if (error) {
                    completion(nil, error);
                    return;
                }

                // Deserialize the response as a user
                NSError* graphError;
                MSGraphUser* user = [[MSGraphUser alloc] initWithData:data error:&graphError];

                if (graphError) {
                    completion(nil, graphError);
                } else {
                    completion(user, nil);
                }
            }];

        // Execute the request
        [meDataTask execute];
    }

    @end
    ```

1. **WelcomeViewController.m** を開き、ファイルの上部に `#import` 次のステートメントを追加します。

    ```objc
    #import "SpinnerViewController.h"
    #import "GraphManager.h"
    #import <MSGraphClientModels/MSGraphClientModels.h>
    ```

1. インターフェイス宣言に次のプロパティを `WelcomeViewController` 追加します。

    ```objc
    @property SpinnerViewController* spinner;
    ```

1. 既存のコードを次 `viewDidLoad` のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.m" id="ViewDidLoadSnippet":::

変更を保存してアプリを今すぐ再起動すると、サインイン後にユーザーの表示名とメール アドレスで UI が更新されます。
