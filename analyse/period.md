# Period analytics
Phân tích tình hình kinh doanh của một chu kỳ thời gian, so sánh thực hiện với chu kỳ trước đó. Chu kỳ thời gian có thể là 1 tuần, 1 tháng, 1 tháng, 1 năm.

Ghi chú:
- Tự động lọc lấy dữ liệu của 4 gam: OPC, OPC Bình Dương, Hóa Dược 2 và TW25.
- Tùy chọn loại bỏ các dữ liệu cho biếu tặng, khuyến mãi (mặc định loại bỏ).

## Period

```
stat.period <- function(this_period, pre_period, promotion = FALSE) {
this_period <- this_period %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH06|TP10|HH25', code))
pre_period <- pre_period %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH06|TP10|HH25', code))
if (!promotion) {
  this_period <- this_period %>% filter(promotion == FALSE)
  this_period <- this_period %>% 
  filter(
    payment_cat != 'P84' & 
    payment_cat != 'P84C' & 
    payment_cat != 'P84E' & 
    payment_cat != 'P87' & 
    payment_cat != 'P87E' | 
    is.na(payment_cat))
  pre_period <- pre_period %>% filter(promotion == FALSE)
  pre_period <- pre_period %>% 
  filter(
    payment_cat != 'P84' & 
    payment_cat != 'P84C' & 
    payment_cat != 'P84E' & 
    payment_cat != 'P87' & 
    payment_cat != 'P87E' | 
    is.na(payment_cat))
}

this_branch <- this_period %>% group_by(branch_name) %>% summarise(this_period = sum(sale))
pre_branch <- pre_period %>% group_by(branch_name) %>% summarise(last_period = sum(sale))
branch <- merge(this_branch, pre_branch, by = 'branch_name', all.x = TRUE)
branch$growth <- (branch$this_period - branch$last_period)/branch$last_period * 100
branch$growth <- round(branch$growth, digits = 2)
branch <- branch %>% arrange(desc(growth))

this_channel <- this_period %>% group_by(channel) %>% summarise(this_period = sum(sale))
last_channel <- pre_period %>% group_by(channel) %>% summarise(last_period = sum(sale))
channel <- merge(this_channel, last_channel, by = 'channel', all.x = TRUE)
channel$growth <- (channel$this_period - channel$last_period)/ channel$last_period * 100
channel$growth <- round(channel$growth, digits = 2)
channel <- channel %>% arrange(desc(growth))


this_gam <- this_period %>% group_by(gam) %>% summarise(this_period = sum(sale))

last_gam <- pre_period %>% group_by(gam) %>% summarise(last_period = sum(sale))
gam <- merge(this_gam, last_gam, by = 'gam', all.x = TRUE)
gam$growth <- (gam$this_period - gam$last_period)/gam$last_period *100
gam$growth <- round(gam$growth, digits = 2)
gam <- gam %>% arrange(desc(growth))

this_product <- this_period %>% group_by(name) %>% summarise(this_count = sum(quantity))
last_product <- pre_period %>% group_by(name) %>% summarise(last_count = sum(quantity))
product <- merge(this_product, last_product, by = 'name', all.x = TRUE)
product$growth <- (product$this_count - product$last_count)/product$last_count *100
product$growth <- round(product$growth, digits = 2)
product <- product %>% arrange(desc(growth))


this_rep <- this_period %>% group_by(rep_name) %>% summarise(this_period = sum(sale))
last_rep <- pre_period %>% group_by(rep_name) %>% summarise(last_period = sum(sale))
rep <- merge(this_rep, last_rep, by = 'rep_name', all.x = TRUE)
rep$growth <- (rep$this_period - rep$last_period)/rep$last_period * 100
rep$growth <- round(rep$growth, digits = 2)
rep <- rep %>% arrange(desc(growth))

this_province <- this_period %>% group_by(province) %>% summarise(this_period = sum(sale))
last_province <- pre_period %>% group_by(province) %>% summarise(last_period = sum(sale))
province <- merge(this_province, last_province, by = 'province', all.x = TRUE)
province$growth <- (province$this_period - province$last_period)/province$last_period * 100
province$growth <- round(province$growth, digits = 2)
province <- province %>% arrange(desc(growth))

sheet <- list('branch' = branch, 'channel' = channel, 'gam' = gam, 'product' = product, 'rep' = rep, 'province' = province)
write_xlsx(sheet, 'period analytisc.xlsx')
return(sheet)
}
```

## Use
Do dataset đã có field 'week', 'month' thể hiện số tuần, tháng trong năm. Do đó, chỉ cần lọc dữ liệu của tuần cần phân tích và tuần liền trước
đưa vào làm tham số cho function stat.period.

```
this_week <- data %>% filter(week == 12)
pre_week <- data %>% filter(week == 11)

week <- stat.period(this_week, pre_week)
```

## Today
Phân tích kết quả hoạt động ngày, thường dùng để phân tích cuối ngày hoặc đầu ngày hôm sau.
```
stat.today <- function(data) {
  day = Sys.Date()
  data.day <- data %>% filter(date == day)
}
```
