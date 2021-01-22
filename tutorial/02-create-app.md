<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ffe3f-101">まず、新しい Objective-C プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-101">Begin by creating a new Objective-C project.</span></span>

1. <span data-ttu-id="ffe3f-102">Xcode を開きます。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-102">Open Xcode.</span></span> <span data-ttu-id="ffe3f-103">[ファイル] **メニューで** 、[新規]、次に [ **プロジェクト]** の順に **選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-103">On the **File** menu, select **New**, then **Project**.</span></span>
1. <span data-ttu-id="ffe3f-104">アプリ テンプレートを **選択し** 、[次へ] を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-104">Choose the **App** template and select **Next**.</span></span>

    ![Xcode テンプレート選択ダイアログのスクリーンショット](./images/xcode-select-template.png)

1. <span data-ttu-id="ffe3f-106">製品名 **の設定と** `GraphTutorial` 言語 **を** **Objective-C に設定します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-106">Set the **Product Name** to `GraphTutorial` and the **Language** to **Objective-C**.</span></span>
1. <span data-ttu-id="ffe3f-107">残りのフィールドに入力し、[次へ] を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-107">Fill in the remaining fields and select **Next**.</span></span>
1. <span data-ttu-id="ffe3f-108">プロジェクトの場所を選択し、[作成] を選択 **します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-108">Choose a location for the project and select **Create**.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="ffe3f-109">依存関係をインストールする</span><span class="sxs-lookup"><span data-stu-id="ffe3f-109">Install dependencies</span></span>

<span data-ttu-id="ffe3f-110">次に進む前に、後で使用する追加の依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-110">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="ffe3f-111">Azure AD で認証を行う iOS 用[Microsoft Authentication Library (MSAL)。](https://github.com/AzureAD/microsoft-authentication-library-for-objc)</span><span class="sxs-lookup"><span data-stu-id="ffe3f-111">[Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) for authenticating to with Azure AD.</span></span>
- <span data-ttu-id="ffe3f-112">[Microsoft Graph への呼び出し](https://github.com/microsoftgraph/msgraph-sdk-objc) を行う目的 C 用 Microsoft Graph SDK。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-112">[Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="ffe3f-113">[ユーザーやイベントなどの Microsoft Graph リソース](https://github.com/microsoftgraph/msgraph-sdk-objc-models) を表す、強力に型指定されたオブジェクト用の Microsoft Graph Models SDK for Objective C。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-113">[Microsoft Graph Models SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) for strongly-typed objects representing Microsoft Graph resources like users or events.</span></span>

1. <span data-ttu-id="ffe3f-114">Xcode を終了します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-114">Quit Xcode.</span></span>
1. <span data-ttu-id="ffe3f-115">ターミナルを開き、ディレクトリを **GraphTu読み込みプロジェクトの場所に変更** します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-115">Open Terminal and change the directory to the location of your **GraphTutorial** project.</span></span>
1. <span data-ttu-id="ffe3f-116">Podfile を作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-116">Run the following command to create a Podfile.</span></span>

    ```Shell
    pod init
    ```

1. <span data-ttu-id="ffe3f-117">Podfile を開き、行の直後に次の行を追加 `use_frameworks!` します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-117">Open the Podfile and add the following lines just after the `use_frameworks!` line.</span></span>

    ```Ruby
    pod 'MSAL', '~> 1.1.13'
    pod 'MSGraphClientSDK', ' ~> 1.0.0'
    pod 'MSGraphClientModels', '~> 1.3.0'
    ```

1. <span data-ttu-id="ffe3f-118">Podfile を保存し、次のコマンドを実行して依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-118">Save the Podfile, then run the following command to install the dependencies.</span></span>

    ```Shell
    pod install
    ```

1. <span data-ttu-id="ffe3f-119">コマンドが完了したら、Xcode で新しく作成した **GraphTutorial.xcworkspace を** 開きます。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-119">Once the command completes, open the newly created **GraphTutorial.xcworkspace** in Xcode.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="ffe3f-120">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="ffe3f-120">Design the app</span></span>

<span data-ttu-id="ffe3f-121">このセクションでは、サインイン ページ、タブ バー ナビゲーター、ウェルカム ページ、カレンダー ページなど、アプリのビューを作成します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-121">In this section you will create the views for the app: a sign in page, a tab bar navigator, a welcome page, and a calendar page.</span></span> <span data-ttu-id="ffe3f-122">アクティビティ インジケーター オーバーレイも作成します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-122">You'll also create an activity indicator overlay.</span></span>

### <a name="create-sign-in-page"></a><span data-ttu-id="ffe3f-123">サインイン ページを作成する</span><span class="sxs-lookup"><span data-stu-id="ffe3f-123">Create sign in page</span></span>

1. <span data-ttu-id="ffe3f-124">Xcode で **GraphTu graphl フォルダー** を展開し **、ViewController.m ファイルを選択** します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-124">Expand the **GraphTutorial** folder in Xcode, then select the **ViewController.m** file.</span></span>
1. <span data-ttu-id="ffe3f-125">ファイル検査 **で、** ファイル **の名前** を次に変更します `SignInViewController.m` 。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-125">In the **File Inspector**, change the **Name** of the file to `SignInViewController.m`.</span></span>

    ![ファイル検査のスクリーンショット](./images/rename-file.png)

1. <span data-ttu-id="ffe3f-127">**SignInViewController.m を** 開き、その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-127">Open **SignInViewController.m** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="ffe3f-128">**ViewController.h ファイルを選択** します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-128">Select the **ViewController.h** file.</span></span>
1. <span data-ttu-id="ffe3f-129">ファイル検査 **で、** ファイル **の名前** を次に変更します `SignInViewController.h` 。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-129">In the **File Inspector**, change the **Name** of the file to `SignInViewController.h`.</span></span>
1. <span data-ttu-id="ffe3f-130">**SignInViewController.h を** 開き、すべてのインスタンスを次に変更 `ViewController` します `SignInViewController` 。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-130">Open **SignInViewController.h** and change all instances of `ViewController` to `SignInViewController`.</span></span>

1. <span data-ttu-id="ffe3f-131">**Main.storyboard ファイルを開** きます。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-131">Open the **Main.storyboard** file.</span></span>
1. <span data-ttu-id="ffe3f-132">[ **コントローラー シーンの表示] を展開** し、[コントローラーの表示 **] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-132">Expand **View Controller Scene**, then select **View Controller**.</span></span>

    ![ビュー コントローラーが選択されている Xcode のスクリーンショット](./images/storyboard-select-view-controller.png)

1. <span data-ttu-id="ffe3f-134">Identity Inspector **を選択し\*\*\*\*、Class** ドロップダウンを **SignInViewController に変更します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-134">Select the **Identity Inspector**, then change the **Class** dropdown to **SignInViewController**.</span></span>

    ![ID インスペクターのスクリーンショット](./images/change-class.png)

1. <span data-ttu-id="ffe3f-136">ライブラリを **選択し**、ボタンを **サインイン** ビュー **コントローラーにドラッグします**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-136">Select the **Library**, then drag a **Button** onto the **Sign In View Controller**.</span></span>

    ![Xcode のライブラリのスクリーンショット](./images/add-button-to-view.png)

1. <span data-ttu-id="ffe3f-138">ボタンを選択した後、属性インスペクタ **ーを** 選択し、ボタン **のタイトル** を次に変更します `Sign In` 。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-138">With the button selected, select the **Attributes Inspector** and change the **Title** of the button to `Sign In`.</span></span>

    ![Xcode の属性インスペクターの Title フィールドのスクリーンショット](./images/set-button-title.png)

1. <span data-ttu-id="ffe3f-140">ボタンを選択した後、ストーリーボードの下部にある [ **配置** ] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-140">With the button selected, select the **Align** button at the bottom of the storyboard.</span></span> <span data-ttu-id="ffe3f-141">コンテナー内の **水平方向** と垂直方向の両方のコンテナー制約を選択し、その値を 0 のままにして、[2 の制約の追加 **] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-141">Select both the **Horizontally in container** and **Vertically in container** constraints, leave their values as 0, then select **Add 2 constraints**.</span></span>

    ![Xcode の配置制約設定のスクリーンショット](./images/add-alignment-constraints.png)

1. <span data-ttu-id="ffe3f-143">サインイン ビュー **コントローラーを選択し、[** 接続インスペクター] **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-143">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="ffe3f-144">[ **受信した操作]** の下で、入力されていない円をドラッグして **、[サインイン] ボタン** に移動します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-144">Under **Received Actions**, drag the unfilled circle next to **signIn** onto the button.</span></span> <span data-ttu-id="ffe3f-145">ポップアップ **メニューの [内側に** タッチ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-145">Select **Touch Up Inside** on the pop-up menu.</span></span>

    ![Xcode のボタンに SignIn アクションをドラッグするスクリーンショット](./images/connect-sign-in-button.png)

### <a name="create-tab-bar"></a><span data-ttu-id="ffe3f-147">タブ バーを作成する</span><span class="sxs-lookup"><span data-stu-id="ffe3f-147">Create tab bar</span></span>

1. <span data-ttu-id="ffe3f-148">ライブラリを **選択し**、タブ バー **コントローラーをストーリー** ボードにドラッグします。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-148">Select the **Library**, then drag a **Tab Bar Controller** onto the storyboard.</span></span>
1. <span data-ttu-id="ffe3f-149">サインイン ビュー **コントローラーを選択し、[** 接続インスペクター] **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-149">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="ffe3f-150">**Triggered Segues の下** で、手動の横にある未入力の円をストーリーボードの **タブ バー** コントローラーにドラッグします。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-150">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Tab Bar Controller** on the storyboard.</span></span> <span data-ttu-id="ffe3f-151">ポップアップ **メニューで [モーダルに** 表示] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-151">Select **Present Modally** in the pop-up menu.</span></span>

    ![Xcode の新しいタブ バー コントローラーに手動セグをドラッグするスクリーンショット](./images/add-segue.png)

1. <span data-ttu-id="ffe3f-153">追加したセグを選択し、属性インスペクターを **選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-153">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="ffe3f-154">[識別子]**フィールドを** 設定 `userSignedIn` し、[プレゼンテーション] を [**全画面\*\*\*\*] に設定します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-154">Set the **Identifier** field to `userSignedIn`, and set **Presentation** to **Full Screen**.</span></span>

    ![Xcode の属性インスペクターの Identifier フィールドのスクリーンショット](./images/set-segue-identifier.png)

1. <span data-ttu-id="ffe3f-156">項目 **1 シーンを選択し**、[接続インスペクター] **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-156">Select the **Item 1 Scene**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="ffe3f-157">**Triggered Segues の下** で、手動の横にある入力されていない円をストーリーボードの **サインイン ビュー** コントローラーにドラッグします。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-157">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Sign In View Controller** on the storyboard.</span></span> <span data-ttu-id="ffe3f-158">ポップアップ **メニューで [モーダルに** 表示] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-158">Select **Present Modally** in the pop-up menu.</span></span>
1. <span data-ttu-id="ffe3f-159">追加したセグを選択し、属性インスペクターを **選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-159">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="ffe3f-160">[識別子]**フィールドを** 設定 `userSignedOut` し、[プレゼンテーション] を [**全画面\*\*\*\*] に設定します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-160">Set the **Identifier** field to `userSignedOut`, and set **Presentation** to **Full Screen**.</span></span>

### <a name="create-welcome-page"></a><span data-ttu-id="ffe3f-161">ウェルカム ページを作成する</span><span class="sxs-lookup"><span data-stu-id="ffe3f-161">Create welcome page</span></span>

1. <span data-ttu-id="ffe3f-162">**Assets.xcassets ファイルを選択** します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-162">Select the **Assets.xcassets** file.</span></span>
1. <span data-ttu-id="ffe3f-163">[エディター **] メニューで** 、[新しいアセットの **追加] を** 選択し、[イメージ セット] **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-163">On the **Editor** menu, select **Add New Asset**, then **Image Set**.</span></span>
1. <span data-ttu-id="ffe3f-164">新しい **画像アセットを選択** し、 **属性インスペクターを使** って名前を **設定** します `DefaultUserPhoto` 。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-164">Select the new **Image** asset and use the **Attribute Inspector** to set its **Name** to `DefaultUserPhoto`.</span></span>
1. <span data-ttu-id="ffe3f-165">既定のユーザー プロファイル写真として使用する画像を追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-165">Add any image you like to serve as a default user profile photo.</span></span>

    ![Xcode のイメージ セット アセット ビューのスクリーンショット](./images/add-default-user-photo.png)

1. <span data-ttu-id="ffe3f-167">**GraphTu touchl** **フォルダーに新** しい Cocoa Touch Class ファイルを作成します `WelcomeViewController` 。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-167">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `WelcomeViewController`.</span></span> <span data-ttu-id="ffe3f-168">フィールド **のサブクラスで UIViewController** **を選択** します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-168">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="ffe3f-169">**WelcomeViewController.h を** 開き、宣言内に次のコードを追加 `@interface` します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-169">Open **WelcomeViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objc
    @property (nonatomic) IBOutlet UIImageView *userProfilePhoto;
    @property (nonatomic) IBOutlet UILabel *userDisplayName;
    @property (nonatomic) IBOutlet UILabel *userEmail;
    ```

1. <span data-ttu-id="ffe3f-170">**WelcomeViewController.m を** 開き、その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-170">Open **WelcomeViewController.m** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="ffe3f-171">**Main.storyboard を開きます**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-171">Open **Main.storyboard**.</span></span> <span data-ttu-id="ffe3f-172">アイテム **1 シーンを選択し**、ID インスペクター **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-172">Select the **Item 1 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="ffe3f-173">クラスの **値を** **WelcomeViewController に変更します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-173">Change the **Class** value to **WelcomeViewController**.</span></span>
1. <span data-ttu-id="ffe3f-174">ライブラリを **使用して**、アイテム 1 シーンに **次の項目を追加します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-174">Using the **Library**, add the following items to the **Item 1 Scene**.</span></span>

    - <span data-ttu-id="ffe3f-175">1 **つのイメージ ビュー**</span><span class="sxs-lookup"><span data-stu-id="ffe3f-175">One **Image View**</span></span>
    - <span data-ttu-id="ffe3f-176">2 **つのラベル**</span><span class="sxs-lookup"><span data-stu-id="ffe3f-176">Two **Labels**</span></span>
    - <span data-ttu-id="ffe3f-177">1 **つのボタン**</span><span class="sxs-lookup"><span data-stu-id="ffe3f-177">One **Button**</span></span>

1. <span data-ttu-id="ffe3f-178">Connections **Inspector を使用して、** 次の接続を行います。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-178">Using the **Connections Inspector**, make the following connections.</span></span>

    - <span data-ttu-id="ffe3f-179">**userDisplayName の出口を** 最初のラベルにリンクします。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-179">Link the **userDisplayName** outlet to the first label.</span></span>
    - <span data-ttu-id="ffe3f-180">**userEmail の出口を** 2 番目のラベルにリンクします。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-180">Link the **userEmail** outlet to the second label.</span></span>
    - <span data-ttu-id="ffe3f-181">**userProfilePhoto の出口を** イメージ ビューにリンクします。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-181">Link the **userProfilePhoto** outlet to the image view.</span></span>
    - <span data-ttu-id="ffe3f-182">signOut **受信アクションを** ボタンの Touch **Up Inside にリンクします**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-182">Link the **signOut** received action to the button's **Touch Up Inside**.</span></span>

1. <span data-ttu-id="ffe3f-183">イメージ ビューを選択し、サイズ検査を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-183">Select the image view, then select the **Size Inspector**.</span></span>
1. <span data-ttu-id="ffe3f-184">幅と **高** さを 196 に設定します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-184">Set the **Width** and **Height** to 196.</span></span>
1. <span data-ttu-id="ffe3f-185">[配置 **] ボタン** を使用して、 **コンテナー内で水平方向に** 制約を追加し、値を 0 に設定します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-185">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="ffe3f-186">[新しい **制約の追加]** ボタン([整列] ボタンの横) を使用して、次の制約を追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-186">Use the **Add New Constraints** button (next to the **Align** button) to add the following constraints:</span></span>

    - <span data-ttu-id="ffe3f-187">上揃え: セーフ エリア、値: 0</span><span class="sxs-lookup"><span data-stu-id="ffe3f-187">Align Top to: Safe Area, value: 0</span></span>
    - <span data-ttu-id="ffe3f-188">[下のスペース]: ユーザー表示名、値: Standard</span><span class="sxs-lookup"><span data-stu-id="ffe3f-188">Bottom Space to: User Display Name, value: Standard</span></span>
    - <span data-ttu-id="ffe3f-189">高さ、値: 196</span><span class="sxs-lookup"><span data-stu-id="ffe3f-189">Height, value: 196</span></span>
    - <span data-ttu-id="ffe3f-190">幅、値: 196</span><span class="sxs-lookup"><span data-stu-id="ffe3f-190">Width, value: 196</span></span>

    ![Xcode の新しい制約設定のスクリーンショット](./images/add-new-constraints.png)

1. <span data-ttu-id="ffe3f-192">1 つ目のラベルを選択し、[配置] ボタンを使用して、コンテナー内の水平方向 **の** 制約を値 0 で追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-192">Select the first label, then use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="ffe3f-193">[新しい **制約の追加] ボタンを使用** して、次の制約を追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-193">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="ffe3f-194">Top Space to: User Profile Photo, value: Standard</span><span class="sxs-lookup"><span data-stu-id="ffe3f-194">Top Space to: User Profile Photo, value: Standard</span></span>
    - <span data-ttu-id="ffe3f-195">[Bottom Space to]: User Email, value: Standard</span><span class="sxs-lookup"><span data-stu-id="ffe3f-195">Bottom Space to: User Email, value: Standard</span></span>

1. <span data-ttu-id="ffe3f-196">2 番目のラベルを選択し、属性インスペクター **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-196">Select the second label, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="ffe3f-197">色を **濃い** 灰色 **に変更** し、 **フォント** をシステム **12.0 に変更します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-197">Change the **Color** to **Dark Gray Color**, and change the **Font** to **System 12.0**.</span></span>
1. <span data-ttu-id="ffe3f-198">[配置 **] ボタン** を使用して、 **コンテナー内で水平方向に** 制約を追加し、値を 0 に設定します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-198">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="ffe3f-199">[新しい **制約の追加] ボタンを使用** して、次の制約を追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-199">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="ffe3f-200">[Top Space to]: User Display Name,value: Standard</span><span class="sxs-lookup"><span data-stu-id="ffe3f-200">Top Space to: User Display Name, value: Standard</span></span>
    - <span data-ttu-id="ffe3f-201">下のスペース: サインアウト、値: 14</span><span class="sxs-lookup"><span data-stu-id="ffe3f-201">Bottom Space to: Sign Out, value: 14</span></span>

1. <span data-ttu-id="ffe3f-202">ボタンを選択し、属性インスペクタ **ーを選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-202">Select the button, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="ffe3f-203">Title を **次に変更** します `Sign Out` 。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-203">Change the **Title** to `Sign Out`.</span></span>
1. <span data-ttu-id="ffe3f-204">[配置 **] ボタン** を使用して、 **コンテナー内で水平方向に** 制約を追加し、値を 0 に設定します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-204">Use the **Align** button to add the **Horizontally in container** constraint with a value of 0.</span></span>
1. <span data-ttu-id="ffe3f-205">[新しい **制約の追加] ボタンを使用** して、次の制約を追加します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-205">Use the **Add New Constraints** button to add the following constraints:</span></span>

    - <span data-ttu-id="ffe3f-206">Top Space to: User Email, value: 14</span><span class="sxs-lookup"><span data-stu-id="ffe3f-206">Top Space to: User Email, value: 14</span></span>

1. <span data-ttu-id="ffe3f-207">シーンの下部にあるタブ バー項目を選択し、[属性インスペクター] を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-207">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="ffe3f-208">Title を **次に変更** します `Me` 。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-208">Change the **Title** to `Me`.</span></span>
1. <span data-ttu-id="ffe3f-209">[エディター **] メニューの**[自動レイアウトの問題 **の** 解決] を選択し、ウェルカム ビュー コントローラーの [すべてのビュー] の下にある [不足している制約の追加 **] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-209">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Welcome View Controller**.</span></span>

<span data-ttu-id="ffe3f-210">完了すると、ようこそシーンは次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-210">The welcome scene should look similar to this once you're done.</span></span>

![ようこそシーンのレイアウトのスクリーンショット](./images/welcome-scene-layout.png)

### <a name="create-calendar-page"></a><span data-ttu-id="ffe3f-212">予定表ページを作成する</span><span class="sxs-lookup"><span data-stu-id="ffe3f-212">Create calendar page</span></span>

1. <span data-ttu-id="ffe3f-213">**GraphTu touchl** **フォルダーに新** しい Cocoa Touch Class ファイルを作成します `CalendarViewController` 。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-213">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `CalendarViewController`.</span></span> <span data-ttu-id="ffe3f-214">フィールド **のサブクラスで UIViewController** **を選択** します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-214">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="ffe3f-215">**CalendarViewController.h を** 開き、宣言内に次のコードを追加 `@interface` します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-215">Open **CalendarViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objc
    @property (nonatomic) IBOutlet UITextView *calendarJSON;
    ```

1. <span data-ttu-id="ffe3f-216">**CalendarViewController.m を** 開き、内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-216">Open **CalendarViewController.m** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="ffe3f-217">**Main.storyboard を開きます**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-217">Open **Main.storyboard**.</span></span> <span data-ttu-id="ffe3f-218">アイテム **2 シーンを選択し**、ID インスペクター **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-218">Select the **Item 2 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="ffe3f-219">クラスの **値を** **CalendarViewController に変更します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-219">Change the **Class** value to **CalendarViewController**.</span></span>
1. <span data-ttu-id="ffe3f-220">ライブラリを **使用して**、アイテム 2 **シーンに\*\*\*\*テキスト ビューを追加します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-220">Using the **Library**, add a **Text View** to the **Item 2 Scene**.</span></span>
1. <span data-ttu-id="ffe3f-221">追加したテキスト ビューを選択します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-221">Select the text view you just added.</span></span> <span data-ttu-id="ffe3f-222">エディターで **、[埋め** 込み **]、次** に [スクロール ビュー] **の順に選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-222">On the **Editor**, choose **Embed In**, then **Scroll View**.</span></span>
1. <span data-ttu-id="ffe3f-223">Connections **Inspector を使用して\*\*\*\*、calendarJSON** の出口をテキスト ビューに接続します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-223">Using the **Connections Inspector**, connect the **calendarJSON** outlet to the text view.</span></span>
1. <span data-ttu-id="ffe3f-224">シーンの下部にあるタブ バー項目を選択し、[属性インスペクター] を **選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-224">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="ffe3f-225">Title を **次に変更** します `Calendar` 。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-225">Change the **Title** to `Calendar`.</span></span>
1. <span data-ttu-id="ffe3f-226">[エディター **] メニューの**[自動レイアウトの問題 **の** 解決] を選択し、ウェルカム ビュー コントローラーの [すべてのビュー] の下にある [不足している制約の追加 **] を選択します**。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-226">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Welcome View Controller**.</span></span>

<span data-ttu-id="ffe3f-227">完了すると、予定表シーンは次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-227">The calendar scene should look similar to this once you're done.</span></span>

![カレンダー シーン レイアウトのスクリーンショット](./images/calendar-scene-layout.png)

### <a name="create-activity-indicator"></a><span data-ttu-id="ffe3f-229">アクティビティ インジケーターを作成する</span><span class="sxs-lookup"><span data-stu-id="ffe3f-229">Create activity indicator</span></span>

1. <span data-ttu-id="ffe3f-230">**GraphTu touchl** **フォルダーに新** しい Cocoa Touch Class ファイルを作成します `SpinnerViewController` 。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-230">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `SpinnerViewController`.</span></span> <span data-ttu-id="ffe3f-231">フィールド **のサブクラスで UIViewController** **を選択** します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-231">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="ffe3f-232">**SpinnerViewController.h** を開き、宣言内に次のコードを追加 `@interface` します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-232">Open **SpinnerViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objc
    - (void) startWithContainer:(UIViewController*) container;
    - (void) stop;
    ```

1. <span data-ttu-id="ffe3f-233">**SpinnerViewController.m を** 開き、内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-233">Open **SpinnerViewController.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/SpinnerViewController.m" id="SpinnerViewSnippet":::

## <a name="test-the-app"></a><span data-ttu-id="ffe3f-234">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="ffe3f-234">Test the app</span></span>

<span data-ttu-id="ffe3f-235">変更内容を保存し、アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-235">Save your changes and launch the app.</span></span> <span data-ttu-id="ffe3f-236">[サインイン] ボタンと [サインアウト]ボタンとタブ バーを使用して、画面間を移動できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffe3f-236">You should be able to move between the screens using the **Sign In** and **Sign Out** buttons and the tab bar.</span></span>

![アプリケーションのスクリーンショット](./images/app-screens.png)
