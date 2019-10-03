<!-- markdownlint-disable MD002 MD041 -->

この手順では、Azure Active Directory 管理センターを使用して、新しい Azure AD ネイティブアプリケーションを作成します。

1. ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)へ移動して、**個人用アカウント** (別名: Microsoft アカウント)、または**職場/学校アカウント**を使用してログインします。

1. 左側のナビゲーションで [ **Azure Active Directory** ] を選択し、[**管理**] の下にある [**アプリの登録**] を選択します。

    ![アプリの登録のスクリーンショット ](./images/aad-portal-app-registrations.png)

1. **[新規登録]** を選択します。 **[アプリケーションを登録]** ページで、次のように値を設定します。

    - `iOS Objective-C Graph Tutorial` に **[名前]** を設定します。
    - [**サポートされているアカウントの種類**] を [**任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント**] に設定します。
    - [**リダイレクト URI**] で、ドロップダウンを [**パブリッククライアント (モバイル & デスクトップ)**] に変更し`msauth.YOUR_BUNDLE_ID://auth`、値`YOUR_BUNDLE_ID`をに設定して、アプリのバンドル ID で置き換えます。

    ![[アプリケーションの登録] ページのスクリーンショット](./images/aad-register-an-app.png)

1. [**登録**] を選択します。 IOS の「 **C Graph のチュートリアル**」ページで、**アプリケーション (クライアント) ID**の値をコピーして保存します。そのためには、次の手順で必要になります。

    ![新しいアプリの登録のアプリケーション ID のスクリーンショット](./images/aad-application-id.png)
