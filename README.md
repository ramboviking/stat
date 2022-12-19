Phân tích dữ liệu kinh doanh bằng phần mềm R. Về cơ bản, việc phân tích sẽ trải qua năm bước như sau:
1. Load: tải dữ liệu, tải thư viện, cài đặt môi trường
2. Transform: chuyển đổi dữ liệu theo chuẩn của người phân tích. Bao gồm: đặt tên biến, xử lý na/ null, thêm cột phụ/ merge table, format data...
3. Select working data: trích xuất dữ liệu phân tích
4. Analyze: phân tích dữ liệu theo model đã xây dựng
5. Export report: xuất báo cáo.

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
# rename input column
input <- rename(input, c('date' = 1, 'invoice' = 2, 'code' = 3, 'name' = 4, 'promotion' = 5, 'unit' = 6, 'quantity' = 7, 
  'raw_price' = 8, 'discount' = 9, 'price' = 10, 'before_tax' = 11, 'tax' = 12, 'sale' = 13, 'customer_code' = 14, 
  'customer_name' = 15, 'note' = 16, 'herbal_type' = 17, 'chem_type' = 18, 'rep_code' = 19, 'rep_name' = 20, 
  'discount_duration' = 21, 'debt_duration' = 22, 'promotion_code' = 23, 'province' = 33, 'district' = 34, 'ward' = 35, 
  'lot-exp' = 38,'lot' = 39, 'address' = 40, 'payment_cat' = 41, 'tax_code' = 42, 'contact' = 43, 'branch_code' = 44, 
  'bill_type' = 45,'dept_code' = 46)
  )


# select working data
source <- select(input, c(date, invoice, code, name, quantity, price, sale, customer_code, customer_name, 
  herbal_type, rep_code, rep_name, province, branch_code))
```
Loại bỏ các dòng, cột nằm ngoài phạm vi quan tâm, xử lý trường hợp dữ liệu rỗng

```
# Loại các sản phẩm không quan tâm
- Chọn 3 gam (OPC, HD2, OPC BD)
data <- source %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH06', code))
- Chọn 4 gam (OPC, HD2, OPC BD, TW25)
data <- source %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH06|HH25', code))
- Chọn 7 gam (OPC, HD2, OPC BD, TW25, thuốc phiến, QTSK, gia công)
data <- source %>% filter(grepl('TP01|TP02|TP03|TP05|TP07|TP08|TP09|TP10|HH04|HH06|HH25', code))

# Chọn các cột
data <- source $>$ select(date, code, name, customer, type, quantity, revenue, branch)
```

```
# Transform
data <- mutate(data, moth = month(date))
data <- mutate(data, week = week(date))

# merge brand data
code <- c('1N101', '2B101-01', '2B101-02', '2N101', '2N101-01', '2N101-03', '2N101-04', '2N101-05', 
  '2N201', '2N202', '2N301', '2N302', '2T101', '2T102', '2T103')
name <- c('Công ty', 'CN Hà Nội', 'CN Hà Nội', 'CN Hồ Chí Minh', 'CN Hồ Chí Minh', 'CN Hồ Chí Minh', 
  'CN Hồ Chí Minh', 'CN Hồ Chí Minh', 'CN Miền Đông', 'CN Vũng Tàu', 'CN Cần Thơ', 'CN Tiền Giang', 
  'CN Nha Trang', 'CN Đà Nẵng', 'CN Nghệ An')
brand <- data.frame(code, name)
Or import from file
brand <- read_excel("refer.xslx", "brand")

data <- merge(data, brand, by = 'brand_code', all.x = TRUE)
```

# 4. Analysis
Phân tích các chỉ số kinh doanh theo [quy ước tại đây](./metric.md)
## Phân tích mô tả
## Group
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

## So sánh

## Dự báo

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
