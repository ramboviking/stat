# Combo
Phân tích tình hình thực hiện combo.
Đếm số đơn hàng bán theo chương trình combo.
```
stat.combo <- function(data){
  data <- data %>% filter(grepl('COMBO|combo', promotion_code))
  bill <- data[!duplicated(data$invoice),]
  result.code <- bill %>% count(promotion_code)
  result.branch <- bill %>% count(branch_name)
  result.group <- bill %>% group_by(branch_name) %>% count(promotion_code)
  result <- list('code' = result.code, 'branch' = result.branch, 'group' = result.group)
  return(result)
}
```
