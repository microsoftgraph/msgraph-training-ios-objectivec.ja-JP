<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d651b-101">この演習では、Microsoft Graph をアプリケーションに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="d651b-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="d651b-102">このアプリケーションでは、microsoft graph [SDK for Use C](https://github.com/microsoftgraph/msgraph-sdk-objc)を使用して microsoft graph を呼び出すことにします。</span><span class="sxs-lookup"><span data-stu-id="d651b-102">For this application, you will use the [Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="d651b-103">Outlook からカレンダー イベントを取得する</span><span class="sxs-lookup"><span data-stu-id="d651b-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="d651b-104">このセクションでは、 `GraphManager`クラスを拡張して、ユーザーのイベントを取得する関数を追加`CalendarViewController`し、これらの新しい関数を使用するように更新します。</span><span class="sxs-lookup"><span data-stu-id="d651b-104">In this section you will extend the `GraphManager` class to add a function to get the user's events and update `CalendarViewController` to use these new functions.</span></span>

1. <span data-ttu-id="d651b-105">**Graphmanager**を開き、宣言の`@interface`上に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="d651b-105">Open **GraphManager.h** and add the following code above the `@interface` declaration.</span></span>

    ```objc
    typedef void (^GetEventsCompletionBlock)(NSData* _Nullable data, NSError* _Nullable error);
    ```

1. <span data-ttu-id="d651b-106">次のコードを`@interface`宣言に追加します。</span><span class="sxs-lookup"><span data-stu-id="d651b-106">Add the following code to the `@interface` declaration.</span></span>

    ```objc
    - (void) getEventsWithCompletionBlock: (GetEventsCompletionBlock)completionBlock;
    ```

1. <span data-ttu-id="d651b-107">**Graphmanager. m**を開き、次の関数を`GraphManager`クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="d651b-107">Open **GraphManager.m** and add the following function to the `GraphManager` class.</span></span>

    ```objc
    - (void) getEventsWithCompletionBlock:(GetEventsCompletionBlock)completionBlock {
        // GET /me/events?$select='subject,organizer,start,end'$orderby=createdDateTime DESC
        NSString* eventsUrlString =
        [NSString stringWithFormat:@"%@/me/events?%@&%@",
         MSGraphBaseURL,
         // Only return these fields in results
         @"$select=subject,organizer,start,end",
         // Sort results by when they were created, newest first
         @"$orderby=createdDateTime+DESC"];

        NSURL* eventsUrl = [[NSURL alloc] initWithString:eventsUrlString];
        NSMutableURLRequest* eventsRequest = [[NSMutableURLRequest alloc] initWithURL:eventsUrl];

        MSURLSessionDataTask* eventsDataTask =
        [[MSURLSessionDataTask alloc]
         initWithRequest:eventsRequest
         client:self.graphClient
         completion:^(NSData *data, NSURLResponse *response, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
                 return;
             }

             // TEMPORARY
             completionBlock(data, nil);
         }];

        // Execute the request
        [eventsDataTask execute];
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="d651b-108">のコードに`getEventsWithCompletionBlock`ついて検討します。</span><span class="sxs-lookup"><span data-stu-id="d651b-108">Consider what the code in `getEventsWithCompletionBlock` is doing.</span></span>
    >
    > - <span data-ttu-id="d651b-109">呼び出される URL は `/v1.0/me/events` です。</span><span class="sxs-lookup"><span data-stu-id="d651b-109">The URL that will be called is `/v1.0/me/events`.</span></span>
    > - <span data-ttu-id="d651b-110">Query `select`パラメーターは、各イベントに対して返されるフィールドを、アプリが実際に使用するものだけに制限します。</span><span class="sxs-lookup"><span data-stu-id="d651b-110">The `select` query parameter limits the fields returned for each events to just those the app will actually use.</span></span>
    > - <span data-ttu-id="d651b-111">クエリ`orderby`パラメーターは、生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="d651b-111">The `orderby` query parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="d651b-112">**Calendarviewcontroller**を開き、その内容全体を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d651b-112">Open **CalendarViewController.m** and replace its entire contents with the following code.</span></span>

    ```objc
    #import "CalendarViewController.h"
    #import "SpinnerViewController.h"
    #import "GraphManager.h"
    #import <MSGraphClientModels/MSGraphClientModels.h>

    @interface CalendarViewController ()

    @property SpinnerViewController* spinner;

    @end

    @implementation CalendarViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.

        self.spinner = [SpinnerViewController alloc];
        [self.spinner startWithContainer:self];

        [GraphManager.instance
         getEventsWithCompletionBlock:^(NSData * _Nullable data, NSError * _Nullable error) {
             dispatch_async(dispatch_get_main_queue(), ^{
                 [self.spinner stop];

                 if (error) {
                     // Show the error
                     UIAlertController* alert = [UIAlertController
                                                 alertControllerWithTitle:@"Error getting events"
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

                 // TEMPORARY
                 self.calendarJSON.text = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                 [self.calendarJSON sizeToFit];
             });
         }];
    }

    @end
    ```

1. <span data-ttu-id="d651b-113">アプリを実行し、サインインして、メニューの [**予定表**] ナビゲーション項目をタップします。</span><span class="sxs-lookup"><span data-stu-id="d651b-113">Run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="d651b-114">アプリ内のイベントの JSON ダンプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d651b-114">You should see a JSON dump of the events in the app.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="d651b-115">結果の表示</span><span class="sxs-lookup"><span data-stu-id="d651b-115">Display the results</span></span>

<span data-ttu-id="d651b-116">これで、JSON ダンプを、ユーザーにわかりやすい方法で結果を表示するためのものに置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="d651b-116">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="d651b-117">このセクションでは、厳密に型`getEventsWithCompletionBlock`指定されたオブジェクトを返すように関数`CalendarViewController`を変更し、テーブルビューを使用してイベントをレンダリングするように変更します。</span><span class="sxs-lookup"><span data-stu-id="d651b-117">In this section, you will modify the `getEventsWithCompletionBlock` function to return strongly-typed objects, and modify `CalendarViewController` to use a table view to render the events.</span></span>

### <a name="update-getevents"></a><span data-ttu-id="d651b-118">GetEvents の更新</span><span class="sxs-lookup"><span data-stu-id="d651b-118">Update getEvents</span></span>

1. <span data-ttu-id="d651b-119">**Graphmanager を**開きます。</span><span class="sxs-lookup"><span data-stu-id="d651b-119">Open **GraphManager.h**.</span></span> <span data-ttu-id="d651b-120">`GetEventsCompletionBlock`型定義を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="d651b-120">Change the `GetEventsCompletionBlock` type definition to the following.</span></span>

    ```objc
    typedef void (^GetEventsCompletionBlock)(NSArray<MSGraphEvent*>* _Nullable events, NSError* _Nullable error);
    ```

1. <span data-ttu-id="d651b-121">**Graphmanager. m**を開きます。</span><span class="sxs-lookup"><span data-stu-id="d651b-121">Open **GraphManager.m**.</span></span> <span data-ttu-id="d651b-122">`getEventsWithCompletionBlock`関数の`completionBlock(data, nil);`行を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d651b-122">Replace the `completionBlock(data, nil);` line in the `getEventsWithCompletionBlock` function with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphManager.m" id="GetEventsSnippet" highlight="24-43":::

### <a name="update-calendarviewcontroller"></a><span data-ttu-id="d651b-123">CalendarViewController の更新</span><span class="sxs-lookup"><span data-stu-id="d651b-123">Update CalendarViewController</span></span>

1. <span data-ttu-id="d651b-124">という名前`CalendarTableViewCell`の**graphtutorial**プロジェクトに、新しい**cocoa タッチクラス**ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="d651b-124">Create a new **Cocoa Touch Class** file in the **GraphTutorial** project named `CalendarTableViewCell`.</span></span> <span data-ttu-id="d651b-125">Field**のサブクラス**で [ **Uitableviewcell セル**を選択します。</span><span class="sxs-lookup"><span data-stu-id="d651b-125">Choose **UITableViewCell** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="d651b-126">**Calendartableviewcell .h**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d651b-126">Open **CalendarTableViewCell.h** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.h" id="CalendarTableCellSnippet":::

1. <span data-ttu-id="d651b-127">**Calendartableviewcell**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d651b-127">Open **CalendarTableViewCell.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.m" id="CalendarTableCellSnippet":::

1. <span data-ttu-id="d651b-128">**メインのストーリーボード**を開いて、**予定表のシーン**を探します。</span><span class="sxs-lookup"><span data-stu-id="d651b-128">Open **Main.storyboard** and locate the **Calendar Scene**.</span></span> <span data-ttu-id="d651b-129">**予定表のシーン**で**ビュー**を選択し、削除します。</span><span class="sxs-lookup"><span data-stu-id="d651b-129">Select the **View** in the **Calendar Scene** and delete it.</span></span>

    ![予定表シーンのビューのスクリーンショット](./images/view-in-calendar-scene.png)

1. <span data-ttu-id="d651b-131">**ライブラリ**から予定表の**シーン**に**テーブルビュー**を追加します。</span><span class="sxs-lookup"><span data-stu-id="d651b-131">Add a **Table View** from the **Library** to the **Calendar Scene**.</span></span>
1. <span data-ttu-id="d651b-132">テーブルビューを選択し、[**属性インスペクター**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d651b-132">Select the table view, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="d651b-133">**プロトタイプセル**を**1**に設定します。</span><span class="sxs-lookup"><span data-stu-id="d651b-133">Set **Prototype Cells** to **1**.</span></span>
1. <span data-ttu-id="d651b-134">**ライブラリ**を使用して、[プロトタイプ] セルに3つの**ラベル**を追加します。</span><span class="sxs-lookup"><span data-stu-id="d651b-134">Use the **Library** to add three **Labels** to the prototype cell.</span></span>
1. <span data-ttu-id="d651b-135">[プロトタイプ] セルを選択し、[ **Identity Inspector**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d651b-135">Select the prototype cell, then select the **Identity Inspector**.</span></span> <span data-ttu-id="d651b-136">**クラス**を**Calendartableviewcell**に変更します。</span><span class="sxs-lookup"><span data-stu-id="d651b-136">Change **Class** to **CalendarTableViewCell**.</span></span>
1. <span data-ttu-id="d651b-137">[ **Attributes インスペクター** ] を選択し、 `EventCell`**識別子**をに設定します。</span><span class="sxs-lookup"><span data-stu-id="d651b-137">Select the **Attributes Inspector** and set **Identifier** to `EventCell`.</span></span>
1. <span data-ttu-id="d651b-138">[ **Eventcell セル**を選択した状態で、[ `durationLabel`**接続インスペクター** ] `subjectLabel` 、[接続] `organizerLabel`、[] の順に選択し、ストーリーボードのセルに追加したラベルに接続します。</span><span class="sxs-lookup"><span data-stu-id="d651b-138">With the **EventCell** selected, select the **Connections Inspector** and connect `durationLabel`, `organizerLabel`, and `subjectLabel` to the labels you added to the cell on the storyboard.</span></span>
1. <span data-ttu-id="d651b-139">3つのラベルのプロパティと制約を次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="d651b-139">Set the properties and constraints on the three labels as follows.</span></span>

    - <span data-ttu-id="d651b-140">**件名ラベル**</span><span class="sxs-lookup"><span data-stu-id="d651b-140">**Subject Label**</span></span>
        - <span data-ttu-id="d651b-141">制約の追加: 先頭の領域をコンテンツビューの先頭余白、値: 0 にします。</span><span class="sxs-lookup"><span data-stu-id="d651b-141">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="d651b-142">制約の追加: 末尾のスペースをコンテンツビューの末尾の余白、値: 0 にします。</span><span class="sxs-lookup"><span data-stu-id="d651b-142">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="d651b-143">制約の追加: 上スペースからコンテンツビューの上余白、値: 0</span><span class="sxs-lookup"><span data-stu-id="d651b-143">Add constraint: Top space to Content View Top Margin, value: 0</span></span>
    - <span data-ttu-id="d651b-144">**開催者のラベル**</span><span class="sxs-lookup"><span data-stu-id="d651b-144">**Organizer Label**</span></span>
        - <span data-ttu-id="d651b-145">フォント: システム12.0</span><span class="sxs-lookup"><span data-stu-id="d651b-145">Font: System 12.0</span></span>
        - <span data-ttu-id="d651b-146">制約の追加: 先頭の領域をコンテンツビューの先頭余白、値: 0 にします。</span><span class="sxs-lookup"><span data-stu-id="d651b-146">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="d651b-147">制約の追加: 末尾のスペースをコンテンツビューの末尾の余白、値: 0 にします。</span><span class="sxs-lookup"><span data-stu-id="d651b-147">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="d651b-148">制約の追加: 上スペースを件名ラベルの下に、値: Standard にします。</span><span class="sxs-lookup"><span data-stu-id="d651b-148">Add constraint: Top space to Subject Label Bottom, value: Standard</span></span>
    - <span data-ttu-id="d651b-149">**期間のラベル**</span><span class="sxs-lookup"><span data-stu-id="d651b-149">**Duration Label**</span></span>
        - <span data-ttu-id="d651b-150">フォント: システム12.0</span><span class="sxs-lookup"><span data-stu-id="d651b-150">Font: System 12.0</span></span>
        - <span data-ttu-id="d651b-151">色: 濃い灰色</span><span class="sxs-lookup"><span data-stu-id="d651b-151">Color: Dark Gray Color</span></span>
        - <span data-ttu-id="d651b-152">制約の追加: 先頭の領域をコンテンツビューの先頭余白、値: 0 にします。</span><span class="sxs-lookup"><span data-stu-id="d651b-152">Add constraint: Leading space to Content View Leading Margin, value: 0</span></span>
        - <span data-ttu-id="d651b-153">制約の追加: 末尾のスペースをコンテンツビューの末尾の余白、値: 0 にします。</span><span class="sxs-lookup"><span data-stu-id="d651b-153">Add constraint: Trailing space to Content View Trailing Margin, value: 0</span></span>
        - <span data-ttu-id="d651b-154">制約の追加: 上のスペースから開催者ラベルの下、値: 標準</span><span class="sxs-lookup"><span data-stu-id="d651b-154">Add constraint: Top space to Organizer Label Bottom, value: Standard</span></span>
        - <span data-ttu-id="d651b-155">制約の追加: 下のスペースをコンテンツビューの下の余白、値: 8 に制限します。</span><span class="sxs-lookup"><span data-stu-id="d651b-155">Add constraint: Bottom space to Content View Bottom Margin, value: 8</span></span>

    ![プロトタイプセルのレイアウトのスクリーンショット](./images/prototype-cell-layout.png)

1. <span data-ttu-id="d651b-157">**Calendarviewcontroller**を開き、プロパティを`calendarJSON`削除します。</span><span class="sxs-lookup"><span data-stu-id="d651b-157">Open **CalendarViewController.h** and remove the `calendarJSON` property.</span></span>
1. <span data-ttu-id="d651b-158">`@interface`宣言を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="d651b-158">Change the `@interface` declaration to the following.</span></span>

    ```objc
    @interface CalendarViewController : UITableViewController
    ```

1. <span data-ttu-id="d651b-159">**Calendarviewcontroller**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d651b-159">Open **CalendarViewController.m** and replace its contents with the following code.</span></span>

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarViewController.m" id="CalendarViewSnippet":::

1. <span data-ttu-id="d651b-160">アプリを実行し、サインインして [**予定表**] タブをタップします。イベントの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="d651b-160">Run the app, sign in, and tap the **Calendar** tab. You should see the list of events.</span></span>

    ![イベント表のスクリーンショット](./images/calendar-list.png)
