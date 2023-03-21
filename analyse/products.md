# Analyse product
Phân tích doanh số sản phẩm theo nhiều tiêu chí.
Tải file phân loại sản phẩm [tại đây](https://docs.google.com/spreadsheets/d/16LjQ8uVkXLTo-fDVaRPkXC-zccALC38UUzlE42bkmLc/edit?usp=sharing)

## Product
Phân tích doanh số 1 sản phẩm (có thể bao gồm một/ một vài mã).
```
# bravo: mã hàng cần phân tích
stat.product <- function(data, bravo) {
	product <- data %>% filter(code %in% bravo)
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
	sale.month <- sale.month %>% arrange(month)
	sale.month$percent <- sale.month$sale / sum(sale.month$sale) * 100
	sale.month$percent <- round(sale.month$percent, digits = 2)
	
	sheet <- list('branch' = sale.branch, 'channel' = sale.channel, 'rep' = sale.rep, 
		'province' = sale.province, 'month' = sale.month, 'product' = sale.product)
	file_name <- paste(bravo, '.xlsx', sep = '')
	write_xlsx(sheet, file_name)
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
### New product group
```
# list.xlsx contain bravo code of new products
stat.new_product <- function(data) {
	new <- read_xlsx('list.xlsx')
	data.new <- data %>% filter(code %in% new$bravo)
	sale.product <- data.new %>% group_by(code, name) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.product <- sale.product %>% arrange(desc(sale))
	sale.branch <- data.new %>% group_by(branch_name) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.branch <- sale.branch %>% arrange(desc(sale))
	sale.channel <- data.new %>% group_by(channel) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.channel <- sale.channel %>% arrange(desc(sale))
	sale.rep <- data.new %>% group_by(branch_name, rep_code, rep_name) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.rep <- sale.rep %>% arrange(branch_name, desc(sale))
	sale.province <- data.new %>% group_by(province) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.province <- sale.province %>% arrange(desc(sale))
	sheet <- list('product' = sale.product, 'branch' = sale.branch, 'channel' = sale.channel, 'rep' = sale.rep, 'province' = sale.province)
	return(sheet)
}
```
