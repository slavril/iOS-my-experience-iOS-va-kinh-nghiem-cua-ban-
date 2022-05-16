Series giới thiệu về phát triển phầm mềm trên iOS, ngắn gọn và bằng tiếng việt.

Hôm nay mình sẽ giới thiệu về iOS app life cycle.

## App Delegate

Do Apple hướng kiến trúc chủ đạo của app iOS theo delegate pattern, cho nên vòng đời của một app iOS sẽ được hệ thống phản hồi tới chương trình của chúng ta dựa trên các hàm được thể hiện trong file app delegate (Scene-Based Life-Cycle Events) hoặc (App-Based Life-Cycle Events), và toàn bộ các sự kiện sẽ được UIKit phản hồi vào đối tượng UIApplicationDelegate hoặc UISceneDelegate (dành cho ios 13 trở về sau hoặc app dạng Scene-Based).

Một vòng đời của cái app sẽ tuân theo thứ tự này

(Scene-Based Life-Cycle Events)

![Scene-Based Life-Cycle Events](https://docs-assets.developer.apple.com/published/c834d5ac04/scene-state@2x.png)


(App-Based Life-Cycle Events)

![App-Based Life-Cycle Events](https://docs-assets.developer.apple.com/published/64a2e0dab8/app-state@2x.png)

>|KHÔNG CHẠY| ->|INACTIVE - Launch screen| -> |ACTIVE|

>|ACTIVE| <-> |INACTIVE| <-> |BACKGROUND| <-> |SUSPENDED|

Cơ bản là các bạn có thể tìm mấy cái hình đẹp hơn trên mạng

### Launch screen

Trong quá trình Launch screen UIKit sẽ phản hồi về 2 method dưới, mục đích chính là để giúp cho bạn có thể biết và chuẩn bị dữ liệu của mình trước khi ứng dụng thực sự khởi chạy. Tìm hiểu thêm tại [đây](https://developer.apple.com/documentation/uikit/app_and_environment/responding_to_the_launch_of_your_app?language=objc).

>application:willFinishLaunchingWithOptions:

>application:didFinishLaunchingWithOptions:

### Active app

Sau khi ứng dụng trải qua quá trình launch, nó sẽ chuyển tiếp qua trạng thái active, đó là khi mà ứng dụng của bạn thực sự hoạt động, đối với lúc này, dựa vào 2 method ở bên trên chúng ta sẽ khởi tạo dữ liệu chuẩn bị giao diện cho ứng dụng khi nó từ hư không lên. Đọc chi tiết ở [đây](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_foreground?language=objc).

Còn khi ứng dụng đang ở trạng thái background  dựa vào các method được cung cấp bởi UIApplicationDelegate hoặc UISceneDelegate, chúng ta vẫn có thể có cơ sở để chuẩn bị cho quá trình này khi ứng dụng được phục hồi qua trạng thái Active thông qua.

> sceneWillEnterForeground: (Scene-Based Life-Cycle Events).

> applicationWillEnterForeground: (App-Based Life-Cycle Events)

Ngay sau đó, hệ thống sẽ sớm chuyển ứng dụng của chúng ta qua trạng thái Active mà chưa hiển thị giao diện người dùng, đây là lúc để mình cấu hình cho các tính chất của giao diện mình muốn hiển thị. Sự kiện này sẽ được phản ánh qua 2 phương thức sau.

>sceneDidBecomeActive:

>applicationDidBecomeActive:

### Background

Là một trạng thái không thể thiếu của vòng đợi một ứng dụng, xuống background là khi ứng dụng của chúng ta không hiển thị giao diện nữa, nhưng cũng không hoàng toàn bị tắt mất trên hệ thống. Đọc chi tiết ở [đây](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background?language=objc).

Đầu tiên UIKit sẽ phản ánh tới 2 phương thức sau, đủ cho bạn thời gian để làm những việc cần trước khi ứng dụng xuống trạng thái background như lưu dữ liệu...

>sceneWillResignActive:

>applicationWillResignActive:

Tiếp theo, UIKit sẽ gọi 2 phương thức sau, sau khi ứng dụng của bạn đã chính thức xuống trạng thái background

>sceneDidEnterBackground:

>applicationDidEnterBackground:

Lưu ý là app ở trạng thái background, có thể vẫn chạy được một số tác vụ, gọi là tác vụ ngầm, trong một khoản thời gian ngắn không xác định, theo mình biết thì nó sẽ phụ thuộc vào tài nguyên còn lại của hệ thống, trước khi chuyển qua trạng thái terminate.

### App inactive 

Là một trạng thái chuyển tiếp trước khi chuyển qua lại giữa Launch - Active - Background, không có phương thức nào thông báo cho trạng thái này.

### App terminate

Sau một thời gian ở background hoặc bị người dùng chủ động tắt, app của bạn sẽ nhanh chóng chuyển qua trạng thái terminate, có nghĩa là nó đã hoàn toàn tắt hẳn, chi tiết [tại](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623111-applicationwillterminate?language=objc). Nó sẽ được thông báo thông qua phương thức.

>applicationWillTerminate:

## Tổng kết

Hệ thống sẽ thông báo ứng dụng của bạn thông qua các phương thức mình đã nói ở trên để giúp cho các lập trình viên có thể chủ động xử lý các sự kiện xảy ra trong suốt vòng đời sử dụng của ứng dụng trên máy, việc này cực kì quan trọng với những lập trình viên iOS nhưng thường bị các bạn bỏ qua ít chú ý. Những nội dung này rất nhiều, nhưng hi vọng, qua bài viết ngày các bạn sẽ có chút kiến thức cơ bản trước khi tìm hiểu sâu hơn.

160522

