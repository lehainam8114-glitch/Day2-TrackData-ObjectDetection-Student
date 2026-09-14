# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Lê Hải Nam<br>
**MSSV:** 2A202602070<br>
**Hình thức:** SOLO<br>
**Mã cặp:** Không

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế | xe van nhỏ; xe tải; ô tô con |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng | thân xe buýt; khoang hàng tách biệt như xe tải |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy) | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy |
| `boundary` (quan hệ mép ảnh) | `inside` (trong ảnh), `truncated` (bị cắt) | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại) | đánh dấu quyết định cần quay lại |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: `drive_008` / VAN 23 — xe thân hộp nhỏ màu trắng, kích thước 22.5×36.6px
- Dấu hiệu nhìn thấy: thân hộp kín, kích thước nhỏ hơn xe buýt rõ rệt, không thấy nhiều cửa sổ hàng ghế
- Quy tắc áp dụng: thân hộp nhỏ, kín, không phải thân xe khách dài → `van` (mã 3); xe buýt cần thân dài với nhiều cửa sổ hoặc hàng ghế rõ ràng
- Quyết định: gán `van`
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đánh dấu `needs_review`, phóng to 100% kiểm lại, nếu vẫn không rõ → hỏi Lab Coach và ghi vào nhật ký

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `drive_038` / xe cẩu cảnh sát (tow truck) ở giữa ảnh
- Dấu hiệu nhìn thấy: có cabin trắng, cần cẩu và thiết bị công vụ gắn trên khung phía sau, chữ "公安" trên thân
- Quy tắc áp dụng: thiết bị công vụ rõ ràng gắn trên khung → `truck` (mã 1); không phải `van` vì khoang sau không kín/hộp
- Quyết định: gán `truck`
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? So sánh với định nghĩa lớp, nếu vẫn chưa chắc → đánh `needs_review` và hỏi Lab Coach về xe chuyên dụng

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `drive_008` / VAN 23 — xe nhỏ bị cắt mép ảnh bên trái
- Dấu hiệu nhìn thấy khi phóng 100%: chỉ thấy khoảng 40–50% thân xe, phần còn lại ra ngoài khung ảnh
- Giá trị `visibility`: `occluded` (bị che một phần, không nhìn thấy toàn thân)
- Giá trị `boundary`: `truncated` (xe bị mép ảnh cắt)
- Trạng thái `review_state`: `needs_review` (kích thước quá nhỏ, không chắc ngưỡng tối thiểu)
- Lý do: vật thể nhỏ (22.5×36.6px) bị cắt mép, vẫn đủ nhận ra lớp `van` nhưng không chắc có đủ ngưỡng kích thước để annotate → đánh dấu để hỏi Lab Coach

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [x] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [ ] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: 26 vật thể trong drive_008 — 40–60 là mục tiêu khối lượng, không phải điểm cắt.

