# Hepler function
Các hàm hepler giúp cho việc trích lọc dữ liệu nhanh hơn.

## Remove
### Remove gift
Loại bỏ các sản phẩm xuất cho biếu tặng, các sản phẩm này nếu xuất có giá thì vẫn có doanh thu nhưng không phải là doanh thu quan tâm khi phân tích kinh doanh.
Nếu phân tích số lượng thì bắt buộc phải loại bỏ các dòng cho biếu tặng để tập trung vào hàng bán.
```
remove.gift(input) {
input <- input %>% filter(grelp('P84|P87'), payment_cat))
return(input)
}
```

### Remove promotion
Loại các sản phẩm khuyến mãi, thích hợp khi phân tích số lượng bán.
```
remove.promotion(input) {
input <- input %>% filter(promotion == true)
return(input)
}
```

### Remove internal
Loại bỏ các người mua hàng là nhân viên nội bộ công ty/ khách quan hệ không phát sinh giao dịch mua bán (ETC). Thường dùng khi phân tích số lượng khách hàng.
```
remove.internal(input) {
input <- input %>% filter(grepl('OTC|ETC', customer_code))
return(input)
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
```
### Extract theo product
```
extract.produc_type
extract.product_cat
extract.product_group
extract.product_form
```
