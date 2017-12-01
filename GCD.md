#GCD ##Về cơ bản Apple mô tả Grand Central Dispatch (GCD) như sau:

Như chúng ta đã biết, xử lý đa luông trong lập trình thực sự rất khó, chính vì vậy, Apple đã tạo ra, và cho rằng Grand Central Dispatch (GCD) sẽ giúp quá trình này trở nên dễ dàng hơn, thông qua các kĩ thuật mới.
GCD hoàn toàn không phải một thư viện hỗ trợ viết xử lý đa luồng hay viết lại kĩ thuật xử lý đa luồng.
GCD có sử dụng luồng trong xử lý của nó, nhưng mà ơn trời, dev không bao giờ cần phải cố mà code như trước.
GCD hỗ trợ chúng ta xử lý đồng thời trong lập trình hướng theo mô hình FIFO.
Cơ chế cơ bản của GCD nôm na dễ hiểu là nó đưa một hoặc nhiều blocks vào hàng đợi, thực hiện các tác vụ rồi trả ra giá trị (callback-block).

GCD hỗ trợ nhiều cách để đưa block vào hàng đợi, có nhiều kiểu hàng đợi khác nhau và phục vụ cho nhiều mục đích khác nhau. Tóm lại, là lập trình viên chỉ cần sắp xếp các tác vụ cần được xử lý đưa vào trong hàng đợi (dispatch), GCD sẽ làm việc của nó, kết quả được trả về và dữ liệu mới được sử dụng cho các bước tiếp.

Điều thú vị ở đây là khi lập trình, chúng ta thấy như nó được xử lý đồng thời, luồng được gọi, xử lý tự động và cân bằng phù hợp với khả năng của hệ thống. Có một lưu ý, là khi xử lý vấn đề về hiển thị, UI nên và phải luôn được thực hiện trên main queue (cái này sẽ nói rõ hơn và phần sau). Đồng thời là bạn phải luôn kiểm tra tài liệu xem Object NS hoặc UI mà đang cần thực hiện có an toàn để xử lý trên đa luồng hay không (coi các phần sau để biết chi tiết hơn).

Cách khởi tạo hoặc gọi ra một hàng đợi. Có 2 loại (serial queues: xếp hàng đợi tới lượt) và (concurrent queues: xếp hàng thích gọi đứa nào thì gọi), hai cái tên nói lên tất cả.

Loại 1 (serial queues): nếu bạn thêm vào một loạt vụ cần thực hiện, mỗi một lúc nó sẽ chỉ lấy ra một cái để xử lý, giống như khi xếp hàng mua KFC vậy, chỉ khi nào tác vụ được xử lý xong, được trả ra và hoàn thành, nó mới lấy một cái khác liền kề ra xử lý tiếp (đứa nào tới trước, xử lý trước, FIFO). Bạn chỉ cần gọi dispatch_queue_create là tạo được một cái kiểu này. (nhiều hàng đợi có thể chuyển qua chuyển lại giữa nhiều thread ngay giữa những task khác nhau, nhưng nó luôn đảm bảo trong cùng 1 hàng đợi, thì thằng trước xong mới tới thằng sau: nôm na là mình tạo ra 10 queue thì khi 10 cái đó chạy, nó chạy trên nhiều thread khác nhau, có thể chỉ có 3 thread thôi, nên 10 queue giành nhau chạy xen kẻ). Kiểu này rất hay, đỡn nhọc công cho chúng ta trong việc xử lý đa luồng với nhau, mình đã từng làm qua xử lý đa luồng trên C++ và đó quả thực là một cực hình. Với GCD, chỉ đơn giản là add vào và viết tiếp, so easy.

Main queue là một cái hàng đợi kiểu serial queues nhưng đặc biệt hơn, những hàng đợi khác có thể nằm trên nhiều thread khác nhau trong một lúc nhưng main queue thì chỉ có một, và nó duy nhất chạy trên main thread (main thread là luồng mạnh nhất và được ưu tiên nhất, apple luôn cho UI chạy trên đó là để tạo trải nghiệm tốt nhất), đó chính là lý do vì sao khi bạn chạy một cái tác vụ nặng, xử lý quá nhiều trên main queue sẽ có thể làm treo UI, nếu theo lý thuyết về serial queues, UI chắc chắn sẽ phải đợi vụ xong thì mới tiếp tục thực hiện các phản hồi từ người dùng được.

Loại thứ 2 (concurrent queues): loại này mắc cười nhất, cũng đơn giản là bỏ task vào queue thôi, nhưng mà khi thực hiện, nếu system thấy thread nào rảnh, là nó sẽ bỏ 1 cái task của nó vào thread xử lý liền. Chính như vậy task phải hoàn toàn được xử lý an toàn (thread safe), đảm bảo tối thiểu ảnh hưởng. mặc dù nó cũng cùng cơ chế FIFO để lấy task ra, nhưng khác với loại 1, lấy thằng nào ra là xử lý liền thằng đó, không quan tâm là thằng trước xong hay chưa.

###Thread safe tức là bạn cố gắng tránh truy suất vào cùng 1 biến ở nhiều thread khác nhau.

Có vài thần chú triệu hồi cơ bản sau dispatch_queue_create // create a serial or concurrent queue dispatch_get_main_queue // get the one and only main queue dispatch_get_global_queue // get one of the global concurrent queues

##Bỏ task vô cái queue Có queue rồi, ở đâu ko quan trọng, giờ bỏ task vô thôi. Có một nguyên tắc cần nhớ, là bạn phải cân nhắc task cần xử lý của bạn mục đích ra sao để sử dụng queue cho phù hợp nhất. Nếu bạn viết một task request từ server một loạt dữ liệu mà dữ liệu này cần cho dữ liệu sau, tức là mang tính tuần tự, tốt nhất nên thực hiện trên serial queues. Còn giả sử, bạn cần load một tấm ảnh với nhiều phần chia nhỏ ra không cần quan tâm thứ tự các mảnh, khuyến khích dùng concurrent queues để tối đa hoá hiệu suất.

Có mấy câu thần chú nữa // Asynchronous functions dispatch_async dispatch_after dispatch_apply // Synchronous functions dispatch_once dispatch_sync

dispatch_async: bỏ task vô queue và trả ra liền luôn, xử lý được liền mạch, không phải đợi task được đưa vào queue xử lý xong mới chạy tiếp. dispatch_after: trả ra liền luôn nhưng mà đợi một chút rồi mới thực hiện task trong queue, kiểu này giống kiểu trên. dispatch_apply: trả ra một lần thôi nhưng mà task được bỏ vào tới mấy lần, kiểu này thông dụng khi bạn cần xử lý lặp đi lặp lại nhiều lần một task nào đó.

dispatch_sync: bỏ task vô nhưng mà đợi task xong thì mới return đúng 1 lần, kiểu này là kiểu xử lý phải đợi task đang đưa vào queue đó xử lý xong mới được chạy tiếp. dispatch_once: bỏ task vô, đúng 1 lần thôi, 1 lần trong suốt đời, rồi đợi xong rồi mới return.

##Vài ghi chú khi muốn sử dụng

không làm task nặng chạy main queue (main thread) nó lock m cái UI đó, nhớ nhé.
tạo một cái serial queue phải có mục đích, đừng tạo tào lao, vì bạn nên nhớ, tạo nhiều serial queue có thể chiếm hết tài nguyên của thiết bị đó.
tạo rồi nhớ đặt cái tên cho dễ hiểu mục đích tạo là gì đặng cho thằng khác vào còn biết.
task sẽ được xứ lý trên concurrent queue phải thread - safe (có nói ở trên, giờ nói lại, thread safe là đảm bảo một cái thread xử lý tác vụ đó với giá trị không bị thay đổi bởi bất kì một xử lý nào ở thread khác, trước khi nó được hoàng thành và trả ra)
sau khi xử lý trên queue xyz nào đó, thì nếu bạn muốn dữ liệu được hiển thị trở lại, nhất thiết phải cập nhật lại UI CHỈ TRÊN MAIN QUEUE.
Chém gió tí, vì sao iPhone không lag còn Android thích lag. Androi có cơ chế để xử lý và cập nhật giao diện trên tất cả các thread các core của mấy, không cần phân biệt thread nào với nhau, cho nên, khi chúng ta có 10 tác vụ chạy trên 8 thread cùng update lên UI, thì với máy thậm chí 4 core đi nữa, nó cũng không thể gánh được 8 thread cùng tranh nhau xử lý, và đôi khi dev android còn cho phép tất cả xử lý của mình cùng chạy một lúc, đó quả thật là một sai lầm. Theo mình tìm hiểu, thì Android không có một cơ chế xử lý như GCD, nhất là JAVA vốn dĩ xử lý luồng bằng tay. Nhưng dev lại quá lười để có thể làm chuyện đó => App android luôn có vấn đề về hiệu suất, dĩ nhiên, có những app rất tốt, được các kĩ sư chăm chút kĩ lưởng hơn nên mượt mà hơn thậm chí đỡ hao pin hơn nữa. iPhone thì khác, một sự kết hợp tuyệt vời giữa obj-c và GCD, vì sao ư, thứ nhất, các xử lý luôn chạy dưới nền, nơi chúng không thể can thiệp vào thread kiểm soát UI, nó làm tăng trài nghiệm người dùng. Bạn sẽ hỏi, bộ chạy nền thì nó không chậm ư, câu trả lời là có chứ, chậm có khi hơn android ấy chứ, nhưng làm sao thấy nó lag được ^.^, một trick rất hay của Apple. Thực ra những điều này rất dài dòng, rảnh mình sẽ nói cho.
