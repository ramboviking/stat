# Week analytics
Phân tích tình hình kinh doanh của một tuần, so sánh thực hiện với tuần trước đó.
Ghi chú:
- Tự động lọc lấy dữ liệu của 4 gam: OPC, OPC Bình Dương, Hóa Dược 2 và TW25.
- Không loại bỏ các dữ liệu cho biếu tặng, khuyến mãi.

## Code

```
stat.week <- function(this_week, pre_week) {
this_week <- this_week %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH06|TP10|HH25', code))
pre_week <- pre_week %>% filter(grepl('TP02|TP03|TP05|TP07|HH04|HH06|TP10|HH25', code))

this_branch <- this_week %>% group_by(branch_name) %>% summarise(this_week = sum(sale))
pre_branch <- pre_week %>% group_by(branch_name) %>% summarise(last_week = sum(sale))
branch <- merge(this_branch, pre_branch, by = 'branch_name')
branch$growth <- (branch$this_week - branch$last_week)/branch$last_week * 100
branch$growth <- round(branch$growth, digits = 2)
branch <- branch %>% arrange(desc(growth))

this_channel <- this_week %>% group_by(channel) %>% summarise(this_week = sum(sale))
last_channel <- pre_week %>% group_by(channel) %>% summarise(last_week = sum(sale))
channel <- merge(this_channel, last_channel, by = 'channel')
channel$growth <- (channel$this_week - channel$last_week)/ channel$last_week * 100
channel$growth <- round(channel$growth, digits = 2)
channel <- channel %>% arrange(desc(growth))


this_gam <- this_week %>% group_by(gam) %>% summarise(this_week = sum(sale))

last_gam <- pre_week %>% group_by(gam) %>% summarise(last_week = sum(sale))
gam <- merge(this_gam, last_gam, by = 'gam')
gam$growth <- (gam$this_week - gam$last_week)/gam$last_week *100
gam$growth <- round(gam$growth, digits = 2)
gam <- gam %>% arrange(desc(growth))

this_product <- this_week %>% group_by(name) %>% summarise(this_count = sum(quantity))
last_product <- pre_week %>% group_by(name) %>% summarise(last_count = sum(quantity))
product <- merge(this_product, last_product, by = 'name')
product$growth <- (product$this_count - product$last_count)/product$last_count *100
product$growth <- round(product$growth, digits = 2)
product <- product %>% arrange(desc(growth))


this_rep <- this_week %>% group_by(rep_name) %>% summarise(this_week = sum(sale))
last_rep <- pre_week %>% group_by(rep_name) %>% summarise(last_week = sum(sale))
rep <- merge(this_rep, last_rep, by = 'rep_name')
rep$growth <- (rep$this_week - rep$last_week)/rep$last_week * 100
rep$growth <- round(rep$growth, digits = 2)
rep <- rep %>% arrange(desc(growth))

this_province <- this_week %>% group_by(province) %>% summarise(this_week = sum(sale))
last_province <- pre_week %>% group_by(province) %>% summarise(last_week = sum(sale))
province <- merge(this_province, last_province, by = 'province')
province$growth <- (province$this_week - province$last_week)/province$last_week * 100
province$growth <- round(province$growth, digits = 2)
province <- province %>% arrange(desc(growth))

sheet <- list('branch' = branch, 'channel' = channel, 'gam' = gam, 'product' = product, 'rep' = rep, 'province' = province)
write_xlsx(sheet, 'week analytisc.xlsx')
}
```

## Use
Do dataset đã có field 'week' thể hiện số tuần trong năm. Do đó, chỉ cần lọc dữ liệu của tuần cần phân tích và tuần liền trước
đưa vào làm tham số cho function stat.week.

```
this_week <- data %>% filter(week == 12)
pre_week <- data %>% filter(week == 11)

week <- stat.week(this_week, pre_week)
```
