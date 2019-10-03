# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="f70a2-101">完了したプロジェクトを実行する方法</span><span class="sxs-lookup"><span data-stu-id="f70a2-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f70a2-102">前提条件</span><span class="sxs-lookup"><span data-stu-id="f70a2-102">Prerequisites</span></span>

<span data-ttu-id="f70a2-103">このフォルダーで完了したプロジェクトを実行するには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="f70a2-103">To run the completed project in this folder, you need the following:</span></span>

- [<span data-ttu-id="f70a2-104">Xcode</span><span class="sxs-lookup"><span data-stu-id="f70a2-104">Xcode</span></span>](https://developer.apple.com/xcode/)
- [<span data-ttu-id="f70a2-105">CocoaPods</span><span class="sxs-lookup"><span data-stu-id="f70a2-105">CocoaPods</span></span>](https://cocoapods.org)
- <span data-ttu-id="f70a2-106">Outlook.com 上のメールボックスを持つ個人の Microsoft アカウント、または Microsoft 職場または学校のアカウントのいずれか。</span><span class="sxs-lookup"><span data-stu-id="f70a2-106">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

> <span data-ttu-id="f70a2-107">**注:** このチュートリアルは、Xcode バージョン10.3 と Cocoアポストロフィ Ds バージョン1.7.5 を使用して作成されました。</span><span class="sxs-lookup"><span data-stu-id="f70a2-107">**Note:** This tutorial was written using Xcode version 10.3 and CocoaPods version 1.7.5.</span></span> <span data-ttu-id="f70a2-108">このガイドの手順は、他のバージョンでは動作しますが、テストされていません。</span><span class="sxs-lookup"><span data-stu-id="f70a2-108">The steps in this guide may work with other versions, but that has not been tested.</span></span>

<span data-ttu-id="f70a2-109">Microsoft アカウントを持っていない場合は、無料のアカウントを取得するためのオプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="f70a2-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="f70a2-110">[新しい個人用 Microsoft アカウントにサインアップ](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)することができます。</span><span class="sxs-lookup"><span data-stu-id="f70a2-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="f70a2-111">[Office 365 開発者プログラムにサインアップ](https://developer.microsoft.com/office/dev-program)して、無料の office 365 サブスクリプションを取得することができます。</span><span class="sxs-lookup"><span data-stu-id="f70a2-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="f70a2-112">Web アプリケーションを Azure Active Directory 管理センターに登録する</span><span class="sxs-lookup"><span data-stu-id="f70a2-112">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="f70a2-113">ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動します。</span><span class="sxs-lookup"><span data-stu-id="f70a2-113">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="f70a2-114">**個人用アカウント** (別名: Microsoft アカウント)、または**職場/学校アカウント**を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="f70a2-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="f70a2-115">左側のナビゲーションで [ **Azure Active Directory** ] を選択し、[**管理**] の下にある [**アプリの登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f70a2-115">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="f70a2-116">アプリの登録のスクリーンショット</span><span class="sxs-lookup"><span data-stu-id="f70a2-116">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="f70a2-117">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f70a2-117">Select **New registration**.</span></span> <span data-ttu-id="f70a2-118">**[アプリケーションを登録]** ページで、次のように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="f70a2-118">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="f70a2-119">`iOS Objective-C Graph Tutorial` に **[名前]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="f70a2-119">Set **Name** to `iOS Objective-C Graph Tutorial`.</span></span>
    - <span data-ttu-id="f70a2-120">[**サポートされているアカウントの種類**] を [**任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント**] に設定します。</span><span class="sxs-lookup"><span data-stu-id="f70a2-120">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="f70a2-121">[**リダイレクト URI**] で、ドロップダウンを [**パブリッククライアント (モバイル & デスクトップ)**] に変更し`msauth.YOUR_BUNDLE_ID://auth`、値`YOUR_BUNDLE_ID`をに設定して、アプリのバンドル ID で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f70a2-121">Under **Redirect URI**, change the dropdown to **Public client (mobile & desktop)**, and set the value to `msauth.YOUR_BUNDLE_ID://auth`, replacing `YOUR_BUNDLE_ID` with the bundle ID for your app.</span></span>

    ![[アプリケーションの登録] ページのスクリーンショット](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="f70a2-123">[**登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f70a2-123">Choose **Register**.</span></span> <span data-ttu-id="f70a2-124">IOS の「 **C Graph のチュートリアル**」ページで、**アプリケーション (クライアント) ID**の値をコピーして保存します。そのためには、次の手順で必要になります。</span><span class="sxs-lookup"><span data-stu-id="f70a2-124">On the **iOS Objective-C Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新しいアプリの登録のアプリケーション ID のスクリーンショット](/tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a><span data-ttu-id="f70a2-126">サンプルを構成する</span><span class="sxs-lookup"><span data-stu-id="f70a2-126">Configure the sample</span></span>

1. <span data-ttu-id="f70a2-127">ファイルの`AuthSettings.plist.example`名前を`AuthSettings.plist`に変更します。</span><span class="sxs-lookup"><span data-stu-id="f70a2-127">Rename the `AuthSettings.plist.example` file to `AuthSettings.plist`.</span></span>
1. <span data-ttu-id="f70a2-128">`AuthSettings.plist`ファイルを編集し、次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="f70a2-128">Edit the `AuthSettings.plist` file and make the following changes.</span></span>
    1. <span data-ttu-id="f70a2-129">を`YOUR_APP_ID_HERE`アプリ登録ポータルで取得した**アプリケーション Id**に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f70a2-129">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="f70a2-130">**Graphtutorial**ディレクトリでターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f70a2-130">Open Terminal in the **GraphTutorial** directory and run the following command.</span></span>

    ```Shell
    pod install
    ```

## <a name="run-the-sample"></a><span data-ttu-id="f70a2-131">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="f70a2-131">Run the sample</span></span>

<span data-ttu-id="f70a2-132">Xcode で、[**製品**] メニューを選択し、[**実行**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f70a2-132">In Xcode, select the **Product** menu, then select **Run**.</span></span>
