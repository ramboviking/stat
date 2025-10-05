/ call library
```
library(readxl)
library(writexl)
library(dplyr)
library(lubridate)
library(openxlsx)
library(data.table)

setwd('D:/DA')

read_data <- function() {
	filename <- 'Mega.xlsx'
	month1 <- read_excel('Mega.xlsx', sheet = 'Data T1')
	month2 <- read_excel('Mega.xlsx', sheet = 'Data T2')
	month3 <- read_excel('Mega.xlsx', sheet = 'Data T3')
	month4 <- read_excel('Mega.xlsx', sheet = 'Data T4')
	month5 <- read_excel('Mega.xlsx', sheet = 'Data T5')
	month6 <- read_excel('Mega.xlsx', sheet = 'Data T6')
	month7 <- read_excel('Mega.xlsx', sheet = 'Data T7')
	month8 <- read_excel('Mega.xlsx', sheet = 'Data T8')
	month9 <- read_excel('Mega.xlsx', sheet = 'Data T9')
	result <- rbind(month9, month2, month3, month4, month5, month6, month7, month8, month1)
	return(result)
}
source <- read_data()

transform <- function(source) {
	source <- rename(source, c('date' = 1, 'title' = 2, 'reference' = 3, 'state' = 4, 'district' = 5, 'account_id' = 6, 'account_name' = 7, 
'product_id' = 8, 'product_name' = 9, 'unit' = 10, 'quantity' = 11, 'price' = 12, 'sale' = 13))
	data <- select(source, c(date, state, account_id, account_name, product_id, product_name, unit, quantity, sale))
	data$year <- year(data$date)
	data$month <- month(data$date)
	return(data)
}
data <- transform(source)


month <- data %>% group_by(month) %>% summarise(sale = sum(sale))
month <- t(month)
product <- data %>% group_by(product_id, product_name, month) %>% summarise(sale = sum(sale))
product <- setDT(product)
dcast(product, product_id + product_name ~ month, value.var = 'sale')
```
