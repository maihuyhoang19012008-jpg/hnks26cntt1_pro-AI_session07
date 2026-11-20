

# Bài tập tổng hợp kết quả học tập 

## PHẦN 1  Các Prompt đọc và xử lý 4 file

### Prompt 1: Đọc File 1 (Danh sách sinh viên)

```
Bạn là trợ lý xử lý dữ liệu. File đính kèm là danh sách sinh viên của lớp Java cơ bản.
Hãy:
1. Đọc toàn bộ file, trích xuất từng bản ghi thành bảng có cột: [Mã SV] | [Họ tên] | [Lớp] | [Ghi chú].
2. Chuẩn hóa Mã SV: giữ đúng định dạng gốc.
3. Chuẩn hóa Họ tên: bỏ khoảng trắng , viết hoa chữ cái đầu mỗi từ.
4. Phát hiện các bản ghi có cùng Mã SV liệt kê riêng.
5. Phát hiện các bản ghi có cùng Họ tên nhưng khác Mã SV và liệt kê riêng.
6. Xuất bảng dưới dạng Markdown. Không được tự ý thêm, xóa hay sửa bất kỳ thông tin nào trong file.
```

### Prompt 2: Đọc File 2 (Chuyên cần + Bài tập)

```
File đính kèm chứa kết quả điểm chuyên cần và điểm bài tập. Cấu trúc file có thể không đồng nhất.
Hãy:
1. Trích xuất thành bảng: [Mã SV] | [Điểm chuyên cần] | [Điểm bài tập].
2. Chỉ nhận Mã SV làm khóa định danh. Không dùng họ tên để ghép dữ liệu.
3. Kiểm tra thang điểm: mọi điểm phải nằm trong [0, 10]. Điểm nào ngoài khoảng này → đánh dấu "Bất thường".
4. Ô nào trống/không có giá trị → ghi "Thiếu dữ liệu", tuyệt đối không tự điền.
5. Nếu một Mã SV xuất hiện nhiều lần với điểm khác nhau → đánh dấu "Trùng bản ghi" và liệt kê tất cả giá trị.
6. Xuất bảng Markdown kèm danh sách bất thường.
```

### Prompt 3: Đọc File 3 (Kiểm tra giữa kỳ)

```
File đính kèm chứa kết quả kiểm tra giữa kỳ.
Hãy:
1. Trích xuất thành bảng: [Mã SV] | [Điểm giữa kỳ].
2. Chuẩn hóa định dạng điểm (ví dụ 7.5 hay 7,5 → thống nhất 7.5).
3. Điểm nằm ngoài [0,10] → "Bất thường". Ô trống → "Thiếu dữ liệu".
4. Mã SV xuất hiện nhiều lần → "Trùng bản ghi", liệt kê mọi giá trị.
5. Xuất bảng Markdown kèm danh sách bất thường. Không suy diễn nguyên nhân điểm thấp.
```

### Prompt 4: Đọc File 4 (Thi cuối kỳ + Nhận xét)

```
File đính kèm chứa kết quả thi cuối kỳ và nhận xét của giảng viên.
Hãy:
1. Trích xuất thành bảng: [Mã SV] | [Điểm cuối kỳ] | [Nhận xét giảng viên].
2. Giữ nguyên nguyên văn nhận xét của giảng viên. Không tóm tắt, không thêm bớt.
3. Điểm cuối kỳ ngoài [0,10] → "Bất thường". Ô trống → "Thiếu dữ liệu".
4. Mã SV trùng → "Trùng bản ghi", liệt kê mọi giá trị.
5. Xuất bảng Markdown kèm danh sách bất thường.
```

---

## PHẦN 2  Prompt đối chiếu dữ liệu

```
Bây giờ bạn có 4 bảng dữ liệu đã trích xuất:
- Bảng A (File 1): Mã SV | Họ tên
- Bảng B (File 2): Mã SV | Chuyên cần | Bài tập
- Bảng C (File 3): Mã SV | Giữa kỳ
- Bảng D (File 4): Mã SV | Cuối kỳ | Nhận xét

Hãy thực hiện đối chiếu theo đúng quy tắc sau:
1. KHÓA CHÍNH là Mã SV. Tuyệt đối không ghép theo Họ tên.
2. Lấy hợp nhất (full outer join) tất cả Mã SV xuất hiện ở bất kỳ bảng nào.
3. Với mỗi Mã SV, gom đủ các cột: Họ tên, Chuyên cần, Bài tập, Giữa kỳ, Cuối kỳ, Nhận xét.
4. Nếu một đầu điểm bị thiếu → ghi "Thiếu dữ liệu", không tự điền.
5. Nếu một Mã SV có nhiều giá trị cùng một đầu điểm → ghi "Trùng dữ liệu: [giá trị 1] / [giá trị 2]".
6. Nếu hai Mã SV khác nhau có cùng Họ tên → đánh dấu "Trùng tên, khác mã".
7. Nếu Mã SV có ở File 1 nhưng không có điểm ở bất kỳ file nào → đánh dấu "Không có điểm".
8. Nếu Mã SV có điểm nhưng không có trong File 1 → đánh dấu "Lạ".
9. Xuất: (a) Bảng tổng hợp đầy đủ; (b) Danh sách bất thường tách riêng.
```

---

## PHẦN 3  Bảng kết quả tổng hợp 30 sinh viên (mẫu)


| Mã SV | Họ tên | CC | BT | GK | CK | Tổng kết | Kết quả |
|-------|--------|----|----|----|----|----------|---------|
| SV001 | Nguyễn Văn An | 9 | 8 | 7 | 6 | 7.0 | Đạt |
| SV002 | Trần Thị Bình | 8 | 7 | 6 | 5 | 6.0 | Đạt |
| SV003 | Lê Văn Cường | 10 | 9 | 8 | 7 | 8.0 | Đạt |
| SV004 | Phạm Thị Dung | 7 | 6 | 5 | 4 | 5.0 | Đạt (biên giới) |
| SV005 | Hoàng Văn Em | **Thiếu** | 8 | 7 | 6 | — | Thiếu dữ liệu |
| SV006 | Vũ Thị Hà | 8 | **Thiếu** | 7 | 6 | — | Thiếu dữ liệu |
| SV007 | Bùi Văn Hùng | 9 | 8 | **Thiếu** | 7 | — | Thiếu dữ liệu |
| SV008 | Đặng Thị Lan | 7 | 6 | 5 | **Thiếu** | — | Thiếu dữ liệu |
| SV009 | Nguyễn Văn An | 6 | 5 | 4 | 3 | 4.0 | Chưa đạt |
| SV010 | Trần Văn Minh | 5 | 4 | 3 | 2 | 3.0 | Chưa đạt |
| SV011 | Lê Thị Nga | 8 | 8 | 8 | 8 | 8.0 | Đạt |
| SV012 | Phạm Văn Oanh | 7 | 7 | 7 | 7 | 7.0 | Đạt |
| SV013 | Hoàng Thị Phượng | 6 | 6 | 6 | 6 | 6.0 | Đạt |
| SV014 | Vũ Văn Quang | 9 | 9 | 9 | 9 | 9.0 | Đạt |
| SV015 | Bùi Thị Rừng | 4 | 3 | 2 | 1 | 2.0 | Chưa đạt |
| SV016 | Đặng Văn Sáng | 8 | 7 | 8 | 7 | 7.4 | Đạt |
| SV017 | Nguyễn Thị Tâm | 7 | 8 | 6 | 7 | 6.9 | Đạt |
| SV018 | Trần Văn Út | 5 | 5 | 5 | 5 | 5.0 | Đạt (biên giới) |
| SV019 | Lê Thị Vi | 10 | 10 | 9 | 9 | 9.3 | Đạt |
| SV020 | Phạm Văn Xuyên | 3 | 4 | 3 | 4 | 3.6 | Chưa đạt |
| SV021 | Hoàng Thị Yến | 8 | 9 | 8 | 9 | 8.6 | Đạt |
| SV022 | Vũ Văn Zụng | 6 | 5 | 6 | 5 | 5.4 | Đạt |
| SV023 | Bùi Thị Ánh | 7 | 7 | 6 | 6 | 6.3 | Đạt |
| SV024 | Trần Thị Bình | 8 | 9 | 7 | 8 | 7.9 | Đạt |
| SV025 | Nguyễn Văn Dũng | 5 | 6 | 4 | 5 | 4.9 | Chưa đạt (biên giới) |
| SV026 | Trần Thị Giang | 9 | 8 | 9 | 8 | 8.4 | Đạt |
| SV027 | Lê Văn Hậu | 6 | 6 | 5 | 5 | 5.3 | Đạt |
| SV028 | Phạm Thị Hương | 7 | 8 | 7 | 8 | 7.6 | Đạt |
| SV029 | Hoàng Văn Khánh | 4 | 5 | 3 | 4 | 3.9 | Chưa đạt |
| SV030 | Vũ Thị Linh | 8 | 7 | 8 | 7 | 7.4 | Đạt |

---

## PHẦN 4  Báo cáo thống kê tỷ lệ đạt / chưa đạt

| Chỉ tiêu | Số lượng | Tỷ lệ |
|----------|----------|-------|
| Tổng số sinh viên | 30 | 100% |
| Có đủ dữ liệu | 26 | 86.7% |
| **Đạt học phần (≥5.0)** | **20** | **66.7% / 76.9%*** |
| **Chưa đạt (<5.0)** | **6** | **20.0% / 23.1%*** |
| Thiếu dữ liệu (không xếp loại) | 4 | 13.3% |

> *Tỷ lệ thứ nhất tính trên tổng 30 SV; tỷ lệ thứ hai tính trên 26 SV có đủ dữ liệu.

**Điểm trung bình:**
- Nhóm đạt: 7.08 / 10
- Nhóm chưa đạt: 3.57 / 10
- Sinh viên điểm cao nhất: SV019 (9.3)
- Sinh viên điểm thấp nhất (có đủ dữ liệu): SV015 (2.0)

---

## PHẦN 5  Danh sách trường hợp bất thường

| STT | Loại bất thường | Đối tượng | Mô tả | Hành động đề xuất |
|--|--|--|--|--|
| 1 | Trùng tên, khác mã | SV001 & SV009 | Cùng tên "Nguyễn Văn An" nhưng 2 mã khác nhau | Giữ riêng, không ghép |
| 2 | Trùng tên, khác mã | SV002 & SV024 | Cùng tên "Trần Thị Bình" nhưng 2 mã khác nhau | Giữ riêng, không ghép |
| 3 | Thiếu điểm chuyên cần | SV005 | File 2 không có điểm CC | Liên hệ giảng viên bổ sung |
| 4 | Thiếu điểm bài tập | SV006 | File 2 không có điểm BT | Liên hệ giảng viên bổ sung |
| 5 | Thiếu điểm giữa kỳ | SV007 | File 3 không có điểm GK | Liên hệ giảng viên bổ sung |
| 6 | Thiếu điểm cuối kỳ | SV008 | File 4 không có điểm CK | Liên hệ giảng viên bổ sung |
| 7 | Điểm biên giới đạt | SV004, SV018 | Tổng kết đúng bằng 5.0 | Ghi chú, đối chiếu lại chấm |
| 8 | Điểm biên giới trượt | SV025 | Tổng kết 4.9 (chỉ thiếu 0.1) | Ghi chú, đối chiếu lại chấm |
| 9 | Trùng bản ghi (ví dụ) | SV012 trong File 2 | Xuất hiện 2 lần với BT = 7 và BT = 7.5 | Xác nhận giá trị đúng với giảng viên |

---

## PHẦN 6 — Báo cáo nhận xét tình hình học tập (khoảng 500 từ)

### Tình hình học tập học phần Java cơ bản  Lớp 30 sinh viên

Kết quả tổng hợp 30 sinh viên cho thấy tình hình học tập của lớp ở mức khá, nhưng vẫn tồn tại nhiều vấn đề cần lưu ý. 
- Trong tổng số 30 sinh viên, có 26 em có đủ dữ liệu điểm để xếp loại, chiếm 86,7%; 4 em còn lại bị thiếu một trong bốn đầu điểm và chưa thể kết luận đạt hay chưa đạt.
- Nhìn chung, tỷ lệ đạt học phần tương đối khả quan. Trong nhóm có đủ dữ liệu, 20/26 em đạt.

Tuy nhiên, nhóm chưa đạt cũng đáng quan tâm. 6 em có điểm tổng kết dưới 5.0, trong đó điểm trung bình chỉ 3.57. 
**Cụ thể, SV015 chỉ đạt 2.0 điểm mức rất thấp**
Lớp này đạt kết quả khá tốt với gần 77% sinh viên đủ điều kiện đạt trong nhóm có đủ dữ liệu. Tuy nhiên, nhà trường cần:
 - (1) bổ sung kịp thời điểm cho 4 sinh viên thiếu dữ liệu; 
 - (2) xem xét lại các trường hợp điểm biên giới;
 - (3) rà soát quy trình thu thập và đối chiếu điểm để giảm thiểu sai sót; 
-  (4) hỗ trợ thêm cho nhóm sinh viên có điểm thấp, đặc biệt là rèn luyện kỹ năng làm bài thi tổng hợp.


