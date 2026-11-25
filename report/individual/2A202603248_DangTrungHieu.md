# Báo cáo Day 13 — Đặng Trung Hiếu, 2A202603248

## 1. Kết luận trước
- Mã số: 2A202603248. Task: `D13-2A202603248`.
- 10 frame: `s01f10`, `s01f53`, `s02f14`, `s02f35`, `s03f31`, `s03f50`, `s04f11`, `s04f27`, `s05f10`, `s05f26`.
- Hệ điều hành: Windows 11. Trình duyệt: Google. Phiên bản CVAT: 2.74.1.
- MSSV bạn ngồi cạnh: 2A20263311
- Tổng số cuboid; frame ít nhất và vì sao: 57 cuboid, frame ít nhất là frame s03f31 vì không có đủ point
- Số cuboid bị `selfcheck` cờ trên tổng số: 7/57.
- Phần tin nhất và phần yếu nhất (theo số liệu tự kiểm): tin nhất là đáy (trong các hộp có `ground_gap_m`, chỉ 1 hộp bị cờ chìm); yếu nhất là kích thước (cả 7 hộp bị cờ đều có chiều dài hoặc chiều rộng ngoài khoảng).

Cách dùng CVAT:

1. Đổi góc nhìn: trong khung 3D, kéo chuột trái để xoay (xoay tới khi nhìn thẳng xuống để có góc nhìn từ trên), kéo chuột phải để dời, lăn con lăn để phóng; nháy đúp chỗ trống để về góc mặc định.
2. Vẽ và chỉnh một cuboid: chọn label, bấm Shape, nháy đúp vào cụm điểm để đặt hộp; chỉnh tâm, dài, rộng, yaw ở Top; chỉnh đáy và chiều cao ở Side, Front.
3. Bật mũi tên hướng và xem cuboid trên ảnh camera: bật Appearance → Cuboid orientation để vạch đỏ ngắn chỉ về đầu vật; bấm Save, chạy `python scripts/lab.py sync` rồi mở ảnh `preview` trong `work/xem/`.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f10-3d`, `t1-s01f10-top`, `t1-s01f53-3d`, `t1-s01f53-top`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f10 | Phía trước (−y) ở cạnh trên; +x chỉ sang trái, +y chỉ xuống dưới | Gần như phẳng; thấp hơn LiDAR khoảng 1,8 m | Xe buýt phía trước, 46,35 m | 5 xe con, 1 xe tải, 1 xe buýt, 1 xe hai bánh | Phía sau: xe con 21,34 m, xe tải sau-trái 24,41 m, xe hai bánh sau-phải 22,30 m; bên trái: xe con 18,12 m | Cụm 70 điểm ở hộp idx 8, bên trái phía trước 21,20 m, sau dải cây; chưa đọc được rõ. Vệt ngang trên cao phía trước khớp với biển treo trong ảnh camera |
| s01f53 | Phía trước (−y) ở cạnh trên; +x chỉ sang trái, +y chỉ xuống dưới | Gần như phẳng; thấp hơn LiDAR khoảng 1,8 m | Hộp xa nhất đã vẽ 16,09 m; còn cụm điểm phía trước khoảng 30 m (ước lượng từ ảnh) | 6 xe con đã vẽ; ảnh camera còn có người đi xe đạp bên phải | Phía sau: xe con 6,48 m và 16,09 m; bên trái phía trước: xe con 9,43 m; bên phải: xe đạp trên lối đi | Cụm nhỏ phía trước khoảng 30 m. Vệt ngang trên cao khớp với giá biển treo trong ảnh camera |

Chỉ camera cho biết:

1. Màu xe: xe con gần nhất ở `s01f10` màu xanh dương; các xe ở `s01f53` có màu trắng và tối.
2. Biển treo phía trước ở cả hai frame là biển chỉ hướng; ở `s01f53` đọc được mũi tên chỉ làn.
3. Vạch sơn trên mặt đường, ví dụ vạch vàng bên phải ở `s01f10`.

Chỉ LiDAR cho biết:

1. Khoảng cách theo mét: xe con gần nhất ở `s01f10` cách 5,99 m, xe buýt cách 46,35 m.
2. Kích thước theo mét: hộp xe con gần nhất ở `s01f10` là 4,994 × 1,986 × 1,607 m.
3. Độ cao mặt đường so với LiDAR: đáy các hộp ở `s01f53` nằm ở −1,707 đến −1,824 m.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f14 | Cạnh trên (ảnh nhìn từ trên xuống của `preview`) | Hàng rào thấp ngăn hai chiều xe ở bên trái |
| s02f35 | Góc trên bên trái (theo ảnh `t4-1-bong-che`) | Cụm điểm thấp hơn mặt đường, phía sau bên trái |
| s03f31 | Cạnh trên (theo ảnh `t2-b-bus-truck`) | Nhà chờ xe buýt phía sau bên phải, cạnh xe buýt |
| s03f50 | Cạnh trên (theo ảnh `t2-c-ped-cyclist`) | Người đi xe đạp ở sau hàng rào thấp bên phải |
| s04f11 | Cạnh trên (ảnh nhìn từ trên xuống của `preview`) | Đáy hộp idx 6 bên trái phía trước ở −2,276 m nhưng `ground_gap_m` 0,11 m: mặt đường ở đó có thể thấp hơn |
| s04f27 | Cạnh trên (ảnh nhìn từ trên xuống của `preview`) | Đáy hộp idx 6 và 7 phía sau bên trái khoảng −2,09 m: mặt đường ở đó có thể thấp hơn |
| s05f10 | Cạnh trên (ảnh nhìn từ trên xuống của `preview`) | Giao lộ; có xe tải phía sau khoảng 20 m chưa có hộp |
| s05f26 | Cạnh trên (ảnh nhìn từ trên xuống của `preview`) | Giao lộ; có cụm điểm và xe con tối màu phía sau khoảng 20 m chưa có hộp |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f10, idx 1 | Car | Trong ảnh, mũi tên chỉ về phía trước; 268,1° | Có; z đáy −1,813 m, `ground_gap_m` −0,05 m | Ảnh camera (`preview`): khớp. Top/Side/Front: chưa có bằng chứng ghi lại | So với sedan: dài 4,994 m trong khoảng; rộng 1,986 m vượt 1,90 m; cao 1,607 m vượt 0,007 m |
| (b) xe buýt hoặc xe tải | s03f31, idx 2 | Bus | Trong ảnh, mũi tên chỉ về phía trước; 267,5° | Có; z đáy −1,661 m, `ground_gap_m` 0,04 m | Ảnh camera (`preview`): khớp. Top/Side/Front: chưa có bằng chứng ghi lại | Dài 12,236 m và cao 3,349 m trong khoảng; rộng 2,947 m, vượt 2,60 m |
| (c) người đi bộ hoặc xe hai bánh | s03f50, idx 4 | Cyclist | Trong ảnh, mũi tên chỉ về phía trước; 272,5° | Có; z đáy −1,745 m, `ground_gap_m` 0,01 m | Ảnh camera (`preview`): khớp. Top/Side/Front: chưa có bằng chứng ghi lại | So với xe đạp có người: rộng 0,610 m và cao 1,622 m trong khoảng; dài 1,491 m thấp hơn 1,6 m |

- Vòng hiệu chuẩn: chưa có file bảng A của vòng này trong `work/hieuchuan/`; chưa ghi lại kết quả so lệch.
- Vật bỏ qua: chưa ghi lại lúc vẽ. Vật thấy trong `preview` nhưng chưa có hộp: s05f10 xe tải phía sau khoảng 20 m, s05f26 xe con phía sau khoảng 20 m, s01f53 người đi xe đạp bên phải.

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s02f35 / `t4-1-bong-che` / của tôi | Giả thuyết: xe con idx 1 (5,98 m) chắn tia LiDAR | Vùng gần như không có điểm phía ngoài xe, bên trái | Vùng trống là chưa quan sát được, không phải đường trống; vật bị che một phần thì bật occluded, cuboid bao cả phần suy luận được |
| 2. Thưa ở xa | s02f35, idx 3 / `t4-2-thua-o-xa` / của tôi | Giả thuyết: xa hơn nên ít tia LiDAR trúng xe | Xe con cách 20,06 m có 408 điểm, so với 1570 điểm của xe con idx 2 cách 10,04 m | Vẽ nếu thấy khoảng 15–20% thể tích hoặc suy luận được; không thì bỏ qua, ghi LOW_VISIBILITY |
| 3. Phản xạ thấp / ít điểm bất thường | s01f10, idx 4 / `t4-3-phan-xa-thap` / của tôi | Giả thuyết: LiDAR chủ yếu thấy đuôi xe. Phản xạ thấp do màu tối thì chưa phù hợp, vì ảnh camera cho thấy xe trắng | Xe con cách 23,66 m có 260 điểm, trong khi xe con `s05f26` idx 4 cách 22,47 m có 573 điểm | Ghi giả thuyết, không khẳng định; không nới hộp theo intensity |
| 4. Điểm lạ | s02f35 / `t4-4-diem-la` / của tôi | Giả thuyết: tia phản xạ nhiều lần hoặc nhiễu đo; chưa rõ | Cụm điểm tách rời, thấp hơn mặt đường, phía sau bên trái | Không gán thành vật |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-2A202603248-bangA.csv`; số điểm lấy từ cột `points_in` của `submission/D13-2A202603248-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z (m) | h (m) | z đáy = z − h/2 (m) | dist_m (m) | r = √(x²+y²+z²) (m) | yaw_deg (°) | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f10, idx 1, Car | −1,009 | 1,607 | −1,813 | 5,99 | 6,07 | 268,1 | Chạm đường |
| s03f50, idx 4, Cyclist | −0,934 | 1,622 | −1,745 | 7,51 | 7,56 | 272,5 | Chạm đường |
| s04f11, idx 6, Car | −1,128 | 2,296 | −2,276 | 28,81 | 28,83 | 86,2 | Chìm so với −1,7 m (lệch 0,576 m), nhưng `ground_gap_m` 0,11 m: có thể do mặt đường ở đó thấp hơn |

(a) Khoảng cách:

- Cuboid gần nhất: s02f14, idx 4, Car; `dist_m` 4,18 m; r 4,30 m.
- Cuboid xa nhất: s01f10, idx 5, Bus; `dist_m` 46,35 m; r 46,35 m.

(b) Kích thước l, w, h so với bảng 4.3:

- Xe đạp có người s03f50 idx 4: 1,491 × 0,610 × 1,622 m. Rộng và cao trong khoảng; dài thấp hơn 1,6 m, có thể hộp chưa bao hết hai đầu xe đạp.
- Xe con s01f10 idx 1: 4,994 × 1,986 × 1,607 m. So với sedan, dài trong khoảng (3,8–5,0 m); rộng vượt 1,90 m và cao vượt 1,60 m. Có thể hộp hơi bao dư theo chiều rộng.

(c) Hướng:

- Xe cùng chiều phía trước: s04f27 idx 1, 270,0°; gần 270°.
- Xe ngược chiều: s02f14 idx 1, 90,9°; gần 90°.

Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m (m) | Số điểm (`points_in`) |
|---|---|---|
| s02f14, idx 4, Car | 4,18 | 3578 |
| s02f35, idx 2, Car | 10,04 | 1570 |
| s04f27, idx 4, Car | 19,82 | 510 |
| s04f11, idx 6, Car | 28,81 | 263 |

- Quy luật: xa gấp khoảng 2 lần (10,04 → 19,82 m) thì số điểm còn khoảng 1/3 (1570 → 510). So với slide: 3578 điểm ở 4,18 m cao hơn 1968; 510 điểm ở 19,82 m thấp hơn 718 (gần cuối khoảng 10–20 m); 263 điểm ở 28,81 m cao hơn 214. Cùng xu hướng giảm.

Tự kiểm:

- Cuboid đã sửa 1: s01f10, idx 7, Cyclist: hạ đáy từ −0,855 m xuống −1,675 m, chiều cao từ 1,312 m thành 2,131 m.
- Cuboid đã sửa 2: s03f31, idx 2, Bus: thu hẹp chiều rộng từ 3,268 m xuống 2,947 m. Ngoài ra đã sửa s01f10 idx 1 (dài 4,475 → 4,994 m, rộng 1,834 → 1,986 m) và s04f11 idx 7 (rộng 3,333 → 2,830 m).
- Cảnh báo giữ nguyên: s01f10 idx 6 và 8, s02f14 idx 3 và 8, s03f50 idx 2, s04f11 idx 2 và 6. Lý do: đã xem lại trên CVAT, thấy hộp khớp cụm điểm. Hộp ngắn (s01f10 idx 6, 8; s02f14 idx 8) có thể do chỉ thấy một phần xe; các hộp còn lại rộng hơn khoảng nên vẫn có thể bao dư.

Đổi kiểm: chưa ghi lại kết quả đổi kiểm.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
(a) Ý nghĩa, nói từ phía model:

- Model cần vị trí và kích thước thật để lái xe; mét không đổi theo khoảng cách hay camera, còn pixel của cùng một vật thì đổi.
- Ít điểm thì không đủ bằng chứng về biên hộp; nếu mỗi người đoán một kiểu, model sẽ học từ nhãn nhiễu.

(b) Dùng AI:

- Công cụ: Claude Code.
- Câu hỏi chính: lệnh và các bước nộp bài, cách chụp ảnh, một số edge case, lý thuyết LiDAR và hệ trục tọa độ; nhờ đọc ảnh chụp, ảnh `preview`, bảng A và phiếu tự kiểm để soạn báo cáo.
- Dùng kết quả thế nào: AI soạn nháp phần lớn mục 1–6, chạy `pack`, bộ kiểm, commit và push; AI không vẽ cuboid.

(c) Điều còn chưa chắc: ảnh camera mới được đối chiếu ở 7/10 frame khi soạn báo cáo; s01f10 idx 7 (Cyclist) không thấy rõ người đi xe trong `preview`, có thể là cây hoặc hàng rào; cột `occluded` của cả 57 cuboid là 0 dù có thể có vật bị che một phần; ảnh `t2-a-car` và `t2-b-bus-truck` chụp trước lần sửa hộp cuối; ảnh `t4-2` là xe cách 20,06 m, chưa phải 30–50 m; s01f10 idx 8 có đáy thấp hơn mặt đường ước lượng 0,92 m và chỉ 70 điểm.
