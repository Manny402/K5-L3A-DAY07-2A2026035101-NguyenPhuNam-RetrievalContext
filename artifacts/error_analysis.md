# Phân tích lỗi dev — Day 07

Chỉ dùng dev để phân tích và quyết định thử nghiệm. Không dùng kết quả test để thay đổi pipeline. Nguồn chính: `results/dev_002/agent_traces.json` và `answer_reviews.json`; G3 được đo riêng ở `results/dev_003/`.

## 1. d7_dev_q06 — DictReader và fieldnames

**Trước, G0 dense:** top context gồm ví dụ DictWriter, ví dụ DictReader không truyền fieldnames và phần giới thiệu chung. Agent trả lời “No”/không đủ để khẳng định, trái với nguồn chuẩn CSV unit 3. Answer grade 0.

**Giả thuyết:** lexical matching vào tên API/fieldnames kết hợp semantic có thể tìm đúng đoạn điều kiện thay vì chỉ ví dụ sử dụng.

**Sau:** G1 và G2 đều có đoạn `c3beb030f5062926e6c0823d`: truyền fieldnames thì dòng đầu được giữ trong kết quả, không truyền thì làm header và bị bỏ. Cả hai trả lời đúng, grade 2.

**Giới hạn:** score similarity cao hoặc metadata đúng không chứng minh đúng điều kiện/đúng API; DictReader và DictWriter rất gần nhau.

## 2. d7_dev_q03 — ensure_ascii=False

**Trước, G0:** rewritten query lấy các đoạn JSON unit 9/10 về mặc định ensure_ascii=True và encoding. Không có đoạn đủ về False; câu cuối bị bộ kiểm tra citation chuyển thành abstain. Answer grade 0.

**Giả thuyết:** lexical token ensure_ascii giữ được đoạn mô tả chính xác; RRF kết hợp thứ hạng thay vì cộng score khác đơn vị.

**Sau:** G1/G2 lấy đoạn tương đương hợp lệ ở JSON unit 7 và mô tả parameter ở unit 3, đã được kê khai trước retrieval. Trả lời non-ASCII được xuất nguyên dạng, grade 2.

**Giới hạn:** đây không phải cải thiện do đổi gold sau khi xem kết quả; gold thay thế được chuyển từ audit Day 06 và kiểm chứng trước Day 07.

## 3. d7_dev_q07 — Sniffer.has_header

G1/G2 chỉ có đoạn giới thiệu Sniffer và ví dụ, thiếu đoạn cảnh báo heuristic/false positive/false negative. Trả lời “không bảo đảm” có phần đúng nhưng thiếu bằng chứng trực tiếp và chi tiết được yêu cầu; grade 1. G0 có đoạn “estimate” hỗ trợ nhiều hơn và được judge chấm 2, cho thấy lexical/hybrid không luôn tốt hơn.

**Thử nghiệm G3:** dùng hybrid top-6, context 4.800 ký tự và tối đa ba lượt search; giả thuyết là agent có thể tìm bổ sung hoặc giữ thêm điều kiện. Đây là thay đổi nhiều yếu tố, không quy kết riêng cho top-k hoặc số lượt. Kết quả G3 được lưu nguyên trạng trong metric/trace và báo cáo, kể cả nếu không cải thiện.

## 4. d7_dev_q19 — basicConfig và force=True

G1 trả lời trường hợp đã có handlers nhưng bỏ ngoại lệ `force=True`; citation thứ hai nói về debug(), không chứng minh hành vi trực tiếp của basicConfig. Grade 1. G0/G2 tìm được điều kiện chính xác và được grade 2. Cần đối chiếu từng điều kiện của câu trả lời, không coi citation tồn tại là hỗ trợ ngữ nghĩa.

## 5. d7_dev_absent_csv — trạng thái từ chối và kết luận âm

G0/G2 dùng action `answer` và citation khi nói corpus không nêu hard row maximum; nội dung có câu “insufficient”, nhưng state vẫn là answered và không chứng minh được một kết luận âm trên toàn corpus chỉ từ top-3. Theo protocol explicit abstain, không tính là từ chối đúng. G1 dùng `abstain`, đạt nhãn từ chối đúng.

Điều này có thành phần khác biệt về giao thức, không nên chỉ từ status suy ra toàn bộ nội dung là bịa. Báo cáo giữ cả câu trả lời và rationale để người chấm kiểm tra. Không sửa nhãn để tăng điểm; audit người độc lập có thể đánh giá cách diễn đạt khác.

## 6. Lỗi kỹ thuật ban đầu

`dev_001` dừng do evaluator trả nhãn abstention không đúng contract Boolean. Lượt đó không được dùng trong so sánh hoàn tất. Đã chuyển evaluator sang strict JSON Schema theo loại câu, thêm regression test và ghi raw response/usage/error trước khi chạy `dev_002`. Đây là sửa lỗi định dạng evaluator trên dev, không phải điều chỉnh theo test hoặc tạo nhãn giả.
