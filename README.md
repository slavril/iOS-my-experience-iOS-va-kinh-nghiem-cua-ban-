# GCD
## Về cơ bản Apple mô tả Grand Central Dispatch (GCD) như sau: 
- Như chúng ta đã biết, xử lý đa luông trong lập trình thực sự rất khó, chính vì vậy, Apple đã tạo ra, và cho rằng Grand Central Dispatch (GCD) sẽ giúp quá trình này trở nên dễ dàng hơn, thông qua các kĩ thuật mới.
- GCD hoàn toàn không phải một thư viện hỗ trợ viết xử lý đa luồng hay viết lại kĩ thuật xử lý đa luồng.
- GCD có sử dụng luồng trong xử lý của nó, nhưng mà ơn trời, dev không bao giờ cần phải cố mà code như trước.
- GCD hỗ trợ chúng ta xử lý đồng thời trong lập trình hướng theo mô hình FIFO.

Cơ chế cơ bản của GCD nôm na dễ hiểu là nó đưa một hoặc nhiều blocks vào hàng đợi, thực hiện các tác vụ rồi trả ra giá trị (callback-block).

GCD hỗ trợ nhiều cách để đưa block vào hàng đợi, có nhiều kiểu hàng đợi khác nhau và phục vụ cho nhiều mục đích khác nhau.
Tóm lại, là lập trình viên chỉ cần sắp xếp các tác vụ cần được xử lý đưa vào trong hàng đợi (dispatch), GCD sẽ làm việc của nó, kết quả được trả về và dữ liệu mới được sử dụng cho các bước tiếp.

Điều thú vị ở đây là khi lập trình, chúng ta thấy như nó được xử lý đồng thời, luồng được gọi, xử lý tự động và cân bằng phù hợp với khả năng của hệ thống. Có một lưu ý, là khi xử lý vấn đề về hiển thị, UI nên và phải luôn được thực hiện trên main queue (cái này sẽ nói rõ hơn và phần sau). Đồng thời là bạn phải luôn kiểm tra tài liệu xem  Object NS hoặc UI mà đang cần thực hiện có an toàn để xử lý trên đa luồng hay không (coi các phần sau để biết chi tiết hơn).

Cách khởi tạo hoặc gọi ra một hàng đợi.
Có 2 loại (serial queues: xếp hàng đợi tới lượt) và (concurrent queues: xếp hàng thích gọi đứa nào thì gọi), hai cái tên nói lên tất cả.

Loại 1 (serial queues): nếu bạn thêm vào một loạt vụ cần thực hiện, mỗi một lúc nó sẽ chỉ lấy ra một cái để xử lý, giống như khi xếp hàng mua KFC vậy, chỉ khi nào tác vụ được xử lý xong, được trả ra và hoàn thành, nó mới lấy một cái khác liền kề ra xử lý tiếp (đứa nào tới trước, xử lý trước, FIFO).
Bạn chỉ cần gọi dispatch_queue_create là tạo được một cái kiểu này. (nhiều hàng đợi có thể chuyển qua chuyển lại giữa nhiều thread ngay giữa những task khác nhau, nhưng nó luôn đảm bảo trong cùng 1 hàng đợi, thì thằng trước xong mới tới thằng sau: nôm na là mình tạo ra 10 queue thì khi 10 cái đó chạy, nó chạy trên nhiều thread khác nhau, có thể chỉ có 3 thread thôi, nên 10 queue giành nhau chạy xen kẻ). Kiểu này rất hay, đỡn nhọc công cho chúng ta trong việc xử lý đa luồng với nhau, mình đã từng làm qua xử lý đa luồng trên C++ và đó quả thực là một cực hình. Với GCD, chỉ đơn giản là add vào và viết tiếp, so easy.

Main queue là một cái hàng đợi kiểu serial queues nhưng đặc biệt hơn, những hàng đợi khác có thể nằm trên nhiều thread khác nhau trong một lúc nhưng main queue thì chỉ có một, và nó duy nhất chạy trên main thread (main thread là luồng mạnh nhất và được ưu tiên nhất, apple luôn cho UI chạy trên đó là để tạo trải nghiệm tốt nhất), đó chính là lý do vì sao khi bạn chạy một cái tác vụ nặng, xử lý quá nhiều trên main queue sẽ có thể làm treo UI, nếu theo lý thuyết về serial queues, UI chắc chắn sẽ phải đợi vụ xong thì mới tiếp tục thực hiện các phản hồi từ người dùng được.

Loại thứ 2 (concurrent queues): loại này mắc cười nhất, cũng đơn giản là bỏ task vào queue thôi, nhưng mà khi thực hiện, nếu system thấy thread nào rảnh, là nó sẽ bỏ 1 cái task của nó vào thread xử lý liền. Chính như vậy task phải hoàn toàn được xử lý an toàn (thread safe), đảm bảo tối thiểu ảnh hưởng. mặc dù nó cũng cùng cơ chế FIFO để lấy task ra, nhưng khác với loại 1, lấy thằng nào ra là xử lý liền thằng đó, không quan tâm là thằng trước xong hay chưa.

### Thread safe tức là bạn cố gắng tránh truy suất vào cùng 1 biến ở nhiều thread khác nhau.

Có vài thần chú triệu hồi cơ bản sau
dispatch_queue_create  // create a serial or concurrent queue 
dispatch_get_main_queue  // get the one and only main queue 
dispatch_get_global_queue  // get one of the global concurrent queues

## Bỏ task vô cái queue
Có queue rồi, ở đâu ko quan trọng, giờ bỏ task vô thôi. Có một nguyên tắc cần nhớ, là bạn phải cân nhắc task cần xử lý của bạn mục đích ra sao để sử dụng queue cho phù hợp nhất. Nếu bạn viết một task request từ server một loạt dữ liệu mà dữ liệu này cần cho dữ liệu sau, tức là mang tính tuần tự, tốt nhất nên thực hiện trên serial queues. Còn giả sử, bạn cần load một tấm ảnh với nhiều phần chia nhỏ ra không cần quan tâm thứ tự các mảnh, khuyến khích dùng concurrent queues để tối đa hoá hiệu suất.

Có mấy câu thần chú nữa
// Asynchronous functions 
dispatch_async 
dispatch_after 
dispatch_apply 
// Synchronous functions 
dispatch_once 
dispatch_sync

dispatch_async: bỏ task vô queue và trả ra liền luôn, xử lý được liền mạch, không phải đợi task được đưa vào queue xử lý xong mới chạy tiếp.
dispatch_after: trả ra liền luôn nhưng mà đợi một chút rồi mới thực hiện task trong queue, kiểu này giống kiểu trên.
dispatch_apply: trả ra một lần thôi nhưng mà task được bỏ vào tới mấy lần, kiểu này thông dụng khi bạn cần xử lý lặp đi lặp lại nhiều lần một task nào đó.

dispatch_sync: bỏ task vô nhưng mà đợi task xong thì mới return đúng 1 lần, kiểu này là kiểu xử lý phải đợi task đang đưa vào queue đó xử lý xong mới được chạy tiếp.
dispatch_once: bỏ task vô, đúng 1 lần thôi, 1 lần trong suốt đời, rồi đợi xong rồi mới return.

## Vài ghi chú khi muốn sử dụng
1. không làm task nặng chạy main queue (main thread) nó lock m cái UI đó, nhớ nhé.
2. tạo một cái serial queue phải có mục đích, đừng tạo tào lao, vì bạn nên nhớ, tạo nhiều serial queue có thể chiếm hết tài nguyên của thiết bị đó.
3. tạo rồi nhớ đặt cái tên cho dễ hiểu mục đích tạo là gì đặng cho thằng khác vào còn biết.
4. task sẽ được xứ lý trên concurrent queue phải thread - safe (có nói ở trên, giờ nói lại, thread safe là đảm bảo một cái thread xử lý tác vụ đó với giá trị không bị thay đổi bởi bất kì một xử lý nào ở thread khác, trước khi nó được hoàng thành và trả ra)
5. sau khi xử lý trên queue xyz nào đó, thì nếu bạn muốn dữ liệu được hiển thị trở lại, nhất thiết phải cập nhật lại UI CHỈ TRÊN MAIN QUEUE.

Chém gió tí, vì sao iPhone không lag còn Android thích lag. Androi có cơ chế để xử lý và cập nhật giao diện trên tất cả các thread các core của mấy, không cần phân biệt thread nào với nhau, cho nên, khi chúng ta có 10 tác vụ chạy trên 8 thread cùng update lên UI, thì với máy thậm chí 4 core đi nữa, nó cũng không thể gánh được 8 thread cùng tranh nhau xử lý, và đôi khi dev android còn cho phép tất cả xử lý của mình cùng chạy một lúc, đó quả thật là một sai lầm. Theo mình tìm hiểu, thì Android không có một cơ chế xử lý như GCD, nhất là JAVA vốn dĩ xử lý luồng bằng tay. Nhưng dev lại quá lười để có thể làm chuyện đó => App android luôn có vấn đề về hiệu suất, dĩ nhiên, có những app rất tốt, được các kĩ sư chăm chút kĩ lưởng hơn nên mượt mà hơn thậm chí đỡ hao pin hơn nữa.
iPhone thì khác, một sự kết hợp tuyệt vời giữa obj-c và GCD, vì sao ư, thứ nhất, các xử lý luôn chạy dưới nền, nơi chúng không thể can thiệp vào thread kiểm soát UI, nó làm tăng trài nghiệm người dùng. Bạn sẽ hỏi, bộ chạy nền thì nó không chậm ư, câu trả lời là có chứ, chậm có khi hơn android ấy chứ, nhưng làm sao thấy nó lag được ^.^, một trick rất hay của Apple. Thực ra những điều này rất dài dòng, rảnh mình sẽ nói cho.

# Core data

Coredata là một framework, nó cung cấp cho chúng ta một cách xử lý, thiết kế dữ liệu trực quan hơn, mạnh mẽ hơn, theo như Apple chém thì coredata sẽ giúp bạn giảm 70% số lượng code để viết query vào database theo bất kì công nghệ nào (XML-SQLite-binary) pla pla pla. Nó có rất nhiều tính năng, nói cả ngày không hết đâu, nên thôi đi vào phần dưới.

Core data stack là một tập hợp của những object trong app mà có thể nó sẽ map với những gì chúng ta lưu trong cơ sở dữ liệu. Core data stack sẽ xử lý tất cả các tuy vấn, xử lí dữ liệu ra và vào cơ sở dữ liệu của thiết bị chúng ta. một stack sẽ bao gồm 3 object chính: managed object context (NSManagedObjectContext) hay gọi dân gian là bối cảnh (nghe cứ quái sao ấy, để bạn tìm tên khác), the persistent store coordinator (NSPersistentStoreCoordinator) hay còn gọi là một người điều phối dữ liệu, trong khái niệm về CSDL thì nó chính là PORT I/O, và NSManagedObjectModel hay nôm na là đối tượng chúng ta xử lý.

Cách khởi tạo coi https://developer.apple.com/library/content///documentation/Cocoa/Conceptual/CoreData/InitializingtheCoreDataStack.html#//apple_ref/doc/uid/TP40001075-CH4-SW1

Giờ mình sẽ mô tả cụ thể từng cái trong stack nó làm gì.

## Đầu tiên là NSManagedObjectModel
cơ bản thì NSManagedObjectModel nó sẽ mô tả nên cái dữ liệu mà chúng ta sẽ truy suất thông qua CoredataStack (có thể nói nó gần giống với database table), thường được gọi với cái tên trìu mến là “mom” (má). khi chúng ta khởi tạo một cái core data stack (coi như là một phiên xủ lý đi, dịch khó quá), core data sẽ load tất cả NSManagedObjectModel lên ram - memory, âm thầm tải lên tất cả dữ liệu của bạn dưới dạng một bảng copy, tăng hiệu suât đọc (cái này liên quan tới context nữa, nói ở sau nhé), đây là một ưu thế đã nói ở trên của core data, khi mà model được khởi tạo và load lên, thì ngay lập tức (store coordinator) cũng sẽ nhanh chóng được cấu trúc và khởi tạo tương ứng.


## Mấy đứa điều phối (manager class - NSPersistentStoreCoorinator, database managerment port I/O)
Trước tiên chúng ta phải làm quen với khái niệm PersistentStore, theo như mình hiểu thì PersistentStore giống như cái kho của bạn vậy (trong một hệ QTCSDL thì nó chính là store, một bộ lưu trữ vật lý, có định dạng), trong nó sẽ định nghĩa ra cách mà hệ thống của chúng ta sắp xếp cấu trúc dữ liệu. Hiện apple core data hỗ trợ 4 kiểu cấu trúc dữ liệu SQlite, Binary (lưu dạng bit byte ấy, file thư viện cũng hay là dạng này), XML và In-Memory (chắc là kiểu cấu trúc dữ liệu lưu trên mem). Vậy hiểu ha, tuỳ vào cấu trúc app mà chúng ta lưu dữ liệu theo nhiều dạng khác nhau, lưu trong mem hay lưu ổ cứng… pla pla 
Chúng ta có Model cấu trúc dữ liệu (giống như một cái kệ có gắng nhãn), thì NSPersistentStoreCoorinator sẽ làm nhiệm vụ đứng trước cửa, bước vào kho, lấy những tấm nhãn mô tả của những món hàng đưa cho chúng ta, chúng ta đặt lên kệ theo đúng ý, hoặc là đem hàng từ kệ đi cất lại vô kho. đồng thời có khi nó sẽ giúp chung ta kiểm tra lại dữ liệu truyền vào trên kệ có hợp với hàng trong kho hay không, tránh kệ để đậu xanh mà lưu thành đậu đen. Khác với kiểu lưu trữ dữ liệu truyền thống, muốn lưu dữ liệu vào DB, bạn phải open port, lúc open port bạn phải lock port lại không cho đứa nào làm nữa hết, sau đó, bạn phải điên lên, vì dữ liệu truyền vào lỗi, ôi mai gót … và Job sinh ra NSPersistentStoreCoorinator để giúp chúng ta đỡ vất vả.

## NSManagedObjectContext
Giả sử, chúng ta lấy hàng, đặt lên kệ, rồi lại bán ra, rồi đặt lại hàng khác lên kệ, rồi lại đem vào cất kho. mọi việc đó cần xử lý trong một lối riêng lẽ. giả sử có rất nhiều người cùng vào nhà kho, lấy hàng ra, đặt trên các kệ khác nhau nhưng lại giống y về cách bố trí, vậy thì mỗi lối người đó đi vào, lấy hàng đi ra, có thể gọi là một bối cảnh NSManagedObjectContext.

Tuần tự, khi chúng ta lấy dữ liệu từ PersistentStore ra, hoặc chúng ta lưu một dữ liệu mới vào Model, thì mặc định chúng vẫn tạm thời chỉ là một bản sao của một dữ liệu thực sự (giống như nó chỉ là cái nhãn của một món hàng vậy). chúng ta có thể thay đổi giá trị của cái dữ liệu đó trên model (thay đổi nhãn trên kệ tuỳ thích) nhưng nó sẽ không ảnh hưởng tới dữ liệu trong đĩa (trong kho_ của bạn. để có thể tương tác được vào đĩa (hoặc kho) chúng ta cần đặt một request cho NSManagedObjectContext hiện tại đang xử lý dữ liệu đó, sau đó NSManagedObjectContext này sẽ đem giá trị mới cho NSPersistentStoreCoorinator và NSPersistentStoreCoorinator sẽ điều phối lưu vào kho dữ liệu.
mỗi Model sẽ phải đăng kí với một NSManagedObjectContext, có thể nhiều model cùng đăng kí với một NSManagedObjectContext, khi thay đổi giá trị model, NSManagedObjectContext sẽ chịu trách nhiệm đối chiếu giá trị mới với những model khác có chung liên kết với model đang bị đổi, để cập nhật lại giá trị cho đúng nhất đồng thời đảm bảo giá trị bạn thay đổi, và phù hợp với tất cả những model bị ảnh hưởng khác.
Đây là điểm khác biệt mạnh nhất của của core data so với việc truy vấn trực tiếp thông qua SQL query thuần tuý. giống như mình đã nói ở trên, Coredata sẽ tự động tạo ra một bảng copy được lưu riêng để dành cho việc truy xuất dữ liệu, không như query thuần tuý, NSManagedObjectContext cho phép chúng ta truy cập dữ liệu chéo liên tục thay vì đóng mở I/O như của SQL query

không có coredata, bạn có thể tự viết cho mình chương trình để quản lý dữ liệu cũng được, nhưng chắc chắn nó sẽ cực thấy m, vì sao, vì bạn sẽ phải tự quản lý tất cả, pla pla pla.

## Các lưu ý.
- muốn lưu dữ liệu của 1 model vào store, hãy chắn chắn model đó đang được xử lý trên NSManagedObjectContext của chính nó.
- NSManagedObjectContext có thể chạy trên nhiều luồng xử lý khác nhau, nên có thể, việc lưu data trên model dựa trên nhiều NSManagedObjectContext khác nhau có thể phát sinh lỗi không mong muốn.

## So sánh một chút tổng quan về việc sử dụng coredata và sql query
- Coredata có tốc độ truy suất nhanh hơn nhất là với những bảng đòi hỏi truy suất dữ liệu liên tục.
- Voredata và sql query đều dễ dàng viết các câu lệnh truy vấn tới CSDL, tuy nhiên, coredata hỗ trợ truy vấn csdl theo dạng truy vấn một object, tức là bảng.thuộc tính hoặc là bảng.bảng.thuộc tính.
- Sql query có thể viết các procedure để hỗ trợ truy vấn, hoặc trigger để hỗ trợ quản lý db
- Cả sql query và coredata đều có thể dùng trên các hệ thống app từ lớn đến nhỏ, nhưng nên nhớ, việc nâng cấp một hệ thống dùng sql query đòi hỏi nhiều công sức hơn, vì phải transfer dữ liệu từ mộ version cũ lên bảng mới hơn. với coredata, việc đó là không cần thiết nữa.

# Block

## Block có thể hiểu là closure như khái niệm của các loại ngôn ngữ lập trình khác. Đúng là khá xoắn thật.

=> Dịch ra đại thể là closure là một hàm hoặc một tham chiếu (hay còn gọi là một cái bao đóng) đi kèm với cái môi trường mà nó tham chiếu đến (khá là xoắn). Cái cần nhấn mạnh ở đây là cái referencing environment (môi trường tham chiếu).
(tham khảo từ: http://ktmt.github.io/blog/2013/05/12/closure-va-scope-cua-javascript/)

Block đơn giản không phải GCD. GCD chỉ là đang dùng block ứng dụng vào mà thôi

## Ưu điểm
- Block giúp code của bạn dễ đọc, nhưng đó là với một block xử lý ít dữ liệu và đơn giản, một block quá nhiều thứ phải xử lý hoặc nhiều block lồng nhau chỉ khiến cho app bạn rối nùi lên và tiêu tùng mà thôi.
- Block tốt nhất là giúp dev chúng ta, giảm line, code gọn, dễ bắt bug.
- Obj-C, có hai cách để một đối tượng có thể đưa ra kết quả của mình (callback) 1 là thông qua selector và target, hai là delegate. Một ngày nào đó mình sẽ nói rõ hơn về 2 thứ này, trước mắt mặt định các bạn hiểu chúng là gì đã. Cách sử dụng hai kĩ thuật này vô cùng phức tạp và nhiều bước, đơn giản hơn thì bạn hãy thử dụng block như một cách callback lại request.
- Giống closure, mọi giá trị được khởi tạo trước khi thực hiện callback đều có thể được sử dụng bên trong block.

## Một số chỗ có thể sử dụng block.
- Khi nào bạn cần callback, dùng block.
- Dùng emulator cùng với block để có thể duyệt qua một danh sách, nếu như bạn cảm thấy là việc duyệt danh sách không quan tâm thứ tự.
- Complete handler và failure handler.
- Block trong obj-c có cú pháp kinh hoàng, cho nên, hãy nhớ nếu bạn cần sử dụng một block lặp đi lặp lại nhiều lần, hãy sử dụng cú pháp typedef để định sẵng những block đó.

# Một chút kinh nghiệm về thực hiện một project trong XCode
Tham khảo: https://github.com/futurice/ios-good-practices


(còn tiếp)

