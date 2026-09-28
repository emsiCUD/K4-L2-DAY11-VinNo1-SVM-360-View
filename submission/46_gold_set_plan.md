# Đề xuất gold set theo camera — tình huống giả lập

**Đầu bài:** 50.000 frame từ bốn camera SVM, ngân sách chọn 200 frame để review/gold. Đây là tình huống trên slide,
**không phải** 50.000 frame có trong repo. Phân bổ đúng 200 ở `45_sampling_plan.csv` cho bốn camera, mỗi camera có
normal và hard slice. “Gold set” ở đây là **kế hoạch tạo** reference sau kiểm chứng, không phải teaching reference
ADASIND hoặc nhãn bạn vừa vẽ. Nếu cần, dùng `notebooks/day11-svm360-colab.ipynb` để thử tổng phân bổ; notebook
không làm thay phần lý do.

| camera_id | Hard case cần chọn | Vì sao dễ sai | Annotation space / calibration cần giữ | Cách review trước khi gọi là gold |
|---|---|---|---|---|
| front | Cảnh đông, vật chồng nhau (người dắt xe, xe sau xe); lóa/ngược sáng; xe ba bánh và xe đặc thù địa phương | Ranh giới box giữa vật chồng nhau và class xe đặc thù là nơi tôi và model sai nhiều nhất ở ADASIND (van→Truck, 4/4 xe ba bánh bị model gọi Truck/Car) | `raw_fisheye` của camera front; calibration_version + mask `lens_border` của đúng camera; timestamp | Hai annotator độc lập theo rules v1.1.0 → reviewer thứ ba phân xử mọi khác biệt class/box; ca không đồng thuận ghi `E5` và loại khỏi gold cho tới khi có frame kề |
| rear | Người/trẻ em/vật thấp sát cản khi lùi; ban đêm; cản xe ego chiếm đáy ảnh | Vùng `ego_body` dễ vẽ lố phủ vật thật (lỗi P0 đã gặp ở reference ADASIND) và vật thấp dễ lọt ngưỡng H | `raw_fisheye` rear; polygon `ego_body` cố định theo calibration của xe; phiên bản mask | Review riêng polygon `ego_body` trước (so với mask chuẩn của xe) rồi mới review box; 100% frame hard rear được soát kép |
| left | Méo rìa mạnh; thân xe ego lớn; curb sát xe; vật bị cắt ở seam trái-trước và trái-sau | `truncated`/`occluded` dễ lẫn; vật ở rìa bị box lố hoặc bị nhầm vào vùng ignore | `raw_fisheye` left; calibration để suy vùng seam; timestamp đồng bộ với front/rear | Reviewer kiểm attribute riêng (truncated vs occluded, R05) và mọi box chạm rìa vòng kính/ego_body |
| right | Xe máy vượt sát bên phải; người đi bộ sát rìa; seam phải-trước và phải-sau | Người/xe sát rìa phải bị nhầm là `ego_body` (đúng lỗi của reference ở 167700/199770) | `raw_fisheye` right; calibration; mask `ego_body` của camera phải | Như left, cộng kiểm chéo mọi `ignore_region` ở mép phải với ảnh gốc trước khi khóa |

- Khi nào cần refresh gold set (đổi camera, calibration hoặc rule): khi đổi camera/ống kính hoặc vị trí gắn, khi
  calibration_version hoặc mask `lens_border`/`ego_body` đổi, khi `rules_version` đổi phần ảnh hưởng nhãn (ví dụ
  v1.0.0 → v1.1.0 thêm R01b về vật bị che/unreadable), khi thêm class, và định kỳ khi phát hiện reference sai (như
  ticket 1) — sửa ca đó rồi chạy lại phép so trên các job đã đánh giá. Teaching reference ADASIND hiện tại **không**
  dùng làm gold vì đã có lỗi P0 ở `ego_body`.
- Một ca seam/cross-camera cần policy và evidence trước khi ghép hai box: xe máy vượt ở góc phải-trước xuất hiện
  đồng thời ở camera front (rìa phải, `edge`) và camera right (vùng `mid`) với hai box khác hình. Mỗi camera vẫn giữ
  box riêng trên ảnh của nó; chỉ gán cùng `object_id`/correspondence khi có timestamp đồng bộ, calibration_version
  hợp lệ để chiếu hai box về cùng vị trí, và policy output (per-camera detector hay fused BEV) nói rõ cách tính. Không
  có đủ ba điều kiện thì không ghép và không xoá box nào.
- Vì sao peer agreement hoặc quality report trên ảnh một camera chưa chứng minh gold set đúng cho cả bốn camera:
  ADASIND chỉ có một camera trước, không có seam, không có cản sau hay rìa trái/phải của xe ô tô; hai người có thể
  cùng sai theo một cách (ví dụ cùng quy ước sai về `ego_body`), và reference ở đây chính nó có lỗi. Mỗi camera có
  distortion, vùng ego và phân bố class khác nhau, nên cần gold riêng từng camera, cùng annotation space và
  calibration với job được đánh giá.
