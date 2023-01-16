# Hepler function
Các hàm hepler giúp cho việc trích lọc dữ liệu nhanh hơn.

## Remove
### Remove gift
Loại bỏ các sản phẩm xuất cho biếu tặng, các sản phẩm này nếu xuất có giá thì vẫn có doanh thu nhưng không phải là doanh thu quan tâm khi phân tích kinh doanh.
Nếu phân tích số lượng thì bắt buộc phải loại bỏ các dòng cho biếu tặng để tập trung vào hàng bán.
```
remove.gift <- function(input) {
input <- input %>% 
  filter(
    payment_cat != 'P84' & 
    payment_cat != 'P84C' & 
    payment_cat != 'P84E' & 
    payment_cat != 'P87' & 
    payment_cat != 'P87E' | 
    is.na(payment_cat))
return(input)
}
```

### Remove promotion
Loại các sản phẩm khuyến mãi, thích hợp khi phân tích số lượng bán.
```
remove.promotion <- function(input) {
input <- input %>% filter(promotion == false)
return(input)
}
```

### Remove internal
Loại bỏ các người mua hàng là nhân viên nội bộ công ty/ khách quan hệ không phát sinh giao dịch mua bán (ETC). Thường dùng khi phân tích số lượng khách hàng.
```
remove.internal <- function(input) {
input <- input %>% filter(grepl('NV', customer_code))
return(input)
}
```

### Remove headquarter
Loại các sản phẩm do công ty bán, thường sử dụng khi phân tích tình hình hoạt động của chi nhánh.
```
remove.headquarter <- function(inut) {
input <- input %>% filter(branch_code != '1N101' & branch_code != '1N100')
}
```

### Remove export
### Remove outsource

## Extract
Trích xuất một phần dữ liệu
### Extract theo gam hàng
```
extract.opc
extract.hd2
extract.opcbd
extract.tw25

# 3 gam: opc (kể cả QTSK), opcbd, hd2
extract.3gam <- function(data) {
data <- data %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH06|TP10', code))
return(data)
}

# 4 gam: opc, opcbd, hd2, tw25
extract.4gam <- function(data) {
data <- data %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH06|TP10', code))
data <- data %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH06|TP10|HH25', code))
return(data)
}
```
### Extract theo product
```
extract.produc_type
extract.product_cat
extract.product_group
extract.product_form
```
