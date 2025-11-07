# Phân phối độc quyền
```
library(readxl)
library(writexl)
library(lubridate)
library(dplyr)


product <- data %>% filter(grepl('TP02-0021|TP02-0023|TP02-0036|TP02-0037|TP02-0009|TP02-0010|TP02-0012', code))

remove.gift <- function(input) {
input <- input %>% 
  filter(
    payment_cat != 'P84' & 
    payment_cat != 'P84C' & 
    payment_cat != 'P84E' & 
    payment_cat != 'P87' & 
    payment_cat != 'P87E' | 
    is.na(payment_cat))
return(input)
}

stat.dq <- function(product) {
sale.sum <- product %>% group_by(name) %>% summarise(count =sum(quantity), sale =sum(sale))
sale.channel <- product %>% group_by(channel) %>% summarise(sale =sum(sale))
sale.type <- product %>% group_by(customer_type) %>% summarise(sale = sum(sale))
# Long Châu, Pharmacity, An Khang, Trung Sơn
chain <- product %>% filter(grepl('NT-2N101-01-1294|CT-1N100-3219|NT-2N101-01-4161|CT-1N100-3220|CT-2N101-01-0926|CT-1N100-3218|PK-2N101-01-3443|CT-1N100-3222|CT-2N301-1509', customer_code))
sale.chain <- chain %>% group_by(customer_name) %>% summarise(count = sum(quantity), sale = sum(sale))
sale.chain_type <- chain %>% group_by(customer_name, name) %>% summarise(count = sum(quantity), sale = sum(sale))
sale.cover <- product %>% group_by(channel) %>%summarise(cover = n_distinct(customer_code))
sale.cover_product <- product %>% group_by(channel, name) %>%summarise(cover = n_distinct(customer_code))
sale.cover_province <- product %>% group_by(province) %>%summarise(cover = n_distinct(customer_code))

dkd25 <- product %>% filter(code == 'TP02-0021')
dkd25.channel <- dkd25 %>% group_by(channel) %>% summarise(count = sum(quantity), sale = sum(sale))
dkd15 <- product %>% filter(code == 'TP02-0023')
dkd15.channel <- dkd15 %>% group_by(channel) %>% summarise(count = sum(quantity), sale = sum(sale))
kttbd <- product %>% filter(code == 'TP02-0037')
kttbd.channel <- kttbd %>% group_by(channel) %>% summarise(count = sum(quantity), sale = sum(sale))
kttbf <- product %>% filter(code == 'TP02-0036')
kttbf.channel <- kttbf %>% group_by(channel) %>% summarise(count = sum(quantity), sale = sum(sale))
csv4 <- product %>% filter(code == 'TP02-0010')
csv4.channel <- csv4 %>% group_by(channel) %>% summarise(count = sum(quantity), sale = sum(sale))
csv10 <- product %>% filter(code == 'TP02-0009')
csv10.channel <- csv10 %>% group_by(channel) %>% summarise(count = sum(quantity), sale = sum(sale))
csv20 <- product %>% filter(code == 'TP02-0012')
csv20.channel <- csv20 %>% group_by(channel) %>% summarise(count = sum(quantity), sale = sum(sale))


result <- list('Over' = sale.sum, 'Channel' = sale.channel, 'Customer' = sale.type, 'Chain' = sale.chain, 'Chain_Type' = sale.chain_type, 'Coverage' = sale.cover,
'Cover_Product' = sale.cover_product, 'Cover_Province' = sale.cover_province,
'DKD25' = dkd25.channel, 'DKD15' = dkd15.channel, 'KTT_BD' = kttbd.channel, 'KTT bphim' = kttbf.channel, 'CSV4g' = csv4.channel, 'CSV10g' = csv10.channel, 'CSV20g' = csv20.channel)
return(result)
}


Đơn giá bình quân
product <- product %>% filter(sale !=0)
product %>% group_by(name) %>% summarise(count = sum(quantity), sale = sum(sale))

```
