# Báo cáo Day 13 — Nguyễn Thị Hương Giang, 2A202603233

## 1. Kết luận trước

Mã số 2A202603233, task `D13-2A202603233`, 10 frame: `s01f11`, `s01f31`, `s02f15`, `s02f52`, `s03f02`, `s03f24`, `s04f28`, `s04f51`, `s05f14`, `s05f30`. Hệ điều hành Windows, trình duyệt `Chrome `, CVAT 2.74.1. Người ngồi cạnh: 2A202603176.

Tổng cộng 69 cuboid (65 Car, 1 Bus, 1 Truck, 1 Pedestrian, 1 Cyclist). Frame ít nhất là `s03f24` (hiện 2 hộp) vì phần lớn vật nằm xa trên 3D không hiển thị. Phiếu `selfcheck` cờ  hiện 4 trên `<69>` cuboid. Tôi tin nhất hướng hộp (xe cùng chiều có yaw quanh 270°, xe ngược chiều quanh 90°) và đáy hộp (61/69 hộp lệch so với mặt đường cục bộ dưới 0,3 m). Phần còn yếu nhất là các vật xa trên 30 m, nơi chỉ còn vài chục điểm, và chiều cao hộp của một số xe.

Cách dùng CVAT:
- Đổi góc nhìn: chuột trái xoay, chuột phải dời, con lăn zoom, nháy đúp vào chỗ trống để về góc nhìn mặc định; dùng góc nhìn từ trên xuống để đọc hướng và vị trí. 
- Vẽ một cuboid: chọn label, bấm Shape, nháy đúp vào cụm điểm để đặt hộp; chỉnh tâm, dài, rộng, yaw ở khung Top; chỉnh độ cao và chiều cao ở khung Side và Front.
- Bật Appearance → Cuboid orientation để thấy vạch đỏ (+X của hộp) chỉ về đầu xe; chạy `python scripts/lab.py preview` để xem cuboid trên ảnh camera.

## 2. Đọc frame (Bước 2)

Hai frame đọc kỹ. Ảnh: `t1-s01f11-3d`, `t1-s01f11-top`, `t1-<frame 2>-3d`, `t1-<frame 2>-top`.

Hai frame đọc kỹ. Ảnh: `t1-s01f11-3d`, `t1-s01f11-top`, `t1-s01f31-3d`, `t1-s01f31-top` (ảnh nhìn từ trên xuống, có đánh dấu phía trước, +x, +y).

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| `s01f11` | Phía trước (−y) ở cạnh trên của ảnh; +x chỉ sang trái, +y chỉ xuống | Phẳng; thấp hơn LiDAR khoảng 1,8 m (đáy hộp ở 6,8 m, 24 m và 39 m đều quanh −1,81 đến −1,85 m) | Khoảng 39 m (xe buýt, 483 điểm) | Xe con, xe buýt, người đi bộ; cây, nhà cao tầng, biển chỉ đường, vạch kẻ đường | Phía sau: 2 xe cùng chiều (~22 m và ~36 m). Hai bên: 4 xe ở làn ngược chiều bên trái (~13–25 m); 1 người đi bộ bên phải, phía trước (~14 m) | dải điểm sáng dài bên trái làn đường, cách tâm khoảng 30 m, chưa xác định là hàng rào, tường hay dải phân cách |
| `s01f31` | Giống `s01f11` (hệ trục không đổi giữa các frame): trước ở cạnh trên, +x trái, +y xuống | Phẳng; thấp hơn LiDAR khoảng 1,75 m (đáy ba hộp từ −1,73 đến −1,79 m) |  ~ 36 m | car | Phía trước: 1 xe cùng chiều (~11 m). Phía sau: 2 xe (~9,5 m và ~18 m)  | Các xe ở phía xa ~ 40 m |

Ba điều chỉ camera cho biết: (1) màu sắc của xe và xe buýt; (2) vạch kẻ đường, mũi tên làn và nội dung biển chỉ đường; (3) phân biệt đầu hay đuôi xe nhờ đèn và kính.
Ba điều chỉ LiDAR cho biết: (1) khoảng cách và kích thước thật của vật tính bằng mét; (2) vật ở phía sau và những hướng không camera nào chiếu tới, vì LiDAR quét 360°; (3) độ cao của mặt đường so với LiDAR (khoảng 1,8 m) và hình dạng 3D của vật.


Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| `s02f15` | Cạnh trên (−y) | Làn bên trái có 6 xe ngược chiều nối đuôi dày từ ~4 m đến ~22 m, che nhau; xe cùng chiều chỉ có 2 chiếc (~12 m phía trước, ~19 m phía sau) |
| `s02f52` | Cạnh trên (−y) | 4 xe ngược chiều bên trái; xe xa nhất (~31 m) chỉ còn khoảng 120 điểm |
| `s03f02` | Cạnh trên (−y) | Cả 8 xe đều cùng chiều xe thu, xếp song song, dày ở cự ly 4–25 m |
| `s03f24` | Cạnh trên (−y) | Rất ít vật: chỉ thấy xe cùng chiều ở phía trước (~25 m và ~36 m), phía sau và hai bên chưa thấy vật đủ điều kiện |
| `s04f28` | Cạnh trên (−y) | Có 1 xe hai bánh có người lái (Cyclist) phía sau bên phải ~12 m; 4 xe ngược chiều bên trái |
| `s04f51` | Cạnh trên (−y) | 3 xe cùng chiều phía trước bên phải (~4–19 m); 2 xe phía sau (28–32 m) chỉ còn 131–223 điểm |
| `s05f14` | Cạnh trên (−y) | 1 xe tải (truck) ngược chiều phía trước bên trái (~21 m); 1 vật lớn, cao khoảng 3 m ở phía sau (~33 m) |
| `s05f30` | Cạnh trên (−y) | 4 xe cùng chiều bên phải và 3 xe ngược chiều bên trái ở khoảng 16–17 m |

## 3. Ba cuboid có hướng dẫn (Bước 3)

Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | `s01f11`, #1 | Car | Có: vạch đỏ chỉ về phía trước (−y), cùng chiều xe thu dữ liệu; `yaw_deg` = 272,6° (gần 270°) | Có: z đáy = −1,813 m, lệch −0,04 m so với mặt đường cục bộ; đáy sát hàng điểm mặt đường ở Side và Front | Có: Top, Side, Front và ảnh `t2-a-car` | Rộng 1,76 m và cao 1,40 m nằm trong khoảng xe con; dài 5,81 m vượt khoảng (3,8–5,3 m), `<CẬP NHẬT sau khi thu hộp>` |
| (b) xe buýt hoặc xe tải | `s01f11`, #3 | Bus | Có: vạch đỏ chỉ về phía trước (−y); `yaw_deg` = 282,0°, lệch 12° so với 270° | Có: z đáy = −1,814 m, lệch +0,01 m; đáy sát hàng điểm mặt đường ở Side và Front | Có: Top, Side, Front và ảnh `t2-b-bus-truck` | Cao 3,04 m nằm trong khoảng xe buýt thường (3,0–3,5 m); dài 13,81 m và rộng 2,88 m vượt khoảng (10–12,5 × 2,5–2,6 m)|
| (c) người đi bộ hoặc xe hai bánh | `s01f11`, #8 | Pedestrian | Có: vạch đỏ chỉ theo hướng đi của người, `yaw_deg` = 297,3° | Có: z đáy = −1,602 m, lệch +0,14 m | Có: Top, Side, Front và ảnh `t2-c-ped-cyclist`| Dài 1,19 m, rộng 1,20 m, cao 2,16 m vượt khoảng người lớn (0,4–0,9 × 0,5–0,9 × 1,50–1,95 m) ở cả ba chiều; hộp có thể đang ôm thêm điểm của vật sát bên |

Vòng hiệu chuẩn: Tôi không tham gia vòng hiệu chuẩn với coach, nên chưa có so sánh với các bạn. Khi tự vẽ, tôi dựa vào guideline và bảng 4.3 để kiểm hướng, đáy và kích thước.

Vật bỏ qua vì không đủ cơ sở: Không có.

## 4. Bốn hiện tượng chất lượng (Bước 4)

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s03f00 / t4-1-bong-che.png | Xe/hàng rào chắn đường truyền tia | Các vùng sau cấu trúc đặc có ít điểm hơn phần mặt đường nhìn thấy; #10 có occluded | Vùng trống chưa chắc là không có xe; em bật occluded cho xe bị che và chỉ vẽ phần có đủ cơ sở suy đoán |
| 2. Thưa ở xa | s04f51 /t4-2-thua-o-xa | Mật độ điểm LiDAR giảm khi xa cảm biến (giả thuyết) | Car #10 ở ~31 m, có ít điểm hơn các xe lân cận | Vẽ nếu thấy khoảng 15–20% thể tích hoặc suy luận được; không thì bỏ qua, ghi LOW_VISIBILITY |
| 3. Phản xạ thấp / ít điểm bất thường | s04f28 / t4-3-phan-xa-thap | Giả thuyết: xe tối màu hoặc nhiều kính nên phản xạ yếu | trên lidar không bắt được điểm nào của xe | không đủ cơ sở để gán |
| 4. Điểm lạ | s03f00 / t4-4-diem-la.png | Có thể là phản xạ nhiều đường hoặc điểm rời; cũng chưa loại trừ bề mặt thật | Có dải điểm lẻ ở vùng trống phía trên dải đường trong góc chụp, chưa xác định vật tương ứng; ảnh này chưa đủ để kết luận nằm dưới đường | Em không gán cụm điểm này thành vật; cần xoay ngang và đối chiếu camera để tránh nhận nhầm nhiễu thành chướng ngại vật |

## 5. Số từ cuboid của tôi (Bước 5)

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét |
|---|---|---|---|---|---|---|---|
| `s01f11` #1 Car | −1,112 | 1,402 | −1,813 | 6,80 | 6,89 | 272,6 | Chạm đường (lệch −0,04 m so với mặt đường cục bộ) |
| `s01f11` #2 Car | −1,087 | 1,530 | −1,852 | 24,32 | 24,35 | 270,3 | Chạm đường (lệch −0,05 m) |
| `s01f11` #3 Bus | −0,296 | 3,036 | −1,814 | 39,28 | 39,28 | 282,0 | Chạm đường (lệch +0,01 m) |

(a) Cuboid gần nhất là `s03f02` #2 (Car): `dist_m` = 3,81 m, r = 3,95 m. Cuboid xa nhất là `s01f11` #3 (Bus): `dist_m` = 39,28 m, r = 39,28 m.

(b) Xe con `s01f11` #2 (4,79 × 1,74 × 1,53 m) nằm trong khoảng sedan/hatchback của bảng 4.3 (3,8–5,0 × 1,70–1,90 × 1,40–1,60 m), không chiều nào ngoài khoảng. Cyclist `s04f28` #7 (2,19 × 0,87 × 1,63 m) nằm trong khoảng xe máy có người. Pedestrian `s01f11` #8 (1,19 × 1,20 × 2,16 m) vượt khoảng người lớn (0,4–0,9 × 0,5–0,9 × 1,50–1,95 m) ở cả ba chiều.

(c) Xe cùng chiều phía trước `s01f11` #2 có `yaw_deg` = 270,3°, gần 270°. Xe ngược chiều `s01f11` #7 có `yaw_deg` = 92,1°, gần 90°.

Mật độ theo khoảng cách (Car):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| `s01f11` #1 Car | 6,80 | 1920 |
| `s02f15` #6 Car | 14,26 | 738 |
| `s01f11` #2 Car | 24,32 | 256 |
| `s01f11` #5 Car | 36,45 | 82 |

Quy luật: xa gấp khoảng 5,4 lần (từ 6,8 m đến 36,5 m) thì số điểm còn khoảng 1/23, xấp xỉ tỉ lệ nghịch bình phương khoảng cách (mũ khoảng 1,9). So với đường cong ở slide (1968 điểm ở 0–10 m, 718 ở 10–20 m, 214 ở 20–30 m, 72 ở 30–40 m), bốn giá trị của tôi nằm sát đường cong.

Tự kiểm: hai cuboid tôi đã sửa sau `selfcheck`:
- `s03f24` #2: đáy cao hơn mặt đường 0,94 m (lơ lửng); tôi hạ hộp xuống mặt đường, z từ −0,29 xuống −0,89 m, z đáy từ −1,07 xuống −1,67 m.
- `s02f52` #6: đáy cao hơn mặt đường 0,54 m (lơ lửng); tôi hạ hộp và chỉnh lại vị trí, z từ −0,51 xuống −0,92 m, z đáy từ −1,37 xuống −1,78 m.

Cảnh báo giữ nguyên kèm lý do:
- `s01f11` #6 và #10: báo đáy thấp hơn mặt đường 0,68 và 0,94 m, trong khi z đáy của hai hộp là −1,60 và −1,95 m. 
- `s04f28` #6: đáy cao hơn mặt đường 0,80 m, chỉ có 36 điểm ở 34,81 m, hạ đáy xuống mặt đường
- `s05f14` #4: đáy cao hơn mặt đường 0,73 m, hạ đáy xuống mặt đường

**Kiểm chéo với bạn học:** Em đã thực hiện đổi kiểm chéo với bạn ngồi cạnh có MSSV **2A202603176**. Em và bạn đã đối chiếu trực tiếp trên màn hình 2 số liệu giữa báo cáo và Bảng A / phiếu tự kiểm:

| Số được kiểm | Giá trị trong báo cáo | Kết quả đối chiếu với Bảng A / Phiếu tự kiểm |
|---|---|---|
| Đáy cuboid `s01f05 #1` | −1,710 m | Khớp Bảng A (z_bottom = −1.710 m) |
| `points_in` của `s01f05 #8` | 162 điểm | Khớp phiếu tự kiểm (points_in = 162) |

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

(a) Model học trên nhãn 3D phải suy ra vị trí và kích thước thật của vật để xe tránh va chạm hay giữ khoảng cách, nên nhãn đo bằng mét trong hệ tọa độ LiDAR chứ không phải pixel, vì pixel đổi theo camera và khoảng cách. Vật thưa điểm cần quy tắc riêng vì cùng một vật ở xa chỉ còn vài chục điểm, nếu mỗi người tự đoán một kiểu thì nhãn sẽ không nhất quán và model học nhiễu.

(b) Có dùng AI. Công cụ: Claude (Anthropic). Câu hỏi chính: hướng dẫn các bước làm lab, giải thích các cảnh báo của phiếu tự kiểm. Cách dùng kết quả: tôi dùng làm gợi ý và để tính số, còn việc sửa hộp tôi tự làm trên CVAT theo point cloud và ảnh camera. 


