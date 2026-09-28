# Guideline patch

- **Rule mới đề xuất:** **R01b — Vật bị che phần lớn và vật khó đọc.** Ngưỡng H=40 đo trên **phần nhìn thấy** của vật
  trên ảnh gốc, không ước lượng phần bị che. Vật có phần nhìn thấy ≥ 40 px **và** nhận ra được class từ phần đó
  (thấy ít nhất một bộ phận đặc trưng: bánh + tay lái với xe hai bánh, đầu + thân với người) thì box phần nhìn thấy
  và đặt `occluded=true`. Nếu phần nhìn thấy ≥ 40 px nhưng không nhận ra class (mờ, tối, bị che gần hết) thì vẽ
  `ignore_region` `reason=unreadable` thay vì box, và ghi frame/lý do vào decision log. Không box một vật ở chỗ mà
  người khác đã vẽ `unreadable`, và ngược lại, trừ khi phân xử lại.
  Ví dụ: C0 `adasind_019560.jpg` L8 — xe máy đỗ sau người đạp xe áo vàng, phần thấy cao 61 px, thấy đèn pha và một
  bánh → box `Bike` occluded. `adasind_145860.jpg` L2 — xe xa cao 52 px, reference vẽ `unreadable` còn tôi box
  Truck → cần tiêu chí "nhận ra class" để hai người ra cùng quyết định.
- **Áp dụng cho:** cả 6 class (R01, R05) và `ignore_region.reason=unreadable` (R06).
- **Vì sao luật hiện tại (`docs/02-rules-vi.md`) không đủ:** R01 chỉ nói "vật cao ≥ 40 px phải có box" mà không nói đo
  cả vật hay phần nhìn thấy, cũng không nói vật bị che gần hết có cần box không; R06 liệt kê `unreadable` nhưng không
  có tiêu chí khi nào dùng thay cho box. Hậu quả trong bài: C0 L8 (tôi box, reference không) và 145860 L2 (tôi box,
  reference ignore) đều thành finding `E2_guideline_gap` dù cả hai bên đều làm đúng theo cách hiểu của mình.
- **`rules_version` mới:** v1.0.0 → v1.1.0
- **Hiệu lực từ:** round `rework` tiếp theo và mọi slice gán nhãn mới; không sửa ngược các bản đã khóa (`calib`,
  `r1_craft`) mà ghi các ca cũ vào decision log với rules v1.0.0.
