# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 4
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 183 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 183 | 0.823 | 91.8% | 49.7% | 84.7% | 43/174 | 0 | CHƯA ĐẠT |
| 077|120 | 6 | 22 | 0.810 | 90.9% | 50.0% | 86.4% | 3/4 | 0 | CHƯA ĐẠT |
| 077|125 | 9 | 55 | 0.865 | 85.5% | 60.0% | 85.5% | 14/44 | 0 | CHƯA ĐẠT |
| 077|229 | 5 | 24 | 0.763 | 95.8% | 41.7% | 87.5% | 6/37 | 0 | CHƯA ĐẠT |
| 120|125 | 5 | 20 | 0.786 | 95.0% | 40.0% | 85.0% | 6/21 | 0 | CHƯA ĐẠT |
| 120|229 | 9 | 29 | 0.777 | 100.0% | 55.2% | 79.3% | 8/47 | 0 | CHƯA ĐẠT |
| 125|229 | 6 | 33 | 0.868 | 90.9% | 39.4% | 84.8% | 6/21 | 0 | CHƯA ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 472 ({'bo_sot_thua': 153, 'iou_thap': 87, 'sai_lop': 15, 'sai_thuoc_tinh': 105, 'bat_dong_ignore': 97, 'co_can_review': 15}), trên 36 ảnh cần calibration.
