# DESIGN-ADMIN.md — Hệ thống thiết kế Web Quản trị kho

Tài liệu trích xuất từ `admin.html` (prototype). Dùng làm spec để dựng lại UI khi code thực tế (framework bất kỳ). Mọi giá trị dưới đây là **nguồn sự thật** về màu, kích thước, spacing, component và tương tác của app Quản trị kho.

- **App**: Quản trị kho thực phẩm (desktop) — điều phối bếp ăn trường học.
- **Phong cách**: gọn, dày thông tin, ít thao tác thừa. Nền xanh lá nhạt, sidebar xanh rừng đậm, accent xanh lá.
- **Ngôn ngữ**: tiếng Việt có dấu, copy ngắn, động từ chủ động.
- **Font**: `'Be Vietnam Pro'`, weights 400/500/600/700/800 (Google Fonts). Fallback: `system-ui, sans-serif`.

---

## 1. Design tokens

### 1.1 Màu (CSS variables `:root`)

| Token | Hex | Vai trò |
|---|---|---|
| `--ink` | `#17251C` | Màu chữ chính (gần đen xanh) |
| `--muted` | `#5D6B62` | Chữ phụ, label mờ, caption |
| `--line` | `#E3E8E1` | Border, đường kẻ bảng, divider |
| `--bg` | `#F4F6F2` | Nền toàn trang (body) |
| `--card` | `#fff` | Nền panel/card/table |
| `--green` | `#7CB342` | Accent xanh lá (focus ring, hover viền) |
| `--green-d` | `#558B2F` | Xanh lá đậm — chữ link, số dương, active |
| `--green-soft` | `#EEF6E4` | Nền pill/badge xanh nhạt |
| `--grad` | `linear-gradient(135deg,#8BC34A,#558B2F)` | Gradient nút chính, logo, nav active |
| `--amber` | `#B7791F` | Chữ cảnh báo / trạng thái chờ |
| `--amber-soft` | `#FCF0DC` | Nền pill amber |
| `--red` | `#C0392B` | Chữ nguy hiểm, số nợ, nút xoá |
| `--red-soft` | `#FBE9E7` | Nền pill/nút xoá |
| `--blue` | `#2C6E9B` | Chữ badge info |
| `--blue-soft` | `#E5F0F7` | Nền pill blue |

**Màu ngoài palette (hardcode, cần chú ý khi tái dựng):**

- Sidebar gradient: `linear-gradient(185deg, #1C3327, #132019)`.
- Sidebar chữ: `#D6E4DA`; nav item mặc định `#AFC4B6`; brand/active/user-bold `#fff`.
- Sidebar hover nav: nền `rgba(255,255,255,.06)`; user buttons nền `rgba(255,255,255,.08)`, border-top `rgba(255,255,255,.12)`.
- Badge số trên nav: nền `#E86A33` (cam), chữ trắng.
- Nền phụ (table header, hover row, day-head, pager, ô tổng hợp): `#FAFBF9`.
- Nền hover notif chưa đọc: `#F6FBF0`.
- Tooltip/toast/status-menu-shadow dùng nền tối `#1C3327`.
- Photo mock gradient: `linear-gradient(135deg,#DDE8DF,#C4D6C9)`.
- Step "todo" dot nền: `#EEF1ED`.
- Overlay drawer/modal: `rgba(23,37,28,.45)`.

### 1.2 Typography

| Ngữ cảnh | Size | Weight |
|---|---|---|
| Body mặc định | `14px` | 400 |
| Tiêu đề trang (`.page-head h2`) | `22px` | 800 |
| Mô tả trang (`.page-head p`) | `13.5px` | 400, màu muted |
| Tiêu đề login `h1` / drawer `h3` | `20px` / `17px` | 800 |
| Brand sidebar | `16px` | 800 |
| Nav item | `14px` | 500 (active 600) |
| Table header `th` | `12px`, uppercase, letter-spacing `.05em` | — |
| Table cell `td` | `14px` (kế thừa) | — |
| Pill / badge | `12px`–`12.5px` | 600–700 |
| Nút `.btn` | `14px` (`.btn-sm` `13px`) | 600 |
| Field label | `13px` | 600 |
| Hint / caption | `12.5px`–`13px` | màu muted |

### 1.3 Bo góc (border-radius)

| Thành phần | Radius |
|---|---|
| Login box | `18px` |
| Panel / card lớn | `14px` |
| debt-card | `12px` |
| Nút, field input, filter input | `10px` |
| Logo box | `14px` (sidebar `9px`) |
| icon-btn, pager button, status-menu button | `7–8px` |
| Pill / badge / status-pill | `999px` (bo tròn hết) |
| Drawer | không bo (dán mép phải) |
| Confirm modal | `16px` |
| Toast / tooltip | `12px` / `8px` |
| Photo mock | `8px` |

### 1.4 Border & Shadow

- Border chuẩn: `1px solid var(--line)`.
- Focus input: `outline:2px solid var(--green); border-color:transparent`.
- Logo box shadow: `0 2px 6px rgba(85,139,47,.35)`.
- Drawer shadow: `-8px 0 30px rgba(0,0,0,.14)`.
- Status-menu shadow: `0 8px 24px rgba(0,0,0,.16)`.
- Tooltip shadow: `0 6px 20px rgba(0,0,0,.22)`.

### 1.5 Ẩn scrollbar toàn cục

```css
*{scrollbar-width:none;-ms-overflow-style:none}
*::-webkit-scrollbar{display:none;width:0;height:0}
```

---

## 2. Layout tổng thể (shell)

- **Grid 2 cột**: `grid-template-columns: 236px 1fr` (sidebar cố định + main co giãn).
- **Sidebar (`aside`)**: `position:sticky; top:0; height:100vh`, padding `22px 14px`, flex column. Brand trên cùng → nav (flex:1) → user block dưới cùng.
- **Main**: padding `28px 32px`, `max-width:1320px`, căn giữa.
- **Page**: chỉ 1 page hiển thị (`.page.on{display:block}`), còn lại `display:none`.
- **Page head**: flex space-between, tiêu đề bên trái + nút hành động (`.actions`) bên phải, wrap khi hẹp.

### Responsive

- `@media(max-width:860px)`: shell về 1 cột; sidebar thành thanh ngang cuộn (`flex-direction:row`, `overflow-x:auto`); ẩn badge & user block; main padding `20px`.
- `@media(max-width:560px)`: `.debt-cards` về 1 cột.

---

## 3. Navigation (sidebar)

Menu gom theo **cụm chức năng** (nhãn chuẩn HanoiCheck). Mỗi cụm là `div.nav-group` mở đầu bằng `div.nav-cap` (caption uppercase 10.5px, `rgba(255,255,255,.62)`, cách cụm trên 15px). Mỗi item có icon SVG 19×19 + label, item active có nền gradient.

| Cụm (`.nav-cap`) | Item | `data-page` | Icon |
|---|---|---|---|
| **Cấu hình nhà cung ứng** | Quản lý tài khoản | `users` | khiên |
| | Quản lý nhân sự | `employees` | 2 người |
| | Quản lý NCC phụ | `supplier` | xe tải |
| | Quản lý kho (danh sách kho) | `warehouses` | nhà kho |
| | Quản lý cơ sở sản xuất | `facilities` | nhà xưởng |
| **Quản lý quy trình sản xuất** | Quản lý khâu sản xuất | `process` | clipboard dòng |
| | Quản lý quy trình sản xuất | `workflows` | sơ đồ |
| **Quản lý thực phẩm** | Danh mục thực phẩm chuẩn | `stdcat` | bảng kẻ |
| | Quản lý thực phẩm | `source` | khối 3D |
| | Quản lý món ăn | `dishes` | mái vòm |
| | QL lô nhập hàng | `batches` | khối lô |
| | Quản lý thực đơn | `menus` | sổ đánh dấu |
| **Quản lý đơn hàng** | Quản lý đơn | `order` | clipboard |
| | Danh sách trường | `kitchen` | đầu bếp |
| **Riêng Smart Kitchen** | Báo cáo | `report` | tài liệu + biểu đồ cột |
| | Thông báo | `notif` | chuông + badge số (mặc định `3`) |
| | Tài khoản PHHS | `parent` | người |

- **Tồn kho** (`page-warehouse`) không còn là mục nav — mở qua nút "Xem tồn kho" trên từng dòng của `warehouses` (`openWarehouseStock(i)` đặt `curWarehouse` rồi `go('warehouse')`). `renderWarehouse()` lấy tiêu đề từ `curWarehouse` nếu có, nếu không thì theo vai trò (Kho tổng/Kho phụ).
- `page-stdcat` (Danh mục thực phẩm chuẩn) **chỉ đọc**: không nút CRUD, phân trang 10, bấm dòng mở Drawer chi tiết (quy đổi gram, loại TP, ghi chú).
- Khâu sản xuất (`page-process`) giữ thanh **hạn giờ đăng ký suất ăn** (`mealDeadlineBar`) + nút export — chức năng riêng Smart Kitchen. `PROCESS` thêm field `code` (Mã) + `note` (Ghi chú).
- **Phần D (4 màn phức tạp):** `employees` (chi tiết + form: khối KSK/ATTP/Vận chuyển hiện theo vai trò đã tick); `workflows` (kéo-thả khâu qua HTML5 DnD `wfDrag*` + nút ▲▼ `wfMove`, Số khâu disabled tự đếm); `dishes` & `batches` dùng **Drawer 2 bước** (`stepBar()`/`stepGo()`, section `.dstep`, footer `Tiếp tục`/`Quay lại` render qua `*FootRender`). Lô có **Mã QR** tự sinh `batchQR()` (disabled). Dòng động + ảnh minh chứng: `photoGrid()`/`addPhotoTo()`.
- `aside nav` có `overflow-y:auto` + scrollbar mảnh — danh sách menu sẽ còn dài thêm.
- `applyRole()` ẩn button không thuộc `ROLES[ROLE].nav`, rồi ẩn luôn `.nav-group` nếu cụm đó không còn button nào hiển thị.
- Trang mặc định sau đăng nhập = `ROLES[ROLE].nav[0]` (`notif`), **không** phải item đầu trên DOM.

- Active state: `.active{ background:var(--grad); color:#fff; font-weight:600 }`.
- Hover (không active): chữ trắng + nền `rgba(255,255,255,.06)`.
- Badge nav: nền cam `#E86A33`, bo `999px`, `margin-left:auto`.
- User block dưới: tên (bold trắng), email (muted), link "🏠 Về trang chủ", nút "Đăng xuất".

**Pattern điều hướng**: `go('pageId', btnEl)` — bỏ `.on` mọi `.page`, thêm `.on` cho `page-<id>`, cập nhật `.active` cho nav button. Trang chi tiết trường (`kdetail`) là page riêng, không có nav button (mở qua `openKitchen(i)`).

---

## 4. Component chi tiết

### 4.1 Buttons (`.btn`)

Base: `border-radius:10px; padding:11px 16px; font:600 14px; inline-flex; gap:6px; center`. Hover: `filter:brightness(1.05)`.

| Class | Style |
|---|---|
| `.btn-primary` | nền `var(--grad)`, chữ trắng, `width:100%` (dùng ở login) |
| `.btn-green` | nền `var(--grad)`, chữ trắng (nút hành động chính) |
| `.btn-ghost` | nền trong suốt, `border:1px solid var(--line)`, chữ ink |
| `.btn-danger` | nền `var(--red-soft)`, chữ `var(--red)` |
| `.btn-sm` | thu nhỏ: `padding:7px 12px; font-size:13px` |
| `.btn-ic` | icon SVG trong nút: `15×15`, `fill:currentColor` |

**icon-btn** (nút icon vuông trong bảng): `32×32`, border line, màu muted; hover → xanh (`.danger` hover → đỏ). Icon `16×16`.

### 4.2 Panel & Table

- `.panel`: nền trắng, border line, radius 14px, `overflow:hidden`.
- `th`: nền `#FAFBF9`, uppercase 12px, letter-spacing `.05em`, màu muted, `padding:11px 16px`, border-bottom.
- `td`: `padding:13px 16px`, border-bottom line, vertical-align middle.
- `tr:hover td`: nền `#FAFBF9`; hàng cuối bỏ border.
- `.name-link`: bold, màu ink, hover → `var(--green-d)` + underline (dùng cho tên click mở chi tiết).
- `.clamp2`: giới hạn 2 dòng (`-webkit-line-clamp:2`), `max-width:280px` — kèm `data-tip` để hiện tooltip đầy đủ.

### 4.3 Pill / Badge trạng thái

`.pill`: bo `999px`, `padding:3px 10px`, `12px`, weight 600. Biến thể màu:

| Class | Nền | Chữ | Dùng cho |
|---|---|---|---|
| `.pill.g` | green-soft | green-d | Tích cực / "Có" / "Hiện tại" |
| `.pill.a` | amber-soft | amber | Chờ / đang thực hiện |
| `.pill.b` | blue-soft | blue | Thông tin / đếm số |
| `.pill.r` | red-soft | red | Tiêu cực / "Không" |

**Map trạng thái trường** (field `s` trong data): `a`=Đang chế biến, `b`=Đã nhập thực phẩm, `g`=Hoàn tất hôm nay, `r`=Chưa bắt đầu.

**Status-pill (đơn hàng)** — bấm mở menu đổi trạng thái. **8 trạng thái chuẩn HanoiCheck**, khai báo một chỗ ở `ORDER_STATUS` (`{v, cls, color}`); class pill = `st-${orderStatusCls(status)}`:

| Trạng thái | class | màu |
|---|---|---|
| Chờ xác nhận | `st-a` | hổ phách |
| Từ chối | `st-r` | đỏ |
| Đang chuẩn bị | `st-b` | xanh dương |
| Đang giao | `st-b` | xanh dương |
| Đã giao | `st-b` | xanh dương |
| Giao hàng thành công | `st-g` | xanh lá |
| Trả hàng | `st-r` | đỏ |
| Hủy | `st-r` | đỏ |

Hằng số phụ: `ORDER_DONE` (= Giao hàng thành công), `ORDER_CLOSED` (đơn đã chốt — `orderOpen()` phủ định), `ORDER_VOID` (Từ chối/Hủy — loại khỏi công nợ). Bộ lọc trạng thái đổ bằng `populateOrderStatusFilter()`, đừng hard-code 8 option vào markup. Menu bung ra là `.status-menu` (position:fixed, đặt dưới nút, có dot màu + dấu ✓ mục đang chọn).

### 4.4 Filters & Toolbar

- `.filters` / `.tbar`: flex, gap 10–12px, wrap. `.tbar` space-between (total trái, controls phải).
- `.f-input`: `padding:9px 12px`, border line, radius 10px, `13.5px` weight 500, focus outline xanh.
- `.f-check`: checkbox bọc trong khung border, `accent-color:var(--green)`.
- `.search`: `min-width:240px`, placeholder có emoji 🔍.
- `.total`: "Tổng: **N** đơn vị" (số in đậm màu ink).

### 4.5 Pagination (`.pager`)

- Đặt trong panel, border-top, nền `#FAFBF9`, padding `12px 16px`.
- Trái: text "Hiển thị a–b trên N". Phải: `‹` [input số trang] `/ N` `›`.
- Nút `30×30` border line, hover nền green-soft. Input trang `width:46px`, căn giữa.
- Logic chung cho notif/source/supplier/order/kitchen/parent — **8 dòng/trang** (`PER=8`).

### 4.6 Notifications

- `.notif`: flex, gap 14px, padding 16px, border-bottom. `.unread` → nền `#F6FBF0`.
- Icon tròn-mềm `38×38`, radius 10px, nền truyền qua biến (amber/green/blue-soft), emoji 17px.
- Nội dung: dòng chính (bold phần tên bếp) + `.time` muted 12.5px.
- `.empty`: khi rỗng — text căn giữa, muted, padding 34px.

### 4.7 Tabs (chi tiết trường)

- `.tabs`: flex, border-bottom line. Button 600/14px màu muted; `.active` → chữ green-d + border-bottom `2px var(--green)`.
- `.tabpage.on{display:block}`. Hai tab: "Danh sách thực đơn" và "Công nợ".

### 4.8 Timeline thực đơn (`.day`)

- Card accordion: `.day-head` (nền #FAFBF9, click toggle `.open`) + `.day-body` (ẩn, hiện khi `.open`).
- `.ing`: grid 3 cột `1fr 110px 1fr` (Thực phẩm / Số lượng / Nguồn), header `.h` uppercase muted, mỗi ô border-bottom dashed.

### 4.9 Báo cáo quy trình (`.steps`)

- `.step`: flex gap 16px, border-bottom. Có `.dot` tròn `34×34` chứa emoji bước.
- Trạng thái: `.done .dot`→green-soft, `.doing .dot`→amber-soft, `.todo .dot`→#EEF1ED (muted).
- `.photos`: hàng thumbnail; `.photo` `86×64`, gradient xám-xanh, emoji 📷.

### 4.10 Drawer (panel phải dùng chung)

- `.overlay`: full-screen `rgba(23,37,28,.45)`, z-index 50.
- `.drawer`: cố định mép phải, `width:min(460px,94vw)`, `height:100vh`, trượt vào bằng `transform:translateX(100%→0)`, transition `.28s cubic-bezier(.4,0,.2,1)`, z-index 60.
- Cấu trúc: `.drawer-head` (title + nút ✕) / `.drawer-body` (scroll, flex:1, padding 22px 24px) / `.drawer-foot` (nút căn phải, border-top).
- API JS: `openDrawer(title, bodyHTML, footHTML)` / `closeDrawer()`.
- `.dropzone`: khung dashed cho import file, hover → viền xanh + nền green-soft.

### 4.11 Confirm modal & Toast

- `.cmodal-overlay`: overlay center, z-index 70. `.cmodal` trắng, radius 16px, `width:min(400px,92vw)`, nút Huỷ (ghost) + nút nguy hiểm (danger).
- API: `confirmDialog(title, msg, okLabel, onOk)` — **chỉ dùng cho xác nhận xoá**.
- `#toast`: cố định `bottom:24px`, giữa màn hình, nền `#1C3327` chữ trắng, trượt lên khi `.on`, tự ẩn sau `2600ms`. API: `toast(msg)`.

### 4.12 Fields (form trong drawer)

- `.field`: `margin-bottom:14px`. Label block 600/13px. Input/select: full width, `padding:11px 13px`, border line, radius 10px.
- Layout 2 cột trong drawer: dùng `display:flex; gap:12px` (vd Đơn giá + Đơn vị tính).

### 4.13 Tooltip dùng chung (`#tip`)

- Đặt trong `<body>` để không bị panel cắt; `position:fixed`, nền `#1C3327`, chữ trắng 12px, `max-width:300px`.
- Kích hoạt qua thuộc tính `data-tip="..."` trên bất kỳ element nào (hover mouseover/mouseout). Định vị phía trên element.

---

## 5. Màn Login

- Center full-viewport. `.login-box`: trắng, border line, radius 18px, padding 40px, `width:380px`.
- Logo box gradient `52×52` (SVG nhà kho) → h1 "Quản trị kho thực phẩm" → p mô tả → 2 field (user/password) → `.btn-primary` "Đăng nhập" → hint → link "← Về trang chủ".
- **Không validation** — nhập gì cũng vào (`doLogin()` chỉ ẩn login, hiện app).

---

## Màn Báo cáo (`page-report`)

Admin chọn ngày → xem trường nào chưa báo cáo quy trình bếp. **Chỉ hiện trường `kitchen:true`.**

- Bộ lọc trong `.tbar-right`: `input[type=date]` (mặc định `TODAY_DEMO`) · select 3 trạng thái · ô tìm theo tên/mã trường.
- Thanh tóm tắt `.rsum` nằm bên trái `.tbar`, **sát trên bảng**: `Tổng N trường có bếp · Đã đủ a · Thiếu b · Chưa báo cáo c`.
- Cột Tiến độ: `{x}/{y} bước` + `.prog` (progress bar). Class đổi theo trạng thái: `.prog.d` đỏ (x=0), `.prog.w` hổ phách (0<x<y), `.prog` gradient xanh (đủ).
- Highlight dòng: `tr.rp-danger` (nền đỏ nhạt + viền trái đỏ) khi chưa báo cáo, `tr.rp-warn` (vàng nhạt) khi thiếu, không class khi đủ.
- `openReportDetail(school, iso)` → Drawer **chỉ xem** (`wide`), tái dùng `.step`: hiện **toàn bộ** bước theo thứ tự. Bước đã báo cáo → ghi chú + ảnh `.photo.view` (bấm mở `#pvOverlay`) + người & giờ báo cáo. Bước chưa báo cáo → `.step.none` + chip `.nope` "Chưa báo cáo". Footer chỉ có nút Đóng.

## Màn Quản lý thực đơn (`page-menus`)

CRUD thủ công, **không thay** luồng import Excel theo tuần ở chi tiết trường (`renderMenu()` — khác `renderMenus()`).

- 5 bộ lọc: nhóm tuổi · trường · trạng thái · thứ · tìm kiếm. Thực đơn **dùng chung** (`schools` rỗng) luôn khớp mọi bộ lọc trường.
- Drawer Thêm/Sửa dùng **stepper dọc 4 section** (`.sect`), rộng `wide`:
  1. Thông tin — tên, mã, nhóm tuổi, trạng thái, ghi chú
  2. Lịch áp dụng — chips thứ (`.pick`) + 2 ô ngày → vùng xem trước `.mf-prev` **cập nhật tức thì**
  3. Trường áp dụng — chips chọn nhiều + "Chọn tất cả" / "Bỏ chọn"; **để trống = dùng chung**
  4. Cấu hình bữa ăn — 4 nhóm chips món, **lọc theo nhóm tuổi** đã chọn ở section 1
- State form giữ ở biến `mf`; mỗi thao tác gọi `mfRender*()` để vẽ lại vùng liên quan. Đổi nhóm tuổi sẽ **bỏ món không còn hợp** và toast báo số món bị bỏ.
- Cảnh báo dùng `.alert` (`warn`/`danger`/`info`) — **không chặn nút Lưu** (prototype không validate): Đến ngày < Từ ngày · 0 ngày · > `MENU_DAYS_WARN` (200) ngày · trùng thực đơn · sửa thực đơn đang áp dụng.
- Trùng thực đơn tính theo **nhóm tuổi + trường + ngày + bữa**, cho chọn `Ghi đè` / `Bỏ qua ngày trùng` (`mf.dup`).


## 6. Cấu trúc dữ liệu (mock) & render

Data là các const array/object ở đầu `<script>`; mỗi màn có hàm `renderX()` đổ HTML bằng template literal. Khi code thực, đây là schema tham chiếu cho API/model.

| Biến | Schema | Ghi chú |
|---|---|---|
| `SOURCES` | `{name, dist, origin, link, price, unit, hist:[[date,price],...]}` | Thực phẩm; `dist` khớp `SUPPLIERS.name`; `hist` mới nhất trước |
| `SUPPLIERS` | `{name, contact, phone, addr}` | Nhà cung ứng phụ — nguồn chọn khi thêm thực phẩm |
| `KITCHENS` | `{name, addr, size, status, s, kitchen, user, lunch}` | Trường/bếp; `s`=màu pill; `size`=suất/ngày |
| `NOTIFS` | `[icon, bgVar, html, time, unread, date]` | Mảng vị trí, không phải object |
| `MENU` | `{d, dish, ing:[[tên, sốlượng],...]}` | Thực đơn theo ngày (tuần 34) |
| `REPORT_STEPS` | `{cls, ic, t, sub, ph}` | 6 bước quy trình; `ph`=số ảnh |
| `MENUS` | `{code, name, age, status, note, wd, from, to, schools, meals}` | `age`=chỉ số `AGE_GROUPS`; `wd`=mảng thứ (`Date.getDay()`); `schools` **rỗng = dùng chung mọi trường** |
| `DISHES` | `{code, name, slot, ages}` | Nguồn chọn món cho thực đơn; `slot` khớp `MEAL_SLOTS.k` |
| `REPORT_PLAN` | `{ngày: {tên trường: số bước đã báo cáo}}` | `buildReportSteps()` nở ra chi tiết từng bước theo `PROCESS` |
| `ORDERS` | `{code, inv, school, date, status, paid, meals\|items, history}` | Mã đơn = `yyyyMMdd`+số thứ tự. `status` ∈ `ORDER_STATUS`. Trường có bếp dùng `meals`, không bếp dùng `items` — **luôn guard `o.items` trước khi loop** |
| `PAYMENTS` | `{ [school]: [[ngày, sốtiền, ghichú],...] }` | Thanh toán ghi tay → tính công nợ |
| `PARENTS` | `{ [school]: [[tênPH, sđt, tênHS, lớp],...] }` | SĐT = tên đăng nhập |

**Hàm tiện ích quan trọng:**

- `money(n)` → `n.toLocaleString('vi-VN') + ' đ'` (vd `22.000 đ`).
- `orderTotal(o)` → tổng `SL × đơngiá` của các item.
- `parseVN('dd/MM/yyyy')` → Date; dùng sort công nợ.
- Công nợ: cộng dồn `debit` (đơn hàng) − `credit` (thanh toán) theo thời gian, ra dư nợ cuối kỳ.

**6 bước quy trình chuẩn** (đồng bộ cả 3 app): 📦 Giao nhận thực phẩm (06h00–06h45) → 🔪 Sơ chế-rửa (06h45–07h30) → 🍲 Chế biến (07h45–09h45) → 🍱 Lưu mẫu-chia ăn-bảo quản (09h45–10h45) → 🍽️ Giao nhận suất ăn (10h45–11h00) → 🍎 Giao nhận, chia quả chiều (13h00–13h30).

---

## 7. Pattern JS chính (để tái hiện hành vi)

| Pattern | Hàm |
|---|---|
| Điều hướng page | `go(page, btn)` |
| Tab chi tiết trường | `ktab(name, btn)`, mở trường `openKitchen(i)` |
| Drawer | `openDrawer(t,body,foot)` / `closeDrawer()` |
| Xác nhận xoá | `confirmDialog(t,msg,ok,onOk)` / `closeConfirm()` |
| Feedback | `toast(msg)` (mọi ghi/xoá/sửa chỉ toast, không persist) |
| Import Excel | `fileImport(msg)` — mở file picker rồi toast, **không parse thật** |
| Phân trang | `paginate/drawPager/pageStep/pageGoto`, `pagerState`, `PER=8` |
| Menu trạng thái đơn | `openStatusMenu(i,btn)` / `pickStatus(v)` / `setOrderStatus(i,v)` |
| Tooltip | thuộc tính `data-tip`, xử lý qua `#tip` |

**Quy ước prototype (giữ khi tái dựng nếu vẫn là demo, bỏ khi lên production thật):**

- Không validation, không error state — mọi nút/form đều chạy.
- Data in-memory, reset khi reload; mọi mutate chỉ báo `toast(... (mock))`.
- Chỉ dependency ngoài: Google Fonts (Be Vietnam Pro).

---

## 8. Checklist khi code thật (production)

Những điểm cần bổ sung so với prototype:

- [ ] Thêm validation form (required, định dạng SĐT/số tiền, xử lý lỗi).
- [ ] Persist dữ liệu qua API/DB thay cho const array in-memory.
- [ ] Parse file Excel thật ở `fileImport` (cân nhắc SheetJS).
- [ ] Auth thật cho login (hiện nhập gì cũng vào).
- [ ] Phân trang/tìm kiếm/lọc phía server nếu dữ liệu lớn (hiện client-side).
- [ ] Trang truy xuất nguồn gốc thật thay cho toast `truyxuat.vn/<slug>`.
- [ ] Giữ nguyên tokens màu/spacing/component ở mục 1–4 để đồng nhất nhận diện.
