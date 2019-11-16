<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f77ac-101">最初に、新しい Swift プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-101">Begin by creating a new Swift project.</span></span>

1. <span data-ttu-id="f77ac-102">Xcode を開きます。</span><span class="sxs-lookup"><span data-stu-id="f77ac-102">Open Xcode.</span></span> <span data-ttu-id="f77ac-103">[**ファイル**] メニューの [**新規作成**]、[**プロジェクト**] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-103">On the **File** menu, select **New**, then **Project**.</span></span>
1. <span data-ttu-id="f77ac-104">[**単一ビューアプリ**] テンプレートを選択し、[**次へ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-104">Choose the **Single View App** template and select **Next**.</span></span>

    ![Xcode テンプレートの選択ダイアログのスクリーンショット](./images/xcode-select-template.png)

1. <span data-ttu-id="f77ac-106">**製品名** `GraphTutorial`と**言語**を**目的-C**に設定します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-106">Set the **Product Name** to `GraphTutorial` and the **Language** to **Objective-C**.</span></span>
1. <span data-ttu-id="f77ac-107">残りのフィールドに入力し、[**次へ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-107">Fill in the remaining fields and select **Next**.</span></span>
1. <span data-ttu-id="f77ac-108">プロジェクトの場所を選択して、[**作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-108">Choose a location for the project and select **Create**.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="f77ac-109">依存関係のインストール</span><span class="sxs-lookup"><span data-stu-id="f77ac-109">Install dependencies</span></span>

<span data-ttu-id="f77ac-110">に進む前に、後で使用する追加の依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f77ac-110">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="f77ac-111">Azure AD での認証については、 [Microsoft Authentication Library (MSAL) For iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)を使用してください。</span><span class="sxs-lookup"><span data-stu-id="f77ac-111">[Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) for authenticating to with Azure AD.</span></span>
- <span data-ttu-id="f77ac-112">Microsoft graph に電話をかけるための[Microsoft GRAPH SDK For 客観 C](https://github.com/microsoftgraph/msgraph-sdk-objc) 。</span><span class="sxs-lookup"><span data-stu-id="f77ac-112">[Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="f77ac-113">Microsoft Graph では、ユーザーやイベントなどの Microsoft Graph のリソースを表す、厳密に型指定されたオブジェクト用の[Microsoft Graph モデル SDK を目的](https://github.com/microsoftgraph/msgraph-sdk-objc-models)としています。</span><span class="sxs-lookup"><span data-stu-id="f77ac-113">[Microsoft Graph Models SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) for strongly-typed objects representing Microsoft Graph resources like users or events.</span></span>

1. <span data-ttu-id="f77ac-114">Xcode を終了します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-114">Quit Xcode.</span></span>
1. <span data-ttu-id="f77ac-115">ターミナルを開き、グラフのディレクトリを**Graphtutorial**プロジェクトの場所に変更します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-115">Open Terminal and change the directory to the location of your **GraphTutorial** project.</span></span>
1. <span data-ttu-id="f77ac-116">Podfile を作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-116">Run the following command to create a Podfile.</span></span>

    ```Shell
    pod init
    ```

1. <span data-ttu-id="f77ac-117">Podfile を開き、行の`use_frameworks!`直後に次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-117">Open the Podfile and add the following lines just after the `use_frameworks!` line.</span></span>

    ```Ruby
    pod 'MSAL', '~> 1.0.2'
    pod 'MSGraphClientSDK', ' ~> 1.0.0'
    pod 'MSGraphClientModels', '~> 1.3.0'
    ```

1. <span data-ttu-id="f77ac-118">Podfile を保存してから、次のコマンドを実行して依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f77ac-118">Save the Podfile, then run the following command to install the dependencies.</span></span>

    ```Shell
    pod install
    ```

1. <span data-ttu-id="f77ac-119">コマンドが完了したら、Xcode で新しく作成した**graphtutorial**を開きます。</span><span class="sxs-lookup"><span data-stu-id="f77ac-119">Once the command completes, open the newly created **GraphTutorial.xcworkspace** in Xcode.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="f77ac-120">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="f77ac-120">Design the app</span></span>

<span data-ttu-id="f77ac-121">このセクションでは、アプリのビュー (サインインページ、タブバーナビゲーター、ウェルカムページ、予定表ページ) を作成します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-121">In this section you will create the views for the app: a sign in page, a tab bar navigator, a welcome page, and a calendar page.</span></span> <span data-ttu-id="f77ac-122">また、アクティビティインジケーターオーバーレイも作成します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-122">You'll also create an activity indicator overlay.</span></span>

### <a name="create-sign-in-page"></a><span data-ttu-id="f77ac-123">サインインページの作成</span><span class="sxs-lookup"><span data-stu-id="f77ac-123">Create sign in page</span></span>

1. <span data-ttu-id="f77ac-124">Xcode の**Graphtutorial**フォルダーを展開し、 **viewcontroller**ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-124">Expand the **GraphTutorial** folder in Xcode, then select the **ViewController.m** file.</span></span>
1. <span data-ttu-id="f77ac-125">**ファイルインスペクター**で、ファイルの**名前**をに`SignInViewController.m`変更します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-125">In the **File Inspector**, change the **Name** of the file to `SignInViewController.m`.</span></span>

    ![ファイル検査のスクリーンショット](./images/rename-file.png)

1. <span data-ttu-id="f77ac-127">**SignInViewController**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f77ac-127">Open **SignInViewController.m** and replace its contents with the following code.</span></span>

    ```objc
    #import "SignInViewController.h"

    @interface SignInViewController ()

    @end

    @implementation SignInViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.
    }

    - (IBAction)signIn {
        [self performSegueWithIdentifier: @"userSignedIn" sender: nil];
    }
    @end
    ```

1. <span data-ttu-id="f77ac-128">**Viewcontroller .h**ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-128">Select the **ViewController.h** file.</span></span>
1. <span data-ttu-id="f77ac-129">**ファイルインスペクター**で、ファイルの**名前**をに`SignInViewController.h`変更します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-129">In the **File Inspector**, change the **Name** of the file to `SignInViewController.h`.</span></span>
1. <span data-ttu-id="f77ac-130">**SignInViewController**を開き、のすべての`ViewController`インスタンスをに`SignInViewController`変更します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-130">Open **SignInViewController.h** and change all instances of `ViewController` to `SignInViewController`.</span></span>

1. <span data-ttu-id="f77ac-131">メインの**ストーリーボード**ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="f77ac-131">Open the **Main.storyboard** file.</span></span>
1. <span data-ttu-id="f77ac-132">[**ビューコントローラーシーン**] を展開し、[**ビューコントローラー**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-132">Expand **View Controller Scene**, then select **View Controller**.</span></span>

    ![ビューコントローラーが選択されている Xcode のスクリーンショット](./images/storyboard-select-view-controller.png)

1. <span data-ttu-id="f77ac-134">[ **Identity Inspector**] を選択してから、**クラス**のドロップダウンを**SignInViewController**に変更します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-134">Select the **Identity Inspector**, then change the **Class** dropdown to **SignInViewController**.</span></span>

    ![Id インスペクターのスクリーンショット](./images/change-class.png)

1. <span data-ttu-id="f77ac-136">**ライブラリ**を選択し、**ボタン**を [サインイン]**ビューコントローラー**にドラッグします。</span><span class="sxs-lookup"><span data-stu-id="f77ac-136">Select the **Library**, then drag a **Button** onto the **Sign In View Controller**.</span></span>

    ![Xcode のライブラリのスクリーンショット](./images/add-button-to-view.png)

1. <span data-ttu-id="f77ac-138">ボタンを選択した状態で、[ **Attributes Inspector** ] \*\*\*\* を選択し、ボタン`Sign In`のタイトルをに変更します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-138">With the button selected, select the **Attributes Inspector** and change the **Title** of the button to `Sign In`.</span></span>

    ![Xcode の Attributes インスペクターのタイトルフィールドのスクリーンショット](./images/set-button-title.png)

1. <span data-ttu-id="f77ac-140">**サインインビューコントローラー**を選択し、[**接続インスペクター**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-140">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="f77ac-141">[**受信した処理**] で、[**サインイン**] の隣にある円をボタンにドラッグします。</span><span class="sxs-lookup"><span data-stu-id="f77ac-141">Under **Received Actions**, drag the unfilled circle next to **signIn** onto the button.</span></span> <span data-ttu-id="f77ac-142">ポップアップメニューで [ **Touch Up** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-142">Select **Touch Up Inside** on the pop-up menu.</span></span>

    ![Xcode のボタンにサインイン操作をドラッグした場合のスクリーンショット](./images/connect-sign-in-button.png)

1. <span data-ttu-id="f77ac-144">[**エディター** ] メニューの [**自動レイアウトの問題を解決**する] を選択し、[すべてのビューの下**にある**[不足している**制約の追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-144">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Sign In View Controller**.</span></span>

### <a name="create-tab-bar"></a><span data-ttu-id="f77ac-145">タブバーを作成する</span><span class="sxs-lookup"><span data-stu-id="f77ac-145">Create tab bar</span></span>

1. <span data-ttu-id="f77ac-146">**ライブラリ**を選択し、**タブバーコントローラー**をストーリーボードにドラッグします。</span><span class="sxs-lookup"><span data-stu-id="f77ac-146">Select the **Library**, then drag a **Tab Bar Controller** onto the storyboard.</span></span>
1. <span data-ttu-id="f77ac-147">**サインインビューコントローラー**を選択し、[**接続インスペクター**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-147">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="f77ac-148">[**トリガー**された Segues] で、[**手動**] の横にある円を、ストーリーボードの**タブバーコントローラー**にドラッグします。</span><span class="sxs-lookup"><span data-stu-id="f77ac-148">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Tab Bar Controller** on the storyboard.</span></span> <span data-ttu-id="f77ac-149">ポップアップメニューの [**モーダル**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-149">Select **Present Modally** in the pop-up menu.</span></span>

    ![Xcode の新しいタブバーコントローラーに手動 segue をドラッグするスクリーンショット](./images/add-segue.png)

1. <span data-ttu-id="f77ac-151">追加した segue を選択し、[ **Attributes Inspector**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-151">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="f77ac-152">**識別子** `userSignedIn`フィールドをに設定し、**プレゼンテーション**を**全画面表示**に設定します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-152">Set the **Identifier** field to `userSignedIn`, and set **Presentation** to **Full Screen**.</span></span>

    ![Xcode の Attributes インスペクターの識別子フィールドのスクリーンショット](./images/set-segue-identifier.png)

1. <span data-ttu-id="f77ac-154">**アイテム1のシーン**を選択し、[**接続インスペクター**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-154">Select the **Item 1 Scene**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="f77ac-155">[**トリガー**された Segues] で、[**手動**] の横にある円を、ストーリーボードの**サインインビューコントローラー**にドラッグします。</span><span class="sxs-lookup"><span data-stu-id="f77ac-155">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Sign In View Controller** on the storyboard.</span></span> <span data-ttu-id="f77ac-156">ポップアップメニューの [**モーダル**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-156">Select **Present Modally** in the pop-up menu.</span></span>
1. <span data-ttu-id="f77ac-157">追加した segue を選択し、[ **Attributes Inspector**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-157">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="f77ac-158">**識別子** `userSignedOut`フィールドをに設定し、**プレゼンテーション**を**全画面表示**に設定します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-158">Set the **Identifier** field to `userSignedOut`, and set **Presentation** to **Full Screen**.</span></span>

### <a name="create-welcome-page"></a><span data-ttu-id="f77ac-159">ウェルカムページを作成する</span><span class="sxs-lookup"><span data-stu-id="f77ac-159">Create welcome page</span></span>

1. <span data-ttu-id="f77ac-160">[ **Assets] セット**ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-160">Select the **Assets.xcassets** file.</span></span>
1. <span data-ttu-id="f77ac-161">[**エディター** ] メニューの [**アセットの追加**] を選択し、[**新しいイメージセット**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-161">On the **Editor** menu, select **Add Assets**, then **New Image Set**.</span></span>
1. <span data-ttu-id="f77ac-162">新しい**画像**アセットを選択し、**属性インスペクター**を使用してその**名前**をに`DefaultUserPhoto`設定します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-162">Select the new **Image** asset and use the **Attribute Inspector** to set its **Name** to `DefaultUserPhoto`.</span></span>
1. <span data-ttu-id="f77ac-163">既定のユーザープロファイル写真として機能する任意のイメージを追加します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-163">Add any image you like to serve as a default user profile photo.</span></span>

    ![Xcode のイメージセットアセットビューのスクリーンショット](./images/add-default-user-photo.png)

1. <span data-ttu-id="f77ac-165">という名前`WelcomeViewController`の**graphtutorial**フォルダーに、新しい**cocoa タッチクラス**ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-165">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `WelcomeViewController`.</span></span> <span data-ttu-id="f77ac-166">Field**のサブクラス**で [ **uiviewcontroller** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-166">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="f77ac-167">**WelcomeViewController**を開き、次のコードを宣言内に`@interface`追加します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-167">Open **WelcomeViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objc
    @property (nonatomic) IBOutlet UIImageView *userProfilePhoto;
    @property (nonatomic) IBOutlet UILabel *userDisplayName;
    @property (nonatomic) IBOutlet UILabel *userEmail;
    ```

1. <span data-ttu-id="f77ac-168">**WelcomeViewController**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f77ac-168">Open **WelcomeViewController.m** and replace its contents with the following code.</span></span>

    ```objc
    #import "WelcomeViewController.h"

    @interface WelcomeViewController ()

    @end

    @implementation WelcomeViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.

        // TEMPORARY
        self.userProfilePhoto.image = [UIImage imageNamed:@"DefaultUserPhoto"];
        self.userDisplayName.text = @"Default User";
        [self.userDisplayName sizeToFit];
        self.userEmail.text = @"default@contoso.com";
        [self.userEmail sizeToFit];
    }

    - (IBAction)signOut {
        [self performSegueWithIdentifier: @"userSignedOut" sender: nil];
    }

    @end
    ```

1. <span data-ttu-id="f77ac-169">**メインのストーリーボード**を開きます。</span><span class="sxs-lookup"><span data-stu-id="f77ac-169">Open **Main.storyboard**.</span></span> <span data-ttu-id="f77ac-170">**アイテム1のシーン**を選択し、[ **id インスペクター**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-170">Select the **Item 1 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="f77ac-171">**クラス**値を**WelcomeViewController**に変更します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-171">Change the **Class** value to **WelcomeViewController**.</span></span>
1. <span data-ttu-id="f77ac-172">**ライブラリ**を使用して、次のアイテムを**アイテム1のシーン**に追加します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-172">Using the **Library**, add the following items to the **Item 1 Scene**.</span></span>

    - <span data-ttu-id="f77ac-173">1つの**イメージビュー**</span><span class="sxs-lookup"><span data-stu-id="f77ac-173">One **Image View**</span></span>
    - <span data-ttu-id="f77ac-174">2つの**ラベル**</span><span class="sxs-lookup"><span data-stu-id="f77ac-174">Two **Labels**</span></span>
    - <span data-ttu-id="f77ac-175">1つの**ボタン**</span><span class="sxs-lookup"><span data-stu-id="f77ac-175">One **Button**</span></span>

1. <span data-ttu-id="f77ac-176">イメージビューを選択し、**サイズインスペクター**を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-176">Select the image view, then select the **Size Inspector**.</span></span>
1. <span data-ttu-id="f77ac-177">**幅**と**高さ**を196に設定します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-177">Set the **Width** and **Height** to 196.</span></span>
1. <span data-ttu-id="f77ac-178">2番目のラベルを選択し、[ **Attributes インスペクター**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-178">Select the second label, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="f77ac-179">**色**を**濃い灰色の色**に変更し、**フォント**を**システム 12.0**に変更します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-179">Change the **Color** to **Dark Gray Color**, and change the **Font** to **System 12.0**.</span></span>
1. <span data-ttu-id="f77ac-180">ボタンを選択し、[ **Attributes Inspector**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-180">Select the button, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="f77ac-181">**タイトル**をに`Sign Out`変更します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-181">Change the **Title** to `Sign Out`.</span></span>
1. <span data-ttu-id="f77ac-182">[**接続インスペクター**] を使用して、次の接続を行います。</span><span class="sxs-lookup"><span data-stu-id="f77ac-182">Using the **Connections Inspector**, make the following connections.</span></span>

    - <span data-ttu-id="f77ac-183">**Userdisplayname**アウトレットを最初のラベルにリンクします。</span><span class="sxs-lookup"><span data-stu-id="f77ac-183">Link the **userDisplayName** outlet to the first label.</span></span>
    - <span data-ttu-id="f77ac-184">**Useremail**アウトレットを2番目のラベルにリンクします。</span><span class="sxs-lookup"><span data-stu-id="f77ac-184">Link the **userEmail** outlet to the second label.</span></span>
    - <span data-ttu-id="f77ac-185">**UserProfilePhoto**アウトレットをイメージビューにリンクします。</span><span class="sxs-lookup"><span data-stu-id="f77ac-185">Link the **userProfilePhoto** outlet to the image view.</span></span>
    - <span data-ttu-id="f77ac-186">**SignOut** received アクションをボタンの**タッチアップ**にリンクします。</span><span class="sxs-lookup"><span data-stu-id="f77ac-186">Link the **signOut** received action to the button's **Touch Up Inside**.</span></span>

1. <span data-ttu-id="f77ac-187">シーンの下部にあるタブバー項目を選択し、[ **Attributes Inspector**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-187">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="f77ac-188">**タイトル**をに`Me`変更します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-188">Change the **Title** to `Me`.</span></span>
1. <span data-ttu-id="f77ac-189">[**エディター** ] メニューの [**自動レイアウトの問題を解決**する] を選択し、[**ウェルカムビューコントローラーのすべてのビューの**下にある [不足している制約を**追加**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-189">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Welcome View Controller**.</span></span>

<span data-ttu-id="f77ac-190">完了したら、開始シーンは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f77ac-190">The welcome scene should look similar to this once you're done.</span></span>

![開始シーンレイアウトのスクリーンショット](./images/welcome-scene-layout.png)

### <a name="create-calendar-page"></a><span data-ttu-id="f77ac-192">予定表ページを作成する</span><span class="sxs-lookup"><span data-stu-id="f77ac-192">Create calendar page</span></span>

1. <span data-ttu-id="f77ac-193">という名前`CalendarViewController`の**graphtutorial**フォルダーに、新しい**cocoa タッチクラス**ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-193">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `CalendarViewController`.</span></span> <span data-ttu-id="f77ac-194">Field**のサブクラス**で [ **uiviewcontroller** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-194">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="f77ac-195">**Calendarviewcontroller**を開き、宣言内に`@interface`次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-195">Open **CalendarViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objc
    @property (nonatomic) IBOutlet UITextView *calendarJSON;
    ```

1. <span data-ttu-id="f77ac-196">**Calendarviewcontroller**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f77ac-196">Open **CalendarViewController.m** and replace its contents with the following code.</span></span>

    ```objc
    #import "CalendarViewController.h"

    @interface CalendarViewController ()

    @end

    @implementation CalendarViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.

        // TEMPORARY
        self.calendarJSON.text = @"Calendar";
        [self.calendarJSON sizeToFit];
    }

    @end
    ```

1. <span data-ttu-id="f77ac-197">**メインのストーリーボード**を開きます。</span><span class="sxs-lookup"><span data-stu-id="f77ac-197">Open **Main.storyboard**.</span></span> <span data-ttu-id="f77ac-198">**アイテム2のシーン**を選択し、[ **id インスペクター**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-198">Select the **Item 2 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="f77ac-199">**クラス**値を**calendarviewcontroller**に変更します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-199">Change the **Class** value to **CalendarViewController**.</span></span>
1. <span data-ttu-id="f77ac-200">**ライブラリ**を使用して、**アイテム2のシーン**に**テキストビュー**を追加します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-200">Using the **Library**, add a **Text View** to the **Item 2 Scene**.</span></span>
1. <span data-ttu-id="f77ac-201">追加したテキストビューを選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-201">Select the text view you just added.</span></span> <span data-ttu-id="f77ac-202">**エディター**で、[**埋め込み**] を選択してから、[ビュー] を**スクロール**します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-202">On the **Editor**, choose **Embed In**, then **Scroll View**.</span></span>
1. <span data-ttu-id="f77ac-203">[**接続インスペクター**] を使用して、 **calendarjson**アウトレットをテキストビューに接続します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-203">Using the **Connections Inspector**, connect the **calendarJSON** outlet to the text view.</span></span>
1. 1. <span data-ttu-id="f77ac-204">シーンの下部にあるタブバー項目を選択し、[ **Attributes Inspector**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-204">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="f77ac-205">**タイトル**をに`Calendar`変更します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-205">Change the **Title** to `Calendar`.</span></span>
1. <span data-ttu-id="f77ac-206">[**エディター** ] メニューの [**自動レイアウトの問題を解決**する] を選択し、[**ウェルカムビューコントローラーのすべてのビューの**下にある [不足している制約を**追加**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-206">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Welcome View Controller**.</span></span>

<span data-ttu-id="f77ac-207">完了すると、予定表のシーンは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f77ac-207">The calendar scene should look similar to this once you're done.</span></span>

![予定表のシーンレイアウトのスクリーンショット](./images/calendar-scene-layout.png)

### <a name="create-activity-indicator"></a><span data-ttu-id="f77ac-209">アクティビティインジケーターの作成</span><span class="sxs-lookup"><span data-stu-id="f77ac-209">Create activity indicator</span></span>

1. <span data-ttu-id="f77ac-210">という名前`SpinnerViewController`の**graphtutorial**フォルダーに、新しい**cocoa タッチクラス**ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-210">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `SpinnerViewController`.</span></span> <span data-ttu-id="f77ac-211">Field**のサブクラス**で [ **uiviewcontroller** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-211">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="f77ac-212">**Spinnerviewcontroller**を開き、宣言内に`@interface`次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-212">Open **SpinnerViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objc
    - (void) startWithContainer:(UIViewController*) container;
    - (void) stop;
    ```

1. <span data-ttu-id="f77ac-213">**Spinnerviewcontroller**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f77ac-213">Open **SpinnerViewController.m** and replace its contents with the following code.</span></span>

    ```objc
    #import "SpinnerViewController.h"

    @interface SpinnerViewController ()
    @property (nonatomic) UIActivityIndicatorView* spinner;
    @end

    @implementation SpinnerViewController

    - (void)viewDidLoad {
        [super viewDidLoad];

        _spinner = [[UIActivityIndicatorView alloc] initWithActivityIndicatorStyle:
                    UIActivityIndicatorViewStyleWhiteLarge];

        self.view.backgroundColor = [UIColor colorWithWhite:0 alpha:0.7];
        [self.view addSubview:_spinner];

        _spinner.translatesAutoresizingMaskIntoConstraints = false;
        [_spinner startAnimating];

        [_spinner.centerXAnchor constraintEqualToAnchor:self.view.centerXAnchor].active = true;
        [_spinner.centerYAnchor constraintEqualToAnchor:self.view.centerYAnchor].active = true;
    }

    - (void) startWithContainer:(UIViewController *)container {
        [container addChildViewController:self];
        self.view.frame = container.view.frame;
        [container.view addSubview:self.view];
        [self didMoveToParentViewController:container];
    }

    - (void) stop {
        [self willMoveToParentViewController:nil];
        [self.view removeFromSuperview];
        [self removeFromParentViewController];
    }

    @end
    ```

## <a name="test-the-app"></a><span data-ttu-id="f77ac-214">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="f77ac-214">Test the app</span></span>

<span data-ttu-id="f77ac-215">変更を保存し、アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="f77ac-215">Save your changes and launch the app.</span></span> <span data-ttu-id="f77ac-216">[**サインイン**] ボタンと [**サインアウト**] ボタン、およびタブバーを使用して、画面間を移動できるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f77ac-216">You should be able to move between the screens using the **Sign In** and **Sign Out** buttons and the tab bar.</span></span>

![アプリケーションのスクリーンショット](./images/app-screens.png)
