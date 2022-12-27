# Overview
Phân tích tổng quan tình hình kinh doanh của công ty.
- Tổng doanh thu, tiến độ.
- Theo gam hàng
- Theo chi nhánh
- Theo kênh bán hàng

```
stat.overview <- function(data) {
total <- data %>% summarise(sale = sum(sale))
branch <- data %>% group_by(branch_name) %>% summarise(sale = sum(sale))
branch <- arrange(branch, desc(sale))
channel <- data %>% group_by(channel) %>% summarise(sale = sum(sale))
channel <- arrange(channel, desc(sale))
province <- data %>% group_by(province) %>% summarise(sale = sum(sale))
province <- arrange(province, desc(sale))
gam <- data %>% group_by(gam_code) %>% summarise(sale = sum(sale))

result <- list('total' = total, 'branch' = branch, 'channel' = channel, 'province' = province, 'gam' = gam)
return(result)
}
```
