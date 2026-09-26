# NCKH Tin học 10 — Pro V2

## Mục tiêu
Ổn định luồng dữ liệu Teacher → Firestore → Student, sửa Import không render sau khi ghi, bổ sung AI Question Studio và tinh gọn điều hướng.

## Teacher dashboard
- Chuẩn hóa `questions` thành nguồn dữ liệu chính.
- Import: parse → normalize → validate → duplicate-check → optional AI review → batch write → read-back verify → Firestore refresh.
- Câu hợp lệ được chọn mặc định khi parse; commit có fallback an toàn nếu selection bị rỗng.
- Duplicate-check không còn phụ thuộc checkbox selection.
- Sửa logic AI Review để object `{duplicate:false}` không bị coi là duplicate.
- Sau khi import thành công, tải lại ngân hàng trực tiếp từ Firestore.
- Thêm tab `AI tạo câu hỏi` với:
  - Topic, difficulty, Bloom, style, số lượng 1–100.
  - Hướng dẫn bổ sung.
  - Progress, thống kê valid/review/duplicate.
  - Preview 4 đáp án và đáp án đúng.
  - Đưa sang Review hoặc lưu DRAFT.
  - Kiểm tra duplicate nội bộ và duplicate với Firestore trước khi lưu trực tiếp.
- Dùng Structured Output JSON Schema khi gọi Groq cho luồng tạo câu hỏi, giữ dữ liệu có cấu trúc ổn định.
- Tái chia sidebar: Dashboard / Lớp học / Học sinh / Ngân hàng / AI / Import / Phân tích / Ma trận / Nghiên cứu / Leaderboard / Settings / Diagnostics.
- Sửa command palette để trỏ đúng tab sau khi đổi thứ tự menu.
- Giữ thiết kế Ocean/Summer Blue, dark mode, responsive.

## Student dashboard
- Học sinh chỉ đọc `questions` đã publish + active + researchEligible.
- Practice và Exam bật `forceRefresh` để tránh kẹt dữ liệu cache khi giáo viên vừa publish.
- Cache question giảm xuống 15 giây.
- Bố cục sidebar: Học tập / Tiến bộ cá nhân / Cộng đồng.
- Mobile bottom navigation được đồng bộ 5 mục: Tổng quan / Luyện tập / Thi thử / Lộ trình / Hồ sơ.
- Sửa liên kết Hồ sơ trong avatar menu sau khi đổi thứ tự navigation.
- Giữ tách biệt AI teacher và AI fallback student: student không tự sinh câu hỏi thay Firestore.

## Firestore Rules
`firestore_pro_v2.rules` tăng kiểm tra schema cho collection `questions`: 4 option, correctAnswer 0–3, topic TH1–TH6, difficulty, status, active, teacherApproved, researchEligible và tính nhất quán khi publish.

## Kiểm tra
- Đã extract toàn bộ `<script>` blocks của cả 2 HTML và chạy `node --check` thành công.
- Không có lỗi cú pháp JavaScript ở các script block đã kiểm tra.
- Đây là kiểm tra tĩnh; cần chạy thử thực tế trên Firebase project để xác nhận quyền, dữ liệu hiện hữu và API key.

## Lưu ý bảo mật
App hiện dùng custom username/password và client-side AI configuration. Rules V2 vẫn là DEVELOPMENT. Trước khi public production, chuyển sang Firebase Authentication + backend/proxy cho API key.


## 2026 UI Upgrade — NCKH Premium
- Thêm `nckh-premium.css` dùng chung cho Teacher + Student để thống nhất typography, spacing, navigation, cards, controls, quiz, dark mode và responsive.
- Chuyển heading sang Space Grotesk, body sang Inter/Manrope; tinh chỉnh line-height, density và hierarchy.
- Giảm hiệu ứng nặng (`background-attachment: fixed`, `will-change` dư thừa, animation app-level) để cải thiện cảm giác mượt.
- Giữ nguyên class/ID nghiệp vụ và hành vi hiện có; không thay đổi pipeline Firestore/IRT.

## 2026 Groq Runtime Upgrade
- Thêm `nckh_runtime_config.js` cho Teacher để tách runtime AI config khỏi HTML.
- Runtime bootstrap có versioning và cập nhật key cũ trong localStorage một lần.
- Thêm `Test Groq` health-check ngay trong Cài đặt & AI, ghi kết quả vào AI Log.
- Sửa 2 lỗi duplicate ID của dashboard giáo viên: `streak-analysis` và `ai-gen-review`.
- QA: Node syntax check PASS, CSS parse PASS, duplicate IDs = 0, local assets = OK.
