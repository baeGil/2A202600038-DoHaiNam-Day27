## 1. Tổng quan
- Họ tên học viên: Đỗ Hải Nam
- MSSV: 2A202600038

Dự án đã hoàn thiện một quy trình review pull request bằng LangGraph, trong đó:

- Agent đọc PR từ GitHub.
- LLM phân tích diff và sinh review có cấu trúc.
- Hệ thống route theo confidence.
- Với trường hợp cần người duyệt, graph sẽ pause bằng `interrupt()`.
- Với trường hợp confidence thấp, hệ thống sẽ escalate bằng câu hỏi cụ thể rồi synthesize lại review.
- Mọi bước quan trọng được ghi vào audit trail có cấu trúc.
- Toàn bộ workflow có thể chạy qua CLI và qua giao diện Streamlit.

## 2. Các yêu cầu của lab đã được đáp ứng

### 2.1 Confidence-based routing

Đã triển khai graph để route theo 3 nhánh:

- `auto_approve` khi confidence > 72%
- `human_approval` khi confidence từ 58% đến 72%
- `escalate` khi confidence < 58%

### 2.2 Human-in-the-loop approval

Đã triển khai pause/resume bằng:

- `interrupt(payload)`
- `Command(resume=...)`
- `MemorySaver` cho luồng CLI của Exercise 2

Người duyệt có thể:

- approve
- reject
- edit

### 2.3 Escalation với reviewer Q&A

Đã triển khai flow cho PR rủi ro thấp:

- LLM tạo `escalation_questions`
- hệ thống hỏi reviewer các câu cụ thể
- reviewer trả lời
- LLM synthesize lại review dựa trên câu trả lời

### 2.4 Structured SQLite audit trail

Đã triển khai audit trail có cấu trúc:

- dùng `AuditEntry`
- ghi vào `audit_events`
- có `AsyncSqliteSaver` để resume graph
- có replay bằng `python -m audit.replay`

### 2.5 Streamlit approval UI

Đã triển khai giao diện Streamlit cho toàn bộ workflow:

- nhập PR URL
- hiển thị card theo `payload["kind"]`
- resume graph từ UI
- lưu `thread_id` trong `st.session_state`
- có sidebar liệt kê recent sessions

## 3. Mapping giữa rubric và file đã triển khai

| Rubric / yêu cầu | File triển khai |
|---|---|
| Agent đọc PR, phân tích, propose comments | [`exercises/exercise_1_confidence.py`](/Users/AI/Vinuni/2A202600038-DoHaiNam-Day27/exercises/exercise_1_confidence.py) |
| Confidence-based routing | [`common/schemas.py`](/Users/AI/Vinuni/2A202600038-DoHaiNam-Day27/common/schemas.py), [`exercises/exercise_1_confidence.py`](/Users/AI/Vinuni/2A202600038-DoHaiNam-Day27/exercises/exercise_1_confidence.py) |
| HITL approve/reject/edit bằng interrupt | [`exercises/exercise_2_hitl.py`](/Users/AI/Vinuni/2A202600038-DoHaiNam-Day27/exercises/exercise_2_hitl.py) |
| Escalation với reviewer Q&A | [`exercises/exercise_3_escalation.py`](/Users/AI/Vinuni/2A202600038-DoHaiNam-Day27/exercises/exercise_3_escalation.py) |
| Audit trail + replay session | [`exercises/exercise_4_audit.py`](/Users/AI/Vinuni/2A202600038-DoHaiNam-Day27/exercises/exercise_4_audit.py), [`audit/schema.sql`](/Users/AI/Vinuni/2A202600038-DoHaiNam-Day27/audit/schema.sql), [`audit/replay.py`](/Users/AI/Vinuni/2A202600038-DoHaiNam-Day27/audit/replay.py) |
| Streamlit approval UI | [`app.py`](/Users/AI/Vinuni/2A202600038-DoHaiNam-Day27/app.py) |

## 4. Kiểm tra đã thực hiện

Đã kiểm tra các điểm sau:

- Cú pháp Python bằng `py_compile`
- Import của các module chính
- Trạng thái OpenSpec: tất cả artifacts đã hoàn tất

Các file cấu hình và trạng thái liên quan:

- [`README.md`](/Users/AI/Vinuni/2A202600038-DoHaiNam-Day27/README.md)
- [`.env.example`](/Users/AI/Vinuni/2A202600038-DoHaiNam-Day27/.env.example)

## 5. Ghi chú khi chạy

Để chạy đầy đủ workflow cần có:

- `OPENROUTER_API_KEY`
- `GITHUB_TOKEN` với scope `public_repo`

Tùy chọn:
