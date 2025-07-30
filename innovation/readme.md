# Phân tích dữ liệu sau sáp nhập
Dữ liệu sau sáp nhập tỉnh thành ngày 1/7/2025 và triển khai bán hàng theo mô hình centralized mới.

## Preprare
### Read data
Tải dữ liệu từ file.
```
input <- read_xlsx('export.xlsx')
input <- rename(input, c('seller' = 1,'distribution' = 2, 'account_id' = 3, 'account' = 4, 'state' = 5, 'industry' = 6, 'employee_id' = 7, 'employee' = 8, 'category' = 9, 'product_id' = 10, 'product' = 11, 'unit' = 12, 'sales_org' = 13, 'company_id' = 14, 'company' = 15, 'value' = 16, 'quantity' = 17))
```
### Clean data
Loại cho biếu tặng
```
input <- input %>% filter(account_id != 1008503)
```
Loại xuất nội bộ
```
input <- input %>% filter(grepl('^[^OPC]', account_id))
```
### Transform
Chuyển dữ liệu mới thành vùng, tạo cột `region`
```
after <- input %>% filter(grepl('MK|HCM|MT|DB|TB', sales_org))
after <- after %>% mutate(region = case_when(
   grepl('MK', sales_org) ~ 'Mekong',
   grepl('HCM', sales_org) ~ 'Hồ Chí Minh',
   grepl('MT', sales_org) ~ 'Miền Trung',
   grepl('DB', sales_org) ~ 'Đông Bắc',
   grepl('TB', sales_org) ~ 'Tây Bắc',
))
```
```
before <- input %>% filter(grepl('OPC', sales_org))
before <- before %>% filter(grepl('OPC_HN|OPC_NA|OPC_DN|OPC_NT|OPC_VT|OPC_MD|OPC_TP|OPC_TG|OPC_CT', company_id))
vec = c('TP Hà Nội' = 'Tây Bắc', 'Lai Châu' = 'Tây Bắc', 'Điện Biên' = 'Tây Bắc', 'Sơn La' = 'Tây Bắc', 'Lào Cai' = 'Tây Bắc', 'Yên Bái' = 'Tây Bắc', 'Tuyên Quang' = 'Tây Bắc', 'Hà Giang' = 'Tây Bắc', 'Phú Thọ' = 'Tây Bắc', 'Vĩnh Phúc' = 'Tây Bắc', 'Hòa Bình' = 'Tây Bắc', 'Bắc Ninh' = 'Tây Bắc', 'Bắc Giang' = 'Tây Bắc', 'Quảng Ninh' = 'Tây Bắc', 'Lạng Sơn' = 'Tây Bắc', 'Cao Bằng' = 'Tây Bắc', 'Thái Nguyên' = 'Tây Bắc', 'Bắc Cạn' = 'Tây Bắc',
'Thanh Hóa' = 'Đông Bắc', 'Nghệ An' = 'Tây Bắc', 'Hà Tĩnh' = 'Tây Bắc', 'Hưng Yên' = 'Tây Bắc', 'Thái Bình' = 'Tây Bắc', 'Ninh Bình' = 'Tây Bắc', 'Hà Nam' = 'Tây Bắc', 'Nam Ðịnh' = 'Tây Bắc', 'TP Hải Phòng' = 'Tây Bắc', 'Hải Dương' = 'Tây Bắc',
'Lâm Ðồng' = 'Miền Trung', 'Đắk Nông' = 'Miền Trung', 'Bình Thuận' = 'Miền Trung', 'Đắk Lắk' = 'Miền Trung', 'Phú Yên' = 'Miền Trung', 'Khánh Hòa' = 'Miền Trung', 'Ninh Thuận' = 'Miền Trung', 'Gia Lai' = 'Miền Trung', 'Bình Định' = 'Miền Trung', 'Quảng Ngãi' = 'Miền Trung', 'Kon Tum' = 'Miền Trung', 'TP Đà Nẵng' = 'Miền Trung', 'Quảng Nam' = 'Miền Trung', 'Thừa Thiên-Huế' = 'Miền Trung', 'TP Huế' = 'Miền Trung', 'Quảng Trị' = 'Miền Trung', 'Quảng Bình' = 'Miền Trung',
'TP Hồ Chí Minh' = 'Hồ Chí Minh', 'Bình Dương' = 'Hồ Chí Minh', 'Bà Rịa - Vũng Tàu' = 'Hồ Chí Minh', 'Ðồng Nai' = 'Hồ Chí Minh', 'Bình Phước' = 'Hồ Chí Minh',
'Cần Thơ' = 'Mekong', 'Sóc Trăng' = 'Mekong', 'Hậu Giang' = 'Mekong', 'An Giang' = 'Mekong', 'Kiên Giang' = 'Mekong', 'Ðồng Tháp' = 'Mekong', 'Tiền Giang' = 'Mekong', 'Tây Ninh' = 'Mekong', 'Long An' = 'Mekong', 'Cà Mau' = 'Mekong', 'Bạc Liêu' = 'Mekong', 'Vĩnh Long' = 'Mekong', 'Bến Tre' = 'Mekong', 'Trà Vinh' = 'Mekong')
before['region'] <- vec[before$state]
```
```
source <- rbind(before, after)
```
Tạo cột `area`

area <- function(x) {
   list = c('Lâm Ðồng' = 'Miền Trung 1', 'Đắk Nông' = 'Miền Trung 1', 'Bình Thuận' = 'Miền Trung 1', 'Đắk Lắk' = 'Miền Trung 1', 'Phú Yên' = 'Miền Trung 1',
      'Khánh Hòa' = 'Miền Trung 2', 'Ninh Thuận' = 'Miền Trung 2', 'Gia Lai' = 'Miền Trung 2', 'Bình Định' = 'Miền Trung 2', 'Quảng Ngãi'       = 'Miền Trung 2', 'Kon Tum' = 'Miền Trung 2', 
      'TP Đà Nẵng' = 'Miền Trung 3', 'Quảng Nam' = 'Miền Trung 3', 'Thừa Thiên-Huế' = 'Miền Trung 3', 'TP Huế' = 'Miền Trung 3', 'Quảng Trị' = 'Miền Trung 3', 'Quảng Bình' = 'Miền Trung 3')
   if (x[2] == 'Miền Trung') {
      return(list[x[1]])
   } else {return('Other')}
}
new_col <- apply(source[, c('state', 'region')], MARGIN=1, area)
source$area <- new_col


https://www.reddit.com/r/rstats/comments/1gmfh51/how_to_create_new_column_based_on_a_named_vector/
## Analyse
sale.region <- function(data) {
   region <- data %>% group_by(region) %>% summarise(sale = sum(value))
   return(region)
}
region <- sale.region(source)
### By region
