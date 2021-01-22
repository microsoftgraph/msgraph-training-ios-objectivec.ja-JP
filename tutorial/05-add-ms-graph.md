<!-- markdownlint-disable MD002 MD041 -->

この演習では、Microsoft Graph をアプリケーションに組み込む必要があります。 このアプリケーションでは [、Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) を使用して Microsoft Graph を呼び出します。

## <a name="get-calendar-events-from-outlook"></a>Outlook からカレンダー イベントを取得する

このセクションでは、クラスを拡張して、現在の週のユーザーのイベントを取得する関数を追加し、この新しい関数を使用 `GraphManager` `CalendarViewController` する更新を行います。

1. **GraphManager.h を開** き、次のコードを宣言の上に追加 `@interface` します。

    ```objc
    typedef void (^GetCalendarViewCompletionBlock)(NSData* _Nullable data,
                                                   NSError* _Nullable error);
    ```

1. 次のコードを宣言に追加 `@interface` します。

    ```objc
    - (void) getCalendarViewStartingAt: (NSString*) viewStart
                              endingAt: (NSString*) viewEnd
                   withCompletionBlock: (GetCalendarViewCompletionBlock) completion;
    ```

1. **GraphManager.m を** 開き、次の関数をクラスに追加 `GraphManager` します。

    ```objc
    - (void) getCalendarViewStartingAt: (NSString *) viewStart
                              endingAt: (NSString *) viewEnd
                   withCompletionBlock: (GetCalendarViewCompletionBlock) completion {
        // Set calendar view start and end parameters
        NSString* viewStartEndString =
        [NSString stringWithFormat:@"startDateTime=%@&endDateTime=%@",
         viewStart,
         viewEnd];

        // GET /me/calendarview
        NSString* eventsUrlString =
        [NSString stringWithFormat:@"%@/me/calendarview?%@&%@&%@&%@",
         MSGraphBaseURL,
         viewStartEndString,
         // Only return these fields in results
         @"$select=subject,organizer,start,end",
         // Sort results by start time
         @"$orderby=start/dateTime",
         // Request at most 25 results
         @"$top=25"];

        NSURL* eventsUrl = [[NSURL alloc] initWithString:eventsUrlString];
        NSMutableURLRequest* eventsRequest = [[NSMutableURLRequest alloc] initWithURL:eventsUrl];

        // Add the Prefer: outlook.timezone header to get start and end times
        // in user's time zone
        NSString* preferHeader =
        [NSString stringWithFormat:@"outlook.timezone=\"%@\"",
         self.graphTimeZone];
        [eventsRequest addValue:preferHeader forHTTPHeaderField:@"Prefer"];

        MSURLSessionDataTask* eventsDataTask =
        [[MSURLSessionDataTask alloc]
         initWithRequest:eventsRequest
         client:self.graphClient
         completion:^(NSData *data, NSURLResponse *response, NSError *error) {
             if (error) {
                 completion(nil, error);
                 return;
             }

             // TEMPORARY
             completion(data, nil);
         }];

        // Execute the request
        [eventsDataTask execute];
    }
    ```

    > [!NOTE]
    > コードの実行を `getCalendarViewStartingAt` 検討します。
    >
    > - 呼び出される URL は `/v1.0/me/calendarview` です。
    >   - The `startDateTime` and query parameters define the start and end of the calendar `endDateTime` view.
    >   - クエリ パラメーターは、各イベントで返されるフィールドを、ビューが実際に使用 `select` するフィールドに限定します。
    >   - クエリ `orderby` パラメーターは、開始時刻で結果を並べ替える。
    >   - クエリ `top` パラメーターは、1 ページあたり 25 の結果を要求します。
    >   - このヘッダーにより、Microsoft Graph はユーザーのタイム ゾーン内の各イベントの開始時刻と終了 `Prefer: outlook.timezone` 時刻を返します。

1. GraphToIana **という名前の** **GraphTu touchl** プロジェクトに新しい Cocoa Touch クラス **を作成します**。 フィールド **のサブクラスで NSObject** **を選択** します。
1. **GraphToIana.h を開き**、その内容を次のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphToIana.h" id="GraphToIanaSnippet":::

1. **GraphToIana.m を** 開き、その内容を次のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphToIana.m" id="GraphToIanaSnippet":::

    これにより、Microsoft Graph によって返されるタイム ゾーン名に基づいて IANA タイム ゾーン識別子を検索する簡単な検索が実行されます。

1. **CalendarViewController.m を** 開き、内容全体を次のコードに置き換えます。

    ```objc
    #import "CalendarViewController.h"
    #import "SpinnerViewController.h"
    #import "GraphManager.h"
    #import "GraphToIana.h"
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

        // Calculate the start and end of the current week
        NSString* timeZoneId = [GraphToIana
                                getIanaIdentifierFromGraphIdentifier:
                                [GraphManager.instance graphTimeZone]];

        NSDate* now = [NSDate date];
        NSCalendar* calendar = [NSCalendar calendarWithIdentifier:NSCalendarIdentifierGregorian];
        NSTimeZone* timeZone = [NSTimeZone timeZoneWithName:timeZoneId];
        [calendar setTimeZone:timeZone];

        NSDateComponents* startOfWeekComponents = [calendar
                                                   components:NSCalendarUnitCalendar |
                                                   NSCalendarUnitYearForWeekOfYear |
                                                   NSCalendarUnitWeekOfYear
                                                   fromDate:now];
        NSDate* startOfWeek = [startOfWeekComponents date];
        NSDate* endOfWeek = [calendar dateByAddingUnit:NSCalendarUnitDay
                                                 value:7
                                                toDate:startOfWeek
                                               options:0];

        // Convert start and end to ISO 8601 strings
        NSISO8601DateFormatter* isoFormatter = [[NSISO8601DateFormatter alloc] init];
        NSString* viewStart = [isoFormatter stringFromDate:startOfWeek];
        NSString* viewEnd = [isoFormatter stringFromDate:endOfWeek];

        [GraphManager.instance
         getCalendarViewStartingAt:viewStart
         endingAt:viewEnd
         withCompletionBlock:^(NSData * _Nullable data, NSError * _Nullable error) {
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

1. アプリを実行し、サインインして、メニューの **[予定表** ] ナビゲーション 項目をタップします。 アプリにイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果の表示

これで、JSON ダンプを何かに置き換え、結果をユーザー に分け親しみのある方法で表示できます。 このセクションでは、関数を変更して、型指定されたオブジェクトを返し、テーブル ビューを使用してイベントを `getCalendarViewStartingAt` `CalendarViewController` レンダリングする変更を行います。

### <a name="update-getcalendarviewstartingat"></a>getCalendarViewStartingAt の更新

1. **GraphManager.h を開きます**。 型定義 `GetCalendarViewCompletionBlock` を次に変更します。

    ```objc
    typedef void (^GetCalendarViewCompletionBlock)(NSArray<MSGraphEvent*>* _Nullable events, NSError* _Nullable error);
    ```

1. **GraphManager.m を開きます**。 既存の `getCalendarViewStartingAt` 関数を、以下のコードで置換します。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphManager.m" id="GetCalendarViewSnippet" highlight="42-61":::

### <a name="update-calendarviewcontroller"></a>CalendarViewController の更新

1. **GraphTu touchl** **プロジェクトに新** しい Cocoa Touch Class ファイルを作成します `CalendarTableViewCell` 。 フィールド **のサブクラスで UITableViewCell** **を選択** します。
1. **CalendarTableViewCell.h を開き**、その内容を次のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.h" id="CalendarTableCellSnippet":::

1. **CalendarTableViewCell.m を** 開き、内容を次のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.m" id="CalendarTableCellSnippet":::

1. **GraphTu touchl** **プロジェクトに新** しい Cocoa Touch Class ファイルを作成します `CalendarTableViewController` 。 フィールド **のサブクラスで UITableViewController** **を選択** します。
1. **CalendarTableViewController.h** を開き、内容を次のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewController.h" id="CalendarTableViewControllerSnippet":::

1. **CalendarTableViewController.m を** 開き、内容を次のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewController.m" id="CalendarTableViewControllerSnippet":::

1. **Main.storyboard を開** き、予定表シーン **を見つける**。 ルート ビューからスクロール ビューを削除します。
1. ライブラリを **使用** して、ビュー **の上部** にナビゲーション バーを追加します。
1. ナビゲーション バーでタイトル **を** ダブルクリックし、次の値に更新します `Calendar` 。
1. ライブラリを **使用** して、ナビゲーション バー **の** 右側にバー ボタン項目を追加します。
1. 新しいバー ボタンを選択し、属性インスペクター **を選択します**。 イメージ **をプラスに****変更します**。
1. ライブラリから **ナビゲーション バー** の **下の** ビューにコンテナー ビューを追加します。 コンテナー ビューのサイズを変更して、ビュー内の残りのすべての領域を取得します。
1. ナビゲーション バーとコンテナー ビューの制約を次のように設定します。

    - **ナビゲーション バー**
        - 制約の追加: 高さ、値: 44
        - 制約の追加: 安全領域への先頭のスペース、値: 0
        - 制約を追加する: 安全領域への末尾のスペース、値: 0
        - 制約を追加する: 安全な領域に上のスペース、値: 0
    - **コンテナー ビュー**
        - 制約の追加: 安全領域への先頭のスペース、値: 0
        - 制約を追加する: 安全領域への末尾のスペース、値: 0
        - 制約の追加: ナビゲーション バーの下に上のスペース、値: 0
        - 制約の追加: セーフ エリアへの下部スペース、値: 0

1. コンテナー ビューを追加するときにストーリーボードに追加された 2 番目のビュー コントローラーを見つける。 埋め込みセグ **によってカレンダー** シーンに接続されます。 このコントローラーを選択し **、Id インスペクターを使用** して **Class** を **CalendarTableViewController に変更します**。
1. カレンダー テーブル **ビュー** コントローラーから **ビューを削除します**。
1. ライブラリから **予定表テーブル ビュー** **コントローラーに** テーブル **ビューを追加します**。
1. テーブル ビューを選択し、属性インスペクター **を選択します**。 プロトタイプ セル **を** **1 に設定します**。
1. プロトタイプ セルの下端をドラッグして、操作する領域を広くします。
1. ライブラリを **使用して** 、プロトタイプ セルに **3 つのラベル** を追加します。
1. プロトタイプ セルを選択し、Identity Inspector を **選択します**。 クラス **を** **CalendarTableViewCell に変更します**。
1. 属性インスペクタ **ーを選択し、[** 識別子 **] を次に設定** します `EventCell` 。
1. **EventCell を** 選択した後 **、Connections Inspector** を選択して接続し、ストーリーボードのセルに追加したラベル `durationLabel` `organizerLabel` `subjectLabel` に接続します。
1. 次のように、3 つのラベルのプロパティと制約を設定します。

    - **件名ラベル**
        - 制約の追加: コンテンツ ビューの先頭に余白を付け、値 : 0
        - 制約を追加する: コンテンツ ビューの末尾の余白に末尾のスペース、値: 0
        - 制約の追加: コンテンツ ビューの上余白に上余白、値: 0
    - **開催者ラベル**
        - フォント: System 12.0
        - 制約の追加: 高さ、値: 15
        - 制約の追加: コンテンツ ビューの先頭に余白を付け、値 : 0
        - 制約を追加する: コンテンツ ビューの末尾の余白に末尾のスペース、値: 0
        - 制約の追加: 件名ラベルの下に上のスペース、値: Standard
    - **期間ラベル**
        - フォント: System 12.0
        - 色: 濃い灰色
        - 制約の追加: 高さ、値: 15
        - 制約の追加: コンテンツ ビューの先頭に余白を付け、値 : 0
        - 制約を追加する: コンテンツ ビューの末尾の余白に末尾のスペース、値: 0
        - 制約の追加: [開催者ラベルの下に上のスペース]、値 : [標準]
        - 制約の追加: コンテンツ ビューの下余白に下のスペース、値: 0

1. **EventCell を選択し**、サイズ検査を **選択します**。 行 **の高さに** 対 **して自動を有効にする**。

    ![予定表と予定表のテーブル ビュー コントローラーのスクリーンショット](images/calendar-table-storyboard.png)

1. **CalendarViewController.h を開き**、プロパティを削除 `calendarJSON` します。

1. **CalendarViewController.m を** 開き、内容を次のコードに置き換えます。

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarViewController.m" id="CalendarViewControllerSnippet":::

1. アプリを実行し、サインインして、[予定表] タブ **をタップ** します。イベントの一覧が表示されます。

    ![イベント表のスクリーンショット](./images/calendar-list.png)
