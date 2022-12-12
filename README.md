Phân tích dữ liệu kinh doanh bằng phần mềm R.

# Get start
Cài dặt môi trường, load dữ liệu
```
getwd()

library(readxl)
input <- read_excel(input, "export.xlsx")

library(dplyr)

# rename column
input <- rename(input, c('date' = 1, 'invoice' = 2, 'code' = 3, 'name' = 4, 'promotion' = 5, 'unit' = 6, 'quantity' = 7, 'raw_price' = 8, 'discount' = 9,
  'price' = 10, 'before_tax' = 11, 'tax' = 12, 'sale' = 13, 'customer_code' = 14, 'customer_name' = 15, 'note' = 16, 'herbal_type' = 17, 'chem_type' = 18, 
  'rep_code' = 19, 'rep_name' = 20, 'province' = 33, 'district' = 34, 'ward' = 35, 'lot' = 39, 'address' = 40, 'brand_code' = 44))
```

```
# select data
data <- select(input, c(date, invoice, code, name, quantity, price, sale, customer_code, customer_name, herbal_type, rep_code, rep_name, province, brand_code))
```


# Clear data
Loại bỏ các dòng, cột nằm ngoài phạm vi quan tâm, xử lý trường hợp dữ liệu rỗng

```
# Loại các sản phẩm không quan tâm
source <- input %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH07|TW25', Code))
# Chọn các cột
source <- source $>$ select(Date, Code, Name, Customer, Type, Quantity, Revenue, Brand)
```

```
# Transform
mutate(input, month(date))
```

# Analysis
Phân tích mô tả
So sánh
Dự báo

# Export
Xuất kết quả phân tích ra file định dạng xlsx
library(writexl)

write_xlsx(result, 'output'xlsx')
