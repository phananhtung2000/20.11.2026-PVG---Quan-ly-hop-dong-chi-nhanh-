# Quản lý hợp đồng — bản PWA (cài đặt được trên Android & Windows)

Bộ file gồm 6 file đặt ngang hàng nhau: `index.html`, `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png`, `icon-512-maskable.png`.
Phần mềm vẫn mở bình thường bằng cách double-click vào `index.html` (file://, offline) như trước — PWA chỉ kích hoạt khi chạy qua http/https.

## Cách deploy lên GitHub Pages

1. Tạo một repo GitHub mới (ví dụ tên repo: `quan-ly-hop-dong`). Mỗi phần mềm một repo riêng, không lồng scope của nhau.
2. Đẩy cả 6 file lên **thư mục gốc của repo** (không đặt trong thư mục con, để các đường dẫn tương đối luôn đúng).
3. Vào repo → **Settings → Pages** → mục *Build and deployment*: Source = **Deploy from a branch**, Branch = **main**, thư mục **/ (root)** → Save.
4. Chờ khoảng 1 phút, app chạy tại `https://<username>.github.io/<ten-repo>/`.
   Vì `start_url` và `scope` trong manifest dùng đường dẫn tương đối (`./`) nên **không cần sửa gì thêm** dù URL cuối cùng là gì.

## Kiểm tra cài đặt

- **Android (Chrome):** mở link → menu 3 chấm → **“Cài đặt ứng dụng”** (hoặc “Thêm vào Màn hình chính”). Sau khi cài, app mở full-screen như app thật, dùng offline được sau lần mở đầu.
- **Windows 11 (Edge/Chrome):** mở link → bấm icon **“Cài đặt”** trên thanh địa chỉ (hoặc menu → Ứng dụng → “Cài đặt trang này như một ứng dụng”). App chạy trong cửa sổ riêng.
- **Kiểm tra nhanh bằng Lighthouse:** F12 → tab **Lighthouse** → chọn **Progressive Web App** → Analyze page load. Đạt các tiêu chí cơ bản: có manifest hợp lệ, có service worker, icon đúng kích thước.

## Cập nhật phiên bản sau này

- Chỉ sửa nội dung `index.html` và tăng số version trong tên cache ở `sw.js` (`quan-ly-hop-dong-cache-v1` → `-v2`).
- **Không đổi** các trường danh tính trong `manifest.json`: `name`, `id`, `start_url`, `scope` và tên file icon. Nếu đổi hình icon, giữ nguyên tên file, chỉ thay nội dung ảnh.

## Khi Chrome Android báo “Ứng dụng này đã được cài đặt rồi” nhưng không mở được

1. Mở app (nếu còn mở được) và **xuất/sao lưu dữ liệu trước**; không bấm các nút đặt lại (Reset).
2. Vào **Cài đặt → Ứng dụng** của điện thoại, gỡ mục tên phần mềm (và portal nếu có).
3. Đóng hẳn Chrome rồi mở lại link, cài lại từ đầu.
4. Nếu Chrome vẫn không hiện “Cài đặt ứng dụng”, kiểm tra lại scope/id: mỗi app phải ở repo riêng (scope là thư mục anh em, không lồng nhau); portal không đặt ở repo gốc `<user>.github.io`.
5. Chỉ khi vẫn kẹt mới xoá dữ liệu Chrome hoặc gỡ Chrome cài lại — cách này xoá luôn dữ liệu localStorage/IndexedDB của mọi app trên domain, nên **bắt buộc sao lưu trước**.

## Ghi chú kỹ thuật

- Dữ liệu nghiệp vụ lưu ở IndexedDB ngay trong trình duyệt; service worker **không** cache hay can thiệp vào dữ liệu — chỉ cache tài nguyên tĩnh (HTML, manifest, icon) để app mở được khi offline.
- Service worker chỉ xoá cache có tiền tố `quan-ly-hop-dong-cache-` của chính app này, không đụng vào cache của app khác cùng domain.
