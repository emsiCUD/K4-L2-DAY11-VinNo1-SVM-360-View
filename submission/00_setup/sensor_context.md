# Sensor context

Quan sát trên 4 frame ADASIND đã mở (C0 `adasind_019560.jpg`; slice B3-dense `adasind_145860.jpg`,
`adasind_167700.jpg`, `adasind_199770.jpg`). Annotation space: `raw_fisheye`, ảnh dọc 1080×1920.

- Rig (theo quan sát, không phải thông số được cung cấp): **một camera fisheye** gắn thấp, nhìn về phía trước theo
  hướng xe chạy trên đường nông thôn/ven chợ ở Ấn Độ. Ở mép trái trong vòng kính thấy tay áo kẻ ca rô cầm tay lái và
  chân người ngồi, nên xe ego nhiều khả năng là **xe hai/ba bánh**, camera đặt gần người lái chứ không phải trên capo ô tô.
  Dữ liệu không kèm vị trí lắp, chiều cao, góc nghiêng, FOV hay calibration → tôi không suy ra khoảng cách thật hay tạo
  box BEV; một camera này cũng không đại diện cho bốn camera SVM front/rear/left/right.
- `ego_body` nhìn thấy ở đâu: chủ yếu ở **mép trái và góc dưới trái bên trong vòng kính** — tay/cánh tay và chân của
  người lái cùng phần thân xe (rõ ở `adasind_145860`, `adasind_167700`, `adasind_199770`). Ở C0 `adasind_019560` chỉ
  thấy một phần tối nhỏ sát mép trái; mảng tối lớn ở đáy ảnh C0 là **bóng** của xe/người trên mặt đường, không phải
  thân xe, nên không vẽ thành `ego_body`. Theo GUIDE, `adasind_006840` và `adasind_271039` không có ego body nhìn thấy.
- Vòng kính (lens circle): ảnh quang học là một vòng tròn gần như **đường kính bằng cả chiều ngang khung**, tâm khoảng
  (540, 906), trải theo chiều dọc từ y≈136 đến y≈1677 (theo polygon `lens_border` prefill); hai bên trái/phải vòng tròn
  bị mép khung cắt trong đoạn y≈410–1400. Vùng hợp lệ chiếm khoảng 73% diện tích khung hình (tính từ diện tích hai polygon `lens_border` prefill ≈ 27%); phần còn lại — dải tối
  phía trên và phía dưới cùng bốn góc — là vỏ ống kính / pixel không có cảnh, được loại bằng hai polygon
  `ignore_region` reason `lens_border` mỗi frame. Rìa vòng kính méo mạnh (cột điện, dây điện bị uốn cong) và ở các
  frame B3 có lóa nắng phía trên (`adasind_167700`, `adasind_199770`).
