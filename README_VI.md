# Palletizer Alarm — Android

Ứng dụng Android dùng camera sau của điện thoại để đọc chữ trên màn hình HMI máy palletizer BUSSE/SJI V4004D bằng Google ML Kit OCR. Khi thấy `ERROR`, `FAULT`, `ALARM`, một số cụm thông báo lỗi, hoặc mã lỗi đi kèm ngữ cảnh lỗi, ứng dụng phát chuông lặp liên tục đến khi người vận hành xác nhận.

## Tính năng
- Camera preview trực tiếp.
- Nhận dạng chữ OCR trên HMI khoảng 2 lần/giây.
- Tìm các từ ERROR / FAULT / ALARM và thông báo lỗi thường gặp.
- Chuông lặp liên tục cho đến khi bấm **XÁC NHẬN / TẮT CHUÔNG**.
- Sau khi xác nhận, không báo lặp lại cùng lỗi đang còn trên màn hình; khi nội dung lỗi biến mất ổn định khoảng 4 giây, hệ thống được phép báo lỗi mới.
- Hiển thị phần chữ OCR đọc được để người vận hành kiểm tra.

## Tạo APK bằng Android Studio
Yêu cầu: máy tính Windows/macOS/Linux, Android Studio, JDK đi kèm Android Studio và kết nối Internet lần đầu để Gradle tải thư viện.

1. Giải nén `PalletizerAlarmApp_Source.zip`.
2. Mở Android Studio → **Open** → chọn thư mục `PalletizerAlarmApp` (thư mục có `settings.gradle.kts`).
3. Chờ Gradle Sync hoàn tất. Nếu hỏi phiên bản Android SDK, cài Android SDK Platform 35.
4. Kết nối điện thoại Android qua USB hoặc bật cấu hình build; điện thoại không bắt buộc để build.
5. Chọn **Build → Build Bundle(s) / APK(s) → Build APK(s)**.
6. Khi build thành công, bấm **locate** trong thông báo Build. Thông thường file nằm tại `app/build/outputs/apk/debug/app-debug.apk`.
7. Chép APK vào điện thoại, mở file để cài. Có thể cần cho phép cài ứng dụng từ nguồn này trong phần cài đặt Android.

## Sử dụng
1. Cố định điện thoại ở vị trí an toàn, hướng camera vào vùng hiển thị chữ và mã lỗi trên HMI.
2. Tránh phản chiếu, rung, ánh sáng chói; để chữ đủ lớn và rõ.
3. Mở app, cấp quyền Camera, bấm **BẮT ĐẦU GIÁM SÁT**.
4. Khi có ERROR/FAULT, app sẽ phát chuông lặp.
5. Người vận hành kiểm tra lỗi thật trên HMI, làm theo quy trình xử lý của nhà máy, rồi bấm **XÁC NHẬN / TẮT CHUÔNG**.

## Tùy chỉnh nhận diện
Các từ khóa nằm trong hàm `looksLikeFault()` tại `MainActivity.kt`. Có thể thêm cụm từ đúng như trên màn hình máy. Để nhận diện mã lỗi cụ thể không kèm từ ERROR/FAULT, cần biết chính xác định dạng hiển thị trên HMI; tránh coi mọi số 3 chữ số là lỗi vì có thể gây báo giả.

## Giới hạn và an toàn
- Đây là cảnh báo phụ bằng camera/OCR, không phải kết nối trực tiếp PLC/HMI và không đảm bảo phát hiện mọi lỗi.
- OCR có thể đọc sai hoặc bỏ sót do góc quay, độ sáng, font, ngôn ngữ, màn hình chuyển nhanh hoặc độ phân giải.
- Không thay thế còi/đèn báo lỗi chính thức, interlock hoặc quy trình an toàn. Hãy thử nghiệm với các lỗi thực tế trước khi dùng trong ca sản xuất.
- Giữ điện thoại được cấp nguồn, cố định chắc chắn và tránh vùng nguy hiểm. Không vào vùng máy đang chạy để điều chỉnh camera.


## Tự động tạo APK bằng GitHub Actions (không cần Android Studio)

1. Tạo repository mới trên GitHub, ví dụ `PalletizerAlarm`. Có thể để **Private**.
2. Giải nén mã nguồn, mở repository trên GitHub → **Add file → Upload files**, tải toàn bộ nội dung thư mục dự án lên, bao gồm thư mục ẩn `.github/workflows/build-apk.yml`. Đảm bảo `settings.gradle.kts` nằm ngay ở thư mục gốc repository.
3. Commit các file lên nhánh `main`.
4. Mở tab **Actions** của repository. Chọn workflow **Build Palletizer Alarm APK**.
5. Bấm **Run workflow** (hoặc đợi workflow tự chạy sau khi push).
6. Khi workflow có dấu xanh, mở lần chạy đó và tải artifact `PalletizerAlarm-debug-apk.zip` trong phần **Artifacts**.
7. Giải nén artifact để lấy `app-debug.apk`, sau đó chuyển APK sang điện thoại Android để cài.

Nếu GitHub không hiện workflow, kiểm tra đã upload đúng đường dẫn `.github/workflows/build-apk.yml` và bật Actions trong repository. File APK debug chỉ phù hợp thử nghiệm; nếu phân phối rộng, cần tạo bản release đã ký.
