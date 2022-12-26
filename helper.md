# Hepler function
Các hàm hepler giúp cho việc phân tích dữ liệu nhanh hơn

## Remove
### Remove gift
Loại bỏ các sản phẩm xuất cho biếu tặng
```
remove.gift(input) {
input <- input %>% filter(grelp('P84|P87'), payment_cat))
return(input)
}
```

### Remove promotion
Loại các sản phẩm khuyến mãi
```
remove.promotion(input) {
input <- input %>% filter(promotion == true)
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
