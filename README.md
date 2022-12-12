Phân tích dữ liệu kinh doanh bằng phần mềm R.

# Get start
Cài dặt môi trường, load dữ liệu
```
getwd()

library(readxl)
input <- read_excel(input, "export.xlsx")

library(dplyr)
input <- input %>% 
  rename('date' = 1, 
         'invoice' = 2, 
         'code' = 3, 
         'name' = 4, 
         'Promotion' = 5, 
         'unit' = 6, 
         'quantity' =  7, 
         'price' = 8, 
         'tax' = 9, 
         'sale' = 10)

# or
input <- rename(input, c('date' = 1, 'invoice' = 2, 'code' = 3, 'name' = 4))
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
muate(input, month(date))
```

# Analysis
Phân tích mô tả
So sánh
Dự báo

# Export
Xuất kết quả phân tích ra file định dạng xlsx
library(writexl)

write_xlsx(result, 'output'xlsx')
