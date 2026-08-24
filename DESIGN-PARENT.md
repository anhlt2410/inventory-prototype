# DESIGN-PARENT.md — Hệ thống thiết kế App Phụ huynh (mobile)

Tài liệu trích xuất từ `parent.html` (prototype). Dùng làm spec để dựng lại UI khi code thực tế.

- **App**: App Phụ huynh (mobile, public) — phụ huynh theo dõi bữa ăn của con & truy xuất nguồn gốc.
- **Được phép "đẹp" hơn** 2 app kia: gradient teal, shadow nhẹ, chip, timeline dọc, bottom sheet mượt.
- **Accent teal** `--teal:#0FA48A`, hero gradient `linear-gradient(160deg,#0FA48A,#0B7A67)`, card bo `20px`.
- **Font**: `'Be Vietnam Pro'`, weights 400/500/600/700/800.
- 2 tab: **Thực đơn** · **Hồ sơ**.

---

## 1. Design tokens

### 1.1 Màu (CSS variables `:root`)

| Token | Hex | Vai trò |
|---|---|---|
| `--ink` | `#122A25` | Chữ chính (xanh đen) |
| `--muted` | `#5F7A73` | Chữ phụ, caption |
| `--line` | `#E2EDEA` | Border, divider |
| `--bg` | `#F2F8F6` | Nền frame (xanh nhạt) |
| `--card` | `#fff` | Nền card |
| `--teal` | `#0FA48A` | Accent teal chính |
| `--teal-dark` | `#0B7A67` | Teal đậm — chữ nhấn, active |
| `--teal-soft` | `#E2F5F0` | Nền pill/chip/step done |
| `--amber` | `#C77E1E` | Trạng thái đang chế biến |
| `--amber-soft` | `#FCF1DE` | Nền banner/chip đang nấu |

**Màu hardcode ngoài palette:**

- Nền ngoài frame (desktop): `#20302c`.
- Hero / login / sheet-head / contact: `linear-gradient(160deg,var(--teal),var(--teal-dark))`; login sâu hơn: `linear-gradient(160deg,#0FA48A 0%,#0B7A67 60%,#095E50 100%)`.
- Status "plan" (chưa bắt đầu): nền `#EEF1F0`, chữ muted.
- Photo timeline: `linear-gradient(135deg,#D7EEE8,#B9DfD4)`.
- Input trên nền hero (login): `rgba(255,255,255,.14)`, chữ trắng.
- Toast nền: `#122A25`.

### 1.2 Typography

| Ngữ cảnh | Size | Weight |
|---|---|---|
| Login `h1` | `28px` | 800 |
| Hero `h1` | `21px` | 800 |
| Sheet `h2` / dish tiêu đề | `18px`–`17px` | 800 |
| Nút `.btn` | `16px` | 700 |
| menu-card dish | `15.5px` | 700 |
| ing tên (`.n`) | `14.5px` | 600 |
| sec (section) | `13px` uppercase, letter-spacing `.06em` | 800, muted |
| status-chip / today-tag | `11px`–`12px` | 700–800 |
| Nav item | `12px` | 600 (icon 23px) |
| Toast | `14px` | 600 |

### 1.3 Bo góc

| Thành phần | Radius |
|---|---|
| Phone frame (desktop) | `38px`, viền `8px solid #111` |
| Card / menu-card | `20px` |
| Hero (dưới) | `0 0 28px 28px` |
| Nút / field input | `13px`–`14px` |
| Bottom sheet | `26px 26px 0 0` |
| Chip / pill / status-chip | `999px` |
| Timeline dot | `50%` (24×24) |
| Photo timeline | `10px` (74×56) |
| Toast | `14px` |

### 1.4 Shadow

- Card: `0 2px 10px rgba(18,42,37,.04)`.
- menu-card feat (nổi bật): `0 6px 18px rgba(15,164,138,.16)`, viền `1.5px --teal`.
- Frame (desktop): `0 30px 80px rgba(0,0,0,.5)`.
- Modal có `backdrop-filter:blur(3px)` khi mở.

---

## 2. Phone frame & layout

- `.frame`: `max-width:420px`, `height:100dvh`, flex column, `overflow:hidden`.
- `@media(min-width:480px)`: bezel — `height:min(880px,calc(100vh-56px))`, `border-radius:38px`, viền `8px solid #111`, shadow lớn. Mobile thật → full-screen.
- `.screen`: mỗi màn cuộn dọc riêng; chỉ `.screen.on{display:flex}`.
- `.hero`: header gradient teal bo dưới `28px`, chứa lời chào + tên PH + thẻ học sinh (`.student` avatar tròn).
- `.content`: `padding:16px 16px 100px` (chừa nav).
- `.home-btn`: fixed góc trên phải; **ẩn khi mở modal** (`openDay` set display none, `closeDay` khôi phục).

---

## 3. Bottom navigation (2 tab)

- `.nav`: `position:absolute; bottom:0`, nền trắng, border-top, grid 2 cột, chừa `env(safe-area-inset-bottom)`.
- Item: 🍽️ Thực đơn · 👤 Hồ sơ; `.active` → chữ teal.
- Điều hướng: `show(id)` + `tab(t,btn)` (set active). Modal chi tiết là overlay, không đổi screen.

---

## 4. Component chi tiết

### 4.1 Buttons

Base `.btn`: `border-radius:14px; font:700 16px; padding:15px; width:100%`.

| Class | Style |
|---|---|
| `.btn-teal` | nền teal, chữ trắng |
| `.btn-soft` | nền teal-soft, chữ teal-dark |
| (login) `.btn` | nền trắng, chữ teal-dark (trên nền hero) |

### 4.2 Hero header

- Gradient teal, chữ trắng. `.hi` (lời chào, opacity .85) + `h1` (tên PH).
- `.student`: khối mờ `rgba(255,255,255,.14)`, avatar tròn `44×44` nền trắng + tên/lớp/trường.

### 4.3 Thực đơn — menu list

- `.sec`: nhãn section uppercase muted.
- `.chipday`: hàng chip ngày cuộn ngang (chip pill, `.active` nền teal). *(có style sẵn, dùng khi cần lọc nhanh)*
- `.filter-bar` + `#dateFilter` (input date viền line) + `.date-clear` (chip teal-soft "Xoá lọc ✕", ẩn khi chưa lọc).
- `.menu-card`: card bấm mở chi tiết — `.mc-top` (ngày + `.today-tag` nếu hôm nay) + `.dish` + `.mc-foot` (`.status-chip` + `.mc-cta` "Xem chi tiết ›").
  - `.menu-card.feat`: thẻ hôm nay nổi bật (viền teal, shadow teal).
- `.status-chip`: `.done` (teal), `.doing` (amber), `.plan` (xám `#EEF1F0`).
- `.empty`: khi lọc không có kết quả.

Sắp xếp danh sách: ngày giảm dần (`iso.localeCompare` đảo); thẻ hôm nay tách riêng lên trên (`#todayCard`).

### 4.4 Modal chi tiết bữa ăn (bottom sheet)

- `.modal`: `inset:0`, nền `rgba(8,20,17,.5)`, mở dần + `backdrop-filter:blur(3px)`.
- `.sheet`: từ `top:34px` xuống đáy, radius `26px 26px 0 0`, trượt lên (`translateY(101%→0)`), transition `.42s cubic-bezier(.22,1,.36,1)`.
- `.sheet-head`: gradient teal, có `.sheet-grip` (thanh kéo) + `.sheet-close` (nút tròn ✕) + ngày (`.d`) + món (`h2`).
- `.sheet-body`: cuộn dọc, chứa banner trạng thái + timeline + nguyên liệu.

### 4.5 Status banner + Timeline dọc (`.tl`)

- `.status-banner`: khối trạng thái đầu sheet; biến thể `.done` (teal-soft), `.doing`/mặc định (amber-soft), `.plan` (xám). Icon 30px + tiêu đề + phụ đề.
- `.tl`: timeline dọc có đường kẻ `::before` (2px --line); mỗi `.tstep` có `.dot` `24×24` tròn:
  - `.done .dot` → nền teal, dấu ✓; `.doing .dot` → viền amber + icon bước; chưa bắt đầu → trống.
- `.tstep .photos`/`.photo` (74×56, gradient teal nhạt, 📷): ảnh do bếp gửi tại từng bước.

### 4.6 Nguyên liệu & nguồn gốc

- `.ing`: dòng flex — trái: `.n` (tên) + `.s` (nguồn) + `.trace` (link "🔗 Truy xuất nguồn gốc"); phải: `.qty` (định lượng/suất, teal-dark). Border-bottom dashed.
- Link truy xuất → chỉ `toast('Mở trang truy xuất: ...')`.

### 4.7 Hồ sơ

- `.card` xem hồ sơ: các `.prow` (label muted trái / giá trị đậm phải) + nút "✏️ Chỉnh sửa" (btn-soft).
- Form sửa: `.prof-input` (viền 1.5px), nút Lưu (teal) / Huỷ (soft). Toggle view↔edit bằng `editProfile(on)`.
- `.contact`: khối gradient teal — icon 💬 + tên phụ trách + giờ hỗ trợ + nút gọi (`<a href="tel:...">`).
- Nút "Đăng xuất" nền trắng viền line.

### 4.8 Toast

- `#toast`: `position:absolute; bottom:96px`, nền `#122A25` chữ trắng, trượt lên khi `.on`, tự ẩn `2200ms`. API: `toast(msg)`.

---

## 5. Màn Login

- Nền gradient teal (full). Logo 🌿 (48px) → "Bếp Trường" → sub "Minh bạch từng bữa ăn của con" → field SĐT + mật khẩu (input trắng mờ trên hero) → nút trắng "Đăng nhập" → hint.
- **SĐT = tên đăng nhập** (format `0912 345 678`). **Không validation** — `login()` chỉ chuyển sang s-menu + bật nav.

---

## 6. Cấu trúc dữ liệu (mock) & render

| Biến | Schema | Ghi chú |
|---|---|---|
| `MENU` | `{dk, iso, today?, day, date, st:{k,icon,title,sub}, dish, steps, ing:[[tên,nguồn,địnhlượng,link],...]}` | Thực đơn theo ngày; `st.k`='doing'/'plan'/'done' |
| `DOING_STEPS` | `[{s,t,ph?}]` | Timeline khi đang nấu (`s`='done'/'doing'/''; `ph`=số ảnh) |
| `PLAN_STEPS` | `[{s,t}]` | Timeline khi chưa bắt đầu (dùng lại cho mọi ngày tương lai) |
| `STEP_NAMES` | `[...]` | Tên 6 bước (index theo timeline) |
| `STEP_ICONS` | `["📦","🔪","🍲","🍱","🍽️","🍎"]` | Icon 6 bước |
| `filterDate` | string | Ngày đang lọc |

**Hàm chính:** `renderList()` (thẻ hôm nay + danh sách sắp xếp), `cardHTML(i,feat)`, `openDay(i)`/`closeDay()`, `onDate/clearDate`, `editProfile/saveProfile`.

**6 bước quy trình chuẩn** (đồng bộ 3 app): 📦 Giao nhận thực phẩm (06h00–06h45) → 🔪 Sơ chế-rửa (06h45–07h30) → 🍲 Chế biến (07h45–09h45) → 🍱 Lưu mẫu-chia ăn-bảo quản (09h45–10h45) → 🍽️ Giao nhận suất ăn (10h45–11h00) → 🍎 Giao nhận, chia quả chiều (13h00–13h30).

---

## 7. Pattern JS chính

| Pattern | Hàm |
|---|---|
| Chuyển màn / tab | `show(id)` / `tab(t,btn)` |
| Modal chi tiết | `openDay(i)` / `closeDay()` (ẩn/hiện home-btn) |
| Lọc theo ngày | `onDate(v)` / `clearDate()` / `renderList()` |
| Hồ sơ | `editProfile(on)` / `saveProfile()` |
| Login | `login()` / `logout()` |
| Feedback | `toast(msg)` — mọi mutate chỉ toast, không persist |

**Quy ước prototype:** không validation; data in-memory reset khi reload; chỉ dependency ngoài là Google Fonts.

---

## 8. Checklist khi code thật (production)

- [ ] Auth thật bằng SĐT (OTP?) — hiện nhập gì cũng vào; SĐT là tên đăng nhập.
- [ ] Lấy thực đơn/tiến độ/ảnh theo thời gian thực từ bếp (đồng bộ với app Bếp & admin).
- [ ] Trang truy xuất nguồn gốc thật (`truyxuat.vn/<slug>`) thay cho toast.
- [ ] Ảnh timeline load từ storage thật (hiện là mock 📷).
- [ ] Đây là app public → chú ý bảo mật dữ liệu học sinh, phân quyền phụ huynh chỉ xem con mình.
- [ ] Giữ nhận diện "đẹp hơn" (gradient teal, timeline, sheet mượt) và tokens ở mục 1–4.
