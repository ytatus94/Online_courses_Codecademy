# Unit 1 Matplotlib

* 畫圖：

```python
from matplotlib import pyplot as plt
x_values = [x1, x2, x3, x4, x5]
y1_values = [y11, y12, y13, y14, y15]
y2_values = [y21, y22, y23, y24, y25]
plt.plot(x_values, y1_values, color='HTML color name or HEX code', linestyle='--', marker='o') # 上網查各種可用的 linestyle 和 marker
plt.plot(x_values, y2_values, color='HTML color name or HEX code', linestyle=':',  marker='s')

# 限制圖的 x, y 上下限
plt.axis([xmin, xmax, ymin, ymax])

# 設定圖片的 X 軸 Y 軸，還有整張圖的標題
plt.xlabel('x title')
plt.ylabel('y title')
plt.title('canvas title')

plt.show() # 前面的指令都只是建立在記憶體中，直到呼叫 show() 時才會畫出來

plt.close('all') # 清除先前的圖，圖片視窗會被關掉

plt.figure( figsize=(width, height) ) # 設定圖片寬與高，單位是 inches

plt.savefig('檔名.png') # 可支援 png, svg, pdf

# 分割圖片成子圖
plt.subplot(幾列, 幾欄, 啟用第幾個子圖)
plt.subplots_adjust(left=0.125, right=0.9, top=0.9, bottom=0.1, wspace=0.2, hspace=0.2) # 可以改間距，這邊列出的是預設值

plt.legend(['legend 1', 'legend 2'], loc=0~10) # 會依照 plt.plot() 在程式碼中出現的順序排列

```

* `loc` 可以指定 legend 放哪邊：0 自動 1 右上 2 左上 3 右下 4 左下 5 右 6 中左 7 中右 8 中下 9 中上 10 中
  * 也可以用 `plt.plot(x_values, y_values, label='legend')` 來指名 legend，用這個方式比較好，但是用這個方式仍然要呼叫 `plt.legend()`
  
* 裝飾圖表

```python
ax = plt.subplot()

ax.set_xticks([列表]) # ticks 是軸上的刻度的小槓，列表表示要在哪邊畫出小橫槓
ax.set_yticks([列表])

ax.set_xticklabels([列表], rotation=角度) # labels 是軸上小槓旁的文字，可以指定角度
ax.set_yticklabels([列表], rotation=角度)
```

* 長條圖：

```python
plt.bar(幾條bar, [bar 高度列表], , bottom=[bar 的底部要從哪邊開始畫的列表，通常是疊在前一個 bar 圖上], yerr=[誤差列表], capsize=誤差上下橫槓有多寬)
```

* 帶狀圖：

```python
plt.fill_between([x_values], [y_lower], [y_upper], alpha=透明度0~1)
```

* 大餅圖：

```python
plt.pie([列表], labels=[legend], autopct='%格式化字串') # 格式化字串中如果有% 則要用兩個 %%
plt.axis('equal') # 畫出正圓的大餅圖
```
 
* histogram: 

```python
plt.hist([列表], range=(xmin, xmax), bins=幾個bins, alpha=透明度0~1, histtype='step', normed=True)
```
預設是顏色填滿的，用 `histtype='step'` 則不填滿只畫線，`normed=True` 會歸一，`range=(xmin, xmax)` 包含 xmin 但並不包含 xmax

# Unit 2 Pandas

* 用 Pandas 處理表格的資料 `import pandas as pd`
* DataFrame 是一個表格物件，每一欄有個名字，每一列有個 id
  * 建立 DataFrame 方式一：用 dictionary
  
    ```python
    pd.DataFrame({'第一欄的名字'：[第一欄的值的 list], '第二欄的名字'：[第二欄的值的 list], '第三欄的名字'：[第三欄的值的 list], ...})
    ```
  
    * 每個列表內的數目要相同
    * 表格建立後欄位是以字母順序排列，不會按照字典內定義的順序
  * 建立 DataFrame 方式二：用 lists

    ```python
    pd.DataFrame([第一欄的值的 list], [第二欄的值的 list], [第三欄的值的 list], columns=['第一欄的名字', 第二欄的名字', '第三欄的名字'])
    ```
    * 用這個方式建立表格的話，欄位順序和建立的順序是相同的
    * 每個列表內的數目要相同
* CSV 
  * 全名是 comma separated values
  * 表格也可以用 CSV 餵入：pd.read_csv('檔名.csv')
* 檢查表格內容

  ```python
  df = pd.read_csv('檔名.csv')
  print(df.head(n)) # 可以列出前 n 列的內容，若沒有輸入 n 則預設是 5
  print(df.info()) # 顯示每一欄的統計資料
  ```
* 選擇表格內資料
  * 選擇欄位：`df['欄位的名字']` 或是 `df.欄位的名字`，若是欄位的名字有空白時則只能用第一種方式
  * 選擇多欄位：用欄位名字的列表當參數 `df[ ['欄位名字一', '欄位名字二'] ]`
  * 選擇列：`df.iloc[n]`  n 表示第 n 列(列的 index)，從 0 開始算起
  * 選擇許多列：`df.iloc[star: end]` 就用 list 的 slice 來選擇，但這些列就是連續的
  * 選擇特定列：`df[ df.欄位名 條件判斷式 ]`，用 `|` 和 `&` 來表示 or 和 and，此時條件判斷式記得加括號，條件判斷式不用加 if
    * 例如：`(df.欄位名 == 'A') | (df.欄位名 == 'B')`
  * 選擇特定列：`df.[ df.欄位名.isin([該欄位名裡面的某些值的列表]) ]`
  * 選擇列後會留下原本的 id，可以用 `df.reset_index()` 改 id
  * 不加參數時，原先的 id 會存成一個新的欄位叫做 index，可以加參數 `drop=True` 而不要這一新欄位
  * `df.reset_index()` 預設傳回一個新的表格物件，用參數 `inplace=True` 可直接傳回修改後的原表格
    * 所以可以用 `df.reset_index(inplace=True, drop=True)`
* 新增一欄：`df['新欄位名字'] = [新欄位值的列表]`，注意列表的數目要和原本表格中的列的數目一樣
  * 若是欄位的值都一樣，那就不用列表，直接放值，例如 `df['欄位名字'] = 5` 則該欄位全部的值都是 5
  * 也可以不用列表，改放算式
    * 例如：`df.欄位名字.apply(lower)` 改小寫，`df.欄位名字.apply(upper)` 改大寫，`df.欄位名字.apply(lambda function)` 可以對欄位做 lambda function 中的運算
* lambda function:
  * `lambda x: OUTCOME IF TRUE if CONDITIONAL else OUTCOME IF FALSE`
  * 對 row 做 lambda function 運算：`lambda row: row 運算`，用 `row.column_name` 或 `row['column_name']` 存取某個特別的欄位的值
  * `df.apply(lambda function row 運算, axis=1)` 對列做 lambda function 的運算
* 欄位改名字
  * 方法一：`df.columns = ['第ㄧ欄的新名字', '第二欄的新名字']`
  * 方法二：`df.rename(columns={'舊名字一':'新名字一', '舊名字二':'新名字二'}, inplace=True)`，用參數 `inplace=True` 是直接修改原表格
* 對欄位做運算：`df.column_name.command()`
  * 常見的運算命令有：
    * mean()：欄位的平均值
    * std()：欄位的標準差
    * median()：欄位的中位數
    * max() 和 min()：欄位的最大值和最小值
    * count()：欄位計數
    * nunique()：欄位內獨立值的個數
    * unique()：傳回欄位內獨立值的列表
* Group by
  * `df.groupby('column1').column2.measurement()` 不會產生 DataFrame
  * `df.groupby('column1').column2.measurement().reset_index()` 會產生 DataFrame
* 樞紐分析表：`df.pivot(columns='ColumnToPivot', index='ColumnToBeRows', values='ColumnToBeValues')`
  * 結尾加入 `.reset_index()` 會產生 DataFrame
* 合併表格，表格有相同的欄位名稱時，會自動合併
  * `pd.merge(DataFrame1, DataFrame2)`
  * `DataFrame1.merge(DataFrame2).merge(DataFrame3)` 可以合併多格表格
  * 當表格的欄位名稱不同時，要指定要依哪個欄位來合併表格：
    * 方法一：改欄位名字後再合併：`pd.merge(DataFrame1, DataFrame2.rename(columns={'在DataFrame2中的欄位名稱': '要改成在DataFrame1中的欄位名稱'}))`
    * 方法二：指名兩個表格要依照哪個欄位合併：`pd.merge(DataFrame1, DataFrame2, left_on='DataFrame1中的欄位名稱', right_on='DataFrame2中的欄位名稱', suffixes=['_欄位名稱後綴1','_欄位名稱後綴2'])`
  * inner merge：當兩個表格中的某一列不合時，合併兩個表格後就會少掉這一列
  * Outer Merge：`pd.merge(DataFrame1, DataFrame2, how='outer')` 合併兩個表格，若有不合的列，一樣保留在合併後的表格，資訊空缺的格子裡會填上 `None` 或 `nan`
  * 左合併：`pd.merge(DataFrame1, DataFrame2, how='left')` 保留完整的 DataFrame1，而 DataFrame2 中符合的才合併
    * 也可以用 `df1.merge(df2, how='left')`
  * 右合併：`pd.merge(DataFrame1, DataFrame2, how='right')` 保留完整的 DataFrame2，而 DataFrame1 中符合的才合併
* 結合多個表格：`pd.concat([df1, df2, df2, ...])` 要結合的表格，表格的欄位順序和數目要ㄧ樣

# Unit 3 NumPy

- import numpy as np

- 建立陣列：my_array = np.array([列表]) 或 np.genfromtxt('sample.csv', delimiter=',') 從檔案中讀取成陣列
- Numpy array 可以用陣列名字直接做加減乘除，會對陣列內的每個元素做對應的加減乘除

- 建立二維陣列：np.array([ [第一列的列表], [第二列的列表] ]) 兩個列表中的元素個數要相等，高維陣列以此列推
- 同一欄位的是 axis=0，同一列的是 axis=1，即 axis=0 是指 column，axis=1 是指 row
- 高維陣列的話，最外面的括號是 axis=0，隨著括號增加而 axis 遞增

- 陣列切片的方式和用在列表上的相同，因此可以使用列表的 slice 的方式來存取 Numpy array 的元素

- 二維陣列的元素：a[row, column] 一樣可以使用列表的 slice 的方式來存取二維陣列的元素

- 選取二維陣列中的一整欄：a[:, 要選取的欄]

- 選取二維陣列中的一整列：a[要選取的列, :]

- 邏輯運算：a[ 用 | 和 & 並且以括號括住每一項條件判斷式 ] 注意只有一個 | 和 &

- 簡單的統計：
平均：np.mean( Numpy array ) 也可以 np.mean( 用 Numpy array 的名字做的條件判斷 )
- 二維陣列平均：np.mean( Numpy array ) 是全部元素的平均， np.mean(Numpy array, axis=0) 是每一欄的平均，np.mean(Numpy array, axis=1) 是每一列的平均
- 排序：np.sort( Numpy array )
- 中位數：np.median( Numpy array ) 列表元素個數是奇數時，中位數是最中間那個元素，列表元素個數是偶數時，中位數是最中間兩個數的平均值
- Percentiles：np.percentile(Numpy array, 40) 會傳回一個數，表示列表中有 40% 的比傳回的數還小，60% 的比傳回的數還大
- 例如 d = [1, 2, 3, 4, 4, 4, 6, 6, 7, 8, 8] 有 11 個值 np.percentile(d, 40) 會傳回第二個 4 因為 [1, 2, 3, 4]是 40% 比較小 [4, 6, 6, 7, 8, 8] 是 60% 比較大
- 標準差：np.std( Numpy array )
- np.mean(條件判斷) 會傳回百分之多少滿足條件判斷
- When np.mean calculates a logical statement, the resulting mean value will be equivalent to the total number of True items divided by the total array length.
- Values that don’t fit within the majority of a dataset are known as outliers
- The 25th percentile is called the first quartile
- The 50th percentile is called the median
- The 75th percentile is called the third quartile
- The minimum, first quartile, median, third quartile, and maximum of a dataset are called a five-number summary.
The difference between the first and third quartile is a value called the interquartile range.
- 50% of the dataset will lie within the interquartile range.
- histogram: skew-right 有尾巴在右邊 skew-left 有尾巴在左邊
- np.random.normal(loc=mean, scale=std, size=number to generate)
- 68% of our samples will fall between +/- 1 standard deviation of the mean
95% of our samples will fall between +/- 2 standard deviations of the mean
99.7% of our samples will fall between +/- 3 standard deviations of the mean
- np.random.binomial(N trials, probability, size=number of experiment)
- A, B 兩獨立事件同時發生的機率是 P(A ∩ B) = P(A) × P(B)

- 罕見病發生機率為 P1，檢測結果正確機率為 P2，檢測到病患有罕見病的情況有兩種：
病患有罕見病，檢測結果也正確 P(檢測到生病情況一) = P1 x P2
- 病患沒有罕見病，檢測結果錯誤 (檢測顯示病患有罕見病，但是其實病患沒病) P(檢測到生病情況二) = (1 - P1) x (1 - P2)
- P(檢測到生病) = P(檢測到生病情況一) + P(檢測到生病情況二)
- Bayes' Theorem
P(A|B) 給定 B 發生的情況下 A 發生的機率
P(A|B) = P(B|A) x P(A) / P(B)

# Unit 4 SciPy

- np.random.choice(population, size=30, replace=False) 從 population 中隨機選取 30 個元素
- A null hypothesis is a statement that the observed difference is the result of chance.
- A null hypothesis, which is a prediction that there is no significant difference.
- 觀察到的差異是來自於偶然，實際上並沒有差異
- Type I error, is finding a correlation between things that are not related. This error is sometimes called a "false positive" and occurs when the null hypothesis is rejected even though it is true.
- 沒關係的事物中找關係
false positive
Null hypothesis 是對的，但分析的結果卻認為它是錯的
- Type II error, is failing to find a correlation between things that are actually related. This error is referred to as a "false negative" and occurs when the null hypothesis is accepted even though it is false.
- 有關係的事物中找關係，卻找不到關係
false negative
Null hypothesis 是錯的，但分析的結果卻認為它是對的
- p-value 是 "null hypothesis 是正確的" 的機率.
p-value = 0.05 表示 null hypothesis 有 5% 的機率是正確的，就是說有 5% 的機率兩個參與判斷的樣本間其實沒有差異
p-value 越大的話越可能是 false-positive
一般來說是選 p-value < 5% 時來 reject null hypothesis，表示參與判斷的兩樣本間其實是有顯著差異的

- 數值的資料用 One Sample T-Tests, Two Sample T-Tests, ANOVA (Analysis of Variance), Tukey Tests
分門別類的資料用 Binomial Tests, Chi Square
- A univariate T-test compares a sample mean to a hypothetical population mean.
- 1 Sample T-Testing:
from scipy.stats import ttest_1samp
tstat, pval = ttest_1samp(example_distribution, expected_mean)
- 2 Sample T-Test:
from scipy.stats import ttest_ind
tstat, pval = ttest_ind(np_array1, np_array2)

- 做越多次 T-test 的話 Type-I error 會越來越大
The p-value is the probability that we incorrectly reject the null hypothesis on each t-test. The more t-tests we perform, the more likely that we are to get a false positive, a Type I error.
- ANOVA
比較超過兩組數值 dataset 時使用
只能告訴我們，至少有一個 dataset 是和其他不同，但是不知道不同的是哪一個
from scipy.stats import f_oneway
fstat, pval = f_oneway(scores_mathematicians, scores_writers, scores_psychologists)
- Tukey's Range Test:
from statsmodels.stats.multicomp import pairwise_tukeyhsd
v = np.concatenate([a, b, c]) 要提供一個把全部資料串起來的列表
- labels = ['a'] * len(a) + ['b'] * len(b) + ['c'] * len(c) 要標記哪個元素屬於哪一個資料
- tukey_results = pairwise_tukeyhsd(v, labels, 0.05) p-value 這邊用 0.05
- Binomial Test
適用於剛好兩個分門別類的 dataset
from scipy.stats import binom_test
pval = binom_test(number of observed successes, number of total trials, expected probability of success)
- Chi Square Test
兩個以上分門別類的 dataset 時使用
要提供一個列表X，列表的欄位是不同的條件，每一列表示一組觀察
from scipy.stats import chi2_contingency
chi2, pval, dof, expected = chi2_contingency(X)
- A sample size calculator requires three numbers
The Baseline conversion rate
The Minimum detectable effect
The Statistical significance
