# Combo
Phân tích tình hình thực hiện combo.
Đếm số đơn hàng bán theo chương trình combo.
```
stat.combo <- function(data){
  data <- data %>% filter(grepl('COMBO|combo', promotion_code))
  bill <- data[!duplicated(data$invoice),]
  result.code <- bill %>% count(promotion_code)
  result.branch <- bill %>% count(branch_name)
  result.medrep <- bill %>% count(rep_name)
  result.date <- bill %>% count(date)
  result.branch_code <- bill %>% group_by(branch_name) %>% count(promotion_code)
  result.branch_medrep <- bill %>% group_by(branch_name) %>% count(rep_name)
  result <- list('code' = result.code, 'branch' = result.branch, 'medrep' = result.medrep,
  'date' = result.date,'branch_code' = result.branch_code, 'branch_medrep' = result.branch_medrep)
  return(result)
}
```
