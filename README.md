# My Fish World — Firebase (48 loài cá)

## Cài đặt nhanh
1. Vào https://console.firebase.google.com → tạo project → thêm **Web App** và sao chép cấu hình vào `firebase-config.js` (chỉ file này).
2. **Authentication → Sign-in method → Email/Password → Enable**. Kiểm tra **Settings → Authorized domains** có tên miền bạn chạy (localhost hoặc tên miền triển khai).
3. **Firestore Database → Create database**, vào tab **Rules**, thay toàn bộ nội dung bằng file `firestore.rules`, rồi Publish.
4. Trong VS Code cài **Live Server** → nhấn chuột phải `index.html` → **Open with Live Server**. Hoặc tại thư mục project chạy `python -m http.server 5500` rồi mở `http://localhost:5500`. Không dùng `file://`.
5. Đăng ký / đăng nhập. Trò chơi tự lưu lên Firestore tại `users/{uid}/saves/main`. Nút **Lưu ngay** để gửi lập tức.

## Cơ chế dữ liệu
- Bắt buộc đăng nhập để vào game; lưu riêng theo UID. Mỗi UID có bản local riêng.
- Bản lưu `myfishworld_v2` ở những bản HTML cũ được giữ nguyên. Nếu phát hiện, màn đăng nhập sẽ có lựa chọn **Nhập tiến trình cũ**; không tự ghi đè cloud.
- Nếu cùng một tài khoản có cloud và dữ liệu local chưa đồng bộ, game ưu tiên cloud và cho phép nhập bản cũ bằng thao tác rõ ràng ở màn chọn.
- Lưu local ngay, gom ghi cloud mỗi ~4 giây. Nếu mất mạng, bản local còn đó và hiện trạng thái chưa đồng bộ; khi online tải lại trang sẽ hỏi chọn bản local hay cloud nếu có thay đổi offline.
- Giao diện quản lý tài khoản: nút **Tài khoản** cạnh nút DEV/tiền. Ở đó có thể lưu ngay hoặc đăng xuất.

## Lưu ý
- Firebase config Web có thể xuất hiện công khai; đây không phải bí mật. Bảo vệ dữ liệu bằng Firestore Rules.
- DEV cheat vẫn có trên bản phát triển. Với game online có kinh tế hoặc PvP, phải thực thi phần thưởng, giao dịch trên backend tin cậy; rules hiện tại **không ngăn người chơi sửa save của chính mình**.
- Khi người chơi chơi trên 2 thiết bị đồng thời, một bản có thể ghi đè bản còn lại. Nên chuyển sang transaction/revision hoặc backend khi vận hành nhiều người chơi thật.
- `setDoc` có thể thất bại khi offline, hoặc khi document vượt giới hạn Firestore; thông báo trong game sẽ phản ánh trạng thái lưu thất bại.
- SDK Firebase được lấy từ CDN và yêu cầu Internet.


## Chỉ bật DEV Cheat Menu theo tài khoản

1. Vào Firebase Console → Authentication → Users. Sao chép **User UID** của tài khoản được phép test.
2. Vào Firestore Database → Data → Start collection, tạo collection `dev_accounts`.
3. Đặt Document ID đúng **User UID**; thêm field `enabled`, kiểu **boolean**, giá trị `true`.
4. Vào tab Rules, thay bằng nội dung `firestore.rules` mới và Publish.
5. Tải lại game rồi đăng nhập tài khoản đó. Nút `🛠 DEV` chỉ hiện khi Firestore đọc được quyền. Các tài khoản khác mặc định không có DEV.
6. Thu hồi quyền bằng cách đặt `enabled = false` hoặc xóa document, rồi người chơi tải lại game/đăng nhập lại.

**Lưu ý bảo mật:** Check UI ở client không chống gian lận tuyệt đối. Firestore save hiện do client ghi và có thể bị sửa thủ công qua DevTools. Nếu triển khai kinh tế game online, hãy đưa cập nhật tài nguyên, giao dịch, vật phẩm qua backend/Cloud Functions và xác thực quyền server-side.
# my-fish-word
