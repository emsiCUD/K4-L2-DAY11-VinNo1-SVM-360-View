# Zone table (slice của bạn)

Lệnh `python3 lab11.py model` tự ghi bảng số (cùng cách đếm với `r1_craft/compare.md` và `model_compare.md`); chạy lại lệnh sẽ cập nhật bảng và giữ nguyên phần nhận xét. Bạn chỉ viết mục Nhận xét.

| Zone | n_ref | L missing | L spurious | M missing (`LR_noM` + `R_only`) | M thừa (`LM_noR` + `M_only`) | Lỗi L chính (`what`) |
|---|---:|---:|---:|---:|---:|---|
| center | 9 | 3 | 3 | 3 | 4 | WRONG_CLASS (1) |
| mid | 7 | 4 | 2 | 3 | 4 | MISSING (3) |
| edge | 4 | 2 | 1 | 4 | 3 | BOX_GEOMETRY (1) |

## Nhận xét

- Zone nào người (L) và model (M) gãy nhiều nhất, dẫn số ở bảng trên:
  - **L gãy nhiều nhất ở `mid`**: 4/7 vật reference bị L bỏ lỡ (MISSING 3). Soi từng ca thì phần lớn không phải "không
    thấy vật" mà là box sai hình: xe đạp chở thùng bị box hụt phần trên (167700 L1/R9), xe đạp bị box lố sang người
    đứng cạnh nên rơi vào ignore (167700 L9/R2), hai xe hai bánh gộp một box (199770 L9/R5+R6). Chỉ một ca là bỏ sót
    thật: người dưới mái tôn 199770 R3.
  - **M gãy nặng nhất ở `edge`**: M missing 4/4 vật reference ở rìa (`LR_noM` + `R_only`). Cả hai ThreeWheeler ở rìa
    đều bị model gọi là Truck/Car thay vì bỏ trống, nên "missing" ở đây chủ yếu là sai class chứ không phải không
    phát hiện.
  - `center` có số tuyệt đối lớn (L missing 3, spurious 3) nhưng tỉ lệ thấp hơn vì n_ref = 9; lỗi L chính ở đây là
    WRONG_CLASS: van trắng tôi gán Truck, cả R và M đều Car (R04).
- Giả thuyết vì sao (méo fisheye, box lỏng, thiếu `ego_body`, ...) và giới hạn của slice ba frame:
  - L: slice `dense`, vật chồng nhau (người dắt xe, xe sau xe) → lỗi đến từ **ranh giới box giữa các vật chồng nhau**
    và vùng tối/mờ, không phải từ méo rìa. Hai lỗi class/thiếu vật ở `center` cho thấy vị trí trên ảnh không đoán
    được lỗi.
  - M: 4/4 xe ba bánh model thấy đều bị gán Truck/Car (E4) và model box người lái ego thành Pedestrian (M4) → giả
    thuyết lệch miền: model không có class ThreeWheeler và không biết `ego_body`. Vì xe ba bánh ở slice này hay nằm
    rìa, lỗi class dồn vào zone `edge`; đây là tương quan trong 3 frame, chưa chứng minh méo rìa là nguyên nhân.
  - Reference cũng có lỗi đáng ngờ ở rìa phải (ego_body phủ người đi bộ ở 167700 và xe ba bánh ở 199770), nên một
    phần số `mid`/`edge` phản ánh reference chứ không phải L hay M.
  - Giới hạn: chỉ 3 frame, n_ref = 20, mỗi zone ≤ 9 vật → một ca đổi được tỉ lệ 10–25 điểm %. Ngưỡng IoU cũng ảnh
    hưởng mạnh: `iou_sweep.md` cho L ở `center` còn 6 matched ở 0.5 nhưng chỉ 2 ở 0.7. Không dùng bảng này để kết
    luận zone nào "nguy hiểm" hơn; cần lặp lại trên nhiều slice.
