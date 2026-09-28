# Tự soát

Slice **B3-dense** (`adasind_145860.jpg`, `adasind_167700.jpg`, `adasind_199770.jpg`), rules v1.0.0. Soát trên bản
nháp export 17:37 (22 box) bằng overlay từng frame, sửa trong CVAT rồi export bản cuối (21 box) trước khi khóa.

## Cảnh báo tự động đã xử lý
- Bản nháp: `adasind_199770.jpg` L4 Pedestrian [203,824,213,858] cao 34 px < H=40 → **đã xoá** trong CVAT (R01).
  Chạy lại `selfqc` trên bản cuối: không còn cảnh báo.

## Checklist thủ công
- [x] Phạm vi H=40 và vật cần vẽ — bản cuối không còn box < 40 px. Đã phóng to dãy người đứng dưới mái tôn bên phải
  `adasind_199770` (x≈850–1000, y≈820–950); giữ nguyên, không thêm box.
- [x] lens_border và ego_body — 2 `lens_border` import sẵn mỗi frame khớp vòng kính, không vẽ mới (R08). `ego_body`
  vẽ ở cả 3 frame (tay áo ca rô, tay lái, chân người lái ở mép trái); không vẽ bóng đổ trên mặt đường (R07).
- [x] Class sáu nhãn — xe ba bánh gán `ThreeWheeler` (R04). Xe trắng `adasind_167700` [574,871,698,989] và xe nhỏ ở xa
  `adasind_199770` [221,817,275,860] đã xem lại, giữ `Truck` (nhận là xe tải nhỏ/pickup theo R04).
- [x] Rider và Bike — 4 cặp người + xe hai bánh (người áo sọc với xe đạp chở thùng, người áo xanh, người quàng khăn ở
  mép phải `adasind_167700`; người áo trắng cạnh xe tay ga đỏ `adasind_199770`) đều đứng dưới đất/dắt xe →
  `Pedestrian` + `Bike` tách riêng (R03). Xe hai bánh ở xa bên trái `adasind_199770` [78,836,146,902] mờ, không tách
  được người với xe → một box `Bike`, không box người riêng (rút kinh nghiệm từ lỗi L2 ở C0).
- [x] Geometry trên ảnh fisheye gốc — box bám phần nhìn thấy trên ảnh gốc, vật chạm rìa vòng kính không kéo box ra
  ngoài phần thấy (R02). ThreeWheeler mép trái `adasind_167700` [26,922,263,1175] đã xem lại, giữ một box.
- [x] truncated và occluded — vật bị vòng kính/khung cắt đánh `truncated` (người mép phải và Bike [756,982,1080,1291]
  ở `adasind_167700`; ThreeWheeler hai mép `adasind_199770`); vật bị vật khác che đánh `occluded` bằng **attribute**,
  không dùng nút Occluded gốc của CVAT (R05).
- [x] Vật thiếu hoặc box trùng — không có hai box cho một vật; các vật ≥ 40 px ở rìa đã có box.
- [x] ignore_region có reason — mỗi polygon có đúng một `reason` (`ego_body` hoặc `lens_border`); không box nào nằm
  ≥ 50% trong ignore (R06, R09).
- [x] Tên task raw_fisheye và export CVAT 1.1 — task `Day11 · ADASIND · B3-dense · raw_fisheye`, export từ Task
  (CVAT for images 1.1, không kèm ảnh).
