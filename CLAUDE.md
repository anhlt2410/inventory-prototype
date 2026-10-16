# CLAUDE.md — inventory-prototype

## Project là gì

Prototype demo (KHÔNG phải production) cho hệ thống chuỗi cung ứng bếp ăn trường học:
Kho tổng nhập thực phẩm → phân bổ thực đơn cho bếp các trường → bếp báo cáo quy trình chế biến (kèm ảnh) → phụ huynh theo dõi & truy xuất nguồn gốc.

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
- Hằng số dùng chung trong `admin.html` — **đừng hard-code lặp**: `AGE_GROUPS` (10 nhóm tuổi), `MEAL_SLOTS` (Sáng/Trưa/Xế/Phụ), `WEEKDAYS` (khớp `Date.getDay()`, 0 = CN), `ORDER_STATUS` (8 trạng thái đơn), `TODAY_DEMO` ("hôm nay" của bản demo = `2026-08-21`).
- `data-page="warehouses"` là **danh sách kho** (master, M-5); `data-page="warehouse"` là **tồn kho của 1 kho** (chi tiết, mở qua nút "Xem tồn kho", không nằm trên nav). Đừng nhầm 2 cái.
- `data-page="stdcat"` (Danh mục thực phẩm chuẩn) **chỉ đọc**, phân trang 10/trang (`PER.stdcat=10`).
- Fixtures phần C: `FACILITIES`, `WAREHOUSES`, `STD_CATEGORIES` (chỉ đọc); khâu `PROCESS` thêm field `code` + `note`.
- Fixtures phần D: `EMPLOYEES` (~20, field `roles[]` + `ksk`/`attp`/`transport`), `WORKFLOWS` (~6, `stages[]` = mảng mã khâu KSX có thứ tự), `BATCHES` (~20, QR `truyxuat.vn/lo/<mã>`). `DISHES` được làm giàu thêm `workflow`/`recipe`/`desc`/`photos` (menu chỉ dùng code/name/slot/ages).
- Màn phức tạp phần D: `employees` (khối giấy tờ KSK/ATTP/Vận chuyển hiện theo vai trò tick), `workflows` (kéo-thả khâu: HTML5 DnD `wfDrag*` + nút ▲▼ `wfMove`), `dishes`/`batches` (**drawer 2 bước** dùng `stepBar()`/`stepGo()`/`dstep`, footer `Tiếp tục`/`Quay lại`).
- Drawer 2 bước: section `.dstep[data-step]`, thanh `.stepbar`, footer đổi theo bước qua `*FootRender`. Dòng động (công thức/khâu) + ảnh minh chứng dùng `photoGrid()`/`addPhotoTo()`.
- Lịch thực đơn: UI chọn **thứ + khoảng ngày**, `genMenuDates(from,to,wd)` sinh ra danh sách **ngày tuyệt đối** `dd/MM/yyyy`. Mock chỉ lưu `wd/from/to`, số ngày suy ra khi render để không bị lệch.
- Quy trình 6 bước chuẩn theo quy trình chế biến thực tế (dùng thống nhất cả 3 app): Giao nhận thực phẩm (06h00–06h45) → Sơ chế - rửa thực phẩm (06h45–07h30) → Chế biến thực phẩm (07h45–09h45) → Lưu mẫu - chia ăn - bảo quản - vận chuyển (09h45–10h45) → Giao nhận suất ăn (10h45–11h00) → Giao nhận, chia quả chiều (13h00–13h30). Icon thống nhất: 📦 🔪 🍲 🍱 🍽️ 🍎.

## Thuật ngữ (chuẩn HanoiCheck — dùng thống nhất cả 4 file)

- **thực phẩm** (KHÔNG dùng "nguyên liệu") · **nhà cung ứng phụ** / **NCC phụ** (KHÔNG dùng "nhà cung cấp") · **khâu sản xuất** (cho bước quy trình ở màn admin).
- Nhãn menu admin (18 mục / 5 cụm): Quản lý tài khoản · Quản lý nhân sự · Quản lý NCC phụ · Quản lý kho · Quản lý cơ sở sản xuất · Quản lý khâu sản xuất · Quản lý quy trình sản xuất · Danh mục thực phẩm chuẩn · Quản lý thực phẩm · Quản lý món ăn · Quản lý thực đơn · QL lô nhập hàng · Quản lý đơn · Danh sách trường · Báo cáo · Thông báo · Tài khoản PHHS.
- Trạng thái đơn: 8 nhãn, khai báo ở `ORDER_STATUS` trong `admin.html` — Chờ xác nhận · Từ chối · Đang chuẩn bị · Đang giao · Đã giao · Giao hàng thành công · Trả hàng · Hủy. Thêm/đổi trạng thái thì sửa hằng số, đừng hard-code vào markup.
- Tên biến/field trong code giữ nguyên tiếng Anh cũ (`SOURCES`, `SUPPLIERS`, `supplier`, `dist`) — chỉ đổi **nhãn hiển thị**.

- **Phần E — mở rộng 5 màn đang chạy (giữ luồng cũ, thêm field mock qua `enrichPhaseE()` chạy 1 lần trước render đầu):**
  - `source` (M-11): +15 field (`code`/`catStd`/`workflow`/`gram`/`wastePct`/`wasteG`/`stdCode`/`gtin`/`originCountry`/`kind`/`shelfDays`/`schools`/`supplyStatus`/`desc`), **5 bộ lọc**. Danh mục chuẩn **disabled khi sửa**; hệ số thải bỏ g tự tính từ %.
  - `order` (M-12): **6 bộ lọc**, nút **⚡ Xử lý nhanh** (`openQuickProcess` → đổi 1 trong 8 trạng thái + người giao + ghi chú xử lý + ảnh); chi tiết hiện Loại đơn/Người giao/Ghi chú xử lý.
  - `supplier` (M-13): +`code`/`taxId`/`cats[]`/`docs[]`/`contract`; **danh sách giấy tờ động** (`supfAddDoc`/`supfDelDoc`); form giờ lưu thẳng vào `SUPPLIERS`.
  - `kitchen` (M-14): cột **Mã trường** + Tổng suất/ngày; form +Mã/Năm học/Tổng suất + nút **Cấu hình công suất theo nhóm tuổi** (`openCapacityConfig` → `k.ageCap`).
  - `users` (M-15): +**Gắn hồ sơ nhân sự** (datalist `NV-xxx - Tên (Vai trò)`) + Địa chỉ.

## Mock data conventions

- Trường học Hà Nội (TH Đoàn Thị Điểm là bếp mặc định, 1.250 suất/ngày).
- Nhà cung ứng phụ thật, dễ tin: CP, Ba Huân, Lộc Trời, Vinamilk, Minh Phú, HTX Vân Nội...
- Link truy xuất: `https://truyxuat.vn/<slug>` (fake domain, chỉ toast khi bấm).
- SĐT phụ huynh = tên đăng nhập, format `0912 345 678`. Ngày trong tuần 17–21/08/2026.
- Khi thêm data mới, giữ tính nhất quán chéo giữa 3 app (cùng thực đơn, cùng bước quy trình, cùng thực phẩm).

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
- Không quay lại thuật ngữ cũ ("nguyên liệu", "nhà cung cấp") — xem mục Thuật ngữ.
