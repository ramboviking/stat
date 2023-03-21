# Analyse product
Phân tích doanh số sản phẩm theo nhiều tiêu chí.
Tải file phân loại sản phẩm [tại đây](https://docs.google.com/spreadsheets/d/16LjQ8uVkXLTo-fDVaRPkXC-zccALC38UUzlE42bkmLc/edit?usp=sharing)

## Product
Phân tích doanh số 1 sản phẩm (có thể bao gồm một/ một vài mã).
```
# bravo: mã hàng cần phân tích
stat.product <- function(data, bravo) {
	product <- data %>% filter(code %in% bravo)
	product <- product %>% filter(promotion == false)
	product <- product %>% 
  		filter(
    		payment_cat != 'P84' & 
    		payment_cat != 'P84C' & 
    		payment_cat != 'P84E' & 
    		payment_cat != 'P87' & 
    		payment_cat != 'P87E' | 
    		is.na(payment_cat))
		
	sale.branch <- product %>% group_by(branch_name) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.branch <- sale.branch %>% arrange(desc(sale))
	sale.branch$percent <- sale.branch$sale / sum(sale.branch$sale) * 100
	sale.channel <- product %>% group_by(channel) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.channel <- sale.channel %>% arrange(desc(sale))
	sale.channel$percent <- sale.channel$sale / sum(sale.channel$sale) * 100
	sale.rep <- product %>% group_by(branch_name, rep_code, rep_name) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.rep <- sale.rep %>% arrange(branch_name, desc(sale))
	sale.rep$percent <- sale.rep$sale / sum(sale.rep$sale) * 100
	sale.province <- product %>% group_by(province) %>% summarise(count = sum(quantity), sale = sum(sale))
	sale.province <- sale.province %>% arrange(desc(sale))
	sale.province$percent <- sale.province$sale / sum(sale.province$sale) * 100
	sheet <- list('branch' = sale.branch, 'channel' = sale.channel, 'rep' = sale.rep, 'province' = sale.province)
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
