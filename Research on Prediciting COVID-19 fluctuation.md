# Research on Prediciting COVID-19 fluctuation

###### tags: `COVID-19` `CSI` `SEIR`

<!-- ### 實驗階段
:::info
1. 從新冠肺炎資料集獲取所需的資料(COVID-19 raw data)
2. 將COVID-19 raw data作為SEIR Model的Input
3. 獲得SEIR Model的Output
4. 結合COVID-19 Raw data與SEIR Model的Output，是為第二筆資料集(CSI dataset)
5. 將第二筆資料集(CSI dataset)作為CSI Model的Input。
6. CSI Model(Supervised)搭配Moving window使用
:::

### My job in this research during winter vacation

1. 了解SEIR Model(X,Y要用)
2. 從學姊給的X,Y定義，備齊資料集
3. 確定CSI(Supervised)程式碼可以使用，重構程式碼
 -->

### SEIR
#### File
1. [(SEIR)03.09-COVID-19.ipynb](https://drive.google.com/file/d/1cC46W6cBN9EcEs0dRwhU0dNU6bEo8u1t/view?usp=sharing)
2. [Covid Act Now Model Reference/Assumptions]([file:///G:/%E6%88%91%E7%9A%84%E9%9B%B2%E7%AB%AF%E7%A1%AC%E7%A2%9F/%E7%A0%94%E7%A9%B6%E6%89%80/%E8%AB%96%E6%96%87%E7%A0%94%E7%A9%B6/COVID-19/2020_%E9%A0%90%E6%A8%A1SEIR_Covid_Act_Now_Model_References_and_Assumptions.pdf](https://drive.google.com/file/d/1DrfzmftybdDwp_-LA8eGEx0xRMzsVJ7n/view?usp=sharing))
#### Parameters we need
1. R0(Reproduction Rate) : default is **2.4**.
2. t_incubation : time between incubation(been infected but not contagious), default is **5.1**.
3. t_infective : time between being infective to recovered, default is **3.3**
4. Total population
    a. Suspectible
    b. **Exposed**
    c. Infected(starts with 1 first)
    d. Recovered
6. alpha(1/t_incubation)
7. gamma(1/t_infective)
8. beta(R0*gamma)
9. u : how well the intervention does. between **[0,1]**

#### Research blog
[Forecasting the COVID-19 trend using the SEIR model](https://towardsdatascience.com/forecasting-the-covid-19-trend-using-the-seir-model-90979abb9e64)


#### Data Source
因[Our World in Data](https://ourworldindata.org/coronavirus)和[Israel Ministry of Health](https://datadashboard.health.gov.il/COVID-19/general)的資料數據有落差:
1. Reproduction Rate
2. Death People
3. Vaccinated people
4. Recovered people

決定以Owid的資料為主，其他為輔
1. **(Owid)** total_cases, new_cases, total_deaths, new_deaths, reproduction rate,
2. **(Israel Ministry of Health)** new_recovers, 

2/20有一筆恢復，但是2/21才有第一筆案例，因此考慮移除掉該筆恢復案例

P.S.
1. new_recovers(Recovered people-0222(Israel Ministry of Health).xlsx)
2. **(Owid)** 2020/03/11 new_cases 的data為-28，無法辨別是否為誤植，故置之不理

* Alpha ranges from 0.000005 to 0.0001.
* Beta ranges from 0.05 to 1.0.
* Epsilon is an inverse of the incubation period. The average incubation period is 5.2 days. We make it a list of values starting from 1/5.6 to 1/4.8.
* Gamma is an inverse of the infectious period. It’s believed that the period is a week or less. We make it a list of values starting from 1/9 to 1/4.
* Initial exposed count. We do not have the exact number. We guess it will start from **2** times official **infectious** to **20** times official **infectious count**.

#### 實際取得最佳參數

[Get best Parameters](https://colab.research.google.com/drive/1ndH4lmfJq30HI5yFMlq-sWez7BYZzQSV?usp=sharing)

定義initial_use.csv，欄位如下 :
1. 從new_recovered計算取得**total_recovered**的欄位
2. 從(total_cases - total_recovered)計算取得**total_infectious**的欄位
3. **total_deaths**
4. **Reproduction rate**  2020/2/21~2020/3/13無資料，將用假設值2.4填補

應使用移動平均數(MA)，但是total的資料有需要用MA?
==我自己覺得可以先不要用MA，直接拿everyday data計算誤差。==

要RandomSearch的參數

1. total_exposed的欄位由2~20倍的total_infectious所估算，做GridSearch or RandomSearch尋找
2. total_population以2020年以色列人口數量(9291000)計算(假設總母體數量不變，且不受其他外力干擾)
3. total_susecptible由total_population-(total_exposed + total_recovered + total_deaths)，
4. alpha的範圍 : 0.000005 to 0.0001
5. Beta的範圍 : 0.05 to 1.0
6. Gamma的範圍 : 1/9 ~ 1/4
7. Epsilon的範圍 : 1/5.6 ~ 1/4.8

目標 : 找出最佳參數，最小化Loss
Loss function : RMSE
$${\sum_{n=s}^{e} sqrt((data_{actual}^{n} - data_{output}^{n})^2)}$$ 

$${data \subset data_{infectious} ,\quad data_{recovered}  }$$





---
#### References
1. [MATH 2120 SEIR Model p.s.這個一看我就懂了，扯](https://www.youtube.com/watch?v=s__bX_81PdY)
2. [03.09-COVID-19.ipynb，整理了數篇論文和實作程式碼](https://colab.research.google.com/github/jckantor/CBE30338/blob/master/docs/03.09-COVID-19.ipynb?hl=en)
3. [The SEIR Model](https://cs.uwaterloo.ca/~paforsyt/SEIR.html)
4. [csdegraaf/CoronaVirusModel](https://github.com/csdegraaf/CoronaVirusModel)
6. [p.34 有SEIR模型圖](https://ir.nctu.edu.tw/bitstream/11536/79103/1/357501.pdf)
7. [p.15~p.18 新冠疫情数据的可视化与建模方法](https://www.stata.com/meeting/china20-TurnTech/slides/China20_Xiao_Guangen.pdf)
--下面的還沒看--
7. [COVID-19 Epidemiological Parameters – What We Know So Far](https://www.publichealthontario.ca/-/media/documents/ncov/what-we-know-feb-04-2020.pdf?la=en)
8. [基于 SEIR 的新冠肺炎传播模型及拐点预测分析](http://www.juestc.uestc.edu.cn/fileDZKJDX_ZKB/journal/article/dzkjdxxbzrkxb/2020/3/PDF/2020029.pdf)
9. [SEIR modeling of the COVID-19 and its dynamics](https://link.springer.com/content/pdf/10.1007/s11071-020-05743-y.pdf)
10. [Modified SEIR and AI prediction of the epidemics trend of COVID-19 in China under public health interventions](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7139011/pdf/jtd-12-03-165.pdf)

### Data Issue
1. [worldometer](https://www.worldometers.info/coronavirus/country/israel/)
2. [Israel COVID-19 Control panel](https://datadashboard.health.gov.il/COVID-19/general)
3. [Google整理的以色列冠狀病毒相關資料](https://news.google.com/covid19/map?hl=zh-TW&mid=%2Fm%2F03spz&gl=TW&ceid=TW%3Azh-Hant&state=4)
    * [社區人流趨勢報告](https://www.google.com/covid19/mobility/)
    * [Coronavirus (COVID-19) Vaccinations](https://ourworldindata.org/covid-vaccinations?country=ISR)
    * [COVID-19 Data Repository by the Center for Systems Science and Engineering (CSSE) at Johns Hopkins University](https://github.com/CSSEGISandData/COVID-19)
    * [An interactive web-based dashboard to track COVID-19 in real time
](https://www.thelancet.com/journals/laninf/article/PIIS1473-3099(20)30120-1/fulltext)

### Code issure
1. [Python小白的數學建模課-09 微分方程模型](https://www.796t.com/article.php?id=299860)
2. [scipy.integrate.odeint](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.odeint.html)
1. [python scipy integrate.odeint用法及代碼示例](https://vimsky.com/zh-tw/examples/usage/python-scipy.integrate.odeint.html)
<!-- ### 隨筆記

1. 以Moving window解決Concept Drifting的問題
2. 整理Covid-19在以色列的資料(還沒切割train, test)，等學姊給出政策的資料來源，再進一步整合
3. 嘗試搞懂SEIR，並以COVID-19 Raw data實作看看 -->

