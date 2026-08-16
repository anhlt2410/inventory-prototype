# CLAUDE.md — inventory-prototype

## Project là gì

Prototype demo (KHÔNG phải production) cho hệ thống chuỗi cung ứng bếp ăn trường học:
Kho tổng nhập nguyên liệu → phân bổ thực đơn cho bếp các trường → bếp báo cáo quy trình chế biến (kèm ảnh) → phụ huynh theo dõi & truy xuất nguồn gốc.

Mục đích duy nhất: **demo luồng nghiệp vụ cho khách hàng/stakeholder**. Ưu tiên tốc độ sửa đổi và độ mượt khi demo, không ưu tiên kiến trúc.

## Nguyên tắc bất di bất dịch

- **Static HTML thuần** — không framework, không build step, không package.json. Mỗi app là 1 file HTML tự chứa (CSS + JS inline). Giữ nguyên như vậy trừ khi được yêu cầu migrate.
- **KHÔNG validation** — mọi nút bấm đều phải chạy, mọi form nhập gì cũng qua, login nhập gì cũng vào. Đây là chủ ý, không phải bug. Không tự thêm required, regex check, error state.
- **Không thêm dependency/CDN mới** ngoài Google Fonts (Be Vietnam Pro) đang dùng.
- Mọi action ghi/xoá/sửa chỉ cần feedback bằng `toast(...)`, không cần persist. Data reset khi reload là chấp nhận được.
- Ngôn ngữ UI: **tiếng Việt**, có dấu đầy đủ. Copy ngắn, động từ chủ động ("Tạo bếp", "Gửi báo cáo").

## Cấu trúc file

| File | App | Ghi chú |
|---|---|---|
| `index.html` | Landing | 3 card mở 3 demo |
| `admin.html` | Quản trị kho (desktop) | Sidebar nav, tables, modals |
| `kitchen.html` | App Bếp (mobile, private) | Phone frame, UI cho người lớn tuổi |
| `parent.html` | App Phụ huynh (mobile, public) | Phone frame, UI hiện đại |

Không có file JS/CSS riêng. Không tạo thêm thư mục `src/`, `assets/` trừ khi cần ảnh thật.

## Design tokens (giữ đúng identity từng app)

- **Chung:** font `'Be Vietnam Pro'`, mock ảnh = div `.photo` gradient + emoji 📷.
- **admin.html:** nền `#F4F6F2`, sidebar `#1C3327`, accent xanh lá `--green:#2F7A4D`. Phong cách: gọn, dày thông tin, không thao tác thừa.
- **kitchen.html:** accent cam `--orange:#E86A33`. **Ràng buộc accessibility:** body text ≥16px, label/nút ≥17px, nút cao ≥56px, tối đa 3 tab bottom-nav. Không thêm hiệu ứng cầu kỳ.
- **parent.html:** accent teal `--teal:#0FA48A`, hero gradient `linear-gradient(160deg, #0FA48A, #0B7A67)`, card bo 20px. Được phép "đẹp" hơn: shadow nhẹ, chip, timeline.
- **Phone frame** (kitchen/parent): pattern `@media(min-width:480px)` bọc bezel; trên mobile thật thì full-screen. Giữ nguyên pattern này cho screen mới.

## Patterns trong code

- Điều hướng: các `div.screen`/`div.page` toggle class `.on`, hàm `show(id)` / `go(page)` / `tab(name)`. Screen mới → thêm div + nút nav, theo đúng pattern sẵn có.
- Mock data: const array ở đầu `<script>` (`MENU`, `SOURCES`, `KITCHENS`, `PARENTS`, `STEPS`, `NOTIFS`) + hàm `renderX()` đổ HTML bằng template literal. Sửa data ở đây, đừng hardcode trong markup nếu đã có render function.
- Import Excel: `fileImport(msg)` mở file picker rồi toast — không parse file thật. Giữ nguyên trừ khi được yêu cầu parse thật (khi đó mới cân nhắc SheetJS).
- Chụp ảnh (kitchen): `<input type="file" accept="image/*" capture="environment">` → thumbnail bằng `URL.createObjectURL`. Đã hoạt động trên mobile thật, đừng thay bằng getUserMedia.
- Quy trình 5 bước chuẩn (dùng thống nhất cả 3 app): Nhập nguyên liệu → Sơ chế → Chế biến → Chia khẩu phần → Lưu mẫu & hoàn tất.

## Mock data conventions

- Trường học Hà Nội (TH Đoàn Thị Điểm là bếp mặc định, 1.250 suất/ngày).
- Nhà cung cấp thật, dễ tin: CP, Ba Huân, Lộc Trời, Vinamilk, Minh Phú, HTX Vân Nội...
- Link truy xuất: `https://truyxuat.vn/<slug>` (fake domain, chỉ toast khi bấm).
- SĐT phụ huynh = tên đăng nhập, format `0912 345 678`. Ngày trong tuần 17–21/08/2026.
- Khi thêm data mới, giữ tính nhất quán chéo giữa 3 app (cùng thực đơn, cùng bước quy trình, cùng nguyên liệu).

## Workflow

- Sửa đổi: **scoped edit trực tiếp vào file liên quan**, không rewrite cả file, không refactor lan sang app khác.
- Test: mở file trực tiếp trong browser hoặc `npx serve .`. Với kitchen/parent, kiểm tra cả viewport 380px.
- Deploy: Vercel auto-deploy từ `main` (framework preset: Other, không build). Push là live.
- Commit message: tiếng Anh ngắn, prefix theo app — `admin:`, `kitchen:`, `parent:`, `landing:`.

## Những việc KHÔNG tự ý làm

- Không thêm backend/API/localStorage (localStorage cũng không — data in-memory là đủ cho demo).
- Không đổi palette/tokens của app này sang app khác.
- Không thêm framework, bundler, TypeScript.
- Không thêm validation hay error handling "cho chắc".
- Không đổi text tiếng Việt sang tiếng Anh.
