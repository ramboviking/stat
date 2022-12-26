# Overview
Phân tích tổng quan tình hình kinh doanh của công ty.
- Tổng doanh thu, tiến độ.
- Theo gam hàng
- Theo chi nhánh
- Theo kênh bán hàng

```
stat.overvie <- function(data) {
total <- data %>% summarise(sale = sum(sale))
branch <- data %>% group_by(branch_name) %>% summarise(sale = sum(sale))
channel <- data %>% group_by(channel) %>% summarise(sale = sum(sale))

result <- list('Total' = total, 'Branch' = branch, 'Channel' = channel)
return(result)
}
```
