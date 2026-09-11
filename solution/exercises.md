# K4 - Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng -
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 - API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 - Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Qua bốn phản hồi, có thể thấy **temperature càng cao thì mức độ đa dạng và sáng tạo của câu trả lời càng tăng**, nhưng đồng thời độ ổn định và chính xác giảm. Ở temperature 0.0–0.5, phản hồi tương đối mạch lạc và kiểm soát được, trong khi 1.0 bắt đầu xuất hiện nhiều thông tin sai lệch và 1.5 cho ra kết quả bị nhiễu, mất tính nhất quán.

### Câu 1.2 - Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**

> Temperature đại diện độ ngẫu hứng, sáng tạo của model 
>
> Temperature thấp -> AI trả lời ổn định, nhất quán, ít sáng tạo. 
>
> Temperature cao -> AI trả lời đa dạng, sáng tạo hơn, nhưng cũng dễ lan man hoặc chọn cách diễn đạt không phù hợp. 
>
> => Chatbot hỗ trợ khách hàng cần độ ổn định, nhất quán và chính xác cao hơn là sự sáng tạo. Vì thế, nên set temperature ở mức 0.1-0.4 tuỳ trường hợp:
>
> 0–0.2: FAQ, tra cứu chính sách, thông tin sản phẩm → rất ổn định.
>
> 0.2–0.4: Customer support tổng quát → cân bằng giữa chính xác và tự nhiên.
>
> Ngoài ra nếu cần độ chính xác cao có thể sử dụng RAG, grounding, guardrails để kiểm soát hallucination 

### Câu 1.3 - Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Với 10.000 user x 3 lần gọi x 350 token đầu ra = **10,5 triệu token output/ngày**. Theo bảng giá trong template: GPT-4o $0,010/1K out -> **$105/ngày (~$3.150/tháng)**; GPT-4o-mini $0,0006/1K out -> **$6,3/ngày (~$189/tháng)**.
>
> => GPT-4o đắt hơn **khoảng 16,7 lần** (tỉ lệ này đúng cho cả input lẫn output).
>
> Đáng dùng GPT-4o: tác vụ suy luận nhiều bước, sai thì thiệt hại lớn -> phân tích hợp đồng, sinh code phức tạp, tư vấn tài chính/pháp lý.
>
> Nên dùng mini: khối lượng lớn nhưng đơn giản -> phân loại ticket, tóm tắt ngắn, trả lời FAQ, trích xuất trường dữ liệu. Thực tế tối ưu nhất là **định tuyến**: mini xử lý mặc định, chỉ đẩy sang 4o khi phát hiện câu khó.

---

## Block 2 - System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 - Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Cùng một câu hỏi nhưng hai persona cho kết quả lệch xa nhau: bản "giáo viên tiểu học" dài **621 từ / 886 token**, bản "chuyên gia tài chính" dài **997 từ / 1.670 token** - gấp gần 2 lần.
>
> Từ vựng khác hẳn: giáo viên dùng hình ảnh đời thường ("cuốn sổ ghi chép", "mỗi khối là một trang", "bé A gửi 5 đồng quà cho bé B"), còn chuyên gia dùng thuật ngữ kỹ thuật (Merkle tree, SHA-256, PoW/PoS, ECDSA, EVM, CBDC, Layer-2 rollups).
>
> Ví dụ minh họa cũng đổi theo persona: một bên là trò đổi quà trong lớp học, một bên là DeFi, token hóa bất động sản và thanh toán xuyên biên giới.
>
> => System prompt không thêm kiến thức mới cho model, mà **định hình cách model chọn lọc và trình bày** kiến thức nó đã có - chi phối độ sâu, từ vựng, loại ví dụ và cả độ dài phản hồi.

### Câu 2.2 - tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Đoạn văn thử nghiệm 105 từ: ước lượng `số từ / 0.75` cho **140 token**, còn `count_tokens` (tiktoken, encoding gpt-4o) đếm được **135 token** -> chênh **-3,6%**. Với tiếng Việt, ước lượng thô này khá sát (sai số dưới 5%).
>
> Nhưng so với tiếng Anh thì khác biệt rõ: tiếng Việt **1,29 token/từ**, tiếng Anh chỉ **1,16 token/từ**; cùng một nội dung, bản tiếng Việt tốn **gấp 1,42 lần** token.
>
> => Nguyên nhân: bộ mã hóa BPE được huấn luyện chủ yếu trên văn bản tiếng Anh, nên từ tiếng Việt - nhất là từ có dấu thanh - bị cắt vụn. Kiểm chứng khi encode thật: `'nghìn'` -> `['ng','h','ìn']` (3 token), `'thang'` -> `['th','ang']` (2 token), trong khi `'thousand'` dài hơn nhưng chỉ tốn 2 token.
>
> Lưu ý: `count_tokens` mặc định lấy `OPENAI_MODEL` (đang là `openai/gpt-oss-20b`) - model này không có trong tiktoken nên rơi vào nhánh fallback `len//4`, cho ra 144 token và kém chính xác hơn. Muốn số thật phải truyền `model="gpt-4o"`.

---

## Block 3 - Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 - Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất khi người dùng **ngồi chờ trực tiếp trước màn hình** và câu trả lời dài - chatbot, trợ lý code, công cụ viết nội dung. Lý do là nó cắt giảm *thời gian chờ cảm nhận được*: thay vì im lặng một lúc rồi đổ ra nguyên khối chữ, token hiện dần nên người dùng biết hệ thống vẫn đang chạy. Thực nghiệm trong lab này cho thấy rất rõ - phản hồi của persona "chuyên gia tài chính" mất **57,9 giây**; nếu không streaming thì đó là gần một phút màn hình trắng, đủ để người dùng tưởng ứng dụng treo và bỏ đi. Ngược lại, non-streaming phù hợp hơn khi **không có ai ngồi chờ** hoặc khi cần nguyên vẹn kết quả trước khi dùng: job chạy nền/batch, API trả JSON cần parse và validate toàn bộ, hoặc khi phải kiểm duyệt nội dung trước lúc hiển thị - vì streaming đã in chữ ra màn hình rồi thì không rút lại được.

### Câu 3.2 - Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Delay cố định giữ nguyên áp lực lên server: nếu nó đang quá tải, hàng nghìn client cứ đều đặn 1 giây lại đập vào một lần, tổng lưu lượng gần như không giảm nên server **không có khoảng lặng nào để hồi phục**.
>
> Exponential backoff giãn khoảng chờ theo cấp số nhân (0.1 -> 0.2 -> 0.4 -> 0.8s...), nên tải retry giảm dần theo thời gian, cho server cơ hội xử lý hết hàng tồn và trở lại bình thường.
>
> => Nếu hàng nghìn client dùng delay cố định giống nhau, chúng sẽ **retry đồng loạt theo từng nhịp** - hiện tượng *thundering herd*: server vừa ngóc đầu dậy lại bị một đợt sóng đồng bộ đánh sập tiếp, tạo vòng lặp không thoát ra được.
>
> Thực tế nên thêm **jitter** (cộng một lượng ngẫu nhiên vào delay), vì exponential backoff thuần vẫn bị đồng bộ nếu tất cả client cùng bắt đầu fail tại một thời điểm.

---

## Block 4 - Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 - Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Persona đang dùng: `"Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn bằng tiếng Việt."`
>
> **"trả lời ngắn gọn"** -> ràng buộc độ dài. Thực nghiệm ở Câu 2.1 cho thấy khi không bị giới hạn, model tự ý viết 600–1.000 từ kèm bảng biểu; với trợ lý CLI thì như vậy là tràn màn hình, khó đọc, lại tốn token và tiền cho mỗi lượt.
>
> **"bằng tiếng Việt"** -> chốt ngôn ngữ đầu ra. Model đang dùng suy luận nội bộ bằng tiếng Anh (thấy rõ trong `reasoning_content` khi debug), nếu không chỉ định rõ nó dễ trả lời lẫn tiếng Anh hoặc pha trộn hai thứ tiếng.
>
> **"trợ giảng thân thiện"** -> đặt tông giọng và phạm vi: giải thích cho người học hiểu, chứ không phán như tài liệu kỹ thuật khô khan.

### Câu 4.2 - Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất: **history bị cắt cứng ở 3 lượt** (`history = history[-6:]`). Trợ lý quên sạch mọi thứ trước đó - sang lượt 5 mà người dùng nhắc lại điều đã nói ở lượt 1 thì nó không hiểu, khiến hội thoại dài bị mất mạch.
>
> Cải thiện đề xuất: thay **cắt theo số lượt** bằng **cắt theo ngân sách token kèm tóm tắt phần bị loại**.
>
> Cách triển khai: mỗi lượt dùng `count_tokens` cộng dồn độ dài history; khi vượt ngưỡng (ví dụ 2.000 token), gom các message cũ nhất gọi thêm một lần API "tóm tắt hội thoại này trong 3 câu", rồi thay chúng bằng một message `{"role": "system", "content": "Tóm tắt trước đó: ..."}`. Như vậy vẫn giữ được ngữ cảnh xa mà khống chế được chi phí.
>
> ---
>
> **Hạn chế thứ hai - không có dữ liệu thời gian thực.** Trợ lý chỉ biết những gì có trong dữ liệu huấn luyện, nên tin tức, giá cả, sự kiện đang diễn ra đều nằm ngoài tầm. Thử thật với câu "Tin tức mới nhất về giá vàng trong nước tuần này?", model trả lời: *"Xin lỗi, hiện tại mình không có dữ liệu thời gian thực"*. Lần này nó từ chối lịch sự, nhưng đó là may mắn - cũng model này đã từng bịa ra địa danh không tồn tại ở Việt Nam.
>
> Cải thiện đề xuất: trang bị **tool calling với hàm `web_search`** để model tự tra cứu khi gặp câu hỏi cần thông tin mới.
>
> Cách triển khai: khai báo tool `web_search(query)` kèm mô tả rõ khi nào nên dùng, truyền vào `tools=` khi gọi API. Model không tự chạy hàm mà chỉ trả về **tên hàm + tham số**; code của mình thực thi tìm kiếm thật (SerpAPI, Tavily...), đẩy kết quả ngược lại dưới message `role="tool"`, rồi gọi API lần hai để model viết câu trả lời dựa trên dữ liệu vừa lấy về.
>
> => Đánh đổi: mỗi lượt hỏi tốn **hai lần gọi API** (chậm và tốn hơn gấp đôi), nhưng câu trả lời được neo vào nguồn thật nên giảm hẳn nguy cơ bịa số.

---

## Danh Sách Kiểm Tra Nộp Bài

- [X] `python grade.py` - xem điểm tự động, mục tiêu ≥ 75/100
- [X] Cả 4 checkpoint pytest đều pass
- [X] Tất cả 9 câu trong file này đã được trả lời
- [X] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
