# Exit ticket

Đọc `docs/10-svm360-reading-vi.md` trước khi trả lời câu 1–2. Các câu về zone, `why`, rework, parking và sampling
đã nằm trong file tương ứng nên không hỏi lại ở đây.

1. Một vật ở vùng seam giữa hai camera thật xuất hiện với hai box khác nhau: đó là lỗi `DUPLICATE` hay cần một quy
   tắc riêng? Vì sao?
   **Không phải `DUPLICATE`, cần quy tắc riêng.** `DUPLICATE` là hai box cho một vật **trên cùng một ảnh**. Ở seam,
   mỗi camera là một ảnh khác, cùng một vật được quan sát hai lần với hình dạng khác nhau (có thể `edge` ở camera
   này, `mid` ở camera kia), nên với detector theo từng camera thì **cả hai box đều hợp lệ** và không được xoá box nào
   chỉ vì camera kia đã có. Cần quy tắc riêng nói: (a) mỗi camera gán nhãn độc lập theo ảnh của nó; (b) việc ghép
   thành một vật (cùng `object_id`) chỉ làm ở tầng cross-camera khi có timestamp đồng bộ, calibration_version và
   policy output (per-camera, stitched hay fused BEV); (c) nếu là ảnh ghép stitched thì vật lặp ở seam là artifact,
   xử lý bằng `ignore_region reason=stitch_seam` chứ không đếm hai lần. Lỗi thật chỉ là khi **cùng một ảnh** có hai
   box cho một vật, hoặc khi ghép hai box mà không có đủ bằng chứng.

2. Một vật đi qua nhiều frame trên cùng camera: khi nào giữ cùng track ID, khi nào thêm keyframe hoặc trạng thái
   Outside? Nêu bằng chứng sẽ cần trước khi nối track qua hai camera.
   Giữ **cùng track ID** khi vẫn là cùng một vật quan sát liên tục (vị trí, hình dáng, màu, class nhất quán giữa các
   frame; bị che ngắn vài frame rồi hiện lại cùng chỗ và cùng dáng thì theo rule re-ID của dự án). Thêm **keyframe**
   khi hình học hoặc độ nhìn thấy đổi đáng kể: vật đi từ tâm ra rìa vòng kính (méo tăng, chuyển động không tuyến tính
   nên nội suy lệch), bị che/bị vòng kính cắt, đổi kích thước lớn. Đặt **Outside** khi vật ra khỏi FOV hoặc thành
   không gán nhãn được (bị che hẳn, lọt vào vùng `ego_body`/`lens_border`), không giữ box ma. Trước khi **nối track qua
   hai camera** cần: timestamp đồng bộ giữa hai camera, calibration_version hợp lệ để chiếu vị trí hai box về cùng hệ
   (BEV) và thấy chúng trùng, sự nhất quán về class/hình dáng/hướng di chuyển, và policy output cho phép correspondence.
   Thiếu một điều kiện thì để hai track riêng và ghi decision log/escalate.

3. Nhìn lại cả buổi: một chỗ bạn tin nhãn mình đúng nhưng reference hoặc người soát nghĩ khác (dẫn frame/`object_ref`),
   bạn đã xử lý thế nào, và nếu làm lại slice này bạn sẽ đổi gì trong cách làm?
   `adasind_199770.jpg` L9 Bike [78,836,146,902]: tôi xác định là **một xe máy có người ngồi** nên theo R03 chỉ một box
   `Bike`; reference tách thành hai Bike (R5, R6), còn model (M5) cũng cho một box như tôi. Tôi không sửa theo
   reference mà ghi `E0_reference_defect` kèm bằng chứng L/R/M trong `findings.csv` và decision log D08, để người giữ
   reference phân xử. Ngược lại, ở C0 tôi đã sai đúng luật này (L2: tách người lái xe máy thành Pedestrian + Bike) và
   ở 167700 tôi gán van trắng là Truck dù đã được nhắc R04 — cả hai đều sửa được nhờ đối chiếu. Nếu làm lại slice
   này: với mọi cặp người–xe, tôi quyết định "ngồi hay dắt" trước khi vẽ; mọi xe bị che một phần và mọi vùng tối
   (dưới mái 199770) tôi phóng to đo chiều cao phần nhìn thấy thay vì lướt qua; và tôi kiểm class từng xe theo bảng
   R04 ngay khi vẽ chứ không đợi self-QC.
