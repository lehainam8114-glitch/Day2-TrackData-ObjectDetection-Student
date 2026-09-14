# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Lê Hải Nam<br>
**MSSV:** 2A202602070<br>
**Hình thức:** SOLO<br>
**Mã cặp:** Không có

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: `F7D99888F21440FB0374D84962B93213BD8C14E665D093CC8D37F4C61B71ED33`
- Bốn mã ảnh: `drive_008`, `drive_022`, `drive_033`, `drive_038`
- Số vật thể thực tế: **76 vật thể** (drive_008: 26, drive_022: 5, drive_033: 22, drive_038: 23)
- Mã SHA-256 của gói YOLO của bạn: `B9167D7687C40B986336E705FE6A15CA8211494FBD432E4269A168505D720E3B`
- Mã SHA-256 của gói CVAT gốc của bạn: `A8CD2B5994B2056775027DAD2DDB1CD16222579C9EC7CA462999E85E555289A9`
- Nguồn đối chiếu: bộ tham chiếu do người hướng dẫn thực hành cấp
- Mã SHA-256 của gói đối chiếu: (nhận từ Lab Coach — điền sau khi có)
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: nhận ngày 14/09/2026, khoảng 04:31 AM (theo timestamp file)

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

Toàn bộ quá trình annotate trên CVAT được thực hiện độc lập trên tài khoản cá nhân trước khi nhận bộ tham chiếu. Hai gói xuất (YOLO và CVAT XML) đã được export và tính SHA-256 xong trước khi mở bất kỳ nguồn đối chiếu nào. Thứ tự thời gian có thể kiểm chứng qua timestamp các file trong Downloads.


## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_??? / vật thể 1 | truck | Xe có cabin trắng, cần cẩu/thiết bị kéo gắn phía sau, thùng chứa phía sau cabin, chữ "公安" trên thân xe | Phương tiện có cabin + thiết bị công việc gắn trên khung → truck; không phải car vì có thùng/thiết bị đặc dụng |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Lớp là **truck** (loại phương tiện — ảnh hưởng đến việc model nhận dạng vật thể nào). Thuộc tính là **màu sắc = trắng** hoặc **công dụng = cảnh sát** — đây là thông tin mô tả thêm về vật thể đó, không thay đổi lớp phân loại.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Ảnh drive_??? / VAN 23: bbox xanh lá của bạn lớn hơn và lệch so với bbox tham chiếu xanh dương; thuộc tính `visibility=unknown`, `boundary=truncated` chưa chắc chắn | thuộc tính + hình học | Nhìn lại thấy box bị lệch so với viền vật thể thực tế; vật thể bị cắt ở mép ảnh nên không rõ toàn thân → đánh dấu `needs_review` | Kéo lại box vừa khít phần nhìn thấy; giữ `boundary=truncated` vì xe bị cắt mép; đặt `visibility=partial` theo quy tắc: chỉ thấy một phần thân xe |

- Số hộp `needs_review` trước và sau khi kiểm: **4 hộp** trước kiểm → **0 hộp** sau khi kiểm và sửa xong
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: VAN 23 bị che/cắt quá nhiều (22.5×36.6px, rất nhỏ), không chắc nên giữ nhãn `van` hay bỏ qua → hỏi Lab Coach để xác nhận ngưỡng kích thước tối thiểu cần annotate.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `1 0.457992 0.221164 0.166609 0.191422`
- Tên lớp và tọa độ điểm ảnh `xyxy`: lớp **truck** (class 1), tọa độ pixel: x1=479, y1=90, x2=692, y2=228 (trên ảnh 1280×720)
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Dòng YOLO chỉ kiểm tra cú pháp (5 số hợp lệ), không kiểm tra nội dung:
- **Sai lớp**: ghán `1` (truck) cho xe đạp — dòng vẫn đúng cú pháp
- **Sai phạm vi**: box bao ra cả nền đường thay vì chỉ vật thể — các số vẫn nằm trong [0,1]
- **Sai hình học**: box quá rộng/hẹp so với vật thể thực tế — định dạng không phát hiện được

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: `drive_022`, `drive_033`, `drive_038`
- Mã ảnh thẩm định: `drive_008`
- Mô tả một dự đoán trong `detect_result.jpg`: Ảnh `drive_008` là giao lộ đông đúc có xe buýt vàng-xanh, xe tải đổ đất màu đỏ, nhiều ô tô và xe máy — nhưng **model không vẽ được bounding box nào** (không có dự đoán nào vượt ngưỡng conf=0.25).
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Gợi ý cần kiểm lại **độ phủ lớp trong dữ liệu train**: ba ảnh train có thể không chứa đủ xe buýt và xe tải đổ đất ở góc nhìn này, hoặc nhãn các lớp đó bị thiếu/sai phạm vi.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Nếu kiểm tra file `drive_022.txt`, `drive_033.txt`, `drive_038.txt` và thấy đã có đủ nhãn `bus`, `truck` với box chính xác → lỗi không phải do thiếu nhãn mà do model quá nhỏ (yolo11n) chưa hội tụ sau 8 epoch với 3 ảnh.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế? Vì tập train chỉ có 3 ảnh và tập val chỉ có 1 ảnh — quá nhỏ để ước lượng precision/recall đáng tin cậy; kết quả phụ thuộc hoàn toàn vào đặc điểm riêng của 4 ảnh đó, không đại diện cho phân phối dữ liệu thực tế.

## 6. Đối chiếu nhãn

- Số hộp ghép được: khoảng 30 cặp (ước tính trực quan trên 4 ảnh, IoU ≥ 0.5)
- IoU trung bình và trung vị: trung bình ≈ 0.65, trung vị ≈ 0.70 (các box lớn như xe buýt, xe tải khớp tốt; xe nhỏ ở xa khớp kém hơn)
- Mức đồng thuận lớp: ≈ 85% — phần lớn các cặp ghép được đều cùng lớp; sai lệch chủ yếu ở `van` vs `car` với xe nhỏ bị che
- Số hộp phía bạn không ghép được: ≈ 8 hộp (bỏ sót một số xe nhỏ/xa ở góc ảnh)
- Số hộp phía đối chiếu không ghép được: ≈ 12 hộp (bộ tham chiếu annotate nhiều xe nền hơn)
- Một điểm khác biệt cụ thể: Trong `drive_038`, xe cẩu (tow truck) ở giữa ảnh — box xanh của bạn bao rộng hơn và cao hơn box đỏ tham chiếu; nguyên nhân là bạn bao gồm cả cần cẩu phía trên cabin, trong khi tham chiếu chỉ bao phần thân xe nhìn thấy rõ.
- Quy tắc hoặc hành động sửa phát sinh: Bổ sung quy tắc rõ hơn — "Box chỉ bao phần thân xe chính, không bao thiết bị gắn ngoài (cần cẩu, thùng hàng nếu không cố định)"; điều chỉnh lại box xe cẩu cho khớp với tham chiếu.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?

Cả hai annotator có thể cùng mắc lỗi hệ thống như nhau (ví dụ: cùng bỏ sót xe bị che >80%, hoặc cùng gán `van` thay vì `truck` cho một loại xe). Đồng thuận cao chỉ đo sự **nhất quán giữa hai người**, không đo **độ đúng so với nhãn vàng** (ground truth thật sự). Cần bộ tham chiếu độc lập từ chuyên gia mới có thể xác nhận tính đúng đắn.

## 7. Kiểm tra kho GitHub cá nhân

- [ x ] Có phiếu quy tắc với ba tình huống mơ hồ.
- [ x ] Có kết quả kiểm hai gói xuất.
- [ x ] Có thông tin lần huấn luyện và ảnh dự đoán.
- [ x ] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x ] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x ] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:


