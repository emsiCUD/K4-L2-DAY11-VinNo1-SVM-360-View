# Kế hoạch review từ lỗi quan sát được

Từ `findings.csv` và `zone_table.md`, chọn **hai lát cắt của bài ADASIND một camera** cần review trước. Bảng này
giải thích dữ liệu thật bạn vừa làm; nó không thay cho kế hoạch bốn camera giả lập ở `45_sampling_plan.csv`.

| Lát cắt / frame | Số ca và loại lỗi | Vì sao review trước | Bằng chứng cần giữ |
|---|---|---|---|
| **Vật chồng nhau trong cảnh đông** (slice `dense`; ví dụ `adasind_167700.jpg`, `adasind_199770.jpg`) | 167700: 6 dòng `r1_craft` (WRONG_CLASS van→Truck, MISSING xe sau van, BOX_GEOMETRY xe đạp chở thùng, 2 IGNORE_SCOPE); 199770: 3 dòng MISSING/SPURIOUS quanh người dắt xe tay ga và cặp xe ba bánh chồng nhau. 4/4 lỗi `E1` của tôi nằm ở đây | Lỗi của người gán nhãn tập trung ở **ranh giới box giữa các vật chồng nhau** (người dắt xe, xe sau xe), không ở rìa ảnh; `delta.md` cho thấy sửa đúng các ca này tăng matched 11 → 14 | Overlay L/R/M từng frame, dòng findings có `object_ref`, ảnh phóng to vùng chồng; decision log D06–D08 cho ca annotator giữ |
| **Mép phải vòng kính có `ego_body` / vật sát rìa** (`adasind_167700.jpg`, `adasind_199770.jpg`) | 2 dòng `E0` escalate (ego_body của reference phủ người đi bộ và xe ba bánh), 3 box của tôi bị IGNORE_SCOPE, model 0/4 matched ở zone `edge` (4/4 xe ba bánh bị gọi Truck/Car) | Sai phạm vi ignore là mức **P0** (R10): nó làm phép so và thống kê zone không còn đáng tin, che luôn lỗi của model ở rìa | `screenshots/p4_ego_body_ref_167700.png`, `…_199770.png`; ticket 1; dòng `r3_diag` `R4`, `M8`, `M12` |

Giới hạn của kết luận từ ba frame ADASIND: chỉ 3 frame của **một** camera, n_ref = 20, mỗi zone ≤ 9 vật; một ca đổi tỷ
lệ 10–25 điểm %. Teaching reference chính nó có ít nhất 2 lỗi P0 nên số khớp chỉ là độ khớp với một bản nháp, không
phải độ chính xác. Frame liền cảnh (cùng chợ, cùng loại xe ba bánh) nên không được xem là mẫu độc lập; giả thuyết
"model không có class ThreeWheeler" mới dựa trên 4 xe trong 2 frame.

## Chuyển sang kế hoạch bốn camera giả lập

Cách soát độ phủ của 200 frame ở `45_sampling_plan.csv`: (1) chọn frame theo **cảnh/sequence** trước, lấy tối đa
2–3 frame mỗi cảnh và cách nhau đủ xa để không đếm nhiều frame liền nhau như nhiều ca độc lập; (2) mỗi ô camera ×
normal/hard có checklist thẻ ca khó (vật chồng nhau, vật sát `ego_body`/rìa vòng kính, xe ba bánh/xe đặc thù địa
phương, người trong vùng tối, lóa, seam) và đếm số frame đã phủ từng thẻ; ô nào có thẻ < 3 frame thì bổ sung từ
cảnh khác; (3) giữ trường `camera_id`, sequence, timestamp, calibration_version cho mỗi frame để còn truy lại.
Kế hoạch này chỉ giúp **tìm ca cần soi**: frame hard được chọn có chủ đích (oversample ca khó), nên tỷ lệ lỗi đo trên
200 frame không đại diện cho 50.000 frame; muốn ước lượng tỷ lệ lỗi thật cần thêm một mẫu ngẫu nhiên phân tầng
theo camera và cân trọng số theo tỷ lệ thật của từng tầng.
