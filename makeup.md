# Markeup
Xử lý chuẩn hóa dữ liệu, không thay đổi dữ liệu mà chỉ là format theo chuẩn/ thêm biến phụ.

```
makeup <- function(data) {
# Rename input column
data <- rename(data, c('date' = 1, 'invoice' = 2, 'code' = 3, 'name' = 4, 'promotion' = 5, 'unit' = 6, 'quantity' = 7, 
  'raw_price' = 8, 'discount' = 9, 'price' = 10, 'sale' = 11, 'tax' = 12, 'revenue' = 13, 'customer_code' = 14, 
  'customer_name' = 15, 'note' = 16, 'herbal_type' = 17, 'chem_type' = 18, 'rep_code' = 19, 'rep_name' = 20, 
  'discount_duration' = 21, 'debt_duration' = 22, 'promotion_code' = 23, 'province' = 33, 'district' = 34, 'ward' = 35, 
  'lot-exp' = 38,'lot' = 39, 'address' = 40, 'payment_cat' = 41, 'tax_code' = 42, 'contact' = 43, 'branch_code' = 44, 
  'bill_type' = 45,'dept_code' = 46)
  )
  
# Merge branch name column
branch_code <- c('1N101', '2B101-01', '2B101-02', '2N101', '2N101-01', '2N101-03', '2N101-04', '2N101-05', 
  '2N201', '2N202', '2N301', '2N302', '2T101', '2T102', '2T103')
branch_name <- c('Công ty', 'CN Hà Nội', 'CN Hà Nội', 'CN Hồ Chí Minh', 'CN Hồ Chí Minh', 'CN Hồ Chí Minh', 
  'CN Hồ Chí Minh', 'CN Hồ Chí Minh', 'CN Miền Đông', 'CN Vũng Tàu', 'CN Cần Thơ', 'CN Tiền Giang', 
  'CN Nha Trang', 'CN Đà Nẵng', 'CN Nghệ An')
branch <- data.frame(branch_code, branch_name)
data <- merge(data, branch, by = 'branch_code', all.x = TRUE)

# Add sub column
data <- mutate(data, month = month(date))
data <- mutate(data, week = week(date))

channel <- substring(data$herbal_type, 1, 3)
data$channel <- channel

data$gam_code <- substring(data$code, 1, 4)
data <- data %>% mutate(gam = 
	case_when(substr(code,1,4) == 'TP02' ~ 'OPC',
		substr(code,1,4) == 'TP03' ~ 'OPC',
		substr(code,1,4) == 'TP05' ~ 'OPC',
		substr(code,1,4) == 'TP07' ~ 'HD2',
		substr(code,1,4) == 'TP08' ~ 'PHIEN',
		substr(code,1,4) == 'TP09' ~ 'PHIEN',
		substr(code,1,4) == 'TP10' ~ 'OPC',
		substr(code,1,4) == 'HH04' ~ 'OPCBD',
		substr(code,1,4) == 'HH06' ~ 'OPC',
		substr(code,1,4) == 'HH25' ~ 'TW25'
	)
)

# Format row
data$date <- as.Date(data$date)

return(data)
}

```
