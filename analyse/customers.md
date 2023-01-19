# Analyse customer
Phân tích khách hàng, chủ yếu là phân tích về số lượng khách hàng/ sale phân bố như thế nào trong từng tiêu chí.

## Customer
- Tổng khách hàng giao dịch
- Phân bố khách hàng trên từng kênh OTC/ ETC
- Phân bố khách hàng theo vùng miền (7 vùng kinh tế)
- Phân bố khách hàng theo tỉnh (63 tỉnh/ thành)
- Phân bố khách hàng theo chi nhánh
- Phân bố khách hàng theo loại khách hàng (chi tiết OTC1, OTC2...)
- Phân bố theo tháng

```
stat.customer <- function(data) {

  total <- data %>% summarise(count = n_distinct(customer_code), sale = sum(sale))
  type <- data %>% group_by(herbal_type) %>% summarise(count = n_distinct(customer_code), sale = sum(sale))
  province <- data %>% group_by(province) %>% summarise(count = n_distinct(customer_code), sale = sum(sale))
  # region <- data %>% group_by(region) %>% summarise(count = n_distinct(customer_code), sale = sum(sale))
  branch <- data %>% group_by(branch_code) %>% summarise(count = n_distinct(customer_code), sale = sum(sale))
  month <- data %>% group_by(month) %>% summarise(count = n_distinct(customer_code), sale = sum(sale))
  sale <- data %>% group_by(customer_code, herbal_type, province) %>% summarise(sale = sum(sale))

  result <- list('Total' = total, 'Type' = type, 'Branch' = branch, 'Month' = month, 'Sale' = sale)
return(result)
}
```

## New customer
Phân tích khách hàng mới

```
# data: sale data set
# created: vector customer code
customer.new <- function(data, created) {
  new <- data %>% filter(customer_code %in% created)
  sale <- new %>% group_by(customer_code, herbal_type) %>% summarise(sale = sum(sale))
  return(sale)
}

new <- customer.new(data, created$code)
```
## Customer by branch
Thống kê khách hàng giao dịch theo chi nhánh (trên từng kênh)

```
customer.trans <- function(data) {
  chem <- data %>% filter(is.na(herbal_type))
  herbal <- data %>% filter(!is.na(herbal_type))
  customer.chem <- chem %>% group_by(branch_name, chem_type) %>% summarise(count = n_distinct(customer_code))
  customer.chem$left <- substring(customer.chem$chem_type, 1, 3)
  customer.herbal <- herbal %>% group_by(branch_name, herbal_type) %>% summarise(count = n_distinct(customer_code))
  customer.herbal$left <- substring(customer.herbal$herbal_type, 1, 3)
  result <- list('herbal' = customer.herbal, 'chem' = customer.chem)
  return(result)
}
```

## Customer by medrep
Thống kê khách hàng theo trình dược viên
```
customer.medrep <- function(data) {
  medrep <- data %>% group_by(branch_name, medrep_code, medrep_name) %>% summarise(count = n_distinct(customer_code), 
    sale = sum(sale))
  return(medrep)
}
```
