# Thành viên và phân vai — Day11 SVM 360 Fisheye

## 1. Thông tin nhóm

- Khóa/lớp: K4 · L2 (AI20K)
- Tên nhóm: VinNo1
- Repo Public: https://github.com/emsiCUD/K4-L2-DAY11-VinNo1-SVM-360-View
- Máy giữ hồ sơ chính / người quản lý: máy của Nguyễn Trọng Minh Đức (CVAT local + repo nộp)
- Slice chung lấy từ mode.json: `B3-dense` (`adasind_145860.jpg`, `adasind_167700.jpg`, `adasind_199770.jpg`)
- Tên định danh vai A dùng cho --self: `2A202602182`
  (`mode --members 2A202602047,2A202602149,2A202602182 --self 2A202602182`)
- Kênh trao đổi nội bộ: Discord
- Đại diện nộp (vai C): Nguyễn Trọng Minh Đức, 2A202602182
- Commit chốt bài: (điền sau khi push)

## 2. Ba vai chính

| Vai | Họ và tên | MSSV | Tên định danh trong mode | Trách nhiệm | Bằng chứng đóng góp |
|---|---|---|---|---|---|
| A · Gán nhãn | Nguyễn Trọng Minh Đức | 2A202602182 | 2A202602182 | Parking/C0/slice, self-QC, lock, rework | `parking/annotations.xml` (16 `parking_line`, 2 `free_space`); `p1_calib/` khóa DDC3-E2CF; `r1_craft/` khóa 467C-4A87 + `selfqc.md`; `rework/` (P5) |
| B · QA độc lập | Nguyễn Trọng Minh Đức | 2A202602182 | 2A202602182 | Review trước reference, finding QA, kiểm lại ca sửa | `r2_qa/qa_review.md` (QA mù bài B2-mid của Thảo, khóa 674A-86BB, theo vòng `team.json`) + 5 dòng `r2_qa` trong `findings.csv`. Slice chung B3-dense không có QA mù riêng (xem mục 4) |
| C · Chẩn đoán & điều phối | Nguyễn Trọng Minh Đức | 2A202602182 | 2A202602182 | Báo cáo, phân xử, kế hoạch, tích hợp, check và nộp | `r3_diag/` (local quality, model compare, zone table), 42 dòng chẩn đoán `r1_craft`/`r3_diag` trong `findings.csv`, các file 20/30/40/45/46/50 |

Bảng này xác định vai của nhóm. Vòng QA tự sinh trong team.json thuộc quy trình nhiều hồ sơ của CLI; nhóm dùng một slice chung và quy trình A → B → C đã nêu trong hướng dẫn.

Hai thành viên còn lại:

- Nguyễn Phương Thảo (2A202602047) — vẽ slice B2-mid (khóa 674A-86BB, là bài được QA ở `r2_qa/`); hỗ trợ vai C phần kế hoạch: viết kế hoạch sampling/gold set bốn camera (`45_sampling_plan.csv`, `45_review_plan.md`, `46_gold_set_plan.md`).
- Nguyễn Đăng Vĩ Anh (2A202602149) — hỗ trợ annotation: vẽ slice B4-mid trên CVAT (task #32, khóa 5990-C4E9) trong giai đoạn mỗi người một slice.

## 3. Bàn giao theo pha

| Mốc | Người giao → nhận | File / commit / mã khóa | Người nhận đã kiểm gì? | Trạng thái / vướng mắc |
|---|---|---|---|---|
| P0 · Chốt môi trường và vai | C → A, B | `00_setup/mode.json` (slice B3-dense), `doctor.txt` không còn ✗ | A: slice in ra đúng B3-dense | Xong. Ban đầu mỗi người nhận một slice (B2-mid/B4-mid/B3-dense); nhóm chốt slice chung B3-dense sau P3 |
| P2 · Khóa bản đầu | A → B, C | `r1_craft/annotations.xml`, `lock.txt`, slice B3-dense, code **467C-4A87** | C: `selfqc` trên bản cuối không còn cảnh báo (box 34 px đã xoá) | Xong 17:43 |
| P3 · Chốt QA mù | B → C, A | `r2_qa/qa_review.md` (B2-mid, khóa 674A-86BB), 5 dòng `r2_qa` trong `findings.csv` | C: mã khóa khớp file (`qa` kiểm), mỗi nhận xét có frame/object_ref/rule | Xong. QA theo vòng `team.json` (Đức soát bài Thảo), không phải QA mù của B3-dense |
| P4 · Quyết định sửa | C → A, B | `findings.csv` (why/severity/owner/action), `r3_diag/zone_table.md`, `40_decision_log.csv` | A: nhận 6 ca rework P1 | Reference mở sau lock; 2 ca escalate (ego_body của reference ở mép phải) |
| P5 · Kiểm bản sửa | A → B → C | `rework/annotations-v2.xml`, `lock2.txt` khóa **7712-C0C4**, `delta.md` | C: delta matched 11 → 14, missing 9 → 6, spurious 6 → 4; 3 ca annotator giữ có lý do (D06–D08) | Xong; không có reviewer thứ hai kiểm lại ca sửa |
| P6 · Chốt nộp | A, B → C | `manifest.json`, commit chốt | [Điền] | Chưa làm |

## 4. Bất đồng và phối hợp

- Một ca đã phân xử: `adasind_199770.jpg` L9 Bike [78,836,146,902] — chẩn đoán (C) đề xuất tách thành hai Bike theo
  reference (R5+R6); annotator (A) xác định là một xe máy có người ngồi → R03 một box, model M5 cũng một box. Quyết
  định: giữ một box, ghi `E0_reference_defect` (decision log D08, dòng `r3_diag` `L9+M5`).
- Ca còn mở: reference vẽ `ego_body` phủ người đi bộ quàng khăn (`adasind_167700`) và xe ba bánh đen (`adasind_199770`)
  ở mép phải — đã escalate, chờ người giữ reference xác nhận (xem `30_escalation_ticket.md`).
- Đóng góp của A/B/C vào kế hoạch và exit ticket: Thảo viết kế hoạch sampling/gold set bốn camera (`45_sampling_plan.csv`, `45_review_plan.md`, `46_gold_set_plan.md`); Đức (A/B/C) viết exit ticket, error card, guideline patch, escalation và decision log; Vĩ Anh hỗ trợ annotation (slice B4-mid).
- Thay đổi phân công nếu có: ban đầu nhóm làm theo vòng `team.json` (mỗi người một slice): Thảo vẽ B2-mid (khóa
  674A-86BB), Vĩ Anh vẽ B4-mid (khóa 5990-C4E9), Đức vẽ B3-dense và QA mù bài B2-mid của Thảo. Sau P3 nhóm chuyển sang
  hồ sơ chung một slice theo `TEAMMATES.md` (slice chung B3-dense) và định để Vĩ Anh QA mù B3-dense, nhưng phần này
  **không được thực hiện**. Hồ sơ giữ QA mù B2-mid của Đức làm `r2_qa` chính thức (decision log D09); vai B vì vậy do
  Đức đảm nhiệm, B3-dense không có reviewer độc lập thứ hai.

## 5. Xác nhận trước khi nộp

- [ ] A xác nhận nhãn và export đúng phiên bản: [Tên / bằng chứng]
- [ ] B xác nhận đã QA độc lập trước reference và kiểm lại ca sửa: [Tên / bằng chứng]
- [ ] C xác nhận báo cáo đúng bản khóa, các file đầy đủ và check exit 0: [Tên / bằng chứng]
- [ ] manifest.json tại commit chốt có failed_gates rỗng.
- [ ] Repo nhóm Public, ảnh và các bằng chứng mở được.
- [ ] C đã push và gửi link repo nhóm + commit qua kênh lớp công bố.

Chỉ đánh dấu việc đã kiểm thật. Nhóm nộp một hồ sơ chung; check không tự chấm đóng góp từng người. Giữ nguyên header/các cột enum của findings.csv; tên người được ghi trong tài liệu này hoặc phần note thích hợp.
