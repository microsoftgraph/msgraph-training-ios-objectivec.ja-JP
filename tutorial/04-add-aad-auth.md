<!-- markdownlint-disable MD002 MD041 -->

この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。 これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。 これを行うには、 [iOS 用 Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)をアプリケーションに統合します。

1. **Authsettings. plist**という名前の**graphtutorial**プロジェクトに、新しい**プロパティリスト**ファイルを作成します。
1. 次の項目を**ルート**ディクショナリのファイルに追加します。

    | キー | 型 | 値 |
    |-----|------|-------|
    | `AppId` | String | Azure portal からのアプリケーション ID |
    | `GraphScopes` | 配列 | 2つの文字列`User.Read`値:`Calendars.Read` |

    ![Xcode の AuthSettings plist ファイルのスクリーンショット](./images/auth-settings.png)

> [!IMPORTANT]
> Git などのソース管理を使用している場合は、アプリ ID が誤ってリークしないように、ソース管理から**Authsettings の設定**ファイルを除外することをお勧めします。

## <a name="implement-sign-in"></a>サインインの実装

このセクションでは、MSAL のプロジェクトを構成し、認証マネージャークラスを作成し、サインインしてサインアウトするためにアプリを更新します。

### <a name="configure-project-for-msal"></a>MSAL のプロジェクトを構成する

1. プロジェクトの機能に新しいキーチェーングループを追加します。
    1. **Graphtutorial**プロジェクトを選択し、 **& 機能に署名**します。
    1. [ **+ 機能**] を選択し、[**キーチェーン共有**] をダブルクリックします。
    1. 値`com.microsoft.adalcache`を持つキーチェーングループを追加します。

1. [コントロール] をクリックし、[**名前を付けて開く**]、[**ソースコード**] の順に選択し**ます。**
1. `<dict>`要素内に次のように追加します。

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

1. **Appdelegate**を開き、ファイルの先頭に次の import ステートメントを追加します。

    ```objc
    #import <MSAL/MSAL.h>
    ```

1. 次の関数を `AppDelegate` クラスに追加します。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AppDelegate.m" id="HandleMsalResponseSnippet":::

### <a name="create-authentication-manager"></a>認証マネージャーを作成する

1. 「 **Authenticationmanager**」という名前の**graphtutorial**プロジェクトに新しい**cocoa タッチクラス**を作成します。 フィールド**のサブクラス**で [ **NSObject** ] を選択します。
1. **Authenticationmanager .h**を開き、その内容を次のコードで置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.h" id="AuthManagerSnippet":::

1. **Authenticationmanager. m**を開き、その内容を次のコードで置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.m" id="AuthManagerSnippet":::

### <a name="add-sign-in-and-sign-out"></a>サインインとサインアウトを追加する

1. **SignInViewController**ファイルを開き、その内容を次のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/SignInViewController.m" id="SignInViewSnippet":::

1. **WelcomeViewController**を開き、次`import`のステートメントをファイルの先頭に追加します。

    ```objc
    #import "AuthenticationManager.h"
    ```

1. 既存の `signOut` 関数を、以下の関数で置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.m" id="SignOutSnippet":::

1. 変更を保存し、シミュレータでアプリケーションを再起動します。

アプリにサインインすると、Xcode の [出力] ウィンドウにアクセストークンが表示されます。

![Xcode でアクセストークンが表示されている出力ウィンドウのスクリーンショット](./images/access-token-output.png)

## <a name="get-user-details"></a>ユーザーの詳細情報を取得する

このセクションでは、Microsoft Graph へのすべての呼び出しを保持するヘルパークラスを作成し、 `WelcomeViewController`を更新して、この新しいクラスを使用してログインしているユーザーを取得します。

1. **Graphtutorial**という名前の**graphtutorial**プロジェクトに新しい**cocoa タッチクラス**を作成します。 フィールド**のサブクラス**で [ **NSObject** ] を選択します。
1. **Graphmanager**を開き、その内容を次のコードで置き換えます。

    ```objc
    #import <Foundation/Foundation.h>
    #import <MSGraphClientSDK/MSGraphClientSDK.h>
    #import <MSGraphClientModels/MSGraphClientModels.h>
    #import <MSGraphClientModels/MSCollection.h>
    #import "AuthenticationManager.h"

    NS_ASSUME_NONNULL_BEGIN

    typedef void (^GetMeCompletionBlock)(MSGraphUser* _Nullable user, NSError* _Nullable error);

    @interface GraphManager : NSObject

    + (id) instance;
    - (void) getMeWithCompletionBlock: (GetMeCompletionBlock)completionBlock;

    @end

    NS_ASSUME_NONNULL_END
    ```

1. **Graphmanager. m**を開き、その内容を次のコードで置き換えます。

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

    - (void) getMeWithCompletionBlock:(GetMeCompletionBlock)completionBlock {
        // GET /me
        NSString* meUrlString = [NSString stringWithFormat:@"%@/me", MSGraphBaseURL];
        NSURL* meUrl = [[NSURL alloc] initWithString:meUrlString];
        NSMutableURLRequest* meRequest = [[NSMutableURLRequest alloc] initWithURL:meUrl];

        MSURLSessionDataTask* meDataTask =
        [[MSURLSessionDataTask alloc]
            initWithRequest:meRequest
            client:self.graphClient
            completion:^(NSData *data, NSURLResponse *response, NSError *error) {
                if (error) {
                    completionBlock(nil, error);
                    return;
                }

                // Deserialize the response as a user
                NSError* graphError;
                MSGraphUser* user = [[MSGraphUser alloc] initWithData:data error:&graphError];

                if (graphError) {
                    completionBlock(nil, graphError);
                } else {
                    completionBlock(user, nil);
                }
            }];

        // Execute the request
        [meDataTask execute];
    }

    @end
    ```

1. **WelcomeViewController**を開き、次`#import`のステートメントをファイルの先頭に追加します。

    ```objc
    #import "SpinnerViewController.h"
    #import "GraphManager.h"
    #import <MSGraphClientModels/MSGraphClientModels.h>
    ```

1. 次のプロパティを`WelcomeViewController`インターフェイス宣言に追加します。

    ```objc
    @property SpinnerViewController* spinner;
    ```

1. 既存`viewDidLoad`のを次のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.m" id="ViewDidLoadSnippet":::

変更を保存して今すぐアプリを再起動すると、サインイン後にユーザーの表示名と電子メールアドレスで UI が更新されます。
