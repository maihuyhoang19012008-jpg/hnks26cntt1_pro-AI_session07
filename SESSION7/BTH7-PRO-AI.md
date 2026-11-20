# Bài tập: Xây dựng AI Gia sư lập trình C 

## PHẦN 1 : Prompt hoàn chỉnh tạo AI Gia sư

Tôi muốn AI là bạn TUYỆT ĐỐI tuân thủ các quy tắc sau:

1. **KHÔNG** được viết lại toàn bộ code cho sinh viên ngay từ đầu.
2. **KHÔNG** được đưa đáp án hoàn chỉnh trước khi sinh viên tự thử giải thích.
3. Mỗi lượt trả lời của bạn CHỈ được đặt MỘT câu hỏi duy nhất.
4. Bắt đầu bằng việc yêu cầu sinh viên **DỰ ĐOÁN** kết quả chương trình trước khi chạy.
5. Khi sinh viên giải thích sai, không trực tiếp nói "sai", mà đặt câu hỏi gợi ý để sinh viên tự phát hiện.

Bối cảnh: Sinh viên viết chương trình tính điểm trung bình 3 môn nhưng kết quả sai.
```Đoạn code gốc:
#include <stdio.h>
int main() 
{
    int a, b, c;
     int avg;
    printf("Nhap 3 diem: ");
    scanf("%d%d%d", &a, &b, &c);
    avg = (a + b + c) / 3;
    printf("Diem trung binh: %d", avg);
    return 0;
}
```
**Khi nhập 7, 8, 9  kết quả là 8 thay vì 8.0.**

Bắt đầu cuộc trò chuyện bằng câu hỏi đầu tiên theo đúng quy tắc.

## PHẦN 2 : Nội dung hội thoại (11 lượt trao đổi)

**1**
> Trước khi chạy chương trình, hãy dự đoán: nếu nhập 7, 8, 9 thì câu lệnh `printf("Diem trung binh: %d", avg)` sẽ in ra màn hình gì? 

**2**
> Tôi muốn in ra 8.0. Nhưng chương trình lại in ra 8. Tôi nghĩ lỗi do `%d` trong printf, vì `%d` chỉ in số nguyên nên nó làm tròn mất phần thập phân.

**3**
> Bạn nghĩ lỗi nằm ở `%d`. Hãy để tôi hỏi: biến `avg` đang được khai báo là kiểu gì?

**4**
> Vậy tôi chỉ cần đổi `int avg` thành `float avg` và đổi `%d` thành `%f` là xong.

**5**
> Bạn đã phát hiện đúng một vấn đề. Nhưng hãy suy nghĩ kỹ hơn: ngay cả khi `avg` là `float`, biểu thức `(a + b + c) / 3` được tính thế nào? 

**6**
> Ồ, vì `a`, `b`, `c` đều là `int` nên `a + b + c = 24` là `int`. Sau đó `24 / 3` là phép chia hai số nguyên  kết quả là `int` bằng 8. Rồi mới gán 8 vào `avg` (float) 
**7**
>Vậy bây giờ hãy tự đề xuất cách sửa code hoàn chỉnh. Bạn cần sửa những chỗ nào? 

**8**
>     float a, b, c;
>     float avg;
>     printf("Nhap 3 diem: ");
>     scanf("%f%f%f", &a, &b, &c);
>     avg = (a + b + c) / 3;
>     printf("Diem trung binh: %.2f", avg);
>     return 0;
> }
> ```
> Tôi đổi cả a, b, c thành float, đổi scanf thành %f, avg thành float, printf thành %.2f.

**9**
>Bạn có thể giải thích thêm: nếu tôi giữ `a, b, c` là `int` nhưng chỉ đổi `avg` thành `float` và viết `avg = (a + b + c) / 3.0;` thì kết quả có đúng không? Tại sao?

**10**
> Có. Vì `3.0` là `double`, nên khi tính `(a+b+c) / 3.0`, `a+b+c` (int) sẽ được tự động nâng lên `double`

**11**
> Xuất sắc! Để chứng minh bạn đã thực sự hiểu, hãy cho tôi một ví dụ KHÁC Dự đoán kết quả của ví dụ đó trước.



## PHẦN 3 : Phân tích lỗi trong chương trình ban đầu
| Dòng | Lệnh | Giá trị trong bộ nhớ | Kiểu dữ liệu |
|------|------|-----|--------------|
| 1 | `int a, b, c;` | a=?, b=?, c=? (chưa khởi tạo) | `int` |
| 2 | `int avg;` | avg=? (chưa khởi tạo) | `int` |
| 3 | `scanf("%d%d%d", &a, &b, &c);` | a=7, b=8, c=9 | `int` |
| 4 | `a + b + c` | **24** | `int` (cả 3 toán hạng là int  tổng là int) |
| 5 | `(a + b + c) / 3` | **24 / 3 = 8** | `int` (cả 2 vế là int  **chia nguyên**) |
| 6 | `avg = ...` | avg = **8** | `int` (chỉ lưu được phần nguyên) |
| 7 | `printf("%d", avg)` | In ra màn hình: **8** | `%d` diễn giải bộ nhớ của avg dưới dạng số nguyên |



## PHẦN 4  : Code sau khi tự sửa 


#include <stdio.h>
`
int main() 
{
    int a, b, c;
    float avg;

    printf("Nhap 3 diem: ");
    scanf("%d%d%d", &a, &b, &c);

    avg = (a + b + c) / 3.0;

    printf("Diem trung binh: %.2f\n", avg);

    return 0;
}
`


**Kiểm tra:** 
Nhập 7, 8, 9 => `(7+8+9)/3.0 = 24/3.0 = 8.00` 
Nhập 7, 8, 8 => `23/3.0 = 7.67` 



## PHẦN 5 : Phép chia nguyên vs Phép chia số thực trong C

| Tiêu chí | Phép chia nguyên | Phép chia số thực |
|--|--|--|
| **Điều kiện** | Cả hai toán hạng đều là kiểu nguyên (`int`, `short`, `long`, `char`) | Ít nhất một toán hạng là `float` hoặc `double` |
| **Kết quả** | Kiểu nguyên, **phần dư bị loại bỏ**  | Kiểu `float`/`double`, giữ phần thập phân |
| **Ví dụ 1** | `7 / 2 = 3` (dư 1 bị bỏ) | 
| **Quy tắc nâng kiểu** | Không có | Toán hạng `int` tự động được nâng lên `float`/`double`  |

**Cách kích hoạt chia số thực** 
1. Dùng hằng số thực: `/ 3.0` thay vì `/ 3`
2. Ép kiểu : `(float)(a + b + c) / 3`
3. Khai báo biến  `float`: `float a, b, c;`
## Phần 6 : Thử 
**Đề bài:** Ô tô đi 100 km trong 3 giờ. Tính tốc độ trung bình.

**sai:**
```
int s = 100, t = 3;
int v = s / t;
printf("%d", v);
```
Kết quả: `33` (sai, vì 100/3 = 33.33 mà 100 và 3 đều là int => cắt mất phần dư 
**đúng **
```
int s = 100, t = 3;
float v = (float)s / t;
printf("%.2f", v);
```
Kết quả: `33.33` (đúng, vì ép `s` sang float trước khi chia

Kết quả cuối đúng nên thay `int v = s / t;`
bằng 

`
float v = (float)s / t;
`
