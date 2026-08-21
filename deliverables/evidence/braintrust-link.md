# Braintrust Project Link

**Project:** ai-evaluation  
**URL:** https://www.braintrust.dev/app/ai-evaluation  
**Traces logged:**
- 25 tutor runs (`tutor-run`) — mỗi câu hỏi = 1 trace, gồm input, output, tool calls, tokens, cost
- 25+25+25+25 judge runs (`judge-run`) — 4 vòng calibration, mỗi vòng 25 verdict traces

**Cách xem trace:**
1. Vào project `ai-evaluation` trên app.braintrust.dev
2. Filter theo `name` = `tutor-run` để xem tutor output + tool_calls
3. Filter theo `metadata.scenario_id` để xem từng case cụ thể
4. Xem `metrics` tab để xem tokens, latency, cost từng trace

**Tracing bắt đầu chạy từ vòng v1 của tutor (model: `gemini/gemini-3.5-flash-lite`).**
