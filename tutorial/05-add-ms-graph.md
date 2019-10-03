<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="9e017-101">この演習では、Microsoft Graph をアプリケーションに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="9e017-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="9e017-102">このアプリケーションでは、microsoft graph [SDK for Use C](https://github.com/microsoftgraph/msgraph-sdk-objc)を使用して microsoft graph を呼び出すことにします。</span><span class="sxs-lookup"><span data-stu-id="9e017-102">For this application, you will use the [Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="9e017-103">Outlook から予定表のイベントを取得する</span><span class="sxs-lookup"><span data-stu-id="9e017-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="9e017-104">このセクションでは、 `GraphManager`クラスを拡張して、ユーザーのイベントを取得する関数を追加`CalendarViewController`し、これらの新しい関数を使用するように更新します。</span><span class="sxs-lookup"><span data-stu-id="9e017-104">In this section you will extend the `GraphManager` class to add a function to get the user's events and update `CalendarViewController` to use these new functions.</span></span>

1. <span data-ttu-id="9e017-105">**Graphmanager**を開き、宣言の`@interface`上に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="9e017-105">Open **GraphManager.h** and add the following code above the `@interface` declaration.</span></span>

    ```objc
    typedef void (^GetEventsCompletionBlock)(NSData* _Nullable data, NSError* _Nullable error);
    ```

1. <span data-ttu-id="9e017-106">次のコードを`@interface`宣言に追加します。</span><span class="sxs-lookup"><span data-stu-id="9e017-106">Add the following code to the `@interface` declaration.</span></span>

    ```objc
    - (void) getEventsWithCompletionBlock: (GetEventsCompletionBlock)completionBlock;
    ```

1. <span data-ttu-id="9e017-107">**Graphmanager. m**を開き、次の関数を`GraphManager`クラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="9e017-107">Open **GraphManager.m** and add the following function to the `GraphManager` class.</span></span>

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
    > <span data-ttu-id="9e017-108">のコードに`getEventsWithCompletionBlock`ついて検討します。</span><span class="sxs-lookup"><span data-stu-id="9e017-108">Consider what the code in `getEventsWithCompletionBlock` is doing.</span></span>
    >
    > - <span data-ttu-id="9e017-109">呼び出し先の URL は`/v1.0/me/events`になります。</span><span class="sxs-lookup"><span data-stu-id="9e017-109">The URL that will be called is `/v1.0/me/events`.</span></span>
    > - <span data-ttu-id="9e017-110">Query `select`パラメーターは、各イベントに対して返されるフィールドを、アプリが実際に使用するものだけに制限します。</span><span class="sxs-lookup"><span data-stu-id="9e017-110">The `select` query parameter limits the fields returned for each events to just those the app will actually use.</span></span>
    > - <span data-ttu-id="9e017-111">クエリ`orderby`パラメーターは、生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="9e017-111">The `orderby` query parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="9e017-112">**Calendarviewcontroller**を開き、その内容全体を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9e017-112">Open **CalendarViewController.m** and replace its entire contents with the following code.</span></span>

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

<span data-ttu-id="9e017-113">これで、アプリを実行し、サインインすると、メニューの [**予定表**] ナビゲーション項目をタップできるようになります。</span><span class="sxs-lookup"><span data-stu-id="9e017-113">You can now run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="9e017-114">アプリ内のイベントの JSON ダンプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="9e017-114">You should see a JSON dump of the events in the app.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="9e017-115">結果を表示する</span><span class="sxs-lookup"><span data-stu-id="9e017-115">Display the results</span></span>

<span data-ttu-id="9e017-116">これで、JSON ダンプを、ユーザーにわかりやすい方法で結果を表示するためのものに置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="9e017-116">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="9e017-117">このセクションでは、厳密に型`getEventsWithCompletionBlock`指定されたオブジェクトを返すように関数`CalendarViewController`を変更し、テーブルビューを使用してイベントをレンダリングするように変更します。</span><span class="sxs-lookup"><span data-stu-id="9e017-117">In this section, you will modify the `getEventsWithCompletionBlock` function to return strongly-typed objects, and modify `CalendarViewController` to use a table view to render the events.</span></span>

### <a name="update-getevents"></a><span data-ttu-id="9e017-118">GetEvents の更新</span><span class="sxs-lookup"><span data-stu-id="9e017-118">Update getEvents</span></span>

1. <span data-ttu-id="9e017-119">**Graphmanager を**開きます。</span><span class="sxs-lookup"><span data-stu-id="9e017-119">Open **GraphManager.h**.</span></span> <span data-ttu-id="9e017-120">`GetEventsCompletionBlock`型定義を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="9e017-120">Change the `GetEventsCompletionBlock` type definition to the following.</span></span>

    ```objc
    typedef void (^GetEventsCompletionBlock)(NSArray<MSGraphEvent*>* _Nullable events, NSError* _Nullable error);
    ```

1. <span data-ttu-id="9e017-121">**Graphmanager. m**を開きます。</span><span class="sxs-lookup"><span data-stu-id="9e017-121">Open **GraphManager.m**.</span></span> <span data-ttu-id="9e017-122">`getEventsWithCompletionBlock`関数の`completionBlock(data, nil);`行を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9e017-122">Replace the `completionBlock(data, nil);` line in the `getEventsWithCompletionBlock` function with the following code.</span></span>

    ```objc
    NSError* graphError;

    // Deserialize to an events collection
    MSCollection* eventsCollection = [[MSCollection alloc] initWithData:data error:&graphError];
    if (graphError) {
        completionBlock(nil, graphError);
        return;
    }

    // Create an array to return
    NSMutableArray* eventsArray = [[NSMutableArray alloc]
                                initWithCapacity:eventsCollection.value.count];

    for (id event in eventsCollection.value) {
        // Deserialize the event and add to the array
        MSGraphEvent* graphEvent = [[MSGraphEvent alloc] initWithDictionary:event];
        [eventsArray addObject:graphEvent];
    }

    completionBlock(eventsArray, nil);
    ```

### <a name="update-calendarviewcontroller"></a><span data-ttu-id="9e017-123">CalendarViewController の更新</span><span class="sxs-lookup"><span data-stu-id="9e017-123">Update CalendarViewController</span></span>

1. <span data-ttu-id="9e017-124">という名前`CalendarTableViewCell`の**graphtutorial**プロジェクトに、新しい**cocoa タッチクラス**ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="9e017-124">Create a new **Cocoa Touch Class** file in the **GraphTutorial** project named `CalendarTableViewCell`.</span></span> <span data-ttu-id="9e017-125">Field**のサブクラス**で [ **Uitableviewcell セル**を選択します。</span><span class="sxs-lookup"><span data-stu-id="9e017-125">Choose **UITableViewCell** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="9e017-126">**Calendartableviewcell .h**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9e017-126">Open **CalendarTableViewCell.h** and replace its contents with the following code.</span></span>

    ```objc
    #import <UIKit/UIKit.h>

    NS_ASSUME_NONNULL_BEGIN

    @interface CalendarTableViewCell : UITableViewCell

    @property (nonatomic) NSString* subject;
    @property (nonatomic) NSString* organizer;
    @property (nonatomic) NSString* duration;

    @end

    NS_ASSUME_NONNULL_END
    ```

1. <span data-ttu-id="9e017-127">**Calendartableviewcell**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9e017-127">Open **CalendarTableViewCell.m** and replace its contents with the following code.</span></span>

    ```objc
    #import "CalendarTableViewCell.h"

    @interface CalendarTableViewCell()

    @property (nonatomic) IBOutlet UILabel *subjectLabel;
    @property (nonatomic) IBOutlet UILabel *organizerLabel;
    @property (nonatomic) IBOutlet UILabel *durationLabel;

    @end

    @implementation CalendarTableViewCell

    - (void)awakeFromNib {
        [super awakeFromNib];
        // Initialization code
    }

    - (void)setSelected:(BOOL)selected animated:(BOOL)animated {
        [super setSelected:selected animated:animated];

        // Configure the view for the selected state
    }

    - (void) setSubject:(NSString *)subject {
        _subject = subject;
        self.subjectLabel.text = subject;
        [self.subjectLabel sizeToFit];
    }

    - (void) setOrganizer:(NSString *)organizer {
        _organizer = organizer;
        self.organizerLabel.text = organizer;
        [self.organizerLabel sizeToFit];
    }

    - (void) setDuration:(NSString *)duration {
        _duration = duration;
        self.durationLabel.text = duration;
        [self.durationLabel sizeToFit];
    }

    @end
    ```

1. <span data-ttu-id="9e017-128">**メインのストーリーボード**を開いて、**予定表のシーン**を探します。</span><span class="sxs-lookup"><span data-stu-id="9e017-128">Open **Main.storyboard** and locate the **Calendar Scene**.</span></span> <span data-ttu-id="9e017-129">**予定表のシーン**で**ビュー**を選択し、削除します。</span><span class="sxs-lookup"><span data-stu-id="9e017-129">Select the **View** in the **Calendar Scene** and delete it.</span></span>

    ![予定表シーンのビューのスクリーンショット](./images/view-in-calendar-scene.png)

1. <span data-ttu-id="9e017-131">**ライブラリ**から予定表の**シーン**に**テーブルビュー**を追加します。</span><span class="sxs-lookup"><span data-stu-id="9e017-131">Add a **Table View** from the **Library** to the **Calendar Scene**.</span></span>
1. <span data-ttu-id="9e017-132">テーブルビューを選択し、[**属性インスペクター**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9e017-132">Select the table view, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="9e017-133">**プロトタイプセル**を**1**に設定します。</span><span class="sxs-lookup"><span data-stu-id="9e017-133">Set **Prototype Cells** to **1**.</span></span>
1. <span data-ttu-id="9e017-134">**ライブラリ**を使用して、[プロトタイプ] セルに3つの**ラベル**を追加します。</span><span class="sxs-lookup"><span data-stu-id="9e017-134">Use the **Library** to add three **Labels** to the prototype cell.</span></span>
1. <span data-ttu-id="9e017-135">[プロトタイプ] セルを選択し、[ **Identity Inspector**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9e017-135">Select the prototype cell, then select the **Identity Inspector**.</span></span> <span data-ttu-id="9e017-136">**クラス**を**Calendartableviewcell**に変更します。</span><span class="sxs-lookup"><span data-stu-id="9e017-136">Change **Class** to **CalendarTableViewCell**.</span></span>
1. <span data-ttu-id="9e017-137">[ **Attributes インスペクター** ] を選択し、 `EventCell`**識別子**をに設定します。</span><span class="sxs-lookup"><span data-stu-id="9e017-137">Select the **Attributes Inspector** and set **Identifier** to `EventCell`.</span></span>
1. <span data-ttu-id="9e017-138">[**エディター** ] メニューの [**自動レイアウトの問題を解決**する] を選択し、[**ウェルカムビューコントローラーのすべてのビューの**下にある [不足している制約を**追加**する] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9e017-138">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Welcome View Controller**.</span></span>
1. <span data-ttu-id="9e017-139">[ **Eventcell セル**を選択した状態で、[ `durationLabel`**接続インスペクター** ] `subjectLabel` 、[接続] `organizerLabel`、[] の順に選択し、ストーリーボードのセルに追加したラベルに接続します。</span><span class="sxs-lookup"><span data-stu-id="9e017-139">With the **EventCell** selected, select the **Connections Inspector** and connect `durationLabel`, `organizerLabel`, and `subjectLabel` to the labels you added to the cell on the storyboard.</span></span>

    ![プロトタイプセルのレイアウトのスクリーンショット](./images/prototype-cell-layout.png)

1. <span data-ttu-id="9e017-141">**Calendarviewcontroller**を開き、プロパティを`calendarJSON`削除します。</span><span class="sxs-lookup"><span data-stu-id="9e017-141">Open **CalendarViewController.h** and remove the `calendarJSON` property.</span></span>
1. <span data-ttu-id="9e017-142">`@interface`宣言を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="9e017-142">Change the `@interface` declaration to the following.</span></span>

    ```objc
    @interface CalendarViewController : UITableViewController
    ```

1. <span data-ttu-id="9e017-143">**Calendarviewcontroller**を開き、その内容を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9e017-143">Open **CalendarViewController.m** and replace its contents with the following code.</span></span>

    ```objc
    #import "WelcomeViewController.h"
    #import "SpinnerViewController.h"
    #import "AuthenticationManager.h"
    #import "GraphManager.h"
    #import <MSGraphClientModels/MSGraphClientModels.h>

    @interface WelcomeViewController ()

    @property SpinnerViewController* spinner;

    @end

    @implementation WelcomeViewController

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

    - (IBAction)signOut {
        [AuthenticationManager.instance signOut];
        [self performSegueWithIdentifier: @"userSignedOut" sender: nil];
    }

    @end
    ```

1. <span data-ttu-id="9e017-144">アプリを実行し、サインインして [**予定表**] タブをタップします。イベントの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9e017-144">Run the app, sign in, and tap the **Calendar** tab. You should see the list of events.</span></span>

    ![イベントの表のスクリーンショット](./images/calendar-list.png)
