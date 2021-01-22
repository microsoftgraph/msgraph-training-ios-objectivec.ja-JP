<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="54b64-101">この演習では、前の演習のアプリケーションを拡張して、Azure AD での認証をサポートします。</span><span class="sxs-lookup"><span data-stu-id="54b64-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="54b64-102">これは、Microsoft Graph を呼び出すのに必要な OAuth アクセス トークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="54b64-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="54b64-103">これを行うには [、iOS 用 Microsoft Authentication Library (MSAL) を](https://github.com/AzureAD/microsoft-authentication-library-for-objc) アプリケーションに統合します。</span><span class="sxs-lookup"><span data-stu-id="54b64-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) into the application.</span></span>

1. <span data-ttu-id="54b64-104">**AuthSettings.plist** という名前の **GraphTu graphl** プロジェクトに新しいプロパティ リスト ファイルを作成します。 </span><span class="sxs-lookup"><span data-stu-id="54b64-104">Create a new **Property List** file in the **GraphTutorial** project named **AuthSettings.plist**.</span></span>
1. <span data-ttu-id="54b64-105">ルート ディクショナリ内のファイルに次の項目 **を** 追加します。</span><span class="sxs-lookup"><span data-stu-id="54b64-105">Add the following items to the file in the **Root** dictionary.</span></span>

    | <span data-ttu-id="54b64-106">キー</span><span class="sxs-lookup"><span data-stu-id="54b64-106">Key</span></span> | <span data-ttu-id="54b64-107">型</span><span class="sxs-lookup"><span data-stu-id="54b64-107">Type</span></span> | <span data-ttu-id="54b64-108">値</span><span class="sxs-lookup"><span data-stu-id="54b64-108">Value</span></span> |
    |-----|------|-------|
    | `AppId` | <span data-ttu-id="54b64-109">String</span><span class="sxs-lookup"><span data-stu-id="54b64-109">String</span></span> | <span data-ttu-id="54b64-110">Azure Portal からのアプリケーション ID</span><span class="sxs-lookup"><span data-stu-id="54b64-110">The application ID from the Azure portal</span></span> |
    | `GraphScopes` | <span data-ttu-id="54b64-111">配列</span><span class="sxs-lookup"><span data-stu-id="54b64-111">Array</span></span> | <span data-ttu-id="54b64-112">3 つの文字列値: `User.Read` `MailboxSettings.Read` 、 、 `Calendars.ReadWrite`</span><span class="sxs-lookup"><span data-stu-id="54b64-112">Three String values: `User.Read`, `MailboxSettings.Read`, and `Calendars.ReadWrite`</span></span> |

    ![Xcode の AuthSettings.plist ファイルのスクリーンショット](./images/auth-settings.png)

> [!IMPORTANT]
> <span data-ttu-id="54b64-114">git などのソース管理を使用している場合は **、AuthSettings.plist** ファイルをソース管理から除外して、アプリ ID が誤って漏洩しないようにする良い時期です。</span><span class="sxs-lookup"><span data-stu-id="54b64-114">If you're using source control such as git, now would be a good time to exclude the **AuthSettings.plist** file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="54b64-115">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="54b64-115">Implement sign-in</span></span>

<span data-ttu-id="54b64-116">このセクションでは、MSAL のプロジェクトを構成し、認証マネージャー クラスを作成し、サインインしてサインアウトするアプリを更新します。</span><span class="sxs-lookup"><span data-stu-id="54b64-116">In this section you will configure the project for MSAL, create an authentication manager class, and update the app to sign in and sign out.</span></span>

### <a name="configure-project-for-msal"></a><span data-ttu-id="54b64-117">MSAL 用にプロジェクトを構成する</span><span class="sxs-lookup"><span data-stu-id="54b64-117">Configure project for MSAL</span></span>

1. <span data-ttu-id="54b64-118">プロジェクトの機能に新しいキーチェーン グループを追加します。</span><span class="sxs-lookup"><span data-stu-id="54b64-118">Add a new keychain group to your project's capabilities.</span></span>
    1. <span data-ttu-id="54b64-119">**GraphTu読み込みプロジェクトを** 選択し、次に **[&機能] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="54b64-119">Select the **GraphTutorial** project, then **Signing & Capabilities**.</span></span>
    1. <span data-ttu-id="54b64-120">[+ **機能] を** 選択し、[キーチェーンの共有] **をダブルクリックします**。</span><span class="sxs-lookup"><span data-stu-id="54b64-120">Select **+ Capability**, then double-click **Keychain Sharing**.</span></span>
    1. <span data-ttu-id="54b64-121">値を持つキーチェーン グループを追加します `com.microsoft.adalcache` 。</span><span class="sxs-lookup"><span data-stu-id="54b64-121">Add a keychain group with the value `com.microsoft.adalcache`.</span></span>

1. <span data-ttu-id="54b64-122">**[Info.plist] をクリックし、[** ファイル名を指定して **開く]、次** に [ソース コード]**の順に選択します**。</span><span class="sxs-lookup"><span data-stu-id="54b64-122">Control click **Info.plist** and select **Open As**, then **Source Code**.</span></span>
1. <span data-ttu-id="54b64-123">要素内に次を追加 `<dict>` します。</span><span class="sxs-lookup"><span data-stu-id="54b64-123">Add the following inside the `<dict>` element.</span></span>

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

1. <span data-ttu-id="54b64-124">**AppDelegate.m** を開き、ファイルの上部に次の import ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="54b64-124">Open **AppDelegate.m** and add the following import statement at the top of the file.</span></span>

    ```objc
    #import <MSAL/MSAL.h>
    ```

1. <span data-ttu-id="54b64-125">次の関数を `AppDelegate` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="54b64-125">Add the following function to the `AppDelegate` class.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AppDelegate.m" id="HandleMsalResponseSnippet":::

### <a name="create-authentication-manager"></a><span data-ttu-id="54b64-126">認証マネージャーを作成する</span><span class="sxs-lookup"><span data-stu-id="54b64-126">Create authentication manager</span></span>

1. <span data-ttu-id="54b64-127">AuthenticationManager という **名前の GraphTu** **touchl** プロジェクトに新しい Cocoa Touch クラス **を作成します**。</span><span class="sxs-lookup"><span data-stu-id="54b64-127">Create a new **Cocoa Touch Class** in the **GraphTutorial** project named **AuthenticationManager**.</span></span> <span data-ttu-id="54b64-128">フィールド **のサブクラスで NSObject** **を選択** します。</span><span class="sxs-lookup"><span data-stu-id="54b64-128">Choose **NSObject** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="54b64-129">**AuthenticationManager.h を開き**、その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="54b64-129">Open **AuthenticationManager.h** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.h" id="AuthManagerSnippet":::

1. <span data-ttu-id="54b64-130">**AuthenticationManager.m を開** き、その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="54b64-130">Open **AuthenticationManager.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.m" id="AuthManagerSnippet":::

### <a name="add-sign-in-and-sign-out"></a><span data-ttu-id="54b64-131">サインインとサインアウトを追加する</span><span class="sxs-lookup"><span data-stu-id="54b64-131">Add sign-in and sign-out</span></span>

1. <span data-ttu-id="54b64-132">**SignInViewController.m ファイル** を開き、その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="54b64-132">Open the **SignInViewController.m** file and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/SignInViewController.m" id="SignInViewSnippet":::

1. <span data-ttu-id="54b64-133">**WelcomeViewController.m** を開き、ファイルの一番上に次 `import` のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="54b64-133">Open **WelcomeViewController.m** and add the following `import` statement to the top of the file.</span></span>

    ```objc
    #import "AuthenticationManager.h"
    ```

1. <span data-ttu-id="54b64-134">既存の `signOut` 関数を、以下の関数で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="54b64-134">Replace the existing `signOut` function with the following.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.m" id="SignOutSnippet":::

1. <span data-ttu-id="54b64-135">変更を保存し、シミュレーターでアプリケーションを再起動します。</span><span class="sxs-lookup"><span data-stu-id="54b64-135">Save your changes and restart the application in Simulator.</span></span>

<span data-ttu-id="54b64-136">アプリにサインインすると、Xcode の出力ウィンドウにアクセス トークンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="54b64-136">If you sign in to the app, you should see an access token displayed in the output window in Xcode.</span></span>

![アクセス トークンを示す Xcode の出力ウィンドウのスクリーンショット](./images/access-token-output.png)

## <a name="get-user-details"></a><span data-ttu-id="54b64-138">ユーザーの詳細情報を取得する</span><span class="sxs-lookup"><span data-stu-id="54b64-138">Get user details</span></span>

<span data-ttu-id="54b64-139">このセクションでは、Microsoft Graph へのすべての呼び出しを保持するヘルパー クラスを作成し、この新しいクラスを使用してログイン ユーザーを取得するために更新 `WelcomeViewController` します。</span><span class="sxs-lookup"><span data-stu-id="54b64-139">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `WelcomeViewController` to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="54b64-140">GraphManager という **名前の GraphTu** **touchl** プロジェクトに新しい Cocoa Touch クラス **を作成します**。</span><span class="sxs-lookup"><span data-stu-id="54b64-140">Create a new **Cocoa Touch Class** in the **GraphTutorial** project named **GraphManager**.</span></span> <span data-ttu-id="54b64-141">フィールド **のサブクラスで NSObject** **を選択** します。</span><span class="sxs-lookup"><span data-stu-id="54b64-141">Choose **NSObject** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="54b64-142">**GraphManager.h を開** き、その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="54b64-142">Open **GraphManager.h** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="54b64-143">**GraphManager.m を開** き、その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="54b64-143">Open **GraphManager.m** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="54b64-144">**WelcomeViewController.m** を開き、ファイルの上部に `#import` 次のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="54b64-144">Open **WelcomeViewController.m** and add the following `#import` statements at the top of the file.</span></span>

    ```objc
    #import "SpinnerViewController.h"
    #import "GraphManager.h"
    #import <MSGraphClientModels/MSGraphClientModels.h>
    ```

1. <span data-ttu-id="54b64-145">インターフェイス宣言に次のプロパティを `WelcomeViewController` 追加します。</span><span class="sxs-lookup"><span data-stu-id="54b64-145">Add the following property to the `WelcomeViewController` interface declaration.</span></span>

    ```objc
    @property SpinnerViewController* spinner;
    ```

1. <span data-ttu-id="54b64-146">既存のコードを次 `viewDidLoad` のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="54b64-146">Replace the existing `viewDidLoad` with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.m" id="ViewDidLoadSnippet":::

<span data-ttu-id="54b64-147">変更を保存してアプリを今すぐ再起動すると、サインイン後にユーザーの表示名とメール アドレスで UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="54b64-147">If you save your changes and restart the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
