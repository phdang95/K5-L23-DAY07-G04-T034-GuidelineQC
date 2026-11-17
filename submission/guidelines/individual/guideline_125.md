# Guideline gán nhãn — v0.1-cá nhân — 125

- Phiên bản: v0.1-cá nhân (nháp độc lập từ v0, chưa thống nhất nhóm)
- Ngày: 16/11/2026
- Người viết / duyệt: 125
- Phạm vi: 40 ảnh `img_001.jpg`–`img_040.jpg`
- Trạng thái: bản cá nhân CP1. Ví dụ lớp đã đối chiếu `data/ATTRIBUTION.csv`; riêng `van` chưa có mẫu chắc chắn nên phải xác nhận bằng mắt khi xem đủ 40 ảnh.

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình phát hiện vật thể 2D (người và phương tiện) trên ảnh đường phố, phục vụ camera giao thông hoặc hệ hỗ trợ lái. Mô hình chỉ cần trả về lớp, hộp bao và các thuộc tính mô tả trạng thái nhìn thấy; không suy luận danh tính, tuổi, giới tính hay biển số.

Lỗi bỏ sót người và phương tiện có thể gây nguy hiểm khi dùng cho lái xe; lỗi thừa box làm tăng chi phí hậu kiểm. Vì vậy guideline ưu tiên quy tắc đo được, ví dụ đúng ảnh và xử lý nhất quán cho ca biên. Ca không chắc phải được đánh dấu `can_review` thay vì tự đoán.

## 2. Lớp và thuộc tính

### Lớp

Mỗi vật thật chỉ thuộc đúng một lớp. `bus` và `tuk_tuk` là **đề xuất bổ sung** cho v1.0, chưa có trong taxonomy v0; nếu taxonomy chưa được cập nhật thì không tự gán các lớp này thành `car`/`truck`/`motorcycle`.

| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con, SUV, hatchback hoặc sedan, kể cả xe con đang được xe khác chở nhưng nhìn thấy đủ thân xe. | `img_002.jpg`: 8 ô tô con nằm trên hai sàn của xe vận tải. | Xe mô hình/đồ chơi `img_038.jpg`; tượng xe bán tải `img_027.jpg`; xe in trên biển quảng cáo hoặc chỉ thấy trong gương (`img_018.jpg`, `img_020.jpg`). |
| `truck` | Xe tải hoặc xe vận tải có cabin và thùng/hệ thống chở hàng riêng, gồm xe chở ô tô. | `img_002.jpg`: xe vận tải chở ô tô; `img_001.jpg`: xe tải có người đi cạnh. | Bán tải gắn nắp thùng cao `img_037.jpg` là ca biên `van`/`truck`, phải `can_review`; tượng xe bán tải `img_027.jpg` không phải xe thật. |
| `van` | Xe thùng kín cỡ nhỏ, khoang chở người/hàng kín và liền với cabin, không có thùng hàng rời phía sau. | Chưa có ảnh nào trong metadata ghi rõ là `van`; phải xác nhận bằng mắt và ghi lại `img_0xx` nếu gặp. Không dùng `img_037.jpg` làm ví dụ chuẩn vì đang tranh chấp. | Bán tải có thùng/nắp thùng cao tách khỏi cabin `img_037.jpg`; xe tải nhỏ có thùng rời `img_036.jpg`; SUV/ô tô con `img_002.jpg`. |
| `bus` | Xe khách/xe buýt cỡ lớn, thân dài, nhiều cửa sổ, chở nhiều người. | `img_009.jpg`, `img_031.jpg`, `img_040.jpg`. | Xe thùng kín cỡ nhỏ `van`; xe tải có thùng hàng `truck`; xe buýt in trên biển quảng cáo. |
| `tuk_tuk` | Xe ba bánh có khoang chở khách hoặc hàng phía sau, khác xe máy hai bánh. | `img_004.jpg`, `img_016.jpg`, `img_035.jpg`. | Xe máy hai bánh; xe ba bánh đạp/xích lô `img_006.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`. |
| `motorcycle` | Xe hai bánh có động cơ, gồm xe máy và mô tô. | `img_005.jpg`, `img_026.jpg`, `img_030.jpg`; `img_022.jpg` là mô tô có thùng bên. | Xe đạp/xích lô không động cơ `img_006.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`; xe mô hình `img_038.jpg`; tuk-tuk ba bánh `img_035.jpg`. |
| `person` | Người thật đang đi bộ, đứng, ngồi, làm việc hoặc điều khiển phương tiện. | `img_001.jpg`, `img_005.jpg`, `img_026.jpg` (người thật, kể cả khi bị che hoặc cắt mép). | Tượng đồng/đá `img_011.jpg`, `img_017.jpg`, `img_027.jpg`; ma-nơ-canh `img_033.jpg`, `img_038.jpg`; chân dung in trên biển quảng cáo `img_018.jpg`; ảnh phản chiếu trong gương `img_020.jpg`. |
| `ignore_region` | Vùng không cần gán từng vật: đám đông dày đặc hoặc cụm vật có chiều cao nhỏ hơn 20 px. | Cụm người/phương tiện rất nhỏ hoặc đám đông không thể tách từng box; chọn đúng vùng bao trùm cụm. | Một người hoặc một xe lớn, rõ ràng vẫn phải gán box riêng, không được che bằng `ignore_region`. |

Vật không thuộc lớp nào:

- Tượng, ma-nơ-canh, đồ chơi, hình in/biển quảng cáo, ảnh phản chiếu: **không gán box**.
- Xe đạp, xích lô không động cơ: **không gán** `motorcycle`; đưa vào bảng ca khó để nhóm quyết định có lớp mới hay không.
- Vật thật nhưng quá nhỏ (< 20 px) hoặc đám đông dày đặc: dùng một `ignore_region` cho cụm, không vẽ từng box.
- Vật thật cần lớp mới (`bus`, `tuk_tuk`) nhưng taxonomy chưa cập nhật: ghi vào ca khó/`image_issues.csv`, không tự gán sang lớp gần nhất.

### Thuộc tính

| Thuộc tính | Giá trị cố định | Nghĩa và ngưỡng đo được |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | `0`: vật bị che 0% (hoặc dưới 1% không đáng kể). `1`: bị che từ 1% đến 30% diện tích ước tính của vật. `2`: bị che từ 31% đến 70%. `3`: bị che trên 70% nhưng vẫn nhận dạng chắc chắn được lớp. Nếu bị che trên 70% và không còn chắc chắn là vật gì, không vẽ box vật đó; dùng `ignore_region` hoặc ghi vấn đề cấp ảnh. |
| `truncated` | `true`, `false` | `true` khi vật tiếp tục ra ngoài khung ảnh và box chạm biên ảnh (khoảng cách tới biên bằng 0 px) tại đúng phía bị cắt. `false` khi toàn bộ vật nằm trong ảnh, kể cả khi box chỉ cách biên 1 px nhưng không bị cắt. Box luôn dừng ở biên ảnh, không vẽ phần ngoài ảnh. |
| `can_review` | tick / không tick | Tick khi thuộc một trong các trường hợp: (1) lớp mơ hồ giữa hai lớp, đặc biệt `van`/`truck` ở `img_037.jpg`;  (2) mức che nằm sát ngưỡng ±5 điểm phần trăm; (3) vật cao 20–30 px hoặc box có nguy cơ ôm sai phần nhìn thấy; (4) ca một vật có nhiều phần như người + xe, xe chở xe, hàng hóa gắn vào xe; (5) vật trông giống tượng/ma-nơ-canh/đồ chơi/ảnh in/phản chiếu nhưng chưa chắc. Không tick mặc định cho mọi box. |

## 3. Quy tắc vẽ box

- **R-01 — Box ôm sát phần nhìn thấy.** Box chỉ bao phần pixel thực sự nhìn thấy của vật, không nới tới phần bị che hoặc phần nằm ngoài ảnh. Các bộ phận rõ ràng thuộc vật và đang nhìn thấy (bánh xe, gương, càng xe, thùng hàng gắn liền) được tính vào box; hàng hóa rời, người đi bộ che trước vật khác thì không tính.
- **R-02 — Che khuất.** Chấm `occluded_level` theo tỷ lệ diện tích ước tính của vật bị vật khác che, không theo tỷ lệ diện tích box bị che. Ghi nhận mức 3 khi vẫn nhận dạng chắc chắn; nếu không nhận dạng được thì bỏ box và dùng `ignore_region`/vấn đề cấp ảnh.
- **R-03 — Cắt mép ảnh.** Box dừng tại biên ảnh và đặt `truncated=true` nếu vật bị cắt. Nếu vật nằm trọn trong ảnh, kể cả sát biên, đặt `truncated=false`.
- **R-04 — Người đi xe máy.** Vẽ hai box riêng cho mỗi người: một `motorcycle` bao toàn bộ xe nhìn thấy (bánh, yên, tay lái, thân xe; không bao cơ thể người lái) và một `person` cho người lái. Hành khách ngồi sau có thêm một box `person` riêng. Hai box được phép chồng lên nhau. Áp dụng cùng quy tắc này cho mọi ảnh có người đi xe máy, kể cả khi người bị che.
- **R-05 — Xe chở xe.** Vẽ một box `truck` cho toàn bộ xe vận tải đang chở. Mỗi ô tô con được chở, nếu nhìn thấy và cao từ 20 px trở lên, được vẽ thêm một box `car` riêng dù nằm trong box `truck`; chấm `occluded_level` theo phần ô tô bị kết cấu xe chở che. Nếu các ô tô con đều nhỏ hơn 20 px và tạo thành cụm, dùng một `ignore_region` cho cụm thay vì vẽ từng box.
- **R-06 — Vật không thật.** Không vẽ box cho tượng, ma-nơ-canh, đồ chơi, hình in trên biển quảng cáo, ảnh dán hoặc phản chiếu trong gương/kính. Chỉ dùng `ignore_region` khi cả vùng đó là cụm chi tiết nhỏ hoặc đám đông gây nhiễu, không dùng để che một tượng/ma-nơ-canh đơn lẻ rõ ràng.
- **R-07 — Xe chủ (ego vehicle).** Không gán bất kỳ phần nào của xe chở camera: nắp ca-pô, gương, ta-lông, dashboard. Vật chỉ xuất hiện qua gương chiếu hậu của xe chủ cũng không được gán như vật thật (`img_020.jpg`).
- **R-08 — Vật quá nhỏ và đám đông.** Vật có chiều cao nhỏ hơn 20 px không vẽ box riêng; nếu chúng tập trung thành cụm hoặc đám đông, vẽ một `ignore_region` bao trùm cụm. Một vật lớn, rõ ràng không được đưa vào `ignore_region`.
- **R-09 — Xe không động cơ.** Xe đạp, xích lô, xe ba bánh đạp (`img_006.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`) không gán `motorcycle`. Đây là ca ngoài taxonomy v0; ghi vào bảng ca khó và chờ nhóm quyết định lớp mới hoặc quy tắc loại bỏ.
- **R-10 — Tuk-tuk và bus.** Đề xuất lớp `tuk_tuk` cho xe ba bánh có khoang chở (`img_004.jpg`, `img_016.jpg`, `img_035.jpg`) và lớp `bus` cho xe khách cỡ lớn (`img_009.jpg`, `img_031.jpg`, `img_037.jpg`, `img_040.jpg`). Không gán các xe này thành `car`, `truck` hoặc `motorcycle` khi taxonomy v1.0 đã có lớp tương ứng.
- **R-11 — Bán tải nắp thùng cao.** Nếu thùng/nắp thùng là khoang kín liền cabin, xét `van`; nếu là thùng hàng rời hoặc nắp thùng cao tách khỏi cabin, xét `truck`. Với `img_037.jpg`, chọn tạm `truck` và tick `can_review` để nhóm chốt ở CP2.
- **R-12 — Một vật có nhiều phần.** Mỗi vật thật được hưởng một box theo lớp của nó. Với người + xe, xe chở + xe được chở, hàng hóa gắn liền với xe, dùng R-04/R-05 và tick `can_review` nếu phạm vi box còn tranh cãi.

## 4. Ca khó

| Ảnh | Tình huống | Rule ID | Phân loại | Quyết định | Lý do |
|---|---|---|---|---|---|
| `img_001.jpg` | Xe tải và người, có che/cắt mép | R-01, R-02, R-03 | Loại theo quy tắc | Box riêng cho xe tải và người; chấm mức che/cắt mép theo phần nhìn thấy | Định nghĩa lớp đã rõ, chỉ cần áp dụng ngưỡng |
| `img_002.jpg` | Xe tải chở 8 ô tô con trên hai sàn | R-05 | Loại theo quy tắc | 1 box `truck` + box `car` cho từng ô tô nhìn thấy, cao từ 20 px | Mô hình phải phát hiện cả xe chở và xe được chở; tránh bỏ sót xe con |
| `img_004.jpg` | Tuk-tuk ba bánh | R-10 | Cần hỏi reviewer | Đề xuất `tuk_tuk`; không gán `car`/`motorcycle` | v0 chưa có lớp phù hợp |
| `img_016.jpg` | Tuk-tuk ba bánh | R-10 | Cần hỏi reviewer | Đề xuất `tuk_tuk`; không gán `car`/`motorcycle` | Cùng vấn đề taxonomy với `img_004.jpg` |
| `img_035.jpg` | Tuk-tuk ba bánh, hàng hóa cao phía sau | R-01, R-10 | Cần hỏi reviewer | Box `tuk_tuk` chỉ ôm phần xe nhìn thấy; không bao hàng hóa rời quá khổ | Cần chốt phạm vi box và lớp mới |
| `img_009.jpg` | Xe buýt cỡ lớn | R-10 | Cần hỏi reviewer | Đề xuất `bus`; không gán `truck`/`van` | v0 chưa có lớp xe buýt |
| `img_031.jpg` | Xe buýt cỡ lớn | R-10 | Cần hỏi reviewer | Đề xuất `bus` | Cùng vấn đề taxonomy |
| `img_037.jpg` | Xe buýt và bán tải gắn nắp thùng cao `Master 75` | R-10, R-11 | Cần hỏi reviewer | Xe buýt: `bus`; bán tải: tạm `truck` + `can_review` | Tranh chấp `van`/`truck`, cần nhóm chốt |
| `img_040.jpg` | Xe buýt cỡ lớn | R-10 | Cần hỏi reviewer | Đề xuất `bus` | Cùng vấn đề taxonomy |
| `img_011.jpg` | Tượng đồng/đá | R-06 | Loại theo quy tắc | Không vẽ box | Không phải người/vật thật |
| `img_017.jpg` | Tượng người cưỡi ngựa | R-06 | Loại theo quy tắc | Không vẽ box `person` | Tượng, không phải người thật; tránh lỗi hai người cùng sai |
| `img_027.jpg` | Tượng xe bán tải | R-06 | Loại theo quy tắc | Không vẽ box `truck` | Vật không thật |
| `img_033.jpg` | Ma-nơ-canh trong tủ kính | R-06 | Loại theo quy tắc | Không vẽ box `person` | Ma-nơ-canh, không phải người |
| `img_038.jpg` | Xe mô hình/đồ chơi trong tủ kính | R-06 | Loại theo quy tắc | Không vẽ box `car`/`motorcycle` | Đồ chơi, không phải xe thật |
| `img_018.jpg` | Chân dung trên biển quảng cáo | R-06 | Loại theo quy tắc | Không vẽ box `person` | Hình in, không phải người thật |
| `img_020.jpg` | Phản chiếu qua gương chiếu hậu | R-06, R-07 | Loại theo quy tắc | Không vẽ box cho ảnh phản chiếu | Phản chiếu và xe chủ không thuộc phạm vi |
| `img_006.jpg` | Xe đạp/xích lô không động cơ | R-09 | Cần hỏi reviewer | Không gán `motorcycle`; chờ nhóm quyết định lớp/loại bỏ | Xe không động cơ ngoài taxonomy v0 |
| `img_023.jpg` | Xích lô/xe ba bánh đạp | R-09 | Cần hỏi reviewer | Không gán `motorcycle`; chờ nhóm quyết định | Cùng vấn đề với `img_006.jpg` |
| `img_034.jpg` | Xích lô/xe ba bánh đạp | R-09 | Cần hỏi reviewer | Không gán `motorcycle`; chờ nhóm quyết định | Cùng vấn đề với `img_006.jpg` |
| `img_039.jpg` | Xe đạp/xích lô không động cơ | R-09 | Cần hỏi reviewer | Không gán `motorcycle`; chờ nhóm quyết định | Cùng vấn đề với `img_006.jpg` |
| `img_029.jpg` | Xe máy chở hàng cồng kềnh | R-01, R-04, R-12 | Cần hỏi reviewer | Gán `motorcycle` + `person`; chỉ bao phần xe và người, không bao hàng hóa rời; tick `can_review` nếu hàng che khuất xe | Phạm vi box giữa xe, người và hàng hóa dễ khác nhau giữa các annotator |
| `img_005.jpg`, `img_026.jpg` | Người và xe bị che/cắt mép ở góc nhìn khác | R-01, R-02, R-03 | Loại theo quy tắc | Vẽ phần nhìn thấy, chấm mức che/cắt mép, không nới box ra ngoài ảnh | Ca chuẩn để kiểm tính nhất quán của R-01–R-03 |

## 5. QC và phiên bản

- Ngưỡng QC lấy từ `submission/taxonomy.json → rules` và không được tự sửa: IoU trung bình của cặp ghép 1–1 ≥ 0,85; đồng thuận lớp ≥ 95%; ngưỡng ghép cặp IoU ≥ 0,5; vật nhỏ hơn 20 px dùng `ignore_region`; tỷ lệ ảnh lỗi để Release ≤ 2%.
- Người quyết ca mơ hồ: nhóm trưởng/người review cuối sau khi họp CP2. Trong CP1, ca mơ hồ được đánh dấu `can_review` hoặc ghi vào bảng ca khó, không tự đoán.
- Bản cá nhân này phải được gửi riêng cho coach/nhóm trưởng trước khi cả nhóm xem chéo. Giữ nguyên bản cá nhân để đối chiếu với `guideline_v1.0_agreed.md`; không sửa lại sau khi họp nhóm.

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0 | Bản khởi đầu của giảng viên | Định nghĩa lớp và thuộc tính còn định tính |
| v0.1-cá nhân | Thêm ngưỡng đo được cho `occluded_level`, `truncated`; thêm ví dụ/phản ví dụ; thêm R-01–R-12; đề xuất `bus`, `tuk_tuk` | `img_002.jpg` (xe chở xe), `img_004.jpg`/`img_016.jpg`/`img_035.jpg` (tuk-tuk), `img_009.jpg`/`img_031.jpg`/`img_037.jpg`/`img_040.jpg` (bus), `img_037.jpg` (bán tải nắp thùng cao), các ảnh tượng/ma-nơ-canh/đồ chơi/phản chiếu |
