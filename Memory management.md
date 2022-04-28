Series giới thiệu về phát triển phầm mềm trên iOS, ngắn gọn và bằng tiếng việt.

## ARC
Automatic Reference Counting hay viết tắt là (ARC), nói ngắn gọn là tính năng quản lí bộ nhớ mà Apple cung cấp cho Xcode, nhằm thay thế cho cách quản lý thủ công bằng tay như trước đây.
Cơ chế hoạt động cơ bản là khi một object/instance (đối tượng) được khởi tạo, nó sẽ được quản lý bởi ARC, mỗi một reference (liên kết) đến đối tượng đó, sẽ được tính là 1 số reference count/retain count. Khi liên kết được huỷ bỏ, reference count giảm xuống 1, cho tới khi nó bằng 0, vùng nhớ dành cho đối tượng đó được huỷ bỏ.

## Memory leak
Cơ chế trên hoạt động hoàn hảo, giúp hệ thống giải phóng bộ nhớ khi không còn liên kết nào tới đối tượng, nhưng đôi khi trong một lúc thần kì nào đó, reference count bị neo giữ ở 1 và không bao giờ về không, tức vẫn còn có liên kết tới đối tượng, nhưng chương trình của bạn, thực tế không còn sử dụng đến nó nữa, chúng ta gọi là reference cycle/retain cycle.
Khi mà hiện tượng reference cycle/retain cycle xảy ra, mình gọi là memory(bộ nhớ) của ứng dụng đã bị leak(rò rỉ). Memory leak rất nghiêm trọng, nhất là với các dự án lớn lượng code nhiều, ứng dụng của bạn sẽ hoạt động kém hiệu quả, tốn nhiều bộ nhớ.

## Funfact
* Objective-C/Swift ARC là compile time hay runtime: compile.
* Closure/block cũng là một reference, nên khi sử dụng phải chú ý, còn cách làm thì bạn hãy GG nó.
* Strong là mạnh, Weak là yếu
* dealloc, là một phương thức được gọi khi mà reference count của một đối tượng về 0.
* Để kiểm tra memory leak, bạn có thể dùng công cụ Xcode Instruments.
