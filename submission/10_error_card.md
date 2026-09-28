# Error analysis card

## Zone × block

| zone | block | what | count |
|---|---|---|---:|
| center | B2 | DUPLICATE | 1 |
| center | B3 | BOX_GEOMETRY | 1 |
| center | B3 | MISSING | 6 |
| center | B3 | SPURIOUS | 7 |
| center | B3 | WRONG_CLASS | 1 |
| center | C0 | SPURIOUS | 2 |
| edge | B2 | WRONG_CLASS | 1 |
| edge | B3 | BOX_GEOMETRY | 1 |
| edge | B3 | MISSING | 5 |
| edge | B3 | SPURIOUS | 3 |
| mid | B2 | ATTRIBUTE | 1 |
| mid | B3 | BOX_GEOMETRY | 1 |
| mid | B3 | IGNORE_SCOPE | 4 |
| mid | B3 | MISSING | 8 |
| mid | B3 | SPURIOUS | 5 |
| unknown | B2 | MISSING | 1 |
| unknown | B2 | SPURIOUS | 1 |
| unknown | prelabel | STRUCTURE | 1 |

## Top defects
- MISSING: 20 (ví dụ frame adasind_086220.jpg)
- SPURIOUS: 18 (ví dụ frame adasind_019560.jpg)
- IGNORE_SCOPE: 4 (ví dụ frame adasind_145860.jpg)

## Phân tích của bạn

Hai bảng trên do `python3 lab11.py card` tính từ `findings.csv`; chạy lại lệnh sẽ cập nhật bảng và giữ nguyên mục này. Viết cho lỗi nổi bật nhất, dẫn frame/`object_ref`.

Lỗi nổi bật nhất: **MISSING ở B3 (slice chung B3-dense), zone `mid` = 8 dòng và `center` = 6**, cộng với SPURIOUS
cùng khối. Bảng đếm gộp cả dòng của model (`r3_diag` cell `RM_noL`, `R_only`, `M_only`) nên con số lớn hơn số lỗi
thật của người gán nhãn.

- Nguyên nhân khả dĩ (`why`) và vì sao bạn nghĩ vậy: tách 20 dòng MISSING theo `why` thì chỉ **4 là
  `E1_annotator_error`** (xe tải bị van che 167700 R8, van trắng gán nhầm Truck nên R4 Car thành thiếu, xe đạp chở
  thùng box hụt 167700 R9), **6 là
  `E0_reference_defect`** (reference vẽ `ego_body` phủ xe ba bánh R4 ở 199770; tách một xe máy có người ngồi thành
  hai Bike R5/R6), **3 là `E4_model_domain`** (model gọi ThreeWheeler là Truck/Car nên không ghép được) và **6 là
  `E5_unresolved`** (vùng tối dưới mái 199770 R3, box xe đạp bị người che 167700 R2). Tôi nghĩ vậy vì mỗi ca đã
  được phóng to trên ảnh gốc và đối chiếu ba nguồn L/R/M: khi M đồng ý với L mà khác R (ví dụ 199770 L9+M5 một box
  Bike) thì nghi reference; khi R và M cùng thấy mà L không (167700 R8+M9) thì là lỗi của tôi. Slice `dense` có
  nhiều vật chồng nhau nên lỗi của tôi là **ranh giới box giữa vật chồng nhau**, không phải méo rìa.
- Cách sửa và ai nhận việc (`owner`): `annotator` — đã rework 3 ca P1 ở 167700 (8 dòng findings `action=rework`: van →
  Car, thêm Truck sau van, kéo box xe đạp chở thùng); 3 ca khác được đề xuất sửa nhưng annotator giữ kèm lý do
  (decision log D06–D08); `delta.md`: matched 11 → 14, missing 9 → 6, spurious 6 → 4. `qa` — escalate hai vùng
  `ego_body` sai của reference (`30_escalation_ticket.md`). `guideline` — thêm rule cho vật bị che phần lớn và cho
  `unreadable` (`20_guideline_patch.md`). `ai_team` — kiểm giả thuyết model không có class ThreeWheeler trên đủ 48
  frame trước khi dùng pre-label cho class này.
- Bằng chứng (ảnh trong `screenshots/`, dòng findings, rule): `screenshots/p4_ego_body_ref_167700.png`,
  `screenshots/p4_ego_body_ref_199770.png` (R07); dòng `r3_diag` 167700 `L5`, `R4+M4`, `R8+M9` (R04, R01) và 199770
  `R4`, `L9+M5` (R07, R03); `rework/delta.md`.
