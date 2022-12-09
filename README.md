Phân tích dữ liệu kinh doanh bằng phần mềm R.

# Get start
Cài dặt môi trường, load dữ liệu
```
getwd()
library(readxl)
input <- read_excel(input, "data.xlsx")
library(dplyr)
```

# Clear data
Loại bỏ các dòng, cột nằm ngoài phạm vi quan tâm
```
# Loại các sản phẩm không quan tâm
source <- input %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH07|TW25', Code))
# Chọn các cột
source <- source $>$ select(Date, Code, Name, Customer, Type, Quantity, Revenue, Brand)
```
