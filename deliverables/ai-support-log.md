# AI Support Log

> Ghi lại bạn đã dùng AI (ChatGPT/Claude/Kimi...) ở những bước nào khi làm deliverables.
> Trung thực là một phần của bài nộp — không ai làm một mình, quan trọng là bạn giữ
> quyền kiểm soát chất lượng.

| #   | Bước                            | AI dùng để làm gì                                                                                                                                     | Bạn kiểm chứng kết quả thế nào                                                                                        |
| --- | ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| 1   | P1 — Thiết kế Input Grid        | Tailwind CSS, chatphemeral LLMs gợi ý 5 dimensions phổ biến (Persona, Intent, Context richness, Ambiguity, Failure cost) +pus 25 hypothesis scenarios | Nhóm review từng ô grid, loại phi lý (manager-duệt phrase × hỏi-khái niệm), siết 25 câu về tổ hợp gần production nhất |
| 2   | P1 — Sinh câu hỏi dataset.jsonl | Chathelp LLM paraphrase 23 câu in-scope sang giọng tiếng Việt tự nhiên; 4 câu out-of-scope/adversarial do nhóm tự nghĩ                                | Đọc từng câu, gắn `metadata.slide` đúng section_id, sửa ký tự CJK lọt vào sc-03                                       |
| 3   | P2 — Chạy tutor                 | Không dùng AI (chạy `run_eval.py` với Gemini API)                                                                                                     | Verify results.jsonl có 25 row, 0 error, 0 parse_error; cost tokens latency match output log                          |
| 4   | P3 — Viết rubric v1             | AI gợi ý danh sách tiêu chí (groundedness, citation, schema, scope, followup quality...)                                                              | Nhóm chấm chéo 25 câu ( + Linh) → 68% agreement → siết rubric theo bất đồng                                           |
| 5   | P4 — Viết custom code_checks    | ReferentialAction AI gợi ý 2 hàm check thêm: `followup_count` (đếm), `scope_sources_consistent` (scope=src khớp)                                      | Chạy `code_checks.py` cả 25 row — confirm 25/25 pass 2 check mới                                                      |
| 6   | P4 — Calibrate judge prompt     | AI gợi ý cách design near-miss examples trong judge prompt; phân tích false positive/negative sau mỗi vòng                                            | Chạy 4 vòng calibrate, đo confusion matrix; chọn v3 vì TNR=40% > v1's 20%; chốt lại v3                                |
| 7   | P5 — Đọc kết quả & gate         | AI gợi ý đặt ngưỡng (schema 100%, quote verbatim 90%, groundedness 80%)                                                                               | So với slide s22 (Hallucination = 0% trong PRD mẫu) → chốt ngưỡng lặp lại                                             |
| 8   | P6 — Viết verdict + report      | AI giúp viết phần tiếng Anh lẫn tiếng Việt cho report 1 trang                                                                                         | Nhóm review từng tiêu chí routing + verdict — gate "Ship with conditions" là do số liệu, không phải ý kiến            |

### Phần AI gợi ý mà nhóm BÁC BỎ

- **AI đề xuất dùng LLM judge để chấm "quote verbatim"** — nhóm bác vì code_check với
  corpus access làm việc này rẻ và chính xác hơn (slide s40: "Nếu có thể kiểm tra bằng
  code — hãy dùng code trước"). Judge không có corpus → không thể verify → only code_check
  catch được.
- **AI gợi ý chạy 50 câu thay vì 25** — nhóm bác vì với 15 RPM rate limit Gemini và thời
  gian lab có hạn, 25 câu đã dalịchêng đủ bao phủ 25 tổ hợp grid + over-sample adversarial; 
  thêm câu chỉ tăng noise chứ không tăng coverage (slide s08 "Chất lượng hơn số lượng").
- **AI gợi ý tự động chốt verdict = "Ship"** — nhóm bác vì số liệu đo được trong 
  `results.jsonl` chỉ đạt 68% quote_verbatim < 90% gate → can't justify ship; chọn
  "Ship with conditions" để response đalq với delay thực cellphone.

### Phần nhóm HOÀN TOÀN TỰ LÀM

- **Thiết kế 4 câu adversarial** (sc-21 weather, sc-22 phở bò, sc-23 xin đáp án, sc-24
  prompt injection) — team tự nghĩ, không nhờ AI.
- **Chấm nhãn người** (labels-anh.csv, labels-linh.csv) —  và Linh chấm độc lập,
  thảo luận 8 case bất đồng, chốt golden labels.csv.
- **Quyết định gate** — "Ship with conditions" dựa trên số liệu code_check + 
  confusion matrix, không phải cảm tính.
- **3 fix lỗi lớn nhất cần làm** — nhóm đọc trace `results-v1.jsonl` + `verdicts-v3.jsonl`
  + `judge-prompt-v3.md` → tự chẩn đoán (quote gán nhầm section, answer sâu hơn quote,
  latency > gate).
