# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con chở người: sedan, SUV, taxi, bán tải | Nóc thấp, capo dài nhô ra phía trước |
| `truck` | Xe chở hàng: xe tải, xe ben, xe đầu kéo | Cabin ngắn phía trước, thùng hàng to phía sau |
| `bus` | Xe chở khách lớn, kể cả xe buýt hai tầng và minibus | Thân rất dài, có dãy cửa sổ dài |
| `van` | Xe van, minivan | Hình hộp, nóc cao hơn car, ngắn hơn bus, capo rất ngắn hoặc gần như không có |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: cạnh box chạm đúng mép xe, không lấy bóng xe hay mặt đường. Mỗi xe chỉ một box.
- Vật thể bị che khuất: vẽ box quanh phần nhìn thấy nếu vẫn nhận ra loại xe.
- Vật thể bị cắt bởi rìa ảnh: box chạm đúng rìa ảnh, không đoán phần bên ngoài.
- Vật thể rất nhỏ/mơ hồ: không chắc loại xe thì không gán, ghi lại và hỏi giảng viên. Xe đỗ ngoài lòng đường vẫn gán theo loại xe.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001` | Xe trắng vuông vức đỗ trước tòa nhà, nhãn nháp ghi truck, không rõ là van, SUV hay xe tải nhỏ | car | Không có thùng hàng tách rời cabin nên không phải truck. Xe có capo và to ngang các xe con bên cạnh nên chọn car thay vì van |
| `drive_001` | Dãy xe đỗ trong bãi và trên vỉa hè bên trái, không nằm trên lòng đường | Vẫn gán theo loại xe (car) | Schema ghi xe đang đỗ vẫn gán theo loại xe. Giảng viên xác nhận áp dụng cả với xe đỗ ngoài lòng đường |
| `drive_052` (eval) | Xe buýt hai tầng ở giữa ảnh nhưng nhãn eval ghi lớp 3 (van). Model train trên Colab dự đoán chiếc xe này là bus | bus. Không sửa nhãn eval | Schema: bus là xe buýt chở khách, van là xe van hoặc minivan. Xe dài, nhiều cửa sổ, chở khách nên là bus. Giảng viên xác nhận xe buýt hai tầng là bus |
