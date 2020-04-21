<!-- markdownlint-disable MD002 MD041 -->

この演習では、Microsoft Graph をアプリケーションに組み込みます。 このアプリケーションでは、microsoft graph [SDK for Use C](https://github.com/microsoftgraph/msgraph-sdk-objc)を使用して microsoft graph を呼び出すことにします。

## <a name="get-calendar-events-from-outlook"></a>Outlook からカレンダー イベントを取得する

このセクションでは、 `GraphManager`クラスを拡張して、ユーザーのイベントを取得する関数を追加`CalendarViewController`し、これらの新しい関数を使用するように更新します。

1. **Graphmanager**を開き、宣言の`@interface`上に次のコードを追加します。

    ```objc
    typedef void (^GetEventsCompletionBlock)(NSData* _Nullable data, NSError* _Nullable error);
    ```

1. 次のコードを`@interface`宣言に追加します。

    ```objc
    - (void) getEventsWithCompletionBlock: (GetEventsCompletionBlock)completionBlock;
    ```

1. **Graphmanager. m**を開き、次の関数を`GraphManager`クラスに追加します。

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
    > のコードに`getEventsWithCompletionBlock`ついて検討します。
    >
    > - 呼び出される URL は `/v1.0/me/events` です。
    > - Query `select`パラメーターは、各イベントに対して返されるフィールドを、アプリが実際に使用するものだけに制限します。
    > - クエリ`orderby`パラメーターは、生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。

1. **Calendarviewcontroller**を開き、その内容全体を次のコードで置き換えます。

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

1. アプリを実行し、サインインして、メニューの [**予定表**] ナビゲーション項目をタップします。 アプリ内のイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果の表示

これで、JSON ダンプを、ユーザーにわかりやすい方法で結果を表示するためのものに置き換えることができます。 このセクションでは、厳密に型`getEventsWithCompletionBlock`指定されたオブジェクトを返すように関数`CalendarViewController`を変更し、テーブルビューを使用してイベントをレンダリングするように変更します。

### <a name="update-getevents"></a>GetEvents の更新

1. **Graphmanager を**開きます。 `GetEventsCompletionBlock`型定義を次のように変更します。

    ```objc
    typedef void (^GetEventsCompletionBlock)(NSArray<MSGraphEvent*>* _Nullable events, NSError* _Nullable error);
    ```

1. **Graphmanager. m**を開きます。 `getEventsWithCompletionBlock`関数の`completionBlock(data, nil);`行を次のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphManager.m" id="GetEventsSnippet" highlight="24-43":::

### <a name="update-calendarviewcontroller"></a>CalendarViewController の更新

1. という名前`CalendarTableViewCell`の**graphtutorial**プロジェクトに、新しい**cocoa タッチクラス**ファイルを作成します。 Field**のサブクラス**で [ **Uitableviewcell セル**を選択します。
1. **Calendartableviewcell .h**を開き、その内容を次のコードで置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.h" id="CalendarTableCellSnippet":::

1. **Calendartableviewcell**を開き、その内容を次のコードで置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.m" id="CalendarTableCellSnippet":::

1. **メインのストーリーボード**を開いて、**予定表のシーン**を探します。 **予定表のシーン**で**ビュー**を選択し、削除します。

    ![予定表シーンのビューのスクリーンショット](./images/view-in-calendar-scene.png)

1. **ライブラリ**から予定表の**シーン**に**テーブルビュー**を追加します。
1. テーブルビューを選択し、[**属性インスペクター**] を選択します。 **プロトタイプセル**を**1**に設定します。
1. **ライブラリ**を使用して、[プロトタイプ] セルに3つの**ラベル**を追加します。
1. [プロトタイプ] セルを選択し、[ **Identity Inspector**] を選択します。 **クラス**を**Calendartableviewcell**に変更します。
1. [ **Attributes インスペクター** ] を選択し、 `EventCell`**識別子**をに設定します。
1. [ **Eventcell セル**を選択した状態で、[ `durationLabel`**接続インスペクター** ] `subjectLabel` 、[接続] `organizerLabel`、[] の順に選択し、ストーリーボードのセルに追加したラベルに接続します。
1. 3つのラベルのプロパティと制約を次のように設定します。

    - **件名ラベル**
        - 制約の追加: 先頭の領域をコンテンツビューの先頭余白、値: 0 にします。
        - 制約の追加: 末尾のスペースをコンテンツビューの末尾の余白、値: 0 にします。
        - 制約の追加: 上スペースからコンテンツビューの上余白、値: 0
    - **開催者のラベル**
        - フォント: システム12.0
        - 制約の追加: 先頭の領域をコンテンツビューの先頭余白、値: 0 にします。
        - 制約の追加: 末尾のスペースをコンテンツビューの末尾の余白、値: 0 にします。
        - 制約の追加: 上スペースを件名ラベルの下に、値: Standard にします。
    - **期間のラベル**
        - フォント: システム12.0
        - 色: 濃い灰色
        - 制約の追加: 先頭の領域をコンテンツビューの先頭余白、値: 0 にします。
        - 制約の追加: 末尾のスペースをコンテンツビューの末尾の余白、値: 0 にします。
        - 制約の追加: 上のスペースから開催者ラベルの下、値: 標準
        - 制約の追加: 下のスペースをコンテンツビューの下の余白、値: 8 に制限します。

    ![プロトタイプセルのレイアウトのスクリーンショット](./images/prototype-cell-layout.png)

1. **Calendarviewcontroller**を開き、プロパティを`calendarJSON`削除します。
1. `@interface`宣言を次のように変更します。

    ```objc
    @interface CalendarViewController : UITableViewController
    ```

1. **Calendarviewcontroller**を開き、その内容を次のコードで置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarViewController.m" id="CalendarViewSnippet":::

1. アプリを実行し、サインインして [**予定表**] タブをタップします。イベントの一覧が表示されます。

    ![イベント表のスクリーンショット](./images/calendar-list.png)
