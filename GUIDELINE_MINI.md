# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe con chở người, thân liền, ≤ 7 chỗ | sedan, SUV, hatchback; thấp, nóc ngắn, không có thùng hàng |
| `truck` | Xe chở hàng, có thùng hàng tách biệt với cabin | xe tải nhỏ/lớn, xe bán tải chở hàng; nhìn thấy thùng hàng phía sau cabin |
| `bus` | Xe khách/buýt lớn, nhiều cửa sổ dọc thân | xe buýt, xe khách giường/ghế; dài, cao, hàng cửa sổ liên tục |
| `van` | Xe van/minivan: thân liền như car nhưng to và cao hơn, không có thùng hàng rời | minivan, xe van chở hàng/người; cao hơn car, nhỏ hơn bus, ít cửa sổ hơn bus |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: cạnh box chạm đúng mép ngoài phần nhìn thấy của xe, không chừa mặt đường/trời/xe bên cạnh, không co lại cắt mất phần thân xe.
- Vật thể bị che khuất: vẫn gán box quanh phần nhìn thấy nếu nhận diện được lớp; hai xe chồng nhau thì vẽ hai box riêng trên phần nhìn thấy của từng xe.
- Vật thể bị cắt bởi rìa ảnh: box chạm đúng rìa ảnh, không đoán phần nằm ngoài khung hình.
- Vật thể rất nhỏ/mơ hồ: nếu vẫn xác định được lớp thì gán box; nếu chỉ còn mảnh nhỏ không xác định chắc lớp thì không gán, ghi vào ca mơ hồ.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_002` | Xe to, thân liền, ban đầu phân vân giữa `bus` và `van` | Gán `van` | Thân liền, không có hàng cửa sổ dọc thân đặc trưng của bus; kích thước và dáng giống minivan |
| `drive_004` | Xe nhỏ ở xa, phân vân `car` hay `truck` | Gán `truck` | Nhìn kỹ thấy thùng hàng phía sau tách biệt cabin — dấu hiệu của truck dù xe nhỏ |
| `drive_031` | Hàng xe đỗ/xe ở xa rất nhỏ ở làn đối diện và hai bên đường | Chỉ gán xe còn nhận diện được lớp; bỏ qua xe quá nhỏ/mờ không xác định chắc | Theo quy tắc vật thể nhỏ/mơ hồ: không tự đoán lớp khi không chắc, tránh tạo nhãn nhiễu |
