# NCKH Tin học 10 — PRO V2

## Thay file
1. Teacher: dùng `giaovien_PRO_v2.html` thay cho HTML giáo viên hiện tại.
2. Student: dùng `index_PRO_v2.html` thay cho HTML học sinh hiện tại.
3. Firestore Rules: `firestore_pro_v2.rules` là bản DEVELOPMENT có kiểm tra schema chặt hơn. Chỉ deploy sau khi xác nhận dữ liệu hiện tại đáp ứng schema.
4. Shared UI: giữ `nckh-premium.css` cùng thư mục với hai HTML để cả Teacher/Student dùng chung giao diện premium 2026.
5. AI runtime: giữ `nckh_runtime_config.js` cùng thư mục với `giaovien_PRO_v2.html`; file này chứa cấu hình runtime của Groq và không nên đưa lên repository công khai.

## Luồng Import mới
File → Parse/AI Smart Parse → Chuẩn hóa → Validate → Duplicate Check → AI Review (tùy chọn) → DRAFT → read-back → refresh ngân hàng.

## Luồng AI mới
AI Question Studio → sinh 1–100 câu → Structured Output → validate → loại duplicate nội bộ → Review hoặc lưu DRAFT → kiểm tra duplicate Firestore → read-back → giáo viên duyệt → PUBLISHED → học sinh đọc.

## Firestore nguồn chính
`questions` là collection nguồn chính cho ngân hàng câu hỏi. `questionBank`, `quizzes`, `quiz` chỉ còn được giữ tương thích/đọc trong giai đoạn migration.

## Học sinh
Student chỉ lấy câu hỏi đã `status='published'`, `active=true`, `researchEligible=true`; Practice và Exam dùng `forceRefresh=true` để tránh kẹt cache khi giáo viên vừa publish.

## Kiểm tra
Tất cả JavaScript blocks trong 2 HTML đã được tách và chạy `node --check` thành công.

## Bảo mật production
Project hiện dùng custom username/password và cấu hình AI phía client. Khi public production nên chuyển sang Firebase Authentication và proxy/backend giữ API key.

## UI 2026
- Tái thiết kế typography, spacing, card, navigation, toolbar, inputs, modals, quiz và responsive theo một design system dùng chung.
- Có guardrail hiệu năng: không dùng nền fixed; hạn chế will-change/animation nặng; giữ prefers-reduced-motion.

## Groq
- Teacher tự nạp runtime Groq config và có nút `Test Groq` để health-check ngay trong trình duyệt.
- Model mặc định: `openai/gpt-oss-20b` cho tác vụ nhanh và `openai/gpt-oss-120b` cho tác vụ nâng cao.
- Nếu public website, nên chuyển AI calls sang proxy/backend để bảo vệ secret.
