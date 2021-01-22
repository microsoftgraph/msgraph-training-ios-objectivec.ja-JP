<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a475b-101">このセクションでは、ユーザーの予定表にイベントを作成する機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="a475b-102">**GraphManager.h を開** き、次のコードを宣言の上に追加 `@interface` します。</span><span class="sxs-lookup"><span data-stu-id="a475b-102">Open **GraphManager.h** and add the following code above the `@interface` declaration.</span></span>

    ```objc
    typedef void (^CreateEventCompletionBlock)(MSGraphEvent* _Nullable event,
                                               NSError* _Nullable error);
    ```

1. <span data-ttu-id="a475b-103">次のコードを宣言に追加 `@interface` します。</span><span class="sxs-lookup"><span data-stu-id="a475b-103">Add the following code to the `@interface` declaration.</span></span>

    ```objc
    - (void) createEventWithSubject: (NSString*) subject
                           andStart: (NSDate*) start
                             andEnd: (NSDate*) end
                       andAttendees: (NSArray<NSString*>* _Nullable) attendees
                            andBody: (NSString* _Nullable) body
                 andCompletionBlock: (CreateEventCompletionBlock) completion;
    ```

1. <span data-ttu-id="a475b-104">**GraphManager.m を** 開き、次の関数を追加して、ユーザーの予定表に新しいイベントを作成します。</span><span class="sxs-lookup"><span data-stu-id="a475b-104">Open **GraphManager.m** and add the following function to create a new event on the user's calendar.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphManager.m" id="CreateEventSnippet":::

1. <span data-ttu-id="a475b-105">**GraphTu touchl** **フォルダーに新** しい Cocoa Touch Class ファイルを作成します `NewEventViewController` 。</span><span class="sxs-lookup"><span data-stu-id="a475b-105">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `NewEventViewController`.</span></span> <span data-ttu-id="a475b-106">フィールド **のサブクラスで UIViewController** **を選択** します。</span><span class="sxs-lookup"><span data-stu-id="a475b-106">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="a475b-107">**NewEventViewController.h** を開き、宣言内に次のコードを追加 `@interface` します。</span><span class="sxs-lookup"><span data-stu-id="a475b-107">Open **NewEventViewController.h** and add the following code inside the `@interface` declaration.</span></span>

    ```objectivec
    @property (nonatomic) IBOutlet UITextField* subject;
    @property (nonatomic) IBOutlet UITextField* attendees;
    @property (nonatomic) IBOutlet UIDatePicker* start;
    @property (nonatomic) IBOutlet UIDatePicker* end;
    @property (nonatomic) IBOutlet UITextView* body;
    ```

1. <span data-ttu-id="a475b-108">**NewEventController.m を** 開き、その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a475b-108">Open **NewEventController.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/NewEventViewController.m" id="NewEventViewControllerSnippet":::

1. <span data-ttu-id="a475b-109">**Main.storyboard を開きます**。</span><span class="sxs-lookup"><span data-stu-id="a475b-109">Open **Main.storyboard**.</span></span> <span data-ttu-id="a475b-110">ライブラリを **使用して** 、ビュー **コントローラーをストーリーボード** にドラッグします。</span><span class="sxs-lookup"><span data-stu-id="a475b-110">Use the **Library** to drag a **View Controller** onto the storyboard.</span></span>
1. <span data-ttu-id="a475b-111">ライブラリを **使用して**、ビュー **コントローラーにナビゲーション バー** を追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-111">Using the **Library**, add a **Navigation Bar** to the view controller.</span></span>
1. <span data-ttu-id="a475b-112">ナビゲーション バーでタイトル **を** ダブルクリックし、次の値に更新します `New Event` 。</span><span class="sxs-lookup"><span data-stu-id="a475b-112">Double-click the **Title** in the navigation bar and update it to `New Event`.</span></span>
1. <span data-ttu-id="a475b-113">ライブラリを **使用** して、ナビゲーション バー **の** 左側にバー ボタン項目を追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-113">Using the **Library**, add a **Bar Button Item** to the left-hand side of the navigation bar.</span></span>
1. <span data-ttu-id="a475b-114">新しいバー ボタンを選択し、属性インスペクター **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="a475b-114">Select the new bar button, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="a475b-115">Title **を次に変更** します `Cancel` 。</span><span class="sxs-lookup"><span data-stu-id="a475b-115">Change **Title** to `Cancel`.</span></span>
1. <span data-ttu-id="a475b-116">ライブラリを **使用** して、ナビゲーション バー **の** 右側にバー ボタン項目を追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-116">Using the **Library**, add a **Bar Button Item** to the right-hand side of the navigation bar.</span></span>
1. <span data-ttu-id="a475b-117">新しいバー ボタンを選択し、属性インスペクター **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="a475b-117">Select the new bar button, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="a475b-118">Title **を次に変更** します `Create` 。</span><span class="sxs-lookup"><span data-stu-id="a475b-118">Change **Title** to `Create`.</span></span>
1. <span data-ttu-id="a475b-119">ビュー コントローラーを選択し、Id インスペクター **を選択します**。</span><span class="sxs-lookup"><span data-stu-id="a475b-119">Select the view controller, then select the **Identity Inspector**.</span></span> <span data-ttu-id="a475b-120">クラス **を** **NewEventViewController に変更します**。</span><span class="sxs-lookup"><span data-stu-id="a475b-120">Change **Class** to **NewEventViewController**.</span></span>
1. <span data-ttu-id="a475b-121">次のコントロールをライブラリから **ビュー** に追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-121">Add the following controls from the **Library** to the view.</span></span>

    - <span data-ttu-id="a475b-122">ナビゲーション バー **の下にラベル** を追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-122">Add a **Label** under the navigation bar.</span></span> <span data-ttu-id="a475b-123">テキストを次に設定します `Subject` 。</span><span class="sxs-lookup"><span data-stu-id="a475b-123">Set its text to `Subject`.</span></span>
    - <span data-ttu-id="a475b-124">ラベルの **下にテキスト フィールド** を追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-124">Add a **Text Field** under the label.</span></span> <span data-ttu-id="a475b-125">Placeholder 属性 **を次に設定** します `Subject` 。</span><span class="sxs-lookup"><span data-stu-id="a475b-125">Set its **Placeholder** attribute to `Subject`.</span></span>
    - <span data-ttu-id="a475b-126">テキスト フィールド **の下にラベル** を追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-126">Add a **Label** under the text field.</span></span> <span data-ttu-id="a475b-127">テキストを次に設定します `Attendees` 。</span><span class="sxs-lookup"><span data-stu-id="a475b-127">Set its text to `Attendees`.</span></span>
    - <span data-ttu-id="a475b-128">ラベルの **下にテキスト フィールド** を追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-128">Add a **Text Field** under the label.</span></span> <span data-ttu-id="a475b-129">Placeholder 属性 **を次に設定** します `Separate multiple entries with ;` 。</span><span class="sxs-lookup"><span data-stu-id="a475b-129">Set its **Placeholder** attribute to `Separate multiple entries with ;`.</span></span>
    - <span data-ttu-id="a475b-130">テキスト フィールド **の下にラベル** を追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-130">Add a **Label** under the text field.</span></span> <span data-ttu-id="a475b-131">テキストを次に設定します `Start` 。</span><span class="sxs-lookup"><span data-stu-id="a475b-131">Set its text to `Start`.</span></span>
    - <span data-ttu-id="a475b-132">ラベルの **下に日付の選択** コントロールを追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-132">Add a **Date Picker** under the label.</span></span> <span data-ttu-id="a475b-133">[優先 **するスタイル] を** **[コンパクト] に** 設定し、**間隔\*\*\*\*を 15 分** に設定し、高さを **35 に設定します**。</span><span class="sxs-lookup"><span data-stu-id="a475b-133">Set its **Preferred Style** to **Compact**, its **Interval** to **15 minutes**, and its height to **35**.</span></span>
    - <span data-ttu-id="a475b-134">日付の選択 **コントロールの** 下にラベルを追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-134">Add a **Label** under the date picker.</span></span> <span data-ttu-id="a475b-135">テキストを次に設定します `End` 。</span><span class="sxs-lookup"><span data-stu-id="a475b-135">Set its text to `End`.</span></span>
    - <span data-ttu-id="a475b-136">ラベルの **下に日付の選択** コントロールを追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-136">Add a **Date Picker** under the label.</span></span> <span data-ttu-id="a475b-137">[優先 **するスタイル] を** **[コンパクト] に** 設定し、**間隔\*\*\*\*を 15 分** に設定し、高さを **35 に設定します**。</span><span class="sxs-lookup"><span data-stu-id="a475b-137">Set its **Preferred Style** to **Compact**, its **Interval** to **15 minutes**, and its height to **35**.</span></span>
    - <span data-ttu-id="a475b-138">日付の選択 **コントロールの下に** テキスト ビューを追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-138">Add a **Text View** under the date picker.</span></span>

1. <span data-ttu-id="a475b-139">新しい **イベント ビュー コントローラーを選択し** 、 **接続インスペクターを使用して** 次の接続を行います。</span><span class="sxs-lookup"><span data-stu-id="a475b-139">Select the **New Event View Controller** and use the **Connection Inspector** to make the following connections.</span></span>

    - <span data-ttu-id="a475b-140">受信した **取り消** し操作を [キャンセル バー **] ボタンに** 接続します。</span><span class="sxs-lookup"><span data-stu-id="a475b-140">Connect the **cancel** received action to the **Cancel** bar button.</span></span>
    - <span data-ttu-id="a475b-141">**createEvent 受信アクションを**[バーの作成]**ボタンに** 接続します。</span><span class="sxs-lookup"><span data-stu-id="a475b-141">Connect the **createEvent** received action to the **Create** bar button.</span></span>
    - <span data-ttu-id="a475b-142">件名の **出口を** 最初のテキスト フィールドに接続します。</span><span class="sxs-lookup"><span data-stu-id="a475b-142">Connect the **subject** outlet to the first text field.</span></span>
    - <span data-ttu-id="a475b-143">出席者の **出口を 2** 番目のテキスト フィールドに接続します。</span><span class="sxs-lookup"><span data-stu-id="a475b-143">Connect the **attendees** outlet to the second text field.</span></span>
    - <span data-ttu-id="a475b-144">最初の **日付の** 選択に開始の出口を接続します。</span><span class="sxs-lookup"><span data-stu-id="a475b-144">Connect the **start** outlet to the first date picker.</span></span>
    - <span data-ttu-id="a475b-145">最後の出口 **を** 2 番目の日付の選択に接続します。</span><span class="sxs-lookup"><span data-stu-id="a475b-145">Connect the **end** outlet to the second date picker.</span></span>
    - <span data-ttu-id="a475b-146">本文の **出口を** テキスト ビューに接続します。</span><span class="sxs-lookup"><span data-stu-id="a475b-146">Connect the **body** outlet to the text view.</span></span>

1. <span data-ttu-id="a475b-147">次の制約を追加します。</span><span class="sxs-lookup"><span data-stu-id="a475b-147">Add the following constraints.</span></span>

    - <span data-ttu-id="a475b-148">**ナビゲーション バー**</span><span class="sxs-lookup"><span data-stu-id="a475b-148">**Navigation Bar**</span></span>
        - <span data-ttu-id="a475b-149">セーフ エリアへの先頭のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-149">Leading space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="a475b-150">セーフ エリアへの末尾のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-150">Trailing space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="a475b-151">トップスペースからセーフ エリア、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-151">Top space to Safe Area, value: 0</span></span>
        - <span data-ttu-id="a475b-152">高さ、値: 44</span><span class="sxs-lookup"><span data-stu-id="a475b-152">Height, value: 44</span></span>
    - <span data-ttu-id="a475b-153">**件名ラベル**</span><span class="sxs-lookup"><span data-stu-id="a475b-153">**Subject Label**</span></span>
        - <span data-ttu-id="a475b-154">表示余白への先頭のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-154">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-155">ビューの余白への末尾のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-155">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-156">ナビゲーション バーの上のスペース、値: 20</span><span class="sxs-lookup"><span data-stu-id="a475b-156">Top space to Navigation Bar, value: 20</span></span>
    - <span data-ttu-id="a475b-157">**[件名] テキスト フィールド**</span><span class="sxs-lookup"><span data-stu-id="a475b-157">**Subject Text Field**</span></span>
        - <span data-ttu-id="a475b-158">表示余白への先頭のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-158">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-159">ビューの余白への末尾のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-159">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-160">件名ラベルの上のスペース、値: Standard</span><span class="sxs-lookup"><span data-stu-id="a475b-160">Top space to Subject Label, value: Standard</span></span>
    - <span data-ttu-id="a475b-161">**出席者ラベル**</span><span class="sxs-lookup"><span data-stu-id="a475b-161">**Attendees Label**</span></span>
        - <span data-ttu-id="a475b-162">表示余白への先頭のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-162">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-163">ビューの余白への末尾のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-163">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-164">件名テキスト フィールドの上のスペース、値: Standard</span><span class="sxs-lookup"><span data-stu-id="a475b-164">Top space to Subject Text Field, value: Standard</span></span>
    - <span data-ttu-id="a475b-165">**Attendees テキスト フィールド**</span><span class="sxs-lookup"><span data-stu-id="a475b-165">**Attendees Text Field**</span></span>
        - <span data-ttu-id="a475b-166">表示余白への先頭のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-166">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-167">ビューの余白への末尾のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-167">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-168">出席者ラベルの上のスペース、値: 標準</span><span class="sxs-lookup"><span data-stu-id="a475b-168">Top space to Attendees Label, value: Standard</span></span>
    - <span data-ttu-id="a475b-169">**開始ラベル**</span><span class="sxs-lookup"><span data-stu-id="a475b-169">**Start Label**</span></span>
        - <span data-ttu-id="a475b-170">表示余白への先頭のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-170">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-171">ビューの余白への末尾のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-171">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-172">件名テキスト フィールドの上のスペース、値: Standard</span><span class="sxs-lookup"><span data-stu-id="a475b-172">Top space to Subject Text Field, value: Standard</span></span>
    - <span data-ttu-id="a475b-173">**開始日の選択**</span><span class="sxs-lookup"><span data-stu-id="a475b-173">**Start Date Picker**</span></span>
        - <span data-ttu-id="a475b-174">表示余白への先頭のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-174">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-175">ビューの余白への末尾のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-175">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-176">出席者ラベルの上のスペース、値: 標準</span><span class="sxs-lookup"><span data-stu-id="a475b-176">Top space to Attendees Label, value: Standard</span></span>
        - <span data-ttu-id="a475b-177">高さ、値: 35</span><span class="sxs-lookup"><span data-stu-id="a475b-177">Height, value: 35</span></span>
    - <span data-ttu-id="a475b-178">**ラベルの終了**</span><span class="sxs-lookup"><span data-stu-id="a475b-178">**End Label**</span></span>
        - <span data-ttu-id="a475b-179">表示余白への先頭のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-179">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-180">ビューの余白への末尾のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-180">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-181">[開始日の選択] の上のスペース、値: 標準</span><span class="sxs-lookup"><span data-stu-id="a475b-181">Top space to Start Date Picker, value: Standard</span></span>
    - <span data-ttu-id="a475b-182">**終了日の選択**</span><span class="sxs-lookup"><span data-stu-id="a475b-182">**End Date Picker**</span></span>
        - <span data-ttu-id="a475b-183">表示余白への先頭のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-183">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-184">ビューの余白への末尾のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-184">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-185">トップスペースからエンド ラベル、値: Standard</span><span class="sxs-lookup"><span data-stu-id="a475b-185">Top space to End Label, value: Standard</span></span>
        - <span data-ttu-id="a475b-186">高さ: 35</span><span class="sxs-lookup"><span data-stu-id="a475b-186">Height: 35</span></span>
    - <span data-ttu-id="a475b-187">**本文テキスト ビュー**</span><span class="sxs-lookup"><span data-stu-id="a475b-187">**Body Text View**</span></span>
        - <span data-ttu-id="a475b-188">表示余白への先頭のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-188">Leading space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-189">ビューの余白への末尾のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-189">Trailing space to View margin, value: 0</span></span>
        - <span data-ttu-id="a475b-190">トップスペースから終了日の選択、値: Standard</span><span class="sxs-lookup"><span data-stu-id="a475b-190">Top space to End Date Picker, value: Standard</span></span>
        - <span data-ttu-id="a475b-191">表示余白の下のスペース、値: 0</span><span class="sxs-lookup"><span data-stu-id="a475b-191">Bottom space to View margin, value: 0</span></span>

    ![ストーリーボードの新しいイベント フォームのスクリーンショット](images/new-event-form.png)

1. <span data-ttu-id="a475b-193">予定表シーン **を選択し**、接続インスペクタ **ーを選択します**。</span><span class="sxs-lookup"><span data-stu-id="a475b-193">Select the **Calendar Scene**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="a475b-194">**Triggered Segues の下** で、手動の横にある未入力の円をストーリーボードの新しいイベント ビュー **コントローラー** にドラッグします。</span><span class="sxs-lookup"><span data-stu-id="a475b-194">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **New Event View Controller** on the storyboard.</span></span> <span data-ttu-id="a475b-195">ポップアップ **メニューで [モーダルに** 表示] を選択します。</span><span class="sxs-lookup"><span data-stu-id="a475b-195">Select **Present Modally** in the pop-up menu.</span></span>
1. <span data-ttu-id="a475b-196">追加したセグを選択し、属性インスペクターを **選択します**。</span><span class="sxs-lookup"><span data-stu-id="a475b-196">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="a475b-197">[識別子] **フィールドを** 次に設定します `showEventForm` 。</span><span class="sxs-lookup"><span data-stu-id="a475b-197">Set the **Identifier** field to `showEventForm`.</span></span>
1. <span data-ttu-id="a475b-198">**showNewEventForm 受信アクションを** ナビゲーション バー ボタン **+** に接続します。</span><span class="sxs-lookup"><span data-stu-id="a475b-198">Connect the **showNewEventForm** received action to the **+** navigation bar button.</span></span>
1. <span data-ttu-id="a475b-199">変更内容を保存し、アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="a475b-199">Save your changes and restart the app.</span></span> <span data-ttu-id="a475b-200">予定表ページに移動し、ボタンをタップ **+** します。</span><span class="sxs-lookup"><span data-stu-id="a475b-200">Go to the calendar page and tap the **+** button.</span></span> <span data-ttu-id="a475b-201">フォームに入力し、[作成] **をタップ** して新しいイベントを作成します。</span><span class="sxs-lookup"><span data-stu-id="a475b-201">Fill in the form and tap **Create** to create a new event.</span></span>

    ![新しいイベント フォームのスクリーンショット](images/create-event.png)
