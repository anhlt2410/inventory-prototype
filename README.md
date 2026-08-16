# inventory-prototype

Demo prototype: hệ thống kho nguyên liệu → bếp trường → phụ huynh.
Static HTML thuần, không build step, không validation — chỉ để hình dung luồng.

## Cấu trúc
- `index.html` — trang landing, mở 3 demo
- `admin.html` — trang quản trị kho (desktop web)
- `kitchen.html` — mobile app Bếp (private, UI chữ to cho người lớn tuổi)
- `parent.html` — mobile app Phụ huynh (public, UI hiện đại)

## Chạy local
```bash
npx serve .
# hoặc mở thẳng index.html
```

## Deploy Vercel
```bash
vercel --prod
```
Hoặc import repo GitHub vào Vercel (framework preset: **Other**, không cần build command, output = root).
