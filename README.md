Phân tích dữ liệu kinh doanh bằng phần mềm R. Về cơ bản, việc phân tích sẽ trải qua năm bước như sau:
1. Load: tải dữ liệu, tải thư viện, cài đặt môi trường
2. Transform: chuyển đổi dữ liệu theo chuẩn của người phân tích. Bao gồm: đặt tên biến, xử lý na/ null, thêm cột phụ/ merge table, format data...
3. Select working data: trích xuất dữ liệu phân tích
4. Analyze: phân tích dữ liệu theo model đã xây dựng
5. Export report: xuất báo cáo.

[Xem sale data set](./dataset.md)
# 1. Load
Cài đặt môi trường, tải thư viện, load dữ liệu
```
getwd()

library(readxl)
library(writexl)
library(dplyr)
library(lubridate)
input <- read_excel(input, "export.xlsx")

# Append data frames with the same column
input <- rbin(input1, input2)
```


# 2. Transform data
```
source <- input

# Rename input column
source <- rename(source, c('date' = 1, 'invoice' = 2, 'code' = 3, 'name' = 4, 'promotion' = 5, 'unit' = 6, 'quantity' = 7, 
  'raw_price' = 8, 'discount' = 9, 'price' = 10, 'before_tax' = 11, 'tax' = 12, 'sale' = 13, 'customer_code' = 14, 
  'customer_name' = 15, 'note' = 16, 'herbal_type' = 17, 'chem_type' = 18, 'rep_code' = 19, 'rep_name' = 20, 
  'discount_duration' = 21, 'debt_duration' = 22, 'promotion_code' = 23, 'province' = 33, 'district' = 34, 'ward' = 35, 
  'lot-exp' = 38,'lot' = 39, 'address' = 40, 'payment_cat' = 41, 'tax_code' = 42, 'contact' = 43, 'branch_code' = 44, 
  'bill_type' = 45,'dept_code' = 46)
  )

```


```
# Add column
source <- mutate(source, month = month(date))
source <- mutate(source, week = week(date))

# Merge branch data
code <- c('1N101', '2B101-01', '2B101-02', '2N101', '2N101-01', '2N101-03', '2N101-04', '2N101-05', 
  '2N201', '2N202', '2N301', '2N302', '2T101', '2T102', '2T103')
name <- c('Công ty', 'CN Hà Nội', 'CN Hà Nội', 'CN Hồ Chí Minh', 'CN Hồ Chí Minh', 'CN Hồ Chí Minh', 
  'CN Hồ Chí Minh', 'CN Hồ Chí Minh', 'CN Miền Đông', 'CN Vũng Tàu', 'CN Cần Thơ', 'CN Tiền Giang', 
  'CN Nha Trang', 'CN Đà Nẵng', 'CN Nghệ An')
branch <- data.frame(code, name)
Or import from file
branch <- read_excel("Refer.xslx", "branch")

source <- merge(source, branch, by = 'branch_code', all.x = TRUE)
```
[Download Refer file tại đây.](https://docs.google.com/spreadsheets/d/16LjQ8uVkXLTo-fDVaRPkXC-zccALC38UUzlE42bkmLc/edit?usp=sharing)

```
# Format row
source$date <- as.Date(source$date)
```
# 3. Select working data


```
# Chọn các cột
data <- select(source, c(date, invoice, code, name, quantity, price, sale, customer_code, customer_name, 
  herbal_type, rep_code, rep_name, province, branch_code))
  
Hoặc
data <- source $>$ select(date, code, name, customer, type, quantity, revenue, branch)
```

```
# Chọn các dòng
- Chọn 3 gam (OPC, HD2, OPC BD)
data <- data %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH06', code))
- Chọn 4 gam (OPC, HD2, OPC BD, TW25)
data <- data %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH06|HH25', code))
- Chọn 7 gam (OPC, HD2, OPC BD, TW25, thuốc phiến, QTSK, gia công)
data <- data %>% filter(grepl('TP01|TP02|TP03|TP05|TP07|TP08|TP09|TP10|HH04|HH06|HH25', code))
```


# 4. Analysis
Phân tích các chỉ số kinh doanh theo [quy ước tại đây.](./metric.md).
Sử dụng các hàm [hepler](./helper.md)

## 4.1 Phân tích mô tả
Thống kê mô tả (Descriptive statistics) sử dụng các phép thống kê tóm tắt để mô tả đặc trưng của dữ liệu. Bao gồm:
- Tần số: số lần xuất hiện của từng giá trị.
- Đo lường xu hướng trung tâm: median, mode, mean.
- Xem hình dạng phân phối: biểu đồ histogram, kiểm tra độ lệch (skewness) và độ nhọn (kurtosis).
- Đo lường sự phân tán: range, tứ phân vị (inter-quartile range), phương sai, độ lệch chuẩn.

Phân tích mô tả ngoài mục đích giúp hiểu biết cơ bản về dữ liệu còn có ứng dụng hỗ trợ làm sạch dữ liệu. Chẳng hạn dữ liệu kinh doanh chỉ có 3 khoản mục (payment_cat) là P27*, P84* và P87* nhưng khi thống kê tần số sẽ phát hiện ra rất nhiều dòng rỗng (null) và không xác định (n/a). Khuyến khích nên thực hiện phân tích mô tả đối với tất cả các trường dữ liệu dự định thực hiện phân tích gộp dữ liệu để hạn chế sai xót.

### Hàm cơ bản
R đã hỗ trợ sẵn các hàm sau đây để thống kê mô tả một biến số.
- mean(): trung bình
- sd(): độ lệch chuẩn
- var(): phương sai
- cor(): ma trận hệ số tương quan của các biến
- max(): giá trị lớn nhất
- min(): giá trị nhỏ nhất
- quantile(): ngũ phân vị

Bên cạnh các hàm cơ bản, R còn hỗ trợ sẵn hàm gộp 6 thông số cơ bản phân tích biến số. Hàm summary() sẽ cho ra min, 1stQuar, median, mean, 3rdQuar, Max.

### Tần số
Đếm số lượng xuất hiện của từng giá trị
```
> table(data$payment_cat)
P27   P84   P87
12    56    78
```
### Histogram
```
hist(data$sale)
```
![Histogram chart](https://cdn.datamentor.io/wp-content/uploads/2017/11/r-histogram.png)

## Group by
```
sale.brand <- data %>% group_by(brand_name) %>% summarise(brand = sum(sale))
sale.province <- data %>% group_by(province) %>% summarise(provice = sum(sale))
sale.rep <- data %>% group_by(brand_name, rep_code) %>% summarise(sale = sum(sale))
sale.month <- data %>% group_by(month) %>% summarise(sale = sum(sale))

Add percent column
sale.month$percent <- sale.month$sale/ sum(sale.month$sale) * 100

Arrange by percent column
arrange(sale.month, sale)
arrange(sale.month, desc(sale))

Add cumulate column
sale.month$acc <- cumsum(sale.month$percent)
```
- [Tổng quan những chỉ số cơ bản](./analyse/overview.md)
- [Phân tích mô tả khách hàng](./analyse/customers.md)
- [Phân tích mô tả sản phẩm](./analyse/products.md)
## 4.2 So sánh

## 4.3 Dự báo

# 5. Export
Xuất kết quả phân tích ra file định dạng xlsx
library(writexl)

```
# One sheet
write_xlsx(result, 'output'xlsx')

# Multiple sheet
sheet <- list(sale.brand, sale.rep, sale.province)
write_xlsx(sheet, 'multiple.xlsx')
```
