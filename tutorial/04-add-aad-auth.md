<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="131f2-101">この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。</span><span class="sxs-lookup"><span data-stu-id="131f2-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="131f2-102">これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="131f2-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="131f2-103">これを行うには、 [iOS 用 Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)をアプリケーションに統合します。</span><span class="sxs-lookup"><span data-stu-id="131f2-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) into the application.</span></span>

1. <span data-ttu-id="131f2-104">**Authsettings. plist**という名前の**graphtutorial**プロジェクトに、新しい**プロパティリスト**ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="131f2-104">Create a new **Property List** file in the **GraphTutorial** project named **AuthSettings.plist**.</span></span>
1. <span data-ttu-id="131f2-105">次の項目を**ルート**ディクショナリのファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="131f2-105">Add the following items to the file in the **Root** dictionary.</span></span>

    | <span data-ttu-id="131f2-106">キー</span><span class="sxs-lookup"><span data-stu-id="131f2-106">Key</span></span> | <span data-ttu-id="131f2-107">型</span><span class="sxs-lookup"><span data-stu-id="131f2-107">Type</span></span> | <span data-ttu-id="131f2-108">値</span><span class="sxs-lookup"><span data-stu-id="131f2-108">Value</span></span> |
    |-----|------|-------|
    | `AppId` | <span data-ttu-id="131f2-109">String</span><span class="sxs-lookup"><span data-stu-id="131f2-109">String</span></span> | <span data-ttu-id="131f2-110">Azure portal からのアプリケーション ID</span><span class="sxs-lookup"><span data-stu-id="131f2-110">The application ID from the Azure portal</span></span> |
    | `GraphScopes` | <span data-ttu-id="131f2-111">配列</span><span class="sxs-lookup"><span data-stu-id="131f2-111">Array</span></span> | <span data-ttu-id="131f2-112">2つの文字列`User.Read`値:`Calendars.Read`</span><span class="sxs-lookup"><span data-stu-id="131f2-112">Two String values: `User.Read` and `Calendars.Read`</span></span> |

    ![Xcode の AuthSettings plist ファイルのスクリーンショット](./images/auth-settings.png)

> [!IMPORTANT]
> <span data-ttu-id="131f2-114">Git などのソース管理を使用している場合は、アプリ ID が誤ってリークしないように、ソース管理から**Authsettings の設定**ファイルを除外することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="131f2-114">If you're using source control such as git, now would be a good time to exclude the **AuthSettings.plist** file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="131f2-115">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="131f2-115">Implement sign-in</span></span>

<span data-ttu-id="131f2-116">このセクションでは、MSAL のプロジェクトを構成し、認証マネージャークラスを作成し、サインインしてサインアウトするためにアプリを更新します。</span><span class="sxs-lookup"><span data-stu-id="131f2-116">In this section you will configure the project for MSAL, create an authentication manager class, and update the app to sign in and sign out.</span></span>

### <a name="configure-project-for-msal"></a><span data-ttu-id="131f2-117">MSAL のプロジェクトを構成する</span><span class="sxs-lookup"><span data-stu-id="131f2-117">Configure project for MSAL</span></span>

1. <span data-ttu-id="131f2-118">プロジェクトの機能に新しいキーチェーングループを追加します。</span><span class="sxs-lookup"><span data-stu-id="131f2-118">Add a new keychain group to your project's capabilities.</span></span>
    1. <span data-ttu-id="131f2-119">**Graphtutorial**プロジェクトを選択し、 **& 機能に署名**します。</span><span class="sxs-lookup"><span data-stu-id="131f2-119">Select the **GraphTutorial** project, then **Signing & Capabilities**.</span></span>
    1. <span data-ttu-id="131f2-120">[ **+ 機能**] を選択し、[**キーチェーン共有**] をダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="131f2-120">Select **+ Capability**, then double-click **Keychain Sharing**.</span></span>
    1. <span data-ttu-id="131f2-121">値`com.microsoft.adalcache`を持つキーチェーングループを追加します。</span><span class="sxs-lookup"><span data-stu-id="131f2-121">Add a keychain group with the value `com.microsoft.adalcache`.</span></span>

1. <span data-ttu-id="131f2-122">[コントロール] をクリックし、[**名前を付けて開く**]、[**ソースコード**] の順に選択し**ます。**</span><span class="sxs-lookup"><span data-stu-id="131f2-122">Control click **Info.plist** and select **Open As**, then **Source Code**.</span></span>
1. <span data-ttu-id="131f2-123">`<dict>`要素内に次のように追加します。</span><span class="sxs-lookup"><span data-stu-id="131f2-123">Add the following inside the `<dict>` element.</span></span>

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

1. <span data-ttu-id="131f2-124">**Appdelegate**を開き、ファイルの先頭に次の import ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="131f2-124">Open **AppDelegate.m** and add the following import statement at the top of the file.</span></span>

    ```objc
    #import <MSAL/MSAL.h>
    ```

1. <span data-ttu-id="131f2-125">次の関数を `AppDelegate` クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="131f2-125">Add the following function to the `AppDelegate` class.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AppDelegate.m" id="HandleMsalResponseSnippet":::

### <a name="create-authentication-manager"></a><span data-ttu-id="131f2-126">認証マネージャーを作成する</span><span class="sxs-lookup"><span data-stu-id="131f2-126">Create authentication manager</span></span>

1. <span data-ttu-id="131f2-127">「 **Authenticationmanager**」という名前の**graphtutorial**プロジェクトに新しい**cocoa タッチクラス**を作成します。</span><span class="sxs-lookup"><span data-stu-id="131f2-127">Create a new **Cocoa Touch Class** in the **GraphTutorial** project named **AuthenticationManager**.</span></span> <span data-ttu-id="131f2-128">フィールド**のサブクラス**で [ **NSObject** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="131f2-128">Choose **NSObject** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="131f2-129">**Authenticationmanager .h**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="131f2-129">Open **AuthenticationManager.h** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.h" id="AuthManagerSnippet":::

1. <span data-ttu-id="131f2-130">**Authenticationmanager. m**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="131f2-130">Open **AuthenticationManager.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.m" id="AuthManagerSnippet":::

### <a name="add-sign-in-and-sign-out"></a><span data-ttu-id="131f2-131">サインインとサインアウトを追加する</span><span class="sxs-lookup"><span data-stu-id="131f2-131">Add sign-in and sign-out</span></span>

1. <span data-ttu-id="131f2-132">**SignInViewController**ファイルを開き、その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="131f2-132">Open the **SignInViewController.m** file and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/SignInViewController.m" id="SignInViewSnippet":::

1. <span data-ttu-id="131f2-133">**WelcomeViewController**を開き、次`import`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="131f2-133">Open **WelcomeViewController.m** and add the following `import` statement to the top of the file.</span></span>

    ```objc
    #import "AuthenticationManager.h"
    ```

1. <span data-ttu-id="131f2-134">既存の `signOut` 関数を、以下の関数で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="131f2-134">Replace the existing `signOut` function with the following.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.m" id="SignOutSnippet":::

1. <span data-ttu-id="131f2-135">変更を保存し、シミュレータでアプリケーションを再起動します。</span><span class="sxs-lookup"><span data-stu-id="131f2-135">Save your changes and restart the application in Simulator.</span></span>

<span data-ttu-id="131f2-136">アプリにサインインすると、Xcode の [出力] ウィンドウにアクセストークンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="131f2-136">If you sign in to the app, you should see an access token displayed in the output window in Xcode.</span></span>

![Xcode でアクセストークンが表示されている出力ウィンドウのスクリーンショット](./images/access-token-output.png)

## <a name="get-user-details"></a><span data-ttu-id="131f2-138">ユーザーの詳細情報を取得する</span><span class="sxs-lookup"><span data-stu-id="131f2-138">Get user details</span></span>

<span data-ttu-id="131f2-139">このセクションでは、Microsoft Graph へのすべての呼び出しを保持するヘルパークラスを作成し、 `WelcomeViewController`を更新して、この新しいクラスを使用してログインしているユーザーを取得します。</span><span class="sxs-lookup"><span data-stu-id="131f2-139">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `WelcomeViewController` to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="131f2-140">**Graphtutorial**という名前の**graphtutorial**プロジェクトに新しい**cocoa タッチクラス**を作成します。</span><span class="sxs-lookup"><span data-stu-id="131f2-140">Create a new **Cocoa Touch Class** in the **GraphTutorial** project named **GraphManager**.</span></span> <span data-ttu-id="131f2-141">フィールド**のサブクラス**で [ **NSObject** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="131f2-141">Choose **NSObject** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="131f2-142">**Graphmanager**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="131f2-142">Open **GraphManager.h** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="131f2-143">**Graphmanager. m**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="131f2-143">Open **GraphManager.m** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="131f2-144">**WelcomeViewController**を開き、次`#import`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="131f2-144">Open **WelcomeViewController.m** and add the following `#import` statements at the top of the file.</span></span>

    ```objc
    #import "SpinnerViewController.h"
    #import "GraphManager.h"
    #import <MSGraphClientModels/MSGraphClientModels.h>
    ```

1. <span data-ttu-id="131f2-145">次のプロパティを`WelcomeViewController`インターフェイス宣言に追加します。</span><span class="sxs-lookup"><span data-stu-id="131f2-145">Add the following property to the `WelcomeViewController` interface declaration.</span></span>

    ```objc
    @property SpinnerViewController* spinner;
    ```

1. <span data-ttu-id="131f2-146">既存`viewDidLoad`のを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="131f2-146">Replace the existing `viewDidLoad` with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.m" id="ViewDidLoadSnippet":::

<span data-ttu-id="131f2-147">変更を保存して今すぐアプリを再起動すると、サインイン後にユーザーの表示名と電子メールアドレスで UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="131f2-147">If you save your changes and restart the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
