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
input <- input %>% filter(account_id != 1008503)
Loại xuất nội bộ
input <- input %>% filter(grepl('^[^OPC]', account_id))
### Transform
Chuyển dữ liệu mới thành vùng
after <- input %>% filter(grepl('MK|HCM|MT|DB|TB', sales_org))
after <- after %>% mutate(region = case_when(
   grepl('MK', sales_org) ~ 'Mekong',
   grepl('HCM', sales_org) ~ 'Hồ Chí Minh',
   grepl('MT', sales_org) ~ 'Miền Trung',
   grepl('DB', sales_org) ~ 'Đông Bắc',
   grepl('TB', sales_org) ~ 'Tây Bắc',
))

before <- input %>% filter(grepl('OPC', sales_org))
before <- before %>% filter(grepl('OPC_HN|OPC_NA|OPC_DN|OPC_NT|OPC_VT|OPC_MD|OPC_TP|OPC_TG|OPC_CT', company_id))
vec = c('Tây Ninh' = 'Mekong', 'Bình Dương' = 'Hồ Chí Minh', 'Bình Thuận' = 'Miền Trung', 'Hải Dương' = 'Đông Bắc')
before['regioin'] <- vec[before$state]

https://www.reddit.com/r/rstats/comments/1gmfh51/how_to_create_new_column_based_on_a_named_vector/
## Analyse
### By region
