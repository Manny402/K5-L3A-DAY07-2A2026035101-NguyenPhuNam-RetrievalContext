# Corpus và benchmark Day 07

Corpus thực tế là 418 chunk B xuất từ Day 06, không phải các file `example_*.json`. Snapshot CPython `v3.13.9` gồm json, csv, pathlib, gzip và logging; 68 đơn vị text, mỗi đơn vị ít nhất 1.500 ký tự. `page` là số đơn vị nguồn trong từng tài liệu, không phải trang PDF.

- `day6_chunks.json`: JSON array schema `id,text,source_id,source,page,metadata`.
- `../artifacts/day6_manifest.json`: hash corpus, model/index reference; bản gốc/manifest/offset đầy đủ ở repo Day 06.
- `../benchmark/questions.json`: 15+2 dev và 5+2 test; câu test có đáp án là các fact khác Day 06 test.
- `../benchmark/absence_checks.json`: lý do thiếu evidence, toàn-corpus term search và giới hạn kết luận.
- `../benchmark/cases.json`: id/split/answerable để tổng hợp metric, không gửi cho agent.

Nguồn: [CPython library docs ở tag v3.13.9](https://github.com/python/cpython/tree/v3.13.9/Doc/library). Bản gốc thuộc Python Software Foundation và các tác giả đóng góp, theo [PSF License v2](PYTHON_LICENSE.txt); ví dụ code cũng có 0BSD. Giữ nguyên license và thông báo nguồn khi chia sẻ.

Text đã bỏ markup RST/heading directive và thụt lề theo pipeline Day 06; điều này có thể mất tên method hoặc context. Day 07 giữ nguyên dữ liệu để so sánh retriever công bằng. Không coi model knowledge là bằng chứng trong corpus.

Schema gold là danh sách evidence thay thế OR, gồm source_id/page/evidence; metric retrieval yêu cầu một chunk đủ evidence, context có thể hợp các source interval liền nhau. Các câu cơ bản không yêu cầu multi-hop có nhãn AND. Câu thiếu evidence có `answerable=false`, `expected_behavior=search_and_abstain` và `absence_reason`. Gold/nhãn chỉ dùng sau retrieval hoặc trong evaluator, không xuất hiện trong payload agent.

`scripts/prepare_benchmark.py` chỉ dùng lúc khởi tạo và từ chối ghi đè benchmark đã tồn tại. `python -m src.run validate` kiểm tra snapshot và evidence; không dùng lại test đã chỉnh cho một tuyên bố holdout độc lập.
