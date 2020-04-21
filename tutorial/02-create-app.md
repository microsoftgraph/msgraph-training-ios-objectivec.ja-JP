<!-- markdownlint-disable MD002 MD041 -->

最初に、新しい Swift プロジェクトを作成します。

1. Xcode を開きます。 [**ファイル**] メニューの [**新規作成**]、[**プロジェクト**] の順に選択します。
1. [**単一ビューアプリ**] テンプレートを選択し、[**次へ**] を選択します。

    ![Xcode テンプレートの選択ダイアログのスクリーンショット](./images/xcode-select-template.png)

1. **製品名** `GraphTutorial`と**言語**を**目的-C**に設定します。
1. 残りのフィールドに入力し、[**次へ**] を選択します。
1. プロジェクトの場所を選択して、[**作成**] を選択します。

## <a name="install-dependencies"></a>依存関係のインストール

に進む前に、後で使用する追加の依存関係をインストールします。

- Azure AD での認証については、 [Microsoft Authentication Library (MSAL) For iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)を使用してください。
- Microsoft graph に電話をかけるための[Microsoft GRAPH SDK For 客観 C](https://github.com/microsoftgraph/msgraph-sdk-objc) 。
- Microsoft Graph では、ユーザーやイベントなどの Microsoft Graph のリソースを表す、厳密に型指定されたオブジェクト用の[Microsoft Graph モデル SDK を目的](https://github.com/microsoftgraph/msgraph-sdk-objc-models)としています。

1. Xcode を終了します。
1. ターミナルを開き、グラフのディレクトリを**Graphtutorial**プロジェクトの場所に変更します。
1. Podfile を作成するには、次のコマンドを実行します。

    ```Shell
    pod init
    ```

1. Podfile を開き、行の`use_frameworks!`直後に次の行を追加します。

    ```Ruby
    pod 'MSAL', '~> 1.1.1'
    pod 'MSGraphClientSDK', ' ~> 1.0.0'
    pod 'MSGraphClientModels', '~> 1.3.0'
    ```

1. Podfile を保存してから、次のコマンドを実行して依存関係をインストールします。

    ```Shell
    pod install
    ```

1. コマンドが完了したら、Xcode で新しく作成した**graphtutorial**を開きます。

## <a name="design-the-app"></a>アプリを設計する

このセクションでは、アプリのビュー (サインインページ、タブバーナビゲーター、ウェルカムページ、予定表ページ) を作成します。 また、アクティビティインジケーターオーバーレイも作成します。

### <a name="create-sign-in-page"></a>サインインページの作成

1. Xcode の**Graphtutorial**フォルダーを展開し、 **viewcontroller**ファイルを選択します。
1. **ファイルインスペクター**で、ファイルの**名前**をに`SignInViewController.m`変更します。

    ![ファイル検査のスクリーンショット](./images/rename-file.png)

1. **SignInViewController**を開き、その内容を次のコードで置き換えます。

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

1. **Viewcontroller .h**ファイルを選択します。
1. **ファイルインスペクター**で、ファイルの**名前**をに`SignInViewController.h`変更します。
1. **SignInViewController**を開き、のすべての`ViewController`インスタンスをに`SignInViewController`変更します。

1. メインの**ストーリーボード**ファイルを開きます。
1. [**ビューコントローラーシーン**] を展開し、[**ビューコントローラー**] を選択します。

    ![ビューコントローラーが選択されている Xcode のスクリーンショット](./images/storyboard-select-view-controller.png)

1. [ **Identity Inspector**] を選択してから、**クラス**のドロップダウンを**SignInViewController**に変更します。

    ![Id インスペクターのスクリーンショット](./images/change-class.png)

1. **ライブラリ**を選択し、**ボタン**を [サインイン]**ビューコントローラー**にドラッグします。

    ![Xcode のライブラリのスクリーンショット](./images/add-button-to-view.png)

1. ボタンを選択した状態で、[ **Attributes Inspector** ] **Title**を選択し、ボタン`Sign In`のタイトルをに変更します。

    ![Xcode の Attributes インスペクターのタイトルフィールドのスクリーンショット](./images/set-button-title.png)

1. ボタンを選択した状態で、ストーリーボードの下部にある [**配置**] ボタンを選択します。 コンテナーの制約で**水平方向コンテナー**と**垂直方向**の両方を選択し、その値を0のままにして、[ **2 つの制約を追加**する] を選択します。

    ![Xcode の配置制限の設定のスクリーンショット](./images/add-alignment-constraints.png)

1. **サインインビューコントローラー**を選択し、[**接続インスペクター**] を選択します。
1. [**受信した処理**] で、[**サインイン**] の隣にある円をボタンにドラッグします。 ポップアップメニューで [ **Touch Up** ] を選択します。

    ![Xcode のボタンにサインイン操作をドラッグした場合のスクリーンショット](./images/connect-sign-in-button.png)

### <a name="create-tab-bar"></a>タブバーを作成する

1. **ライブラリ**を選択し、**タブバーコントローラー**をストーリーボードにドラッグします。
1. **サインインビューコントローラー**を選択し、[**接続インスペクター**] を選択します。
1. [**トリガー**された Segues] で、[**手動**] の横にある円を、ストーリーボードの**タブバーコントローラー**にドラッグします。 ポップアップメニューの [**モーダル**] を選択します。

    ![Xcode の新しいタブバーコントローラーに手動 segue をドラッグするスクリーンショット](./images/add-segue.png)

1. 追加した segue を選択し、[ **Attributes Inspector**] を選択します。 **識別子** `userSignedIn`フィールドをに設定し、**プレゼンテーション**を**全画面表示**に設定します。

    ![Xcode の Attributes インスペクターの識別子フィールドのスクリーンショット](./images/set-segue-identifier.png)

1. **アイテム1のシーン**を選択し、[**接続インスペクター**] を選択します。
1. [**トリガー**された Segues] で、[**手動**] の横にある円を、ストーリーボードの**サインインビューコントローラー**にドラッグします。 ポップアップメニューの [**モーダル**] を選択します。
1. 追加した segue を選択し、[ **Attributes Inspector**] を選択します。 **識別子** `userSignedOut`フィールドをに設定し、**プレゼンテーション**を**全画面表示**に設定します。

### <a name="create-welcome-page"></a>ウェルカムページを作成する

1. [ **Assets] セット**ファイルを選択します。
1. [**エディター** ] メニューの [**アセットの追加**] を選択し、[**新しいイメージセット**] を選択します。
1. 新しい**画像**アセットを選択し、**属性インスペクター**を使用してその**名前**をに`DefaultUserPhoto`設定します。
1. 既定のユーザープロファイル写真として機能する任意のイメージを追加します。

    ![Xcode のイメージセットアセットビューのスクリーンショット](./images/add-default-user-photo.png)

1. という名前`WelcomeViewController`の**graphtutorial**フォルダーに、新しい**cocoa タッチクラス**ファイルを作成します。 Field**のサブクラス**で [ **uiviewcontroller** ] を選択します。
1. **WelcomeViewController**を開き、次のコードを宣言内に`@interface`追加します。

    ```objc
    @property (nonatomic) IBOutlet UIImageView *userProfilePhoto;
    @property (nonatomic) IBOutlet UILabel *userDisplayName;
    @property (nonatomic) IBOutlet UILabel *userEmail;
    ```

1. **WelcomeViewController**を開き、その内容を次のコードで置き換えます。

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

1. **メインのストーリーボード**を開きます。 **アイテム1のシーン**を選択し、[ **id インスペクター**] を選択します。 **クラス**値を**WelcomeViewController**に変更します。
1. **ライブラリ**を使用して、次のアイテムを**アイテム1のシーン**に追加します。

    - 1つの**イメージビュー**
    - 2つの**ラベル**
    - 1つの**ボタン**

1. [**接続インスペクター**] を使用して、次の接続を行います。

    - **Userdisplayname**アウトレットを最初のラベルにリンクします。
    - **Useremail**アウトレットを2番目のラベルにリンクします。
    - **UserProfilePhoto**アウトレットをイメージビューにリンクします。
    - **SignOut** received アクションをボタンの**タッチアップ**にリンクします。

1. イメージビューを選択し、**サイズインスペクター**を選択します。
1. **幅**と**高さ**を196に設定します。
1. [**配置**] ボタンを使用して、**コンテナー内の水平方向**の制限値を0に追加します。
1. [**新しい制約の追加**] ボタン ([**配置**] ボタンの横) を使用して、以下の制約を追加します。

    - 上揃え: セーフエリア、値: 0
    - 下スペース: ユーザーの表示名、値: 標準
    - 高さ、値: 196
    - 幅、値: 196

    ![Xcode の新しい制約設定のスクリーンショット](./images/add-new-constraints.png)

1. 最初のラベルを選択してから、[**配置**] ボタンを使用して、値が0の**コンテナー内の水平方向**の制約を追加します。
1. [**新しい制約の追加**] ボタンを使用して、以下の制約を追加します。

    - [最大サイズ]: ユーザープロファイル写真、値: 標準
    - 下スペース: ユーザーの電子メール、値: 標準

1. 2番目のラベルを選択し、[ **Attributes インスペクター**] を選択します。
1. **色**を**濃い灰色の色**に変更し、**フォント**を**システム 12.0**に変更します。
1. [**配置**] ボタンを使用して、**コンテナー内の水平方向**の制限値を0に追加します。
1. [**新しい制約の追加**] ボタンを使用して、以下の制約を追加します。

    - 先頭のスペース: ユーザーの表示名、値: 標準
    - 下にスペースを挿入: サインアウト、値:14

1. ボタンを選択し、[ **Attributes Inspector**] を選択します。
1. **タイトル**をに`Sign Out`変更します。
1. [**配置**] ボタンを使用して、**コンテナー内の水平方向**の制限値を0に追加します。
1. [**新しい制約の追加**] ボタンを使用して、以下の制約を追加します。

    - [最大サイズ]: ユーザーの電子メール、値:14

1. シーンの下部にあるタブバー項目を選択し、[ **Attributes Inspector**] を選択します。 **タイトル**をに`Me`変更します。
1. [**エディター** ] メニューの [**自動レイアウトの問題を解決**する] を選択し、[**ウェルカムビューコントローラーのすべてのビューの**下にある [不足している制約を**追加**する] を選択します。

完了したら、開始シーンは次のようになります。

![開始シーンレイアウトのスクリーンショット](./images/welcome-scene-layout.png)

### <a name="create-calendar-page"></a>予定表ページを作成する

1. という名前`CalendarViewController`の**graphtutorial**フォルダーに、新しい**cocoa タッチクラス**ファイルを作成します。 Field**のサブクラス**で [ **uiviewcontroller** ] を選択します。
1. **Calendarviewcontroller**を開き、宣言内に`@interface`次のコードを追加します。

    ```objc
    @property (nonatomic) IBOutlet UITextView *calendarJSON;
    ```

1. **Calendarviewcontroller**を開き、その内容を次のコードで置き換えます。

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

1. **メインのストーリーボード**を開きます。 **アイテム2のシーン**を選択し、[ **id インスペクター**] を選択します。 **クラス**値を**calendarviewcontroller**に変更します。
1. **ライブラリ**を使用して、**アイテム2のシーン**に**テキストビュー**を追加します。
1. 追加したテキストビューを選択します。 **エディター**で、[**埋め込み**] を選択してから、[ビュー] を**スクロール**します。
1. [**接続インスペクター**] を使用して、 **calendarjson**アウトレットをテキストビューに接続します。
1. シーンの下部にあるタブバー項目を選択し、[ **Attributes Inspector**] を選択します。 **タイトル**をに`Calendar`変更します。
1. [**エディター** ] メニューの [**自動レイアウトの問題を解決**する] を選択し、[**ウェルカムビューコントローラーのすべてのビューの**下にある [不足している制約を**追加**する] を選択します。

完了すると、予定表のシーンは次のようになります。

![予定表のシーンレイアウトのスクリーンショット](./images/calendar-scene-layout.png)

### <a name="create-activity-indicator"></a>アクティビティインジケーターの作成

1. という名前`SpinnerViewController`の**graphtutorial**フォルダーに、新しい**cocoa タッチクラス**ファイルを作成します。 Field**のサブクラス**で [ **uiviewcontroller** ] を選択します。
1. **Spinnerviewcontroller**を開き、宣言内に`@interface`次のコードを追加します。

    ```objc
    - (void) startWithContainer:(UIViewController*) container;
    - (void) stop;
    ```

1. **Spinnerviewcontroller**を開き、その内容を次のコードで置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/SpinnerViewController.m" id="SpinnerViewSnippet":::

## <a name="test-the-app"></a>アプリのテスト

変更を保存し、アプリを起動します。 [**サインイン**] ボタンと [**サインアウト**] ボタン、およびタブバーを使用して、画面間を移動できるようにする必要があります。

![アプリケーションのスクリーンショット](./images/app-screens.png)
