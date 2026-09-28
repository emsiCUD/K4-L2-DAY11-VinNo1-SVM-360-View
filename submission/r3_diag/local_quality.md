# Đối chiếu chất lượng cục bộ — rectangle

Teaching reference, không phải gold set đã phê duyệt; không có điểm đạt tự động.
Nguồn: export r1_craft đã khóa SHA256 `467c4a87ae4f08f9d021705f18796e634d8a9ade9729ff073e91dbc779e6d065`; slice `B3-dense`.
Ghép hình học greedy một-một theo IoU ≥ 0.50, rồi so class; H ≥ 40 px.
Box trái nằm chủ yếu trong ignore_region reference không tính. Polygon, polyline, track không được chấm.
Đây là phép tính offline của lab, không phải báo cáo hay kết quả tương đương CVAT Premium.

Frame được tính: adasind_145860.jpg, adasind_167700.jpg, adasind_199770.jpg. Frame thiếu trong export: không.
TP=11; FP=6; FN=9; số lần đối chiếu=25; mean IoU của TP=0.757.

| Chỉ số | Micro | Macro | Nhãn thấp nhất |
|---|---:|---:|---:|
| accuracy | 0.440 | 0.880 | 0.680 |
| precision | 0.647 | 0.700 | 0.250 |
| recall | 0.550 | 0.583 | 0.167 |
| jaccard | 0.423 | 0.482 | 0.111 |
| dice | 0.595 | 0.623 | 0.200 |

| Nhãn | TP | FP | FN | Accuracy | Precision | Recall | Jaccard | Dice |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Bike | 1 | 3 | 5 | 0.680 | 0.250 | 0.167 | 0.111 | 0.200 |
| Car | 1 | 0 | 1 | 0.960 | 1.000 | 0.500 | 0.500 | 0.667 |
| Pedestrian | 3 | 1 | 1 | 0.920 | 0.750 | 0.750 | 0.600 | 0.750 |
| ThreeWheeler | 3 | 1 | 1 | 0.920 | 0.750 | 0.750 | 0.600 | 0.750 |
| Truck | 3 | 1 | 1 | 0.920 | 0.750 | 0.750 | 0.600 | 0.750 |

| Frame | TP | FP | FN | Accuracy | Precision | Recall |
|---|---:|---:|---:|---:|---:|---:|
| adasind_145860.jpg | 2 | 0 | 0 | 1.000 | 1.000 | 1.000 |
| adasind_167700.jpg | 5 | 2 | 4 | 0.500 | 0.714 | 0.556 |
| adasind_199770.jpg | 4 | 4 | 5 | 0.308 | 0.500 | 0.444 |

Confusion matrix: hàng = teaching reference; cột = export đã khóa.
`<missing>` là thiếu box; `<extra>` là box thừa. Xem `local_quality_confusion.csv`.

| Reference \ Export | Bike | Car | Pedestrian | ThreeWheeler | Truck | <missing> |
|---|---:|---:|---:|---:|---:|---:|
| Bike | 1 | 0 | 0 | 0 | 0 | 5 |
| Car | 0 | 1 | 0 | 0 | 1 | 0 |
| Pedestrian | 0 | 0 | 3 | 0 | 0 | 1 |
| ThreeWheeler | 0 | 0 | 0 | 3 | 0 | 1 |
| Truck | 0 | 0 | 0 | 0 | 3 | 1 |
| <extra> | 3 | 0 | 1 | 1 | 0 | 0 |

Chi tiết xung đột trong `local_quality_conflicts.csv`; dữ liệu máy đọc trong `local_quality.json`.
Mismatching label đóng góp một FP cho class vẽ và một FN cho class reference; attribute khác được báo riêng.
Micro accuracy đếm mỗi cặp ghép sai class là một lần đối chiếu; Jaccard đếm cả FP và FN.
Macro/worst bỏ nhãn không xuất hiện ở cả hai phía; chỉ số không có mẫu là N/A.
