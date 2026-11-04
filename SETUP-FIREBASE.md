# Bật đồng bộ PC ↔ điện thoại bằng Firebase

Bạn chỉ cần làm một lần, khoảng 10 phút. Gói miễn phí (Spark) là đủ dùng, không cần thẻ thanh toán.

## 1. Tạo project

1. Mở https://console.firebase.google.com rồi bấm **Create a project** (hoặc **Add project**).
2. Đặt tên, ví dụ `app-tieng-trung`. Không cần bật Google Analytics. Bấm **Create project**.

## 2. Đăng ký web app và lấy cấu hình

1. Trong trang **Project Overview**, bấm nút **+ Add app** ở trên cùng, rồi chọn biểu tượng **`</>`** (Web).
2. Đặt tên app tùy ý. **Không** tick ô Firebase Hosting. Bấm **Register app**.
3. Firebase sẽ hiện một đoạn code có `const firebaseConfig = { ... }`. Copy phần `{ ... }` đó.

## 3. Bật đăng nhập Google

1. Ở menu trái, vào **Security → Authentication** (giao diện cũ: **Build → Authentication**), bấm **Get started**.
2. Chọn tab **Sign-in method**, bấm **Google**, bật **Enable**, chọn email hỗ trợ của bạn rồi bấm **Save**.
3. Chọn tab **Settings → Authorized domains**, bấm **Add domain** và thêm:
   `01652066596h-boop.github.io`

## 4. Tạo Firestore và đặt quy tắc bảo mật

1. Ở menu trái, vào **Databases & Storage → Firestore** (giao diện cũ: **Build → Firestore Database**), bấm **Create database**.
2. Chọn location `asia-southeast1` (Singapore), rồi chọn **Start in production mode**.
3. Chọn tab **Rules**, xóa nội dung cũ, dán đoạn dưới đây vào rồi bấm **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

Quy tắc này đảm bảo mỗi tài khoản Google chỉ đọc và ghi được dữ liệu của chính mình.

## 5. Dán cấu hình vào app

Mở `index.html` và tìm dòng:

```js
const FIREBASE_CONFIG=null;
```

Thay `null` bằng đoạn `{ ... }` bạn đã copy ở bước 2, ví dụ:

```js
const FIREBASE_CONFIG={apiKey:"AIza...",authDomain:"app-tieng-trung.firebaseapp.com",projectId:"app-tieng-trung",storageBucket:"app-tieng-trung.firebasestorage.app",messagingSenderId:"123...",appId:"1:123...:web:abc..."};
```

Đoạn cấu hình này có thể để công khai. Bảo mật nằm ở quy tắc của bước 4.

Để chặt hơn, hãy khóa API key: vào https://console.cloud.google.com/apis/credentials, chọn project, rồi bấm vào **Browser key (auto created by Firebase)**:
- **Application restrictions → Websites:** thêm `https://01652066596h-boop.github.io/*` và `https://app-tieng-trung-5681b.firebaseapp.com/*`
- **API restrictions → Restrict key:** chỉ tick **Identity Toolkit API**, **Token Service API** và **Cloud Firestore API**

Sau đó đẩy file lên GitHub:

```bash
git add index.html && git commit -m "Bật đồng bộ Firebase" && git push
```

## 6. Dùng

- Trên PC và điện thoại, mở https://01652066596h-boop.github.io/app-tieng-Trung/ và bấm **Đăng nhập để đồng bộ**. Dùng **cùng một tài khoản Google** trên cả hai máy.
- Ở lần đăng nhập đầu tiên, nếu cả máy và mây đều đã có bài học, app sẽ hỏi bạn muốn **ghép cả hai**, **dùng bản trên mây** hay **dùng bản của máy này**.
- Từ đó, mỗi lần bạn lưu, máy kia sẽ cập nhật sau vài giây. Khi mất mạng, app vẫn lưu trên máy và tự đồng bộ khi có mạng lại.
- Nếu sửa trên hai máy cùng lúc, lần lưu sau cùng sẽ được giữ.

**Lưu ý với iPhone:** nếu bạn mở app từ biểu tượng ngoài màn hình chính mà đăng nhập Google không được, hãy đăng nhập trong Safari trước. Nếu vẫn không được, dùng app trực tiếp trong Safari.
