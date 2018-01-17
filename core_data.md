# Core data

Coredata là một framework, nó cung cấp cho chúng ta một cách xử lý, thiết kế dữ liệu trực quan hơn, mạnh mẽ hơn, theo như Apple chém thì coredata sẽ giúp bạn giảm 70% số lượng code để viết query vào database theo bất kì công nghệ nào (XML-SQLite-binary) pla pla pla. Nó có rất nhiều tính năng, nói cả ngày không hết đâu, nên thôi đi vào phần dưới.

Core data stack là một tập hợp của những object trong app mà có thể nó sẽ map với những gì chúng ta lưu trong cơ sở dữ liệu. Core data stack sẽ xử lý tất cả các tuy vấn, xử lí dữ liệu ra và vào cơ sở dữ liệu của thiết bị chúng ta. một stack sẽ bao gồm 3 object chính: managed object context (NSManagedObjectContext) hay gọi dân gian là bối cảnh (nghe cứ quái sao ấy, để bạn tìm tên khác), the persistent store coordinator (NSPersistentStoreCoordinator) hay còn gọi là một người điều phối dữ liệu, trong khái niệm về CSDL thì nó chính là PORT I/O, và NSManagedObjectModel hay nôm na là đối tượng chúng ta xử lý.

Cách khởi tạo coi https://developer.apple.com/library/content///documentation/Cocoa/Conceptual/CoreData/InitializingtheCoreDataStack.html#//apple_ref/doc/uid/TP40001075-CH4-SW1

Giờ mình sẽ mô tả cụ thể từng cái trong stack nó làm gì. Trước tiên, bạn nên đặt suy nghĩ của mình như một nhà quản lý và tư vấn ngành hậu cần logistic.

## Đầu tiên là NSManagedObjectModel 

cơ bản thì NSManagedObjectModel nó sẽ mô tả nên cái dữ liệu mà chúng ta sẽ truy suất thông qua CoredataStack (có thể nói nó gần giống với database table), thường được gọi với cái tên trìu mến là “mom” (má). khi chúng ta khởi tạo một cái core data stack (coi như là một phiên xủ lý đi, dịch khó quá), core data sẽ load tất cả NSManagedObjectModel lên ram - memory, âm thầm tải lên tất cả dữ liệu của bạn dưới dạng một bảng copy, tăng hiệu suât đọc (cái này liên quan tới context nữa, nói ở sau nhé).

Đây chính là một ưu thế đã nói ở trên của core data, khi mà model được khởi tạo và load lên, thì ngay lập tức (store coordinator) cũng sẽ nhanh chóng được cấu trúc và khởi tạo tương ứng.

## Mấy đứa điều phối (manager class - NSPersistentStoreCoorinator, database managerment port I/O) 

Trước tiên chúng ta phải làm quen với khái niệm PersistentStore, theo như mình hiểu thì PersistentStore giống như cái kho vậy (trong một hệ QTCSDL thì nó chính là store, một bộ lưu trữ vật lý, có định dạng), trong nó sẽ định nghĩa ra cách mà hệ thống của chúng ta sắp xếp cấu trúc dữ liệu. Hiện apple core data hỗ trợ 4 kiểu cấu trúc dữ liệu SQlite, Binary (lưu dạng bit byte ấy, file thư viện cũng hay là dạng này), XML và In-Memory (chắc là kiểu cấu trúc dữ liệu lưu trên mem). Vậy hiểu ha, tuỳ vào cấu trúc app mà chúng ta lưu dữ liệu theo nhiều dạng khác nhau, lưu trong mem hay lưu ổ cứng… pla pla Chúng ta có Model cấu trúc dữ liệu (giống như một cái kệ có gắng nhãn), thì NSPersistentStoreCoorinator sẽ làm nhiệm vụ đứng trước cửa, bước vào kho, lấy những tấm nhãn mô tả của những món hàng đưa cho chúng ta, chúng ta đặt lên kệ theo đúng ý, hoặc là đem hàng từ kệ đi cất lại vô kho. đồng thời có khi nó sẽ giúp chung ta kiểm tra lại dữ liệu truyền vào trên kệ có hợp với hàng trong kho hay không, tránh kệ để đậu xanh mà lưu thành đậu đen. Khác với kiểu lưu trữ dữ liệu truyền thống, muốn lưu dữ liệu vào DB, bạn phải open port, lúc open port bạn phải lock port lại không cho đứa nào làm nữa hết, sau đó, bạn phải điên lên, vì dữ liệu truyền vào lỗi, ôi mai gót … và Job sinh ra NSPersistentStoreCoorinator để giúp chúng ta đỡ vất vả.

## NSManagedObjectContext 

Giả sử, chúng ta lấy hàng, đặt lên kệ, rồi lại bán ra, rồi đặt lại hàng khác lên kệ, rồi lại đem vào cất kho. mọi việc đó cần xử lý trong một lối riêng lẽ. giả sử có rất nhiều người cùng vào nhà kho, lấy hàng ra, đặt trên các kệ khác nhau nhưng lại giống y về cách bố trí, vậy thì mỗi lối người đó đi vào, lấy hàng đi ra, có thể gọi là một bối cảnh NSManagedObjectContext.

Tuần tự, khi chúng ta lấy dữ liệu từ PersistentStore ra, hoặc chúng ta lưu một dữ liệu mới vào Model, thì mặc định chúng vẫn tạm thời chỉ là một bản sao của một dữ liệu thực sự (giống như nó chỉ là cái nhãn của một món hàng vậy). chúng ta có thể thay đổi giá trị của cái dữ liệu đó trên model (thay đổi nhãn trên kệ tuỳ thích) nhưng nó sẽ không ảnh hưởng tới dữ liệu trong đĩa (trong kho_ của bạn. để có thể tương tác được vào đĩa (hoặc kho) chúng ta cần đặt một request cho NSManagedObjectContext hiện tại đang xử lý dữ liệu đó, sau đó NSManagedObjectContext này sẽ đem giá trị mới cho NSPersistentStoreCoorinator và NSPersistentStoreCoorinator sẽ điều phối lưu vào kho dữ liệu. mỗi Model sẽ phải đăng kí với một NSManagedObjectContext, có thể nhiều model cùng đăng kí với một NSManagedObjectContext, khi thay đổi giá trị model, NSManagedObjectContext sẽ chịu trách nhiệm đối chiếu giá trị mới với những model khác có chung liên kết với model đang bị đổi, để cập nhật lại giá trị cho đúng nhất đồng thời đảm bảo giá trị bạn thay đổi, và phù hợp với tất cả những model bị ảnh hưởng khác. Đây là điểm khác biệt mạnh nhất của của core data so với việc truy vấn trực tiếp thông qua SQL query thuần tuý. giống như mình đã nói ở trên, Coredata sẽ tự động tạo ra một bảng copy được lưu riêng để dành cho việc truy xuất dữ liệu, không như query thuần tuý, NSManagedObjectContext cho phép chúng ta truy cập dữ liệu chéo liên tục thay vì đóng mở I/O như của SQL query

không có coredata, bạn có thể tự viết cho mình chương trình để quản lý dữ liệu cũng được, nhưng chắc chắn nó sẽ cực thấy m, vì sao, vì bạn sẽ phải tự quản lý tất cả, pla pla pla.

## Các lưu ý.

muốn lưu dữ liệu của 1 model vào store, hãy chắn chắn model đó đang được xử lý trên NSManagedObjectContext của chính nó.
NSManagedObjectContext có thể chạy trên nhiều luồng xử lý khác nhau, nên có thể, việc lưu data trên model dựa trên nhiều NSManagedObjectContext khác nhau có thể phát sinh lỗi không mong muốn.

## So sánh một chút tổng quan về việc sử dụng coredata và sql query

Coredata có tốc độ truy suất nhanh hơn nhất là với những bảng đòi hỏi truy suất dữ liệu liên tục.
Voredata và sql query đều dễ dàng viết các câu lệnh truy vấn tới CSDL, tuy nhiên, coredata hỗ trợ truy vấn csdl theo dạng truy vấn một object, tức là bảng.thuộc tính hoặc là bảng.bảng.thuộc tính.
Sql query có thể viết các procedure để hỗ trợ truy vấn, hoặc trigger để hỗ trợ quản lý db
Cả sql query và coredata đều có thể dùng trên các hệ thống app từ lớn đến nhỏ, nhưng nên nhớ, việc nâng cấp một hệ thống dùng sql query đòi hỏi nhiều công sức hơn, vì phải transfer dữ liệu từ mộ version cũ lên bảng mới hơn. với coredata, việc đó là không cần thiết nữa.
