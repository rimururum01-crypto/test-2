# NCKH 2026 — QA / UI Upgrade Report

## Phạm vi kiểm tra
- `giaovien_PRO_v2.html`
- `index_PRO_v2.html`
- `nckh-premium.css`
- `nckh_runtime_config.js`
- `firestore_pro_v2.rules`

## Đã kiểm tra
- JavaScript inline: `node --check` — PASS cho toàn bộ inline script block của cả 2 dashboard.
- Runtime config JavaScript: `node --check` — PASS.
- CSS parser (`tinycss2`) — 0 lỗi cú pháp.
- Duplicate HTML IDs — không còn ID trùng trong cả 2 dashboard.
- Local asset references — không có file cục bộ bị thiếu.
- Groq model IDs hiện dùng: `openai/gpt-oss-20b` và `openai/gpt-oss-120b`.
- Groq live health-check đã được thêm vào dashboard giáo viên: nút `Test Groq` trong Cài đặt & AI.

## Kiểm tra API trực tiếp từ môi trường build
Không thể hoàn tất live request từ môi trường build vì DNS/network của môi trường này không phân giải được `api.groq.com` (curl trả về HTTP 000 / could not resolve host). Vì vậy báo cáo này không ghi nhận giả rằng API key đã được live-verify tại đây.

## Bảo mật
API key được tách khỏi HTML vào `nckh_runtime_config.js` để source giao diện sạch hơn và thay key dễ hơn. Tuy nhiên đây vẫn là ứng dụng client-side: bất kỳ key nào mà trình duyệt sử dụng đều có thể bị người dùng trang xem được. Với public production nên chuyển lệnh gọi Groq sang backend/proxy và giữ secret ở server.

## UI
Lớp `nckh-premium.css` được dùng chung cho 2 dashboard, tập trung vào:
- typography mới: Space Grotesk + Inter/Manrope;
- sidebar/topbar hiện đại, gọn, nhất quán;
- card, button, input, table, modal và quiz đồng bộ;
- light/dark mode;
- responsive desktop/tablet/mobile;
- giảm hiệu ứng nặng và bỏ `background-attachment: fixed` để hạn chế cảm giác lag;
- không thay đổi tên class/ID nghiệp vụ ngoài hai ID bị trùng đã sửa.
