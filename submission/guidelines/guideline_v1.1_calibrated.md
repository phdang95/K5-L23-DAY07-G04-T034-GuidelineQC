# Guideline gán nhãn — v1.1 — Nhóm G04-T034

- Phiên bản: v1.1 (sau Calibration — Pha 5)
- Ngày: 2026-11-16
- Người viết / duyệt: Nhóm G04-T034 (`229`, `077`, `120`, `215`) — Điều phối: Phan Hải Đăng (`229`), Review cuối: Lương Hoàng Bảo Trân (`077`)

## 1. Mục tiêu
Bộ nhãn dùng để huấn luyện mô hình **Object Detection 2D (bounding box)** phát hiện **người và phương tiện giao thông** trên ảnh đường phố châu Á, phục vụ camera phân tích lưu lượng giao thông và hệ thống hỗ trợ lái (ADAS).
- **Đầu ra mô hình:** Lớp đối tượng, tọa độ box ôm sát phần nhìn thấy, và trạng thái hiển thị (`occluded_level`, `truncated`); không suy luận danh tính hay biển số.
- **Sai sót nguy hiểm:** Bỏ sót (`False Negative`) người hoặc phương tiện đang lưu thông; gán nhầm vật giả (tượng, ma-nơ-canh, đồ chơi, hình in, phản chiếu gương) thành vật thật; hiểu khác nhau giữa các người gán làm tụt IAA.

## 2. Lớp và thuộc tính

### Lớp (mỗi vật thật thuộc đúng một lớp)
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con chở người cỡ nhỏ đến trung (sedan, hatchback, SUV, MPV, taxi), kể cả ô tô con đang được xe khác chở. | `img_006.jpg` (taxi trắng), `img_002.jpg` (8 ô tô con trên sàn xe vận tải). | Không gồm xe mô hình/đồ chơi (`img_038.jpg`), tượng (`img_027.jpg`), xe tải van (`van`) hay xe bán tải (`truck`). |
| `truck` | Xe tải hoặc xe vận tải có cabin và thùng/sàn chở hàng riêng, gồm xe chở ô tô và xe bán tải (kể cả bán tải gắn nắp thùng cao). | `img_001.jpg`, `img_002.jpg` (xe vận tải chở ô tô), `img_037.jpg` (bán tải nắp thùng cao). | Không gồm xe tải van liền thân (`van`), tượng xe bán tải (`img_027.jpg`). |
| `van` | Xe thùng kín cỡ nhỏ/trung có khoang lái và khoang sau liền khối (chở hàng hoặc chở khách kiểu minibus 10–16 chỗ). | `img_005.jpg` (xe bạc phía sau), xe van giao hàng, Transit 16 chỗ. | Không gồm xe buýt cỡ lớn (`bus`), SUV (`car`), hay xe bán tải nắp thùng cao (`truck` ở `img_037.jpg`). |
| `bus` | Xe buýt / xe khách cỡ lớn (> 16 chỗ), thân dài, nhiều cửa sổ. | `img_009.jpg`, `img_031.jpg`, `img_040.jpg`. | Không gồm xe khách 16 chỗ/minibus (`van`) hay hình xe buýt trên biển quảng cáo. |
| `motorcycle` | Phương tiện cơ giới 2 bánh có động cơ (xe máy, xe tay ga, mô tô). | `img_005.jpg`, `img_026.jpg`, `img_030.jpg`, `img_022.jpg` (mô tô thùng bên). | Không gồm xe đạp (`bicycle`), xe ba bánh (`tuk_tuk`, `cyclo`), không bao luôn người ngồi vào box xe. |
| `tuk_tuk` | Xe cơ giới 3 bánh có động cơ, có mái che hoặc khoang chở khách/hàng phía sau. | `img_004.jpg`, `img_016.jpg`, `img_035.jpg`. | Không gồm xe máy 2 bánh (`motorcycle`) và xích lô đạp không động cơ (`cyclo`). |
| `bicycle` | Xe 2 bánh không có động cơ, di chuyển bằng sức người đạp (kể cả xe đạp chở hàng). | `img_006.jpg` (xe đạp rìa phải), `img_010.jpg`. | Không gồm xe máy (`motorcycle`) và xe ba bánh (`cyclo`). |
| `cyclo` | Xe 3 bánh không có động cơ, đạp bằng sức người, có khoang chở khách hoặc hàng. | `img_023.jpg`, `img_034.jpg`, `img_039.jpg`. | Không gồm xe 3 bánh gắn máy (`tuk_tuk`) và xe đạp 2 bánh (`bicycle`). |
| `person` | Con người thật đang đi bộ, đứng, ngồi hoặc điều khiển phương tiện (giữ 1 lớp chung, không tách vai trò). | `img_001.jpg`, `img_003.jpg`, `img_005.jpg`, `img_014.jpg`, `img_026.jpg`. | Không gồm tượng (`img_011.jpg`, `img_017.jpg`), ma-nơ-canh (`img_033.jpg`), người trên biển quảng cáo (`img_018.jpg`), hay ảnh trong gương (`img_020.jpg`). |
| `ignore_region` | Vùng bỏ qua (không phải vật thể): đám đông dày đặc chồng lấn, cụm nhiều vật quá nhỏ (`< 20 px`), hoặc vùng ảnh mờ/nhòe (`blur`) không thể nhận diện chắc chắn vật thể. | `img_021.jpg` (cụm xe máy dày đặc phía xa), vùng chuyển động mờ nhòe nặng (`blur`). | Không dùng để che 1–2 vật đứng riêng lẻ nhìn rõ và cao ≥ 20 px; không dùng thay cho quy tắc loại tượng/gương. |

- **Vật không thuộc lớp nào** (tượng, ma-nơ-canh, đồ chơi/mô hình, hình in/biển quảng cáo, ảnh phản chiếu qua gương, hàng hóa rời): **Không gán box** và **không** phủ `ignore_region`.

### Thuộc tính
| Thuộc tính | Giá trị cố định | Nghĩa & Ngưỡng đo được |
|---|---|---|
| `occluded_level` | `0` | Không bị che (`0%` diện tích ước lượng bị vật khác che). |
| | `1` | Che nhẹ: bị vật khác che **`1% – 30%`** diện tích ước lượng toàn vật. |
| | `2` | Che vừa: bị vật khác che **`31% – 70%`** diện tích ước lượng toàn vật. |
| | `3` | Che nặng: bị vật khác che **`> 70%`** nhưng vẫn nhận diện chắc chắn lớp của vật. *(Nếu > 70% mà không nhận diện được lớp → không gán box, đưa vào `ignore_region` nếu là cụm).* |
| `truncated` | `no` | Toàn bộ vật nằm trong khung ảnh (không bị cạnh ảnh cắt). |
| | `yes` | Một phần thân vật bị cắt trực tiếp bởi ít nhất 1 cạnh khung ảnh (không tính bị che bởi vật khác). |
| `can_review` | `false` / `true` | Tick `true` khi gặp ca mơ hồ về lớp, ranh giới box hoặc ngưỡng che (±5%) để đưa ra Calibration (Pha 5). |

## 3. Quy tắc vẽ box
| Rule ID | Tên quy tắc | Nội dung chuẩn |
|---|---|---|
| `R-01` | **Box ôm sát phần nhìn thấy** | Box khít rìa ngoài cùng phần pixel thực sự nhìn thấy của vật (bao gồm tay lái và gương chiếu hậu gắn liền trên xe máy/xe đạp/xích lô); không đoán vẽ ra ngoài phần bị che khuất hay ngoài mép ảnh; không vẽ trùng 2 box cho cùng 1 vật. |
| `R-02` | **Che khuất & Cắt mép** | Chấm `occluded_level` (`0, 1, 2, 3`) theo tỷ lệ diện tích bị vật khác che; không tính phần cắt mép ảnh là che khuất. Vật bị cắt bởi cạnh ảnh đặt `truncated = yes`. |
| `R-03` | **Người đi xe máy / xe đạp** | Tách box riêng: **1 box phương tiện** (`motorcycle` / `bicycle` / `tuk_tuk` / `cyclo`, chỉ ôm xe + tay lái/gương, với mô tô thùng bên ôm cả thân xe và thùng bên; không ôm hàng hóa rời quá khổ) + **1 box `person`** cho **mỗi người** ngồi trên xe (kể cả người ngồi trong thùng bên). Cho phép box chồng lấn nhau. |
| `R-04` | **Xe chở xe** | Vẽ **1 box `truck`** cho xe vận tải đang chở, và vẽ **box `car` riêng** cho từng ô tô con nhìn thấy trên thùng có chiều cao `≥ 20 px` (`img_002.jpg`: 1 `truck` + 8 `car`). Nếu cụm xe trên thùng `< 20 px` không tách được → phủ `ignore_region`. |
| `R-05` | **Vật không thật** | Tuyệt đối **không gán** tượng, ma-nơ-canh, đồ chơi/mô hình, hình in trên biển quảng cáo/thân xe, thân vỏ xe rời không thể vận hành. |
| `R-06` | **Loại gương & Xe chủ (Ego)** | **Có gương là loại gương:** Không gán bất kỳ vật nào chỉ xuất hiện qua ảnh phản chiếu trong gương chiếu hậu/kính (`img_020.jpg`), và không gán các bộ phận của xe chủ gắn camera (nắp capo, gương, khung kính). |
| `R-07` | **Vật nhỏ `< 20 px`, Đám đông & Vùng mờ (`blur`)** | Vật đơn lẻ cao `< 20 px` → bỏ qua không gán. Cụm nhiều vật `< 20 px`, đám đông chồng lấn dày đặc, hoặc vùng ảnh mờ/nhòe (`blur`) không nhận diện chắc chắn ranh giới/lớp → vẽ 1 `ignore_region` bao cụm đó; vật rõ ở tiền cảnh vẫn tách box riêng. |
| `R-08` | **Người ngồi trong ô tô** | Chỉ gán `person` nếu nhìn thấy đủ rõ phần đầu/thân qua kính hoặc cửa mở (`≥ 20 px`); không suy đoán người bị khuất hoàn toàn trong cabin. |
| `R-09` | **Bán tải nắp thùng cao** | Chốt gán **`truck`** cho xe bán tải gắn nắp thùng cao (`img_037.jpg`). |
| `R-10` | **Khóa guideline trong Pha 3** | Từ khi chốt `v1.0` tới hết Pha 3 không tự ý sửa quy tắc; ca lưỡng lự tick `can_review = true`. |

## 4. Ca khó đã thống nhất
| Ảnh | Tình huống | Rule ID | Quyết định thống nhất | Lý do |
|---|---|---|---|---|
| `img_002.jpg` | Xe tải chở 8 ô tô con trên 2 sàn | `R-04` | Gán **1 `truck` + 8 `car`** riêng (các xe ≥ 20 px) | Mô hình cần phát hiện cả xe chở và các xe con nhìn thấy trên thùng |
| `img_004.jpg`, `img_016.jpg`, `img_035.jpg` | Tuk-tuk / xe ba bánh có động cơ | `R-03` | Gán lớp **`tuk_tuk`** (chỉ ôm xe, không ôm hàng rời quá khổ) | Tách riêng khỏi `motorcycle` 2 bánh và `cyclo` không động cơ |
| `img_006.jpg`, `img_010.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg` | Xe đạp và xích lô | `R-03` | Xe đạp → **`bicycle`**; xích lô → **`cyclo`**; người đi xe → **`person`** riêng | Phân biệt phương tiện không động cơ với `motorcycle` và `tuk_tuk` |
| `img_009.jpg`, `img_031.jpg`, `img_040.jpg` | Xe buýt cỡ lớn | `R-01` | Gán lớp **`bus`** | Tách riêng khỏi `van` (10–16 chỗ) |
| `img_037.jpg` | Xe buýt và bán tải nắp thùng cao | `R-09` | Xe buýt → `bus`; bán tải nắp thùng cao → **`truck`** | Thống nhất 1 lớp duy nhất cho bán tải, tránh lẫn `van`/`truck` |
| `img_011.jpg`, `img_017.jpg`, `img_018.jpg`, `img_027.jpg`, `img_033.jpg`, `img_038.jpg` | Tượng, ma-nơ-canh, đồ chơi/mô hình, hình in quảng cáo | `R-05` | **Không gán** (không dùng `ignore_region`) | Không phải thực thể giao thông thật |
| `img_020.jpg` | Ảnh phản chiếu trong gương chiếu hậu | `R-06` | **Loại gương — không gán** | Ảnh ảo phản chiếu và bộ phận xe chủ không thuộc phạm vi phát hiện |
| `img_014.jpg` | Người đi bộ bị mờ chuyển động (`blur`) | `R-01`, `R-07` | Người còn nhận diện chắc chắn → gán `person` phần nhìn thấy; vùng/người mờ nhòe nặng không rõ ranh giới → khoanh `ignore_region` | Tránh bỏ sót người rõ và tránh lệch IoU ở vùng quá mờ |
| `img_021.jpg`, `img_030.jpg` | Đám đông xe máy dày đặc, che khuất | `R-07` | Tách box tiền cảnh nhìn rõ (`occluded_level` 1–3); cụm chồng lấn/`< 20 px` phía sau → `ignore_region` | Cân bằng giữa không bỏ sót vật rõ và không ép gán vùng bất khả thi |

## 5. QC và phiên bản
- **Ngưỡng QC (`submission/taxonomy.json`):** IoU trung bình cặp ghép 1-1 `≥ 0.85`; đồng thuận lớp `≥ 95%`; IoU từng box `≥ 0.85` (cao `≥ 50 px`) hoặc `≥ 0.70` (cao `20–50 px`); vật tối thiểu `20 px`; tỉ lệ ảnh lỗi để Release `≤ 2%`.
- **Phân quyền quyết định:** Ca mơ hồ trong Pha 3 tick `can_review = true`, tổng hợp quyết định tại buổi Calibration (Pha 5) và nâng cấp lên `guideline_v1.1_calibrated.md`.

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0 | Bản khởi đầu của giảng viên (6 lớp gốc) | Thiếu định nghĩa ranh giới lớp, thiếu ngưỡng đo thuộc tính, thiếu quy tắc ca biên |
| v0 → v1.0 | Thống nhất từ 4 bản cá nhân (`077`, `120`, `215`, `229`):<br/>• Giữ 1 lớp `person`, chuẩn tên `motorcycle`.<br/>• Thêm `bus`, `tuk_tuk`, `bicycle`, `cyclo`.<br/>• Chốt `occluded_level` 4 mức (`0, 1, 2, 3`), `truncated` (`no`/`yes`).<br/>• Chốt xe chở xe (`1 truck + 8 car`), `img_037` = `truck`, loại gương (`img_020`), loại vật giả, ngưỡng `< 20 px`. | Giải quyết toàn bộ bất đồng Pha 1 trên `img_002`, `img_004`, `img_006`, `img_009`, `img_011`, `img_014`, `img_016`–`024`, `img_027`, `img_030`, `img_031`, `img_033`–`040` |
| v1.0 → v1.1 | Làm rõ quy tắc sau Calibration (`472` ca `C001–C472` trên `36` ảnh):<br/>• `R-01`: Xe máy / xe đạp / xích lô **bo cả tay lái và gương chiếu hậu**; không để box trùng lặp.<br/>• `R-03`: Mô tô có thùng bên (`img_022`) gán `motorcycle` ôm cả xe + thùng bên và tách `person` ngồi trong thùng.<br/>• `R-05`: Xóa sạch 16 box xe mô hình trong tủ kính (`img_038`) và thân vỏ rời (`img_024`).<br/>• `R-02`: Thống nhất lại ngưỡng `occluded_level` và bắt buộc `truncated = yes` khi chạm mép ảnh (`x=0`, `y=0`, `x=960`). | `C001–C472` trên 36 ảnh bị cờ (`flagged_images.txt`) |
