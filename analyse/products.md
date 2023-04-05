# Analyse product
Phân tích doanh số sản phẩm theo nhiều tiêu chí.
Tải file phân loại sản phẩm [tại đây](https://docs.google.com/spreadsheets/d/16LjQ8uVkXLTo-fDVaRPkXC-zccALC38UUzlE42bkmLc/edit?usp=sharing)

## Product
Phân tích doanh số 1 sản phẩm (có thể bao gồm một/ một vài mã).
```
# bravo: mã hàng cần phân tích
stat.product <- function(product) {
	product <- product %>% filter(promotion == FALSE)
	product <- product %>% 
  		filter(
    		payment_cat != 'P84' & 
    		payment_cat != 'P84C' & 
    		payment_cat != 'P84E' & 
    		payment_cat != 'P87' & 
    		payment_cat != 'P87E' | 
    		is.na(payment_cat))
		
	sale.product <- product %>% group_by(code, name) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.product <- sale.product %>% arrange(desc(sale))
	sale.product$percent <- sale.product$sale / sum(sale.product$sale) * 100
	sale.branch <- product %>% group_by(branch_name) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.branch <- sale.branch %>% arrange(desc(sale))
	sale.branch$percent <- sale.branch$sale / sum(sale.branch$sale) * 100
	sale.branch$percent <- round(sale.branch$percent, digits = 2)
	sale.channel <- product %>% group_by(channel) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.channel <- sale.channel %>% arrange(desc(sale))
	sale.channel$percent <- sale.channel$sale / sum(sale.channel$sale) * 100
	sale.channel$percent <- round(sale.channel$percent, digits = 2)
	sale.rep <- product %>% group_by(branch_name, rep_code, rep_name) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.rep <- sale.rep %>% arrange(branch_name, desc(sale))
	sale.rep$percent <- sale.rep$sale / sum(sale.rep$sale) * 100
	sale.rep$percent <- round(sale.rep$percent, digits = 2)
	sale.province <- product %>% group_by(province) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.province <- sale.province %>% arrange(desc(sale))
	sale.province$percent <- sale.province$sale / sum(sale.province$sale) * 100
	sale.province$percent <- round(sale.province$percent, digits = 2)
	
	sale.month <- product %>% group_by(month) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.month$percent <- sale.month$sale / sum(sale.month$sale) * 100
	sale.month$percent <- round(sale.month$percent, digits = 2)
	
	sale.week <- product %>% group_by(week) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.week$percent <- sale.week$sale / sum(sale.week$sale) * 100
	sale.week$percent <- round(sale.week$percent, digits = 2)
	
	sheet <- list('branch' = sale.branch, 'channel' = sale.channel, 'rep' = sale.rep, 
		'province' = sale.province, 'month' = sale.month, 'week' = sale.week,'product' = sale.product)
	return(sheet)
}
```

## Products
- Gam: 7 gam hàng (opc, hd2, opcbd...)
- Category: danh mục sản phẩm (chủ lực, truyền thống, mục tiêu...)
- Group: nhóm điều trị (bổ dưỡng, xương khớp...)
- Type: dạng đăng ký (thuốc/ TPBVSK/ TBYT...)
- Form: dạng bào chế (viên hoàn cứng/ dung dịch...)

```
stat.products <- function(data, classify) {
# gam
# category
# group
# type
# form
}

```

## Group product
Phân tích theo nhóm sản phẩm
### New products group
```
# list.xlsx contain bravo code of new products
stat.new_product <- function(data) {
	new <- read_xlsx('list.xlsx')
	data.new <- data %>% filter(code %in% new$bravo)
	data.3gam <- data %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH06|TP10', code))
	data.4gam <- data %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH06|TP10|HH25', code))
	
	sale.new <- data.new %>% summarise(new = sum(sale))
	sale.3gam <- data.3gam %>% summarise(sale_3gam = sum(sale))
	sale.4gam <- data.4gam %>% summarise(sale_4gam = sum(sale))
	total <- data.frame(sale.new, sale.3gam)
	total$percent_3gam <- total$new / total$sale_3gam * 100
	total$percent_3gam <- round(total$percent_3gam, digits = 2)
	total <- data.frame(total, sale.4gam)
	total$percent_4gam <- total$new / total$sale_4gam * 100
	total$percent_4gam <- round(total$percent_4gam, digits = 2)
	sale.product <- data.new %>% group_by(code, name) %>% summarise(count = sum(quantity), sale = sum(sale))
	
	format_sale <- function(frame, desc = TRUE) {
		if (desc) {frame <- frame %>% arrange(desc(sale))}
		frame$percent <- frame$sale / sum(frame$sale) * 100
		frame$percent <- round(frame$percent, digits = 2)
		return(frame)
	}
	
	sale.product <- format_sale(sale.product)
	sale.branch <- data.new %>% group_by(branch_name) %>% summarise(sale = sum(sale))
	sale.branch <- format_sale(sale.branch)
	
	sale.channel <- data.new %>% group_by(channel) %>% summarise(sale = sum(sale))
	sale.channel <- format_sale(sale.channel)
	
	sale.rep <- data.new %>% group_by(branch_name, rep_code, rep_name) %>% summarise(sale = sum(sale))
	sale.rep <- format_sale(sale.rep)
	
	sale.province <- data.new %>% group_by(province) %>% summarise(sale = sum(sale))
	sale.province <- format_sale(sale.province)
	
	sale.month <- data.new %>% group_by(month) %>% summarise(sale = sum(sale))
	sale.month <- format_sale(sale.month, desc = FALSE)
	
	sale.week <- data.new %>% group_by(week) %>% summarise(sale = sum(sale))
	sale.week <- format_sale(sale.week, desc = FALSE)
	
	sheet <- list('total' = total, 'product' = sale.product, 'branch' = sale.branch, 'channel' = sale.channel, 'rep' = sale.rep, 'province' = sale.province,
		'month' = sale.month, 'week' = sale.week)
	return(sheet)
}
```
