# Overview
Phân tích tổng quan tình hình kinh doanh của công ty. Chỉ lấy những chỉ số tổng quát nhất, sau đó sẽ đi chi tiết ở các báo cáo cụ thể hơn.
- Tổng doanh thu, tiến độ.
- Theo gam hàng
- Theo chi nhánh
- Theo kênh bán hàng
- Theo địa phương

```
stat.overview <- function(data) {
format_sale <- function(frame, desc = TRUE) {
		if (desc) {frame <- frame %>% arrange(desc(sale))}
		frame$percent <- frame$sale / sum(frame$sale) * 100
		frame$percent <- round(frame$percent, digits = 2)
		return(frame)
	}
total <- data %>% summarise(sale = sum(sale))
branch <- data %>% group_by(branch_name) %>% summarise(sale = sum(sale))
branch <- format_sale(branch)
channel <- data %>% group_by(channel) %>% summarise(sale = sum(sale))
channel <- format_sale(channel)
province <- data %>% group_by(province) %>% summarise(sale = sum(sale))
province <- format_sale(province)
gam <- data %>% group_by(gam) %>% summarise(sale = sum(sale))
gam <- format_sale(gam)

result <- list('total' = total, 'branch' = branch, 'channel' = channel, 'province' = province, 'gam' = gam)
return(result)
}
```

## Use
Phân tích tổng quan trong 1 khoảng thời gian cho trước.
``
stat.overview(data)
```
