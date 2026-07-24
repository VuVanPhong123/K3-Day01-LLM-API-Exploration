# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay từng dòng placeholder bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Khi temperature thấp như 0.0, câu trả lời thường ổn định, trực tiếp và ít biến thể giữa các lần gọi. Khi tăng lên 1.0 hoặc 1.5, model có xu hướng dùng cách diễn đạt phong phú hơn, chọn sự thật thú vị đa dạng hơn, nhưng cũng dễ lan man hoặc kém nhất quán hơn.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Với chatbot hỗ trợ khách hàng, tôi sẽ đặt temperature khoảng 0.2-0.4. Mục tiêu chính là trả lời đúng, nhất quán và ít bịa đặt; vẫn để một chút linh hoạt để câu trả lời không quá máy móc.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Output mỗi ngày khoảng 10,000 * 3 * 350 = 10,500,000 token. Với giá output trong lab, GPT-4o khoảng 105 USD/ngày còn GPT-4o-mini khoảng 6.3 USD/ngày, tức GPT-4o đắt hơn khoảng 16.7 lần. GPT-4o đáng dùng cho tác vụ cần lập luận sâu, phân tích pháp lý/kỹ thuật hoặc phản hồi chất lượng cao; mini phù hợp cho FAQ, phân loại, tóm tắt ngắn và tác vụ khối lượng lớn.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Persona giáo viên tiểu học thường trả lời ngắn hơn, dùng từ đơn giản và ví dụ gần gũi như "cuốn sổ chung của cả lớp". Persona chuyên gia tài chính dùng nhiều thuật ngữ hơn như sổ cái phân tán, cơ chế đồng thuận, tính bất biến và rủi ro thị trường. System prompt ảnh hưởng trực tiếp đến giọng điệu, độ sâu, mức kỹ thuật và kiểu ví dụ mà model chọn. Nó giống phần định hướng vai trò trước khi model xử lý câu hỏi cụ thể.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Với đoạn mẫu 92 từ tiếng Việt, `count_tokens(..., model="gpt-4o")` đếm được 109 token, còn công thức thô `số từ / 0.75` cho khoảng 123 token, tức chênh khoảng 11%. Công thức theo số từ chỉ là ước lượng nhanh nên có thể lệch tùy văn bản. Tiếng Việt thường tốn token hơn tiếng Anh cùng độ dài ý nghĩa vì dấu, từ ghép, âm tiết tách rời và cách tokenizer chia các cụm không quen thuộc thành nhiều mảnh nhỏ.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất khi phản hồi dài hoặc người dùng cần cảm giác hệ thống đang làm việc ngay, ví dụ chatbot tư vấn, sinh nội dung, giải thích code hoặc trợ lý CLI. Người dùng có thể đọc dần, dừng sớm hoặc tiếp tục suy nghĩ trong lúc model còn sinh câu trả lời. Non-streaming phù hợp hơn khi cần xử lý kết quả trọn gói trước khi hiển thị, như JSON có cấu trúc, tác vụ batch, đánh giá tự động hoặc khi giao diện chỉ nhận một response hoàn chỉnh.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff giúp giảm áp lực lên API khi hệ thống đang quá tải vì mỗi lần thất bại tiếp theo sẽ chờ lâu hơn trước khi thử lại. Nếu tất cả client đều retry với delay cố định giống nhau, chúng có thể tạo thành các đợt request đồng loạt, làm server vừa hồi phục lại bị quá tải tiếp. Backoff làm nhịp retry thưa dần, tăng cơ hội request sau thành công và lịch sự hơn với hạ tầng.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Persona tôi chọn: "Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn, chính xác, bằng tiếng Việt, ưu tiên ví dụ thực hành và chỉ nêu bước tiếp theo khi cần." Cụm "trả lời ngắn gọn" giúp CLI dễ đọc và giảm token; "bằng tiếng Việt" giữ trải nghiệm nhất quán với người học trong lớp. "Ưu tiên ví dụ thực hành" buộc trợ lý gắn kiến thức API với thao tác code cụ thể.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất hiện tại là history chỉ giữ 3 lượt gần nhất, nên trợ lý dễ quên mục tiêu dài hạn của phiên làm việc. Một cải tiến cụ thể là thêm bộ nhớ tóm tắt: trước khi cắt history, dùng một lời gọi model ngắn để cập nhật `session_summary`, rồi gửi summary này trong system/context message ở các lượt sau. Cách đó giữ chi phí thấp hơn việc gửi toàn bộ lịch sử nhưng vẫn bảo toàn các quyết định quan trọng.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
