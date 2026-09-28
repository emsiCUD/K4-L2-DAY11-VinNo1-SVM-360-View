# Escalation ticket

## Ticket 1

- **Frame:** `adasind_167700.jpg` và `adasind_199770.jpg` (slice B3-dense), mép phải vòng kính.
- **Ảnh chụp:** `submission/screenshots/p4_ego_body_ref_167700.png`, `submission/screenshots/p4_ego_body_ref_199770.png`
  (viền vàng = polygon `ego_body` của teaching reference; đỏ = box của tôi bị tính IGNORE_SCOPE; xanh = box reference).
- **Expected impact:** teaching reference vẽ `ignore_region reason=ego_body` phủ **vật không thuộc xe ego**: ở 167700
  là người quàng khăn đang dắt xe đạp đứng ngoài xe (reference vẫn box xe đạp R2 của anh ta); ở 199770 là cả xe ba
  bánh đen có bánh và chữ trên thân, trong khi reference đồng thời box chính xe đó là R4 ThreeWheeler → reference tự
  mâu thuẫn. Vì compare/local-quality bỏ qua box nằm ≥ 50% trong ignore (R09), 3 box của tôi (167700 L4, L9; 199770
  L4) bị loại khỏi phép so và R4 thành MISSING giả. Nếu không sửa: số `mid`/`edge` của slice này (và mọi học viên
  nhận B3-dense) bị lệch, học viên có thể học sai rằng người đi bộ sát rìa là `ego_body`, và model/eval sau này
  không bị phạt khi bỏ sót người/xe ở mép phải.
- **Owner:** `qa` (người giữ teaching reference / Lab Coach); cần `data_ops` xác nhận cấu hình gắn camera nếu
  reference cho rằng mép phải thuộc xe ego.
- **Recommendation:** sửa reference B3-dense: thu polygon `ego_body` ở 167700 và 199770 về đúng phần thân xe/người
  lái ego nhìn thấy (theo R07), bỏ phần phủ người đi bộ và xe ba bánh; giữ R4 ThreeWheeler ở 199770 và thêm box
  Pedestrian cho người quàng khăn ở 167700. Sau khi sửa, chạy lại `compare`/`local-quality` cho slice này và soát các
  slice khác có `ego_body` chạm mép phải. Đến khi có phản hồi, nhóm giữ nguyên nhãn của mình (decision log D05).
