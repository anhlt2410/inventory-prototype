# DESIGN-KITCHEN.md — Hệ thống thiết kế App Bếp Trường (mobile)

Tài liệu trích xuất từ `kitchen.html` (prototype). Dùng làm spec để dựng lại UI khi code thực tế.

- **App**: App Trường (mobile, private) — dùng ngay tại bếp/văn phòng trường.
- **Hai chế độ (mode)** chọn ở màn login:
  - `kitchen` (**Có bếp**): Thực đơn → Nhật ký → Báo cáo quy trình (kèm ảnh).
  - `shop` (**Không bếp**): Đặt hàng → Đơn hàng → Công nợ (mua suất ăn từ Kho tổng).
- **Ràng buộc accessibility (bắt buộc — người dùng lớn tuổi):** body text ≥16px, label/nút ≥17px, nút cao ≥56px (`min-height:60px`), **tối đa 3 tab** bottom-nav. Không thêm hiệu ứng cầu kỳ.
- **Accent cam** `--orange:#E86A33`. Phong cách ấm, nút to, tương phản rõ.
- **Font**: `'Be Vietnam Pro'`, weights 400/500/600/700/800.

---

## 1. Design tokens

### 1.1 Màu (CSS variables `:root`)

| Token | Hex | Vai trò |
|---|---|---|
| `--ink` | `#26221E` | Chữ chính (nâu đen ấm) |
| `--muted` | `#7A736B` | Chữ phụ, caption |
| `--line` | `#ECE6DE` | Border, divider |
| `--bg` | `#FBF8F4` | Nền frame (kem ấm) |
| `--card` | `#fff` | Nền card |
| `--orange` | `#E86A33` | Accent cam chính |
| `--orange-dk` | `#D2571F` | Cam đậm — chữ nhấn, active |
| `--orange-soft` | `#FDEDE4` | Nền pill/nav active/step hiện tại |
| `--green` | `#2F7A4D` | Xanh — trạng thái hoàn tất |
| `--green-soft` | `#E7F2EA` | Nền badge/step done |

**Màu hardcode ngoài palette cần chú ý:**

- Nền ngoài frame (desktop): `#232323` / mobile `#1f1f1f` (giả lập điện thoại trên nền tối).
- Gradient nút cam: `linear-gradient(145deg,#F0793F,var(--orange-dk))`.
- Gradient nút xanh: `linear-gradient(145deg,#3E9A63,var(--green))`.
- Card "hôm nay"/công nợ (`.today`,`.debt-card`): `linear-gradient(158deg,#FFF3EB,#FDE7DA)`, viền `#F6C6AC`.
- Head screen: `linear-gradient(180deg,#fff,#FCFAF7)`.
- Photo-zone: `linear-gradient(160deg,#FFF7F1,#FDEEE4)`, viền dashed `#E7B896`.
- Step-btn picked bg: `linear-gradient(150deg,#FFF3EB,#FDE7DA)`.
- Badge chờ giao (`.pending`): nền `#FEF3D6`, chữ `#9A6B00`.
- Toast nền: `#26221E`.

### 1.2 Typography (cỡ lớn cho người lớn tuổi)

| Ngữ cảnh | Size | Weight |
|---|---|---|
| Head `h1` | `22px` | 800 |
| Login `h1` | `26px` | 800 |
| Nút `.btn` | `19px` | 700 |
| Field label | `16px` | 700 |
| Field input | `18px` | 400 |
| Card tiêu đề (`.today h2`, `.sheet h2`) | `20px` | 800 |
| ing-row / order-line | `16px`–`17px` | — |
| section-title | `14px` uppercase, letter-spacing `.05em` | 800, muted |
| Badge | `12px` | 800 |
| Nav item | `13px` | 600 (icon 26px) |
| Toast | `16px` | 700 |

### 1.3 Bo góc

| Thành phần | Radius |
|---|---|
| Phone frame (desktop) | `42px`, viền `9px solid #0c0c0c` |
| Card, step-btn, log-card | `18px`–`20px` |
| today/debt-card | `22px` |
| Nút `.btn` | `16px` |
| Field input / note-input | `14px` |
| Bottom sheet | `28px 28px 0 0` |
| Nav item | `14px` |
| Pill / badge / stepper vòng | `999px` / `12px` |
| Thumb ảnh | `14px` (92×92) |
| Toast | `14px` |

### 1.4 Shadow

- Nút cam: `0 8px 20px rgba(210,87,31,.28)`; nút xanh: `0 8px 20px rgba(47,122,77,.26)`.
- Card: `0 4px 16px rgba(38,34,30,.04)`.
- Cartbar: `0 12px 30px rgba(210,87,31,.35)`.
- Frame (desktop): `0 30px 80px rgba(0,0,0,.55)`.

---

## 2. Phone frame & layout

- `.frame`: `max-width:420px`, căn giữa, `height:100vh`, flex column, `overflow:hidden`.
- `@media(min-width:480px)`: bọc bezel — `height:min(calc(100vh-56px),880px)`, `border-radius:42px`, viền đen `9px`, đổ bóng lớn; nền body tối. Trên mobile thật → full-screen.
- `.screen`: mỗi màn `flex:1`, cuộn dọc riêng; chỉ `.screen.on{display:flex}`.
- `.head`: sticky top, gradient trắng, border-bottom.
- `.content`: `padding:18px 16px 130px` (chừa chỗ cho nav + cartbar).
- Nút "🏠 Trang chủ": `.home-btn` fixed góc trên phải, pill trắng mờ.

---

## 3. Bottom navigation (tối đa 3 tab)

Hai thanh nav riêng theo mode, cùng lúc chỉ 1 thanh `.on`:

- `#nav` (mode kitchen): 🍽️ Thực đơn · 📖 Nhật ký · 📷 Báo cáo.
- `#navShop` (mode shop): 🛒 Đặt hàng · 🧾 Đơn hàng · 💳 Công nợ.
- `.nav`: `position:absolute; bottom:0`, nền trắng mờ + `backdrop-filter:blur(8px)`, grid 3 cột, chừa `env(safe-area-inset-bottom)`.
- Item: cột icon (26px) + label; `.active` → chữ cam, nền `--orange-soft` (nav kitchen).

Điều hướng: `show(id)` bỏ/thêm `.on` cho `.screen`. `tab(t,btn)` (kitchen) / `tabShop(t,btn)` (shop) chuyển màn + set active + gọi render tương ứng. Chi tiết (log/order detail) là screen riêng, có nút `.back`.

---

## 4. Component chi tiết

### 4.1 Buttons

Base `.btn`: `border-radius:16px; font:700 19px; padding:18px; width:100%; min-height:60px`.

| Class | Style |
|---|---|
| `.btn-orange` | gradient cam, chữ trắng, shadow cam; `:active` dịch xuống 1px |
| `.btn-green` | gradient xanh, chữ trắng, shadow xanh |
| `.btn-outline` | nền trắng, viền `2px --line`, chữ ink |

### 4.2 Field / input (login)

- `.field`: `margin-bottom:16px`. Label 700/16px. Input `padding:16px`, viền `2px --line`, radius 14px, `18px`. Focus → viền cam.
- `.seg` (segmented toggle chọn mode): khung bg + viền, 2 nút; `.seg-btn.on` → nền trắng + chữ cam + shadow.

### 4.3 Card "Hôm nay" / thực đơn

- `.today`: card gradient cam nhạt, viền `#F6C6AC`. `.tag` (nhãn uppercase cam) + `h2` (món) + `.pill` (số suất, nền trắng) + `.ing-list`.
- `.ing-row`: flex space-between, border-bottom dashed, `17px`.
- `.day-item`: dòng ngày trong tuần — `.d` (thứ, cam) + `.m` (món) + `.chev` (›); `:active` opacity giảm.

### 4.4 Nhật ký (log)

- `.filter`: input date (viền 2px, focus cam) + nút `.clr` "Tất cả".
- `.log-card`: icon tròn `52×52` nền bg + info (tiêu đề 16px, `.dish` muted, badge) + chev.
- `.badge`: `.done` (xanh) / `.progress` (cam) / `.pending` (vàng `#FEF3D6`).
- `.empty`: text căn giữa muted, padding 50px.

### 4.5 Chi tiết nhật ký — timeline bước

- `.step-row`: icon vuông `46×46` + nội dung (tên 17px, `.t` muted có giờ) + `.mk` (✅/⏳/⚪).
- Trạng thái: `.ok .ic`→green-soft, `.cur .ic`→orange-soft.

### 4.6 Báo cáo — accordion 6 bước

- `.step-btn`: nút bước to (viền 2px, `19px`, icon 30px); `.picked` → viền cam + nền gradient cam; `:disabled` mờ.
- `.acc`: wrapper; `.acc.open` mở body, `.acc.done` head nền xanh nhạt `#F4FAF6`.
- `.acc-body`: viền cam, chứa `.acc-desc` (danh sách công việc, nền orange-soft) + photo-zone + note + nút gửi.
- `.photo-zone`: khung dashed cam, emoji 📷 44px — bấm `snap()` chụp ảnh.
- `.thumbs`/`.thumb` (92×92): ảnh chụp qua `<input capture="environment">` + `URL.createObjectURL`.
- `.note-input`: textarea, min-height 76px, focus cam.
- `.success`: màn xác nhận đã gửi (emoji 72px, tiêu đề 24px).

### 4.7 Bottom sheet (modal)

- `.modal`: `position:absolute; inset:0`, nền `rgba(24,18,12,.5)`, align-items:flex-end.
- `.sheet`: nền trắng, radius `28px 28px 0 0`, trượt từ dưới (`translateY(100%→0)`), transition `.3s cubic-bezier(.2,.8,.2,1)`, max-height 90%.
- `.grab` (thanh kéo), `.sheet-head` (tag + h2 + nút `.x`).
- Dùng cho: chi tiết món (`openMenu`), giỏ hàng (`openCart`), xác nhận hoàn thành bước (`confirmStep`).

### 4.8 Shop mode (trường không bếp)

- `.shop-item`: icon 56×56 + info (tên, `.sup` nguồn+ĐVT, `.price` cam) + stepper/add-btn.
- `.stepper`: nút − / qty / +, mỗi nút `40×40` viền 2px. `.add-btn` `44×44` gradient cam.
- `.cartbar`: thanh nổi trên nav (`bottom:calc(84px + safe-area)`), gradient cam, hiện khi có món (`.on`) — "🛒 N món ... tổng ›".
- `.cart-row`, `.total-row` (tổng đậm cam 22px) trong sheet giỏ hàng.
- `.order-card`: icon (📦 đã giao / 🕒 chờ) + info (mã, deliver, `.amt` cam, badge).
- `.order-line`: dòng chi tiết đơn (món + `.q` đơn giá×SL, thành tiền đậm).
- `.debt-card`: gradient cam, `.amt` 34px (công nợ hiện tại) + meta hạn thanh toán.
- `.pay-row`: lịch sử thanh toán — icon ✅ nền green-soft + info + `.amt` xanh (−số tiền).

### 4.9 Toast

- `#toast`: `position:absolute; bottom:104px`, nền `#26221E` chữ trắng, trượt lên khi `.on`, tự ẩn `2200ms`. API: `toast(msg)`.

---

## 5. Màn Login

- Logo 🏫 (56px) → "App Trường" → sub tên trường (đổi theo mode) → **seg toggle mode** → field user/password → nút cam "ĐĂNG NHẬP" → hint.
- **Không validation** — `login()` chỉ chuyển màn theo `mode` (`kitchen`→s-menu+#nav, `shop`→s-shop+#navShop).

---

## 6. Cấu trúc dữ liệu (mock) & render

| Biến | Schema | Ghi chú |
|---|---|---|
| `UPCOMING` | `{dow, date, dish, portions, ing:[[tên,SL],...]}` | Ngày tiếp theo (Thực đơn) |
| `LOGS` | `{dow, date, iso, today?, ic, dish, status, steps:[[ic,tên,giờ,state,ghichú],...]}` | Nhật ký; `status`='done'/'progress'; step state='done'/'cur'/'todo' |
| `STEPS` | `{ic, t, time, desc:[...], done, at}` | 6 bước báo cáo (mutable — `done`/`at` cập nhật khi hoàn thành) |
| `CATALOG` | `[{cat, items:[{id,ic,name,unit,sup,price}]}]` | Danh mục đặt hàng (shop) |
| `ITEM` | map `id → item` | Dựng từ CATALOG |
| `ORDERS` | `{id, date, deliver, status, lines:[[id,qty],...]}` | Đơn (shop); status='pending'/'delivered' |
| `PAYMENTS` | `{date, method, amount, note}` | Lịch sử thanh toán (shop) |
| `DEBT` | số | Công nợ hiện tại (mutable) |
| `cart` | `{id: qty}` | Giỏ hàng in-memory |

**Hàm tiện ích:**
- `vnd(n)` → `n.toLocaleString('vi-VN')+'₫'`.
- `cartCount()`, `cartTotal()`, `orderTotal(o)`.
- Chụp ảnh: `snap()` — `<input type=file accept=image/* capture=environment>` + `URL.createObjectURL` (giữ nguyên, đừng thay getUserMedia).

**6 bước quy trình chuẩn** (đồng bộ 3 app): 📦 Giao nhận thực phẩm (06h00–06h45) → 🔪 Sơ chế-rửa (06h45–07h30) → 🍲 Chế biến (07h45–09h45) → 🍱 Lưu mẫu-chia ăn-bảo quản-vận chuyển (09h45–10h45) → 🍽️ Giao nhận suất ăn (10h45–11h00) → 🍎 Giao nhận, chia quả chiều (13h00–13h30).

---

## 7. Pattern JS chính

| Pattern | Hàm |
|---|---|
| Chọn mode login | `setMode(m,btn)` / `login()` / `logout()` |
| Chuyển màn | `show(id)` |
| Tab (kitchen / shop) | `tab(t,btn)` / `tabShop(t,btn)`; helper `navBtn`/`navShopBtn` |
| Modal sheet | `openMenu/openCart/confirmStep`... + `closeModal()` |
| Báo cáo accordion | `renderReport/accItem/accBody/pick(i)/snap/send/confirmStep/completeStep` |
| Giỏ hàng | `inc/dec/renderCartBar/openCart/placeOrder` |
| Feedback | `toast(msg)` — mọi mutate chỉ toast, không persist |

**Quy ước prototype:** không validation; data in-memory reset khi reload; chỉ dependency ngoài là Google Fonts.

---

## 8. Checklist khi code thật (production)

- [ ] Auth thật + phân quyền theo tài khoản trường (hiện nhập gì cũng vào).
- [ ] Upload ảnh báo cáo thật lên server/storage (hiện chỉ `URL.createObjectURL` in-memory).
- [ ] Persist nhật ký/đơn hàng/công nợ qua API; đồng bộ với admin (Kho tổng) & parent.
- [ ] Cân nhắc offline/lỗi mạng tại bếp (mạng yếu) — retry gửi báo cáo.
- [ ] Giữ nguyên ràng buộc accessibility (cỡ chữ, nút ≥56px, ≤3 tab) khi mở rộng.
- [ ] Truy xuất nguồn gốc thật thay cho toast.
- [ ] Giữ tokens màu/spacing/component ở mục 1–4 để đồng nhất nhận diện app Bếp.
