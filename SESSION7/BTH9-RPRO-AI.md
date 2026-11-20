
# Bài tập: Phân tích nghiệp vụ hệ thống quản lý nhân sự bằng AI



## PHẦN 1 : Sơ đồ quy trình xử lý tài liệu bằng AI

```
┌─────────────────────────────────────────────────────────────────┐
│                    GIAI ĐOẠN 1: TIẾP NHẬN & PHÂN LOẠI            │
│  ┌──────────┐   ┌──────────────┐   ┌─────────────────────────┐  │
│  │ 7 tài liệu│→ │ Phân loại chủ│→ │ Đánh giá ưu tiên + đề xuất│  │
│  │  gốc      │   │ đề & định danh│   │ chia nhỏ (chunking)     │  │
│  └──────────┘   └──────────────┘   └────────────┬────────────┘  │
└─────────────────────────────────────────────────┼───────────────┘
                                                  ↓
┌─────────────────────────────────────────────────────────────────┐
│                  GIAI ĐOẠN 2: TRÍCH XUẤT & XỬ LÝ                │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │
│  │ Trích xuất   │→ │ Chuẩn hóa theo│→ │ Tóm tắt từng tài liệu│  │
│  │ quy trình/quy│  │ cấu trúc thống│  │ + gắn nguồn (file+trang)│ │
│  │ định          │  │ nhất          │  │                      │  │
│  └──────────────┘  └──────────────┘  └──────────┬───────────┘  │
└─────────────────────────────────────────────────┼───────────────┘
                                                  ↓
┌─────────────────────────────────────────────────────────────────┐
│                 GIAI ĐOẠN 3: TỔNG HỢP & ĐỐI CHIẾU               │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │
│  │ Tổng hợp quy │→ │ Phát hiện trùng│→ │ Phát hiện mâu thuẫn +│  │
│  │ trình liên quan│  │ lặp          │  │ lỗ hổng thông tin    │  │
│  └──────────────┘  └──────────────┘  └──────────┬───────────┘  │
└─────────────────────────────────────────────────┼───────────────┘
                                                  ↓
┌─────────────────────────────────────────────────────────────────┐
│                  GIAI ĐOẠN 4: PHÂN TÍCH & BÁO CÁO               │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │
│  │ Xác định yêu│→ │ Phân loại NFR/│→ │ Đề xuất chức năng +  │  │
│  │ cầu nghiệp vụ│  │ FR + kiểm chứng│  │ câu hỏi xác nhận BGD │  │
│  └──────────────┘  └──────────────┘  └──────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

**Nguyên tắc xuyên suốt:** 
- Mọi thông tin trích xuất đều gắn `[Mã tài liệu - Trang/Vị trí]`. 
- Mọi suy luận đều đánh dấu `[SUY LUẬN]`. 
- Mọi đề xuất đều đánh dấu `[ĐỀ XUẤT]`. 


## PHẦN 2 : Bộ Prompt theo từng giai đoạn

### Giai đoạn 1: Tiếp nhận và phân loại

**Prompt 1.1 : Phân loại và định danh tài liệu:**
```
Bạn là trợ lý phân tích nghiệp vụ. Tôi sẽ gửi lần lượt từng tài liệu.
Với mỗi tài liệu, hãy xuất bảng: 
[Mã tài liệu] | [Tên file] | [Loại file] |
[Số trang/độ dài] | [Chủ đề chính] | [Loại nội dung: Quy định/Quy trình/Dữ liệu/Vấn đề].
Đánh dấu [CẦN XÁC NHẬN] nếu không chắc chắn. Không suy luận nội dung chi tiết ở bước này.
```

**Prompt 1.2 : Đề xuất chiến lược chia nhỏ (chunking):**
```
Dựa trên loại và độ dài tài liệu, đề xuất cách chia nhỏ để đưa vào AI mà không vượt
giới hạn context. 
Quy tắc: 
- PDF quy định => chia theo chương/điều; DOCX quy trình =>chia theo bước quy trình
- Excel dữ liệu => không đưa vào prompt, chỉ trích xuất thống kê
mô tả. 
Xuất bảng: 
[Mã tài liệu] | [Số đoạn] | [Tiêu chí chia] | [Kích thước ước tính/đoạn].
```

### Giai đoạn 2: Trích xuất và xử lý

**Prompt 2.1  : Trích xuất quy trình nghiệp vụ (cho DOCX):**
```
File đính kèm là mô tả quy trình. Hãy trích xuất thành bảng chuẩn hóa:
[Mã quy trình] | [Tên quy trình] | [Bước] | [Người thực hiện] | [Input] | [Hành động] |
[Output] | [Thời gian/Quy tắc] | [Nguồn: file + trang/đoạn].
Nếu một bước không rõ ràng => ghi [THIẾU THÔNG TIN], không tự suy diễn.
```

**Prompt 2.2 : Trích xuất quy định (cho PDF quy định):**
```
File đính kèm là quy định nhân sự. Trích xuất theo cấu trúc:
[Mã điều] | [Chủ đề] | [Nội dung quy định (nguyên văn)] | [Đối tượng áp dụng] |
[Hiệu lực (nếu có)] | [Nguồn: file + trang].
Tuyệt đối không diễn giải. Giữ nguyên văn. Nếu không có ngày hiệu lực → ghi
[KHÔNG CÓ CĂN CỨ HIỆU LỰC].
```

**Prompt 2.3 : Xử lý dữ liệu nhân sự (Excel):**
```
File Excel chứa dữ liệu 200 nhân viên.
KHÔNG được sửa đổi dữ liệu gốc.
Hãy: 
(1) Xuất thống kê mô tả: số lượng theo phòng ban, theo chức vụ, theo mức lương
(khoảng)
(2) Phát hiện cột nào chứa thông tin cá nhân nhạy cảm (CMND, số tài khoản,
địa chỉ chi tiết) => đề xuất ẩn trong phân tích; 
(3) Phát hiện bản ghi trùng lặp theo
mã nhân viên; 
(4) Phát hiện giá trị thiếu hoặc bất thường. Không xuất danh sách cá nhân.
```

**Prompt 2.4 : Tóm tắt từng tài liệu + gắn nguồn:**
```
Với mỗi tài liệu đã trích xuất, viết tóm tắt 150-200 từ. Mọi ý trong tóm tắt đều phải
kèm mã nguồn [Txx-trang]. 
Phân biệt rõ: (a) thông tin trích xuất trực tiếp; (b) thông
tin suy luận [SUY LUẬN]. 
```

### Giai đoạn 3: Tổng hợp và đối chiếu

**Prompt 1 :Tổng hợp quy trình liên quan:**
```
Tôi có các bảng trích xuất từ nhiều tài liệu. Hãy tổng hợp các quy trình cùng chủ đề
(ví dụ: tuyển dụng, đánh giá, chấm công, nghỉ phép). Với mỗi quy trình tổng hợp, liệt kê
các bước và gắn nguồn tương ứng [Txx-trang]. Nếu hai tài liệu mô tả khác nhau cho cùng
một bước → tách thành 2 dòng, đánh dấu [KHÁC NHAU - CẦN XÁC NHẬN].
```

**Prompt 2 : Phát hiện trùng lặp và mâu thuẫn:**
```
Đối chiếu tất cả nội dung đã trích xuất. Xuất 2 danh sách:
A. Trùng lặp: nội dung giống nhau xuất hiện ở ≥2 tài liệu => [Nội dung] | [Nguồn 1] | [Nguồn 2].
B. Mâu thuẫn: cùng một vấn đề được quy định khác nhau => 
[Vấn đề] | [Quy định A + nguồn] |
[Quy định B + nguồn] | [Mức độ: Cần BGD quyết định/Chỉ là khác biệt cách diễn đạt].
Tuyệt đối không tự chọn quy định nào đúng.
```

**Prompt 3.3 : Xác định thông tin còn thiếu:**
```
Dựa trên các quy trình đã tổng hợp, liệt kê những thông tin cần có nhưng không tìm thấy
trong bất kỳ tài liệu nào. 
Ví dụ: ai phê duyệt nghỉ phép trên 3 ngày? tiêu chí đánh giá
cụ thể? Xuất: [Thông tin thiếu] | [Thuộc quy trình nào] | [Tác động nếu không có].
```

### Giai đoạn 4: Phân tích và báo cáo

**Prompt 1 : Xác định yêu cầu nghiệp vụ:**
```
Từ các quy trình và quy định đã tổng hợp, rút ra yêu cầu nghiệp vụ cho hệ thống phần mềm.
Phân loại: 
[Mã YC] | [Nội dung yêu cầu] | [Loại: Chức năng/Phi chức năng] |
[Quy trình liên quan] | [Nguồn]. Mỗi yêu cầu phải có nguồn. Nếu là suy luận => [SUY LUẬN].
```

**Prompt 2 : Đề xuất chức năng + câu hỏi xác nhận:**
```
Đề xuất danh sách chức năng cần xây dựng, nhóm theo module. Đồng thời, liệt kê những điểm
mâu thuẫn/thiếu thông tin cần ban giám đốc xác nhận. 
Mỗi câu hỏi phải kèm: bối cảnh, các
phương án tìm thấy trong tài liệu, và tác động nếu chọn phương án này hay phương án kia.
```

**Prompt 3 : Kiểm chứng kết luận:**
```
Tôi có danh sách các kết luận quan trọng trong báo cáo. Hãy đối chiếu từng kết luận với
nguồn gốc. Xuất bảng: 
[STT] | [Kết luận] | [Nguồn hỗ trợ (file+trang)] | [Mức độ tin cậy:
Có căn cứ/Cần bổ sung/Suy luận]. Nếu một kết luận không có nguồn => đánh dấu [KHÔNG CĂN CỨ].
```



## PHẦN 3 : Bảng phân loại và quản lý nguồn tài liệu

| Mã | Tên file (ví dụ) | Loại | Số trang | Chủ đề chính | Ưu tiên | Chiến lược chia nhỏ |
|--|--|--|--|--|--|--|
| T01 | Quy định nhân sự tổng thể.pdf | PDF | 80 | Quy định chung: hợp đồng, lương, kỷ luật | Cao | Chia theo chương (I→VIII), ~10 trang/đoạn |
| T02 | Quy định tuyển dụng.pdf | PDF | 50 | Quy trình và tiêu chí tuyển dụng | Cao | Chia theo điều, ~8 trang/đoạn |
| T03 | Quy định đánh giá & khen thưởng.pdf | PDF | 50 | Đánh giá hiệu quả, khen thưởng, kỷ luật | Cao | Chia theo chương, ~8 trang/đoạn |
| T04 | Quy trình tuyển dụng.docx | DOCX | 15 | Các bước tuyển dụng chi tiết | Cao | Chia theo giai đoạn (sơ tuyển→phỏng vấn→trúng tuyển) |
| T05 | Quy trình đánh giá nhân viên.docx | DOCX | 12 | Chu kỳ đánh giá, form đánh giá | Cao | Chia theo bước trong chu kỳ đánh giá |
| T06 | Dữ liệu nhân sự 200 NV.xlsx | Excel | 1 sheet | Dữ liệu cá nhân, phòng ban, chức vụ, lương | Trung bình | Không đưa vào prompt; chỉ trích xuất thống kê mô tả + ẩn thông tin nhạy cảm |
| T07 | Vấn đề vận hành.pdf | PDF | 12 | Các vấn đề phát sinh, khiếu nại, lỗi thủ công | Trung bình | Chia theo loại vấn đề |

**Thông tin nhạy cảm cần ẩn (từ T06):** 
- số CMND/CCCD, số tài khoản ngân hàng, địa chỉ thường trú chi tiết, số điện thoại cá nhân. 
- Chỉ giữ lại: mã NV, phòng ban, chức vụ, thâm niên, mức lương (dạng khoảng).

---

## PHẦN 4  Bảng tổng hợp yêu cầu nghiệp vụ (mẫu)

| Mã YC | Nội dung yêu cầu | Loại | Quy trình liên quan | Nguồn |
|--|--|--|--|--|
| FR01 | Hệ thống lưu hồ sơ nhân viên điện tử với đầy đủ thông tin hợp đồng | Chức năng | Quản lý hồ sơ | T01-tr.12 |
| FR02 | Hỗ trợ đăng ký tuyển dụng, lưu CV, lịch sử phỏng vấn | Chức năng | Tuyển dụng | T02-tr.5; T04-mục 2 |
| FR03 | Tự động gửi email thông báo lịch phỏng vấn cho ứng viên | Chức năng | Tuyển dụng | T04-mục 3 [SUY LUẬN] |
| FR04 | Thiết lập chu kỳ đánh giá 6 tháng/lần| Chức năng | Đánh giá NV | T03-tr.20; T05-mục 1 |
| FR05 | Quản lý ngày nghỉ phép| Chức năng | Nghỉ phép | T01-tr.35 [SUY LUẬN - không quy định rõ kênh phê duyệt] |
| FR06 | Báo cáo biến động nhân sự theo phòng ban theo tháng | Chức năng | Báo cáo | T07 mục 4 (vấn đề thiếu báo cáo) |
| NFR01 | Hệ thống phải phân quyền | Phi chức năng | Bảo mật | T01-tr.40 (quy định bảo mật thông tin) |
| NFR02 | Thời gian phản hồi thao tác phổ biến dưới 2 giây | Phi chức năng | Hiệu năng | [SUY LUẬN - không có trong tài liệu] |
| NFR03 | Dữ liệu nhân sự phải được sao lưu hàng ngày | Phi chức năng | Sao lưu | [SUY LUẬN - không có trong tài liệu] |
| NFR04 | Hỗ trợ 200 nhân viên sử dụng đồng thời | Phi chức năng | Khả năng mở rộng | T06 (200 nhân viên hiện tại) |

---

## PHẦN 5 : Danh sách điểm trùng lặp và mâu thuẫn (mẫu)

### A. Trùng lặp

| STT | Nội dung | Nguồn 1 | Nguồn 2 | Hành động |
|--|---|---|---|---|
| 1 | Quy trình nộp đơn nghỉ phép 3 bước | T01-tr.35 | T03-tr.42 | Gộp, giữ nguồn chính T01 |
| 2 | Tiêu chí đánh giá gồm 5 yếu tố | T03-tr.18 | T05-mục 2 | Gộp, đối chiếu chi tiết từng yếu tố |

### B. Mâu thuẫn (không tự giải quyết — cần BGD xác nhận)

| STT | Vấn đề | Quy định A | Quy định B | Mức độ |
|-----|--------|------------|------------|--------|
| 1 | Thời gian thử việc | T01-tr.8: thử việc 60 ngày | T02-tr.12: thử việc 45 ngày | **Cần BGD quyết định** |
| 2 | Ai phê duyệt nghỉ phép >3 ngày | T01-tr.36: Trưởng phòng | T03-tr.43: Giám đốc bộ phận | **Cần BGD quyết định** |
| 3 | Chu kỳ đánh giá | T03-tr.20: 6 tháng/lần | T05-mục 1: 12 tháng/lần | **Cần BGD quyết định** |
| 4 | Ngưỡng lương thử việc | T01-tr.10: 85% lương chính thức | T02-tr.15: 90% lương chính thức | **Cần BGD quyết định** |
| 5 | Hiệu lực tài liệu | T01 ban hành 2019, T02 ban hành 2021 — không có văn bản bãi bỏ T01 | — | [KHÔNG CÓ CĂN CỨ] kết luận T01 hết hiệu lực |

---

## PHẦN 6 : Báo cáo phân tích nghiệp vụ (khoảng 2.500 từ)

### BÁO CÁO PHÂN TÍCH NGHIỆP VỤ HỆ THỐNG QUẢN LÝ NHÂN SỰ

#### 1. Tổng quan và phương pháp

Báo cáo này tổng hợp kết quả phân tích 07 tài liệu do ban giám đốc cung cấp phục vụ thiết kế hệ thống quản lý nhân sự. Bộ tài liệu gồm 03 file PDF quy định nhân sự (tổng 180 trang), 02 file DOCX mô tả quy trình tuyển dụng và đánh giá nhân viên, 01 file Excel dữ liệu nhân sự của 200 nhân viên, và 01 file PDF ghi nhận các vấn đề phát sinh trong vận hành.

Do khối lượng tài liệu lớn và cấu trúc không đồng nhất, toàn bộ quá trình phân tích được thực hiện theo phương pháp 4 giai đoạn: (1) tiếp nhận và phân loại; (2) trích xuất và xử lý; (3) tổng hợp và đối chiếu; (4) phân tích và báo cáo. Mọi thông tin trích xuất đều được gắn nguồn (mã tài liệu và số trang/vị trí), mọi suy luận đều được đánh dấu tách biệt với dữ liệu gốc, và mọi mâu thuẫn giữa các quy định đều được liệt kê thay vì tự ý giải quyết.

Đặc biệt, dữ liệu nhân sự trong file Excel không bị thay đổi dưới bất kỳ hình thức nào. Các thông tin cá nhân nhạy cảm (số CMND/CCCD, số tài khoản ngân hàng, địa chỉ chi tiết, số điện thoại) được ẩn trong toàn bộ phân tích, chỉ sử dụng dữ liệu dạng tổng hợp hoặc định danh mã nhân viên.

#### 2. Hiện trạng nghiệp vụ

Từ việc trích xuất và tổng hợp, hệ thống nhân sự hiện tại của công ty vận hành chủ yếu dựa trên các quy định văn bản và thủ công. Năm quy trình nghiệp vụ cốt lõi đã được xác định: (i) tuyển dụng; (ii) quản lý hồ sơ nhân viên; (iii) đánh giá hiệu quả công việc; (iv) quản lý nghỉ phép và chấm công; (v) khen thưởng và kỷ luật.

**Quy trình tuyển dụng** được mô tả chi tiết trong T02 và T04, gồm 5 bước chính: tiếp nhận yêu cầu tuyển dụng từ phòng ban, đăng tin và tiếp nhận hồ sơ, sơ tuyển, phỏng vấn (có thể nhiều vòng), và ra quyết định trúng tuyển + ký hợp đồng. Tuy nhiên, hai tài liệu này mô tả khác nhau ở bước phỏng vấn: T02 quy định 2 vòng phỏng vấn (vòng chuyên môn và vòng giám đốc), trong khi T04 mô tả 3 vòng (bao gồm vòng kiểm tra năng lực). Đây là một trong những điểm mâu thuẫn cần làm rõ.

**Quy trình đánh giá nhân viên** được mô tả trong T03 và T05. Cả hai đều thống nhất đánh giá dựa trên 5 tiêu chí: chất lượng công việc, tiến độ, tinh thần hợp tác, kỷ luật, và năng lực giải quyết vấn đề. Tuy nhiên, chu kỳ đánh giá lại khác nhau: T03 quy định 6 tháng/lần, T05 lại nêu 12 tháng/lần. Điều này ảnh hưởng trực tiếp đến thiết kế module đánh giá trong hệ thống.

**Dữ liệu nhân sự (T06)** cho thấy công ty hiện có 200 nhân viên phân bố trên 6 phòng ban. Phòng kỹ thuật chiếm tỷ lệ lớn nhất (38%), tiếp theo là phòng kinh doanh (25%). Mức lương phân bố từ khoảng 5 triệu đến trên 30 triệu đồng/tháng. Có 3 nhân viên bị trùng mã nhân viên trong file Excel (cùng mã nhưng khác tên), đây là lỗi dữ liệu cần làm sạch trước khi nhập hệ thống mới.

#### 3. Các vấn đề phát hiện được

Từ file T07 (vấn đề vận hành) và đối chiếu với quy định, có thể xác định các vấn đề chính:

**Thứ nhất, thủ công và thiếu hệ thống.** Hiện hồ sơ nhân viên được lưu dưới dạng giấy và file rời rạc. Tìm kiếm hồ sơ mất trung bình 15-20 phút (theo ghi nhận T07 mục 2). Việc tính lương, chấm công được thực hiện bằng Excel thủ công, dễ phát sinh sai sót.

**Thứ hai, quy trình không thống nhất.** Như đã nêu, nhiều quy định mâu thuẫn giữa các tài liệu. Điều này gây khó khăn cho cả nhân viên và bộ phận nhân sự trong việc áp dụng. Ví dụ: thời gian thử việc được quy định 60 ngày ở T01 nhưng 45 ngày ở T02; ngưỡng lương thử việc là 85% ở T01 nhưng 90% ở T02. Các mâu thuẫn này không thể tự giải quyết mà cần ban giám đốc phê duyệt chính thức.

**Thứ ba, thiếu thông tin ở một số bước quan trọng.** Ví dụ: không có quy định rõ ai có thẩm quyền phê duyệt nghỉ phép trên 3 ngày (T01 nói Trưởng phòng, T03 nói Giám đốc bộ phận). Không có tiêu chí cụ thể cho điểm đánh giá "tốt" hay "xuất sắc" — chỉ có mô tả định tính. Không có quy định về xử lý khiếu nại đánh giá.

**Thứ tư, vấn đề dữ liệu.** File Excel có 3 bản ghi trùng mã nhân viên, khoảng 5% bản ghi thiếu thông tin ngày vào làm, và một số cột chứa thông tin nhạy cảm không được bảo vệ. Dữ liệu cần được làm sạch và chuẩn hóa trước khi nhập hệ thống mới.

**Thứ năm, hiệu lực tài liệu chưa rõ.** T01 ban hành năm 2019, T02 ban hành năm 2021. Không có văn bản nào bãi bỏ hoặc sửa đổi T01. Do đó, không thể kết luận T01 đã hết hiệu lực — đây là điểm cần ban giám đốc xác nhận, vì nó ảnh hưởng đến việc quy định nào được áp dụng.

#### 4. Yêu cầu nghiệp vụ đề xuất

Dựa trên phân tích, hệ thống quản lý nhân sự cần đáp ứng các yêu cầu chức năng chính sau:

**Module hồ sơ nhân viên:** Lưu trữ hồ sơ điện tử đầy đủ, tìm kiếm nhanh theo mã nhân viên, phòng ban, chức vụ. Hỗ trợ cập nhật thông tin (địa chỉ, số điện thoại, trình độ học vấn) với lịch sử thay đổi. Phân quyền: nhân viên chỉ xem và sửa thông tin cá nhân của mình; bộ phận nhân sự xem toàn bộ; ban giám đốc xem báo cáo tổng hợp.

**Module tuyển dụng:** Quản lý yêu cầu tuyển dụng từ phòng ban, lưu trữ CV ứng viên, lịch sử phỏng vấn, điểm đánh giá từng vòng, và trạng thái ứng viên. Tự động gửi email thông báo lịch phỏng vấn và kết quả. Xuất báo cáo số lượng tuyển dụng theo kỳ.

**Module đánh giá:** Thiết lập chu kỳ đánh giá (sau khi ban giám đốc xác định 6 hay 12 tháng), gửi form đánh giá tự động cho nhân viên và quản lý trực tiếp, tổng hợp điểm, lưu kết quả và lịch sử đánh giá. Hỗ trợ xem xét lại khiếu nại đánh giá.

**Module chấm công và nghỉ phép:** Đăng ký nghỉ phép online, luồng phê duyệt theo thẩm quyền (cần xác nhận rõ), tự động trừ số ngày nghỉ còn lại, báo cáo nghỉ phép theo phòng ban. Tích hợp chấm công (vân tay/máy chấm) nếu có.

**Module báo cáo:** Báo cáo biến động nhân sự (vào/ra), báo cáo tỷ lệ nghỉ việc theo phòng ban, báo cáo tuổi thọ trung bình, báo cáo chi phí nhân sự. Các báo cáo xuất được dạng Excel/PDF.

**Yêu cầu phi chức năng:** Bảo mật dữ liệu cá nhân (phân quyền, mã hóa), hiệu năng (phản hồi dưới 2 giây với 200 người dùng đồng thời), sao lưu dữ liệu hàng ngày, giao diện dễ sử dụng cho nhân viên không chuyên công nghệ, khả năng mở rộng lên 500 nhân viên trong 3 năm tới.

#### 5. Đề xuất giải pháp và lộ trình

Đề xuất xây dựng hệ thống theo mô hình module hóa, ưu tiên triển khai theo thứ tự: (1) Module hồ sơ + phân quyền (nền tảng); (2) Module chấm công & nghỉ phép (giảm tải thủ công ngay lập tức); (3) Module tuyển dụng; (4) Module đánh giá; (5) Module báo cáo nâng cao.

Trước khi triển khai, cần hoàn thành các việc sau: (a) ban giám đốc xác nhận các điểm mâu thuẫn trong quy định; (b) làm sạch dữ liệu nhân sự (xử lý trùng mã, bổ sung thông tin thiếu); (c) ban hành văn bản hợp nhất các quy định để làm căn cứ duy nhất cho hệ thống.

#### 6. Kết luận

Hệ thống quản lý nhân sự là cần thiết để giải quyết các vấn đề thủ công, sai sót và thiếu thống nhất hiện nay. Tuy nhiên, thành công của hệ thống phụ thuộc lớn vào việc làm rõ các quy định mâu thuẫn và chuẩn hóa dữ liệu đầu vào. Báo cáo này đã xác định 5 điểm mâu thuẫn chính, 3 lỗ hổng thông tin quan trọng, và 12 câu hỏi cần ban giám đốc xác nhận trước khi đi vào thiết kế chi tiết. Việc trì hoãn xác nhận các điểm này sẽ dẫn đến rủi ro xây dựng hệ thống theo quy định sai hoặc đã lỗi thời.



## PHẦN 7 : Danh sách câu hỏi cần xác nhận với ban giám đốc

| STT | Câu hỏi | Bối cảnh / Phương án tìm thấy | Tác động nếu không xác nhận |
|--|--|--|--|
| 1 | Thời gian thử việc là 45 hay 60 ngày? | T01-tr.8: 60 ngày; T02-tr.12: 45 ngày | Sai hợp đồng lao động, vi phạm luật |
| 2 | Lương thử việc bằng 85% hay 90% lương chính thức? | T01-tr.10: 85%; T02-tr.15: 90% | Sai tính lương, khiếu nại từ NV |
| 3 | Chu kỳ đánh giá là 6 hay 12 tháng? | T03-tr.20: 6 tháng; T05-mục 1: 12 tháng | Sai thiết kế module đánh giá |
| 4 | Ai phê duyệt nghỉ phép >3 ngày? | T01-tr.36: Trưởng phòng; T03-tr.43: GĐ bộ phận | Sai luồng phê duyệt trong hệ thống |
| 5 | Quy trình phỏng vấn có 2 hay 3 vòng? | T02: 2 vòng; T04: 3 vòng | Sai module tuyển dụng |
| 6 | Quy định T01 (2019) còn hiệu lực hay đã bị T02 (2021) thay thế? | Không có văn bản bãi bỏ | Sai căn cứ áp dụng quy định |
| 7 | Tiêu chí điểm "tốt"/"xuất sắc" trong đánh giá là bao nhiêu điểm? | Chỉ có mô tả định tính | Không thể tự động chấm điểm |
| 8 | Có áp dụng chấm công bằng máy vân tay không? | Không có trong tài liệu | Ảnh hưởng module chấm công |
| 9 | Xử lý khiếu nại đánh giá như thế nào? | Không có quy định | Thiếu tính năng quan trọng |
| 10 | Hệ thống có cần tích hợp với phần mềm tính lương hiện có không? | Không có thông tin | Ảnh hưởng kiến trúc hệ thống |
| 11 | Ngưỡng phê duyệt lương/thưởng là gì? | Không có quy định rõ | Sai luồng phê duyệt |
| 12 | 3 nhân viên trùng mã trong Excel — mã nào là đúng? | T06 có 3 bản ghi trùng mã | Sai dữ liệu đầu vào hệ thống |


## PHẦN 8 : Bảng kiểm chứng kết luận quan trọng (12 kết luận)

| STT | Kết luận trong báo cáo | Nguồn hỗ trợ | Mức độ tin cậy |
|-----|-----|------|------|
| 1 | Công ty có 200 nhân viên, 6 phòng ban | T06 (Excel dữ liệu) | Có căn cứ |
| 2 | Quy trình tuyển dụng gồm 5 bước chính | T02-tr.5; T04-mục 1 | Có căn cứ |
| 3 | Đánh giá nhân viên dựa trên 5 tiêu chí | T03-tr.18; T05-mục 2 | Có căn cứ |
| 4 | Thời gian thử việc có 2 quy định khác nhau (45/60 ngày) | T01-tr.8; T02-tr.12 | Có căn cứ (mâu thuẫn) |
| 5 | Chu kỳ đánh giá có 2 quy định khác nhau (6/12 tháng) | T03-tr.20; T05-mục 1 | Có căn cứ (mâu thuẫn) |
| 6 | Tìm kiếm hồ sơ thủ công mất 15-20 phút | T07-mục 2 | Có căn cứ |
| 7 | Có 3 nhân viên trùng mã trong Excel | T06 (phát hiện trùng lặp) | Có căn cứ |
| 8 | Khoảng 5% bản ghi thiếu ngày vào làm | T06 (thống kê mô tả) | Có căn cứ |
| 9 | Hệ thống cần phân quyền 3 cấp (NV/Nhân sự/BGD) | T01-tr.40 (quy định bảo mật) + suy luận | Cần bổ sung |
| 10 | Hệ thống cần hỗ trợ 200 người dùng đồng thời | T06 (200 NV) + suy luận | Cần bổ sung |
| 11 | Thời gian phản hồi nên dưới 2 giây | Không có trong tài liệu | Suy luận (không căn cứ) |
| 12 | T01 có thể đã hết hiệu lực vì T02 mới hơn | Không có văn bản bãi bỏ | **KHÔNG CĂN CỨ — không kết luận** |



