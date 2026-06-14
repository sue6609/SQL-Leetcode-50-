# 窗口函數 (SQL Window Functions)
窗口函數 (Window Functions) 又稱為分析函數 (Analytic Functions)，它可以對一組相關的資料列進行運算，而不會像 GROUP BY 那樣將多列合併一列。

## 窗口函數語法

```sql
function_name(expression)
OVER (
    [PARTITION BY column_name]
    [ORDER BY column_name]
    [ROWS | RANGE frame_clause]
)
```
- PARTITION BY: 將資料分組，每組獨立計算(似group by)
- ORDER BY: 決定計算時資料的排列順序
- frame_clause: 定義計算的資料範圍 (窗口框架)

## OVER 子句
OVER 子句是窗口函數的核心，用來定義「窗口」的範圍。
```sql
-- 空的 OVER() 代表整個結果集為一個窗口
SELECT
    name,
    salary,
    SUM(salary) OVER() AS total
FROM employees;
```

## 常見的窗口函數
### 排名函數

| 函數 | 說明 |
|------|------|
| ROW_NUMBER() | 為每列指派唯一的連續編號 |
| RANK() | 排名，相同值同排名，之後跳號 |
| DENSE_RANK() | 密集排名，相同值同排名，之後不跳號 |
| NTILE(n) | 將資料平均分成 n 組 |


### 彙總函數
一般的彙總函數加上 `OVER()` 子句就成為窗口函數：
| 函數 | 說明 |
|------|------|
| SUM() | 累計加總 |
| AVG() | 累計平均 |
| COUNT() | 累計計數 |
| MIN() | 累計最小值 |
| MAX() | 累計最大值 |

### 位移函數
| 函數 | 說明 |
|------|------|
| LAG(column, n) | 取前 n 列的值 |
| LEAD(column, n) | 取後 n 列的值 |
| FIRST_VALUE(column) | 取窗口中第一列的值 |
| LAST_VALUE(column) | 取窗口中最後一列的值 |
| NTH_VALUE(column, n) | 取窗口中第 n 列的值 |


## 用法範例 (Example)
假設我們有一個 `sales` 資料表：

| Month | Region | Amount |
|------|--------|--------|
| 1    | 北部   | 1000   |
| 2    | 北部   | 1500   |
| 1    | 南部   | 800    |
| 2    | 南部   | 1200   |


### 累計加總
```sql
SELECT
    Month, Region, Amount,
    SUM(Amount) OVER (PARTITION BY Region ORDER BY Month) AS RunningTotal
FROM sales;
```
| Month | Region | Amount | RunningTotal |
|------|--------|--------|--------------|
| 1    | 北部   | 1000   | 1000         |
| 2    | 北部   | 1500   | 2500         |
| 1    | 南部   | 800    | 800          |
| 2    | 南部   | 1200   | 2000         |