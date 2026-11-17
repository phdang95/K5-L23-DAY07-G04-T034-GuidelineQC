# Báo cáo QC — G04-T034

## 1. Mục đích, dữ liệu và phiên bản

- Mục đích và giới hạn sử dụng: Thực hành đặc tả, gán và QC nhãn bounding box 2D cho người và phương tiện giao thông. Bộ 40 ảnh được tuyển chọn để luyện các ca biên; độ đại diện cho dữ liệu vận hành chưa được đánh giá, nên không dùng kết quả này để khẳng định chất lượng triển khai thực tế.
- Nguồn và phép sử dụng dữ liệu: 40 ảnh từ Wikimedia Commons; ghi công, tác giả và giấy phép từng ảnh trong data/ATTRIBUTION.csv. Giấy phép gồm CC BY, CC BY-SA, CC0 và Public domain; khi phân phối lại cần giữ ghi công và tuân theo giấy phép tương ứng.
- Manifest và checksum nhãn cuối: submission/qc/batch_manifest.json; SHA-256 manifest 142eeff0f65f2436519074aa6e77115aa8b9f2f1fff9f1ca745623d8bdfef31f. Nhãn submission/qc/labels_calibrated.json, SHA-256 f7840eef6008d65de69affee14a308ffe4373f89df5ce091ac6302f09233b7b0. Batch dùng COCO bbox pixel, taxonomy v1.0.
- Guideline và taxonomy khi gán / khi bàn giao: Gán ban đầu theo guideline v1.0; bản bàn giao ghi guideline v1.1 sau calibration. Taxonomy giữ v1.0, không đổi schema hay ngưỡng.

## 2. Phân công và đồng thuận trước calibration

- Lượt hoàn thành / được giao: 80/80 lượt hợp lệ trên 40 ảnh; 4 thành viên (077, 120, 125, 229), mỗi ảnh có 2 người gán.
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: 183 cặp box ghép 1–1; IoU trung bình 0.823; cùng lớp 168/183 = 91.8%. Đồng thuận thuộc tính: occluded_level 49.7%, truncated 84.7%. Các ngưỡng IAA của lab là IoU ≥ 0.85 và cùng lớp ≥ 95%; kết quả trước calibration chưa đạt.
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: 43 box chưa ghép phía A và 174 phía B; 0 cặp bị bỏ qua do ignore_region; báo cáo IAA ghi nhận 97 ca bất đồng vùng ignore trong 472 ca bất thường cần calibration.
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: Đây là rủi ro cần lưu ý, không phải kết luận hai người đã cùng mắc từng lỗi. Ví dụ, ảnh img_001.jpg có box #5 đang là car dù quyết định C006 và guideline yêu cầu xe bán tải là truck; ảnh img_003.jpg còn box trùng #24 dù C029/C035 yêu cầu chỉ giữ một box. Vì vậy cần so nhãn cuối với ảnh và rule, không chỉ dựa vào đồng thuận hoặc quyết định calibration.

## 3. Calibration và kiểm lại

- Ca đã xử lý, rule ID, bản sửa và người xác nhận: calibration_decisions.csv ghi 472 ca C001–C472 trên 36 ảnh, tập trung vào R-01, R-02, R-03, R-04, R-05 và R-07. Người chốt trong log: 077 (158 ca), 120 (48), 125 (60), 229 (206). Tuy nhiên, cả 472 dòng hiện vẫn có phieu_sua=can_sua và kiem_lai=chua_kiem; log chưa chứng minh các sửa đổi đã hoàn tất hoặc được kiểm lại.
- Tác động v1.1 lên nhãn và ảnh: v1.1 làm rõ box ôm phần nhìn thấy và gồm tay lái/gương nhưng không trùng box (R-01), mức che khuất và cắt mép (R-02), xe có thùng bên (R-03), loại tượng/đồ chơi/thân vỏ rời (R-05), và cách xử lý cụm đông/vật nhỏ/vùng mờ (R-07). Các quy tắc này làm rõ cách áp dụng; taxonomy và ngưỡng vẫn là v1.0.
- Tham chiếu bằng chứng khi Rework: Chưa có vòng kiểm lại mới. Bằng chứng hiện có là snapshot vòng 1 tại submission/qc/reviews/round-01/; cần giữ nguyên snapshot này, sửa nhãn theo các ID/vùng được ghi trong qc_review.csv, tạo bản nhãn và manifest mới, rồi mở vòng QC toàn lô tiếp theo.

## 4. Kết quả duyệt

- Vòng QC: 1.
- Người review và ngày: 077, 2026-11-17 (theo mã reviewer trong phiếu QC và thông tin nhóm).
- Đã duyệt / cả lô: 40/40 ảnh. Snapshot ghi mode demo; mẫu gồm 36 ảnh ca_kho và 4 ảnh ngau_nhien, tổng cộng phủ đủ 40 ảnh.
- Ảnh lỗi / ảnh đã duyệt: 6/40 = 15.0%. Trong nhóm ca_kho: 6/36 = 16.7%; nhóm ngau_nhien: 0/4.
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: 8/369 = 2.2% (đếm annotation lỗi theo đối tượng phân biệt; vùng ignore_region không nằm trong mẫu số). Phiếu có 10 phát hiện: 9 dòng gắn object_id trên 8 annotation phân biệt (box #47 có hai lỗi), cộng 1 dòng vật bỏ sót.
- Vật bỏ sót, ảnh và vùng: 1 vật — img_040.jpg, vùng tiền cảnh bên phải, dưới người đội mũ đen; phiếu ghi thiếu box motorcycle theo R-01/C465.
- Ca khó / phần còn lại, lỗi lặp và ví dụ: Phiếu ghi 3 lỗi sai_thuoc_tinh, 3 thua, 2 sai_vi_tri_kich_thuoc, 1 sai_lop và 1 vật bỏ sót. Có hai lỗi thuộc tính cắt mép theo R-02 (#23 và #47), cần kiểm soát lại quy tắc này. Ví dụ: #5 ở img_001.jpg sai lớp; #23/#24 ở img_003.jpg sai truncated và trùng box; #42/#47 ở img_005.jpg sai thuộc tính/ranh giới; #245 ở img_024.jpg là box cần loại theo C289/C290; #298 ở img_031.jpg cao 19.07 px, dưới ngưỡng 20 px; #381 ở img_040.jpg không ôm sát bus. Tự động tổng hợp ghi nhận 6 ảnh lỗi và trạng thái HOLD.
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: Có thể phát hiện box trùng chính xác #22/#24 trong img_003.jpg bằng so sánh tọa độ/lớp; lỗi này cần được loại bỏ. Lỗi phân biệt car/truck ở #5 img_001.jpg cần người đối chiếu ảnh với định nghĩa lớp và C006; kiểm tra schema đơn thuần không xác nhận được lớp thực tế.

## 5. Quyết định bàn giao

- Quyết định: Hold.
- Lý do, người nhận và phạm vi sử dụng: qc_result.json ghi HOLD. Vòng có mode demo, và tỷ lệ ảnh lỗi 15.0% vượt ngưỡng 2%; do đó chưa đạt điều kiện bàn giao, dù danh sách vòng này có đủ 40 ảnh. Chuyển cho nhóm G04-T034/coach để sửa và kiểm tra; phạm vi hiện tại chỉ là thực hành Day 07, không phải dữ liệu vận hành đã được chứng nhận.
- Nếu còn việc: Điều phối viên 229 phối hợp sửa nhãn theo 10 dòng lỗi và kiểm tra trạng thái 472 quyết định calibration; reviewer cuối 077 xác nhận lại. Tạo batch manifest mới, mở vòng QC toàn lô mới, cập nhật báo cáo và kết quả QC sau khi các sửa đổi được kiểm chứng.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: img_001.jpg #5 → C006/R-01; img_003.jpg #23/#24 → R-02 và C029/C035/R-01; img_005.jpg #42/#47 → C062/C059/C064; img_024.jpg #245 → C289/C290; img_031.jpg #298 → R-07/ngưỡng 20 px; img_040.jpg #381 và vùng bỏ sót → R-01/C464/C465. ID và ghi chú chi tiết nằm trong submission/qc/reviews/round-01/qc_review.csv; ảnh, nhãn, manifest và snapshot calibration cùng vòng nằm trong thư mục round-01.
