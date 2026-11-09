# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp     | Định nghĩa của bạn | Ví dụ/điểm phân biệt                                                         |
| ------- | ------------------ | ---------------------------------------------------------------------------- |
| `car`   | Xe con chở người   | Nhỏ nhất, nóc thấp, capo dài nhô ra phía trước                               |
| `truck` | Xe chở hàng        | Cabin ngắn phía trước, thùng hàng to phía sau                                |
| `bus`   | Xe chở khách lớn   | Rất dài, có dãy cửa sổ dài                                                   |
| `van`   | Xe van, minivan    | Hình hộp, nóc cao hơn car, ngắn hơn bus, capo rất ngắn hoặc gần như không có |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: cạnh box chạm đúng mép xe, không lấy bóng hay mặt đường.
- Vật thể bị che khuất: vẽ box quanh phần nhìn thấy.
- Vật thể bị cắt bởi rìa ảnh: box chạm đúng rìa ảnh.
- Vật thể rất nhỏ/mơ hồ: không chắc thì không gán, ghi lại.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh         | Tình huống | Quyết định cuối | Lý do theo schema |
| ----------- | ---------- | --------------- | ----------------- |
| `drive_001` | Xe trắng vuông vức đỗ trước tòa nhà (giữa-trái ảnh), nhãn nháp ghi `truck`; không rõ là van, SUV hay xe tải nhỏ | `car` | Không thấy thùng hàng tách rời cabin nên không phải `truck`; có capo, kích thước ngang các xe con bên cạnh nên chọn `car` thay vì `van` |
| `drive_001` | Dãy xe đỗ trong bãi/vỉa hè bên trái và xe rất nhỏ ở xa góc trên phải, không nằm trên lòng đường | Gán theo loại xe (`car`). Đã hỏi giảng viên: xe đỗ ngoài lòng đường **có gán** | Schema ghi "xe đang đỗ vẫn gán theo loại xe"; giảng viên xác nhận áp dụng cả cho xe đỗ ngoài lòng đường. Nhãn eval `drive_052` không gán dãy xe đỗ bên trái, nên đã hỏi để thống nhất |
| `drive_052` (eval) | Xe buýt hai tầng ở giữa ảnh, nhưng nhãn eval ghi lớp `3` (`van`) | `bus`. Đã hỏi giảng viên: xe buýt hai tầng là `bus`. Không sửa nhãn eval | Schema: `bus` là xe buýt chở khách; `van` là xe van/minivan. Xe dài, nhiều cửa sổ, chở khách nên là `bus`; giảng viên xác nhận |
