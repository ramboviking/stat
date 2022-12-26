# Analyse customer
Phân tích khách hàng, chủ yếu là phân tích về số lượng khách hàng/ sale phân bố như thế nào trong từng tiêu chí.

Customer
- Tổng khách hàng giao dịch
- Phân bố khách hàng trên từng kênh OTC/ ETC
- Phân bố khách hàng theo vùng miền (7 vùng kinh tế)
- Phân bố khách hàng theo tỉnh (63 tỉnh/ thành)
- Phân bố khách hàng theo chi nhánh
- Phân bố khách hàng theo loại khách hàng (chi tiết OTC1, OTC2...)
- Phân bố theo tháng

```
analyse.customer <- function(data) {

total <- data %>% summarise(count = n_distinct(customer_code), sale = sum(sale))
type <- data %>% group_by(herbal_type) %>% summarise(count = n_distinct(customer_code), sale = sum(sale))
province <- data %>% group_by(province) %>% summarise(count = n_distinct(customer_code), sale = sum(sale))
# region <- data %>% group_by(region) %>% summarise(count = n_distinct(customer_code), sale = sum(sale))
branch <- data %>% group_by(branch_code) %>% summarise(count = n_distinct(customer_code), sale = sum(sale))
month <- data %>% group_by(month) %>% summarise(count = n_distinct(customer_code), sale = sum(sale))
sale <- data %>% group_by(customer_code) %>% summarise(sale = sum(sale))

result <- list('Total' = total, 'Type' = type, 'Branch' = branch, 'Month' = month, 'Sale' = sale)
return(result)
}
```
