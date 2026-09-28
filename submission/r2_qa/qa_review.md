# QA review · B2-mid

> QA của hồ sơ theo vòng `team.json` (mỗi người một slice): Minh Đức soát bài B2-mid của Thảo (khóa 674A-86BB) khi
> chưa ai mở reference của B2-mid. Slice chung B3-dense **không** có QA mù của vai B riêng — xem `TEAMMATES.md` mục 4
> và decision log D09. 5 dòng `r2_qa` slice B2-mid trong `findings.csv` là của bản này.

Mã khóa: 674A-86BB

Người soát: 2A202602182 (Minh Đức) · bài của 2A202602047 (Thảo), nhận theo vòng `team.json`. Soát **mù** trên
`qa_overlay.html` + ảnh gốc theo rules v1.0.0; chưa mở teaching reference, model overlay hay worked HTML.
`object_ref` theo số L trong overlay (chỉ box cao ≥ 40 px, đánh số theo thứ tự XML trong từng frame). Bảng chỉ ghi
**điều quan sát và luật liên quan**, không chẩn đoán nguyên nhân.

| frame | object_ref | rule_id | nhận xét |
|---|---|---|---|
| adasind_102750.jpg | Truck [334,915,369,952] (không có số L vì < H) | R01 | Box cao 37 px, dưới H=40 → theo R01 vật này không cần box; export vẫn còn box. Đề nghị xoá hoặc kiểm lại chiều cao phần nhìn thấy. |
| adasind_086220.jpg | L5 Bike + L6 ThreeWheeler | R04 | Hai box chồng một phần lên nhau ở cùng cụm xe xa (L5 [315,980,360,1027], L6 [322,961,378,1006]). Cần xem trên ảnh đây là hai vật khác nhau hay một vật bị gán hai box với hai class (checklist mục 7 — trùng). |
| adasind_102750.jpg | L3 Bus | R04 | Xe màu nâu đỏ sát mép trái [0,845,86,963], bị khung cắt (đã có `truncated`). Hình dáng giống xe van/tempo nhỏ hơn xe buýt; đề nghị xem lại class theo bảng R04 (minibus → Bus, van chở người → Car, xe tải nhỏ → Truck). |
| adasind_102750.jpg | L5 ThreeWheeler | R04 | Box [116,892,168,949] quanh một xe nhỏ màu sáng ở xa; từ overlay chưa chắc là xe ba bánh hay xe con — đề nghị phóng to kiểm class. |
| adasind_086220.jpg | L1 ThreeWheeler | R05 | Auto-rickshaw vàng lớn ở mép phải, box chạm biên phải x=1080 nhưng `truncated=false`. Nếu thân xe bị khung ảnh cắt thì theo R05 cần `truncated`. |
| adasind_086220.jpg | vùng trái, gần gốc cây (x≈40–100, y≈965–1010) | R01 | Có một xe ba bánh đỗ bên lề trái không có box; cần đo chiều cao — nếu ≥ 40 px thì theo R01 phải có box. |
| adasind_060000.jpg | L5 ThreeWheeler, L8 Bike | R03 | Đạt: người lái ngồi trong e-rickshaw L5 không box riêng; người đội mũ bảo hiểm trên xe máy L8 gộp một box `Bike`. |
| adasind_060000.jpg, 086220, 102750 | ignore_region | R07, R08 | Đạt: mỗi frame có `ego_body` bám tay/chân người lái ở mép trái và giữ 2 `lens_border` import sẵn. |

Tóm tắt: 1 vi phạm phạm vi rõ (R01, box 37 px), 4 ca cần tác giả kiểm lại (class R04 ×3, `truncated` R05), 1 ca có
thể thiếu vật (R01). Luật rider và vùng ignore được áp dụng nhất quán.
