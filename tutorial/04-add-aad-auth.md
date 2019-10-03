<!-- markdownlint-disable MD002 MD041 -->

この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。 これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。 これを行うには、 [iOS 用 Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)をアプリケーションに統合します。

1. **Authsettings. plist**という名前の**graphtutorial**プロジェクトに、新しい**プロパティリスト**ファイルを作成します。
1. 次の項目を**ルート**ディクショナリのファイルに追加します。

    | Key | 型 | 値 |
    |-----|------|-------|
    | `AppId` | String | Azure portal からのアプリケーション ID |
    | `GraphScopes` | 配列 | 2つの文字列`User.Read`値:`Calendars.Read` |

    ![Xcode の AuthSettings plist ファイルのスクリーンショット](./images/auth-settings.png)

> [!IMPORTANT]
> Git などのソース管理を使用している場合は、アプリ ID が誤ってリークしないように、ソース管理から**Authsettings の設定**ファイルを除外することをお勧めします。

## <a name="implement-sign-in"></a>サインインの実装

このセクションでは、MSAL のプロジェクトを構成し、認証マネージャークラスを作成し、サインインしてサインアウトするためにアプリを更新します。

### <a name="configure-project-for-msal"></a>MSAL のプロジェクトを構成する

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

    ```objc
    - (BOOL)application:(UIApplication *)app
                openURL:(NSURL *)url
                options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url
                                             sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```

### <a name="create-authentication-manager"></a>認証マネージャーを作成する

1. 「 **Authenticationmanager**」という名前の**graphtutorial**プロジェクトに新しい**cocoa タッチクラス**を作成します。 フィールド**のサブクラス**で [ **NSObject** ] を選択します。
1. **Authenticationmanager .h**を開き、その内容を次のコードで置き換えます。

    ```objc
    #import <Foundation/Foundation.h>
    #import <MSAL/MSAL.h>

    NS_ASSUME_NONNULL_BEGIN

    typedef void (^GetTokenCompletionBlock)(NSString* _Nullable accessToken, NSError* _Nullable error);

    @interface AuthenticationManager : NSObject

    + (id) instance;
    - (void) getTokenInteractivelyWithCompletionBlock: (GetTokenCompletionBlock)completionBlock;
    - (void) getTokenSilentlyWithCompletionBlock: (GetTokenCompletionBlock)completionBlock;
    - (void) signOut;

    @end

    NS_ASSUME_NONNULL_END
    ```

1. **Authenticationmanager. m**を開き、その内容を次のコードで置き換えます。

    ```objc
    #import "AuthenticationManager.h"

    @interface AuthenticationManager()

    @property NSString* appId;
    @property NSArray<NSString*>* graphScopes;
    @property MSALPublicClientApplication* publicClient;

    @end

    @implementation AuthenticationManager

    + (id) instance {
        static AuthenticationManager *singleInstance = nil;
        static dispatch_once_t onceToken;
        dispatch_once(&onceToken, ^ {
            singleInstance = [[self alloc] init];
        });

        return singleInstance;
    }

    - (id) init {
        if (self = [super init]) {
            // Get app ID and scopes from AuthSettings.plist
            NSString* authConfigPath =
            [NSBundle.mainBundle pathForResource:@"AuthSettings" ofType:@"plist"];
            NSString* bundleId = NSBundle.mainBundle.bundleIdentifier;
            NSDictionary* authConfig = [NSDictionary dictionaryWithContentsOfFile:authConfigPath];

            self.appId = authConfig[@"AppId"];
            self.graphScopes = authConfig[@"GraphScopes"];

            // Create the MSAL client
            self.publicClient = [[MSALPublicClientApplication alloc] initWithClientId:self.appId
                                                                        keychainGroup:bundleId
                                                                                error:nil];
        }

        return self;
    }

    - (void) getTokenInteractivelyWithCompletionBlock:(GetTokenCompletionBlock)completionBlock {
        // Call acquireToken to open a browser so the user can sign in
        [self.publicClient
         acquireTokenForScopes:self.graphScopes
         completionBlock:^(MSALResult * _Nullable result, NSError * _Nullable error) {

            // Check error
            if (error) {
                completionBlock(nil, error);
                return;
            }

            // Check result
            if (!result) {
                NSMutableDictionary* details = [NSMutableDictionary dictionary];
                [details setValue:@"No result was returned" forKey:NSDebugDescriptionErrorKey];
                completionBlock(nil, [NSError errorWithDomain:@"AuthenticationManager" code:0 userInfo:details]);
                return;
            }

            NSLog(@"Got token interactively: %@", result.accessToken);
            completionBlock(result.accessToken, nil);
        }];
    }

    - (void) getTokenSilentlyWithCompletionBlock:(GetTokenCompletionBlock)completionBlock {
        // Check if there is an account in the cache
        NSError* msalError;
        MSALAccount* account = [self.publicClient allAccounts:&msalError].firstObject;

        if (msalError || !account) {
            NSMutableDictionary* details = [NSMutableDictionary dictionary];
            [details setValue:@"Could not retrieve account from cache" forKey:NSDebugDescriptionErrorKey];
            completionBlock(nil, [NSError errorWithDomain:@"AuthenticationManager" code:0 userInfo:details]);
            return;
        }

        // Attempt to get token silently
        [self.publicClient
         acquireTokenSilentForScopes:self.graphScopes account:account
         completionBlock:^(MSALResult * _Nullable result, NSError * _Nullable error) {
             // Check error
             if (error) {
                 completionBlock(nil, error);
                 return;
             }

             // Check result
             if (!result) {
                 NSMutableDictionary* details = [NSMutableDictionary dictionary];
                 [details setValue:@"No result was returned" forKey:NSDebugDescriptionErrorKey];
                 completionBlock(nil, [NSError errorWithDomain:@"AuthenticationManager" code:0 userInfo:details]);
                 return;
             }

             NSLog(@"Got token silently: %@", result.accessToken);
             completionBlock(result.accessToken, nil);
         }];
    }

    - (void) signOut {
        NSError* msalError;
        NSArray* accounts = [self.publicClient allAccounts:&msalError];

        if (msalError) {
            NSLog(@"Error getting accounts from cache: %@", msalError.debugDescription);
            return;
        }

        for (id account in accounts) {
            [self.publicClient removeAccount:account error:nil];
        }
    }

    @end
    ```

### <a name="add-sign-in-and-sign-out"></a>サインインとサインアウトを追加する

1. **SignInViewController**ファイルを開き、その内容を次のコードに置き換えます。

    ```objc
    #import "SignInViewController.h"
    #import "SpinnerViewController.h"
    #import "AuthenticationManager.h"

    @interface SignInViewController ()
    @property SpinnerViewController* spinner;
    @end

    @implementation SignInViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.

        self.spinner = [SpinnerViewController alloc];
        [self.spinner startWithContainer:self];

        [AuthenticationManager.instance
         getTokenSilentlyWithCompletionBlock:^(NSString * _Nullable accessToken, NSError * _Nullable error) {
             dispatch_async(dispatch_get_main_queue(), ^{
                 [self.spinner stop];

                 if (error || !accessToken) {
                     // If there is no token or if there's an error,
                     // no user is signed in, so stay here
                     return;
                 }

                 // Since we got a token, user is signed in
                 // Go to welcome page
                 [self performSegueWithIdentifier: @"userSignedIn" sender: nil];
             });
        }];
    }

    - (IBAction)signIn {
        self.spinner = [SpinnerViewController alloc];
        [self.spinner startWithContainer:self];

        [AuthenticationManager.instance
         getTokenInteractivelyWithCompletionBlock:^(NSString * _Nullable accessToken, NSError * _Nullable error) {
             dispatch_async(dispatch_get_main_queue(), ^{
                 [self.spinner stop];

                 if (error || !accessToken) {
                     // Show the error and stay on the sign-in page
                     UIAlertController* alert = [UIAlertController
                                                 alertControllerWithTitle:@"Error signing in"
                                                 message:error.debugDescription
                                                 preferredStyle:UIAlertControllerStyleAlert];

                     UIAlertAction* okButton = [UIAlertAction
                                                actionWithTitle:@"OK"
                                                style:UIAlertActionStyleDefault
                                                handler:nil];

                     [alert addAction:okButton];
                     [self presentViewController:alert animated:true completion:nil];
                     return;
                 }

                 // Since we got a token, user is signed in
                 // Go to welcome page
                 [self performSegueWithIdentifier: @"userSignedIn" sender: nil];
             });
         }];
    }
    @end
    ```

1. **WelcomeViewController**を開き、既存`signOut`の関数を次のように置き換えます。

    ```objc
    - (IBAction)signOut {
        [AuthenticationManager.instance signOut];
        [self performSegueWithIdentifier: @"userSignedOut" sender: nil];
    }
    ```

1. 変更を保存し、シミュレータでアプリケーションを再起動します。

アプリにサインインすると、Xcode の [出力] ウィンドウにアクセストークンが表示されます。

![Xcode でアクセストークンが表示されている出力ウィンドウのスクリーンショット](./images/access-token-output.png)

## <a name="get-user-details"></a>ユーザーの詳細を取得する

このセクションでは、Microsoft Graph へのすべての呼び出しを保持するヘルパークラスを作成し、 `WelcomeViewController`を更新して、この新しいクラスを使用してログインしているユーザーを取得します。

1. **Authenticationmanager .h**を開き、次`#import`のステートメントをファイルの先頭に追加します。

    ```objc
    #import <MSGraphMSALAuthProvider/MSGraphMSALAuthProvider.h>
    ```

1. 宣言内に`@interface`次の行を追加します。

    ```objc
    - (MSALAuthenticationProvider*) getGraphAuthProvider;
    ```

1. **Authenticationmanager. m**を開き、次の関数を`AuthenticationManager`クラスに追加します。

    ```objc
    - (MSALAuthenticationProvider*) getGraphAuthProvider {
        // Create an MSAL auth provider for use with the Graph client
        return [[MSALAuthenticationProvider alloc]
                initWithPublicClientApplication:self.publicClient
                andScopes:self.graphScopes];
    }
    ```

1. **Graphtutorial**という名前の**graphtutorial**プロジェクトに新しい**cocoa タッチクラス**を作成します。 フィールド**のサブクラス**で [ **NSObject** ] を選択します。
1. **Graphmanager**を開き、その内容を次のコードで置き換えます。

    ```objc
    #import <Foundation/Foundation.h>
    #import <MSGraphClientSDK/MSGraphClientSDK.h>
    #import <MSGraphClientModels/MSGraphClientModels.h>
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

            MSALAuthenticationProvider* authProvider =
            [AuthenticationManager.instance getGraphAuthProvider];

            // Create the Graph client
            self.graphClient = [MSClientFactory
                                createHTTPClientWithAuthenticationProvider:authProvider];
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

    ```objc
    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.

        self.spinner = [SpinnerViewController alloc];
        [self.spinner startWithContainer:self];

        self.userProfilePhoto.image = [UIImage imageNamed:@"DefaultUserPhoto"];

        [GraphManager.instance
         getMeWithCompletionBlock:^(MSGraphUser * _Nullable user, NSError * _Nullable error) {
            dispatch_async(dispatch_get_main_queue(), ^{
                [self.spinner stop];

                if (error) {
                    // Show the error
                    UIAlertController* alert = [UIAlertController
                                                alertControllerWithTitle:@"Error getting user profile"
                                                message:error.debugDescription
                                                preferredStyle:UIAlertControllerStyleAlert];

                    UIAlertAction* okButton = [UIAlertAction
                                               actionWithTitle:@"OK"
                                               style:UIAlertActionStyleDefault
                                               handler:nil];

                    [alert addAction:okButton];
                    [self presentViewController:alert animated:true completion:nil];
                    return;
                }

                // Set display name
                self.userDisplayName.text = user.displayName ? : @"Mysterious Stranger";
                [self.userDisplayName sizeToFit];

                // AAD users have email in the mail attribute
                // Personal accounts have email in the userPrincipalName attribute
                self.userEmail.text = user.mail ? : user.userPrincipalName;
                [self.userEmail sizeToFit];
            });
         }];
    }
    ```

変更を保存して今すぐアプリを再起動すると、サインイン後にユーザーの表示名と電子メールアドレスで UI が更新されます。
