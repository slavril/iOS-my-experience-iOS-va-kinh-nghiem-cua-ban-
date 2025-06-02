# SOLID

Sau những năm tháng làm mobile app với Objective-C và Swift, giờ tôi đã chuyển sang làm việc với React Native. Cảm giác nhanh chóng hơn, nhẹ nhàng hơn. Đôi khi, chúng ta cần sự đổi mới.

React Native (và nhiều ngôn ngữ UI hiện đại khác) không còn đặt nặng tư duy hướng đối tượng (OOP) như trước. Thay vào đó, chúng nhấn mạnh hơn vào Reactive Programming.
Tuy vậy, cá nhân tôi vẫn tin rằng: kết hợp cả hai tư duy — OOP và Reactive sẽ mang lại giải pháp tốt hơn.


Hôm nay, tôi chia sẻ ngắn gọn về SOLID, một bộ nguyên tắc thiết kế trong OOP mà bạn nên luôn mang theo bên tôi, hoặc có thể bạn đã áp dụng từ lâu nhưng chưa gọi tên nó mà thôi.

Và với sự trợ giúp của một số AI free đỉnh kao nhất hiện nay

Bắt đầu nhé

===>

## SOLID là một tập hợp 5 nguyên tắc thiết kế phần mềm trong lập trình hướng đối tượng (OOP) được đề xuất bởi Robert C. Martin (Uncle Bob). https://en.wikipedia.org/wiki/Robert_C._Martin

Mục tiêu của SOLID là giúp viết code:

- Dễ hiểu (dĩ nhiên rồi)

- Dễ mở rộng

- Dễ bảo trì

- Ít lỗi khi thay đổi

Nó chia làm 5 chữ cái S - O - L - I - D, mỗi chữ cái đại diện cho nguyên tắc chữ viết tắt đầu nguyên tắc của nó

S – Single Responsibility Principle (Nguyên tắc trách nhiệm duy nhất)
O – Open/Closed Principle (Nguyên tắc mở rộng – đóng)
L – Liskov Substitution Principle (Nguyên tắc thay thế Liskov)
I – Interface Segregation Principle (Nguyên tắc phân tách giao diện)
D – Dependency Inversion Principle (Nguyên tắc đảo ngược phụ thuộc)

Nghe phức tạp quá .......

## S – Single Responsibility Principle (Nguyên tắc trách nhiệm duy nhất)
Nó có nghĩa là một class chỉ nên có một lý do để thay đổi, tức là nó chỉ nên đảm nhận một nhiệm vụ duy nhất. Ok giả sữ bạn là DEV RN thì class có còn đâu mà, vậy thì hãy áp dụng nó, triệt để nhất có thể, cho Hook, cho Context, cho ... Single ton class :D

_Ví dụ ông bạn có ```Class Camera```, thì chỉ nên chụp ảnh quay clip, chớ nên chiên khoai tây trong ấy nhé._

## O – Open/Closed Principle (Nguyên tắc mở rộng – đóng)
Code nên mở để mở rộng, nhưng đóng để sửa đổi. Cụ thể là bạn có thể thêm chức năng mới mà không cần sửa code cũ (một trong những nguyên tắc quan trọng nhất của lập trình). Ủa là làm sao ta

_Ví dụ Class Camera của anh bạn cần thêm một tính năng mới, Capture with HDR, nếu ông bạn là dân chụp ảnh, hẳn ông cũng biết rằng chụp HDR chẳng qua chỉ là chỉnh sửa một vài thông số của máy ảnh gốc mà thôi_

_Vậy thì thay vì tạo hàm ```Capture()```, tôi khuyên các bạn nên viết một hàm ```Capture(preset)```, và sau này để thêm capture với HDR, hoặc là capture với Vivid filter, sẽ không phải sửa code class camera nữa mà chỉ cần input với một preset mới_

Chính vì điều này dẫn tới một bài học mới, hãy thiết kế class của bạn, trước khi bắt đầu bắt tay vào viết code, hãy dạy não của mấy bạn kỹ năng hoạch định chiến lược trước khi tham chiến

## L – Liskov Substitution Principle (Nguyên tắc thay thế Liskov)

Các lớp con nên có thể thay thế lớp cha mà không làm thay đổi tính đúng đắn của chương trình. Một trong 5 nguyên tắc khó hiểu bậc nhất của SOLID, tôi sẽ cố dùng hết trí tuệ để giải thích cái nguyên tắc này cho các ông.

Nó có nghĩa nôm na là, tôi đang dùng class Camera với các hàm Capture(preset), TurnFlash(on:off), khi tôi có class con Video được kế thừa từ Camera, tôi cũng phải dùng được các hàm trên mà không được gặp bất kỳ hành vi nào khác với cha, (tôi sẽ gọi TurnFlash(on), nhưng hóa ra, video không thể mở flash on được, đấy chính là sai trái)

Vậy câu hỏi đặt ra là chúng ta phải áp dụng ra sao, quay lại nguyên tắc đóng - mở, mấy ông phải lên kế hoạch chuẩn bị code trước, kỹ lưỡng và chu đáo. Không phải cứ trong giống giống nhau là mấy ông có thể tạo lớp kế thừa lung tung được, nhưng thứ này thực sự cần kinh nghiệm rất nhiều để nghiệm ra được, bản thân tôi còn toàn hay gặp lỗi này.

Có một trick cho mấy ông nếu muốn biết liệu mình có vi phạm LSP không, đó là khi mấy ông Override lại một yếu tố của cha, thì nhiều khả năng là mấy ông đã vi phạm LSP, ví dụ sau thuộc về chatGPT

```
class Bird {
  fly() {
    console.log("I can fly!");
  }
}

class Penguin extends Bird {
  fly() {
    throw new Error("Penguins can't fly!");
  }
}

function letItFly(bird: Bird) {
  bird.fly();
}

letItFly(new Penguin()); // ❌ Crash! Penguins can't fly!```
```
Đoạn code trên vẫn chính xác, các ông chạy vẫn tốt, nhưng hãy nhìn đoạn dưới và thấy nó rõ ràng như thế nào 

```
interface Flyable {
  fly(): void;
}

class Sparrow implements Flyable {
  fly() {
    console.log("Sparrow flying...");
  }
}

class Penguin {
  swim() {
    console.log("Penguin swimming...");
  }
}

// Hàm này chỉ chấp nhận loài biết bay
function letItFly(bird: Flyable) {
  bird.fly();
}

letItFly(new Sparrow()); // ✅ OK
// letItFly(new Penguin()); // ❌ TypeScript không cho vì Penguin không implements Flyable
```
_LSP sẽ giúp code mấy ông siêu rõ ràng, dễ sửa hơn rất là nhiều luôn, và những DEV tới sau sẽ không phải khổ sở ngồi đọc rồi lọ mọ chỉnh này nọ chi cho cực nữa_

Một ví dụ khác về button trong RN, DEV lỏ sẽ cố gắng làm như vầy để disable button

```
// Base class
type ButtonProps = {
  label: string;
  onPress: () => void;
};

const BaseButton = ({ label, onPress }: ButtonProps) => (
  <TouchableOpacity onPress={onPress}>
    <Text>{label}</Text>
  </TouchableOpacity>
);

// Subclass
const DisabledButton = ({ label }: { label: string }) => (
  <TouchableOpacity onPress={() => { throw new Error("Disabled!") }}>
    <Text style={{ color: 'gray' }}>{label}</Text>
  </TouchableOpacity>
);

// App
const App = () => (
  <>
    <BaseButton label="Submit" onPress={() => alert('Submitted')} />
    <DisabledButton label="Can't click" />
  </>
);
```
Nhưng DEV pro sẽ làm như bên dưới
```
type BaseButtonProps = {
  label: string;
};

type ClickableButtonProps = BaseButtonProps & {
  onPress: () => void;
};

const ClickableButton = ({ label, onPress }: ClickableButtonProps) => (
  <TouchableOpacity onPress={onPress} style={{ backgroundColor: 'blue' }}>
    <Text style={{ color: 'white' }}>{label}</Text>
  </TouchableOpacity>
);

const DisabledButton = ({ label }: BaseButtonProps) => (
  <View style={{ backgroundColor: 'gray', opacity: 0.5 }}>
    <Text style={{ color: 'white' }}>{label}</Text>
  </View>
);

// App
const App = () => (
  <>
    <ClickableButton label="Submit" onPress={() => alert('Submitted')} />
    <DisabledButton label="Disabled" />
  </>
);
```
Tôi rất hay bị mắc cái lỗi này, và tôi nhận ra rằng, mặc dù code vẫn chạy nhưng nhìn cứ newbie kiểu gì, hãy pro lên hơn nhé, bởi nếu các ông mắc phải, chắc chắn ông sẽ IF ELSE tới tận cùng thế giới đấy ....

## I – Interface Segregation Principle (Nguyên tắc phân tách giao diện)

Không nên ép class phải implement những interface mà nó không cần, quá dễ hiểu, đừng bắt con bạn phải đi hoặc chạy khi nó mới 6 tháng .....

_Các ông nên tách Interface ra càng chuyên biệt càng tốt, có nghĩa rằng, ví dụ như ```Class Camera``` làm ơn đừng có thuộc tính ```hasTripod``` ở trỏng_

## D – Dependency Inversion Principle (Nguyên tắc đảo ngược phụ thuộc)

Nôm na là hãy độc lập, đừng cố phụ thuộc vào ai các ông nhé

Ví dụ bên dưới

```
interface AuthProvider {
  login(username: string, password: string): Promise<boolean>;
}

// Firebase
class FirebaseAuth implements AuthProvider {
  async login(username: string, password: string) {
    console.log("Logging in with Firebase...");
    return true;
  }
}

// Service
class AuthService {
  constructor(private provider: AuthProvider) {}

  async signIn(user: string, pass: string) {
    return this.provider.login(user, pass);
  }
}
```
_Giờ mấy ông muốn sửa Provider thành AWS hay Azure thay vì Firebase thì cũng sẽ chả ảnh hưởng gì code của AuthService cả, hiểu chứ_

### Kết bài, mặc dù SOLID nó cực hiệu quả trong quản lý code, nhưng nhiều ông sẽ không thấy ngay lợi ích nó mang lại ngay trước mắt, một thời gian sau, khi code dài ra cả chục ngàn dòng, các ông sẽ thấm ngay. Nhưng tôi khuyên thật lòng, đừng bao giờ cố áp dụng triệt để SOLID, thứ nhất không phải khi nào cũng áp dụng tối đa được, thứ 2 áp dụng tối đa sẽ bào mòn sự sáng tạo của các ông đấy, hãy thử và trải nghiệm, tự đúc kết ra trước đã, chúc các ông thành công.

===



