餐饮店铺销售数据分析
#拿到数据 python进行数据

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from openpyxl.styles import NamedStyle 
plt.rcParams['font.sans-serif'] = 'SimHei' ## 设置中文显示
%matplotlib inline

# 1.加载数据
data1 = pd.read_excel('meal_order_detail.xlsx',sheet_name='meal_order_detail1')
data2 = pd.read_excel('meal_order_detail.xlsx',sheet_name='meal_order_detail2')
data3 = pd.read_excel('meal_order_detail.xlsx',sheet_name='meal_order_detail3')

# 2.数据预处理（合并数据,NA等处理），分析数据
data = pd.concat([data1,data2,data3],axis=0)  #按照行进行拼接数据
# data.head(5)
data.dropna(axis=1,inplace=True) #按照列删除na列，并且修改源数据data
```
#频数统计，什么菜最受欢迎（对菜名进行频数统计，取最大前10名）
```python
dishes_count = data['dishes_name'].value_counts()[:10]
#3数据可视化matplotlib
dishes_count.plot(kind="line",color=['r'])
dishes_count.plot(kind="bar",fontsize=16)
for x,y in enumerate(dishes_count):
    print(x,y)
    plt.text(x,y+3,y,ha="center",fontsize=12)

```
<img width="864" height="869" alt="image" src="https://github.com/user-attachments/assets/9e4fc6f1-e062-42ed-a73a-aacf83138fd6" />

#订单点菜的种类最多（）
```python
data_group = data['order_id'].value_counts()[:10]
data_group.plot(kind="bar",fontsize=16,color=['r','m','b','y','g'])
plt.title('订单点菜的种类top10')
plt.xlabel('订单ID',fontsize=16)
plt.ylabel('点菜种类',fontsize=16)
#8月份餐厅点单点菜种类前10名，平均点菜25个菜品
```
<img width="957" height="719" alt="image" src="https://github.com/user-attachments/assets/97ab9df2-596c-4262-b3a1-1cab16559520" />

#订单ID点菜数量Top10（分组order_id,counts求和，排序，前十）
```python
data['total_amounts'] = data['counts']*data['amounts'] #统计单道菜消费总额
dataGroup = data[['order_id','counts','amounts','total_amounts']].groupby(by="order_id")
Group_sum = dataGroup.sum() #分组求和
Group_sum
sort_counts = Group_sum.sort_values(by='counts',ascending=False)
sort_counts['counts'][:10].plot(kind="bar",fontsize=16)
plt.title('订单ID点菜数量TOP10')
plt.xlabel('订单ID')
plt.ylabel('点菜数量')
```
<img width="912" height="732" alt="image" src="https://github.com/user-attachments/assets/9dc12980-3a58-4578-b4e9-b0ee1c85188b" />

#哪个订单ID吃的钱最多（排序）
```python
sort_total_amounts = Group_sum.sort_values(by="total_amounts",ascending=False)
sort_total_amounts['total_amounts'][:10].plot(kind="bar")
plt.xlabel('订单ID')
plt.ylabel('消费金额')
plt.title('消费金额前10')
```
<img width="909" height="717" alt="image" src="https://github.com/user-attachments/assets/c663c69e-3004-480e-9086-dea551d08f97" />


#哪个订单ID平均消费最贵
```python
Group_sum['average'] = Group_sum['total_amounts']/Group_sum['counts']
sort_average = Group_sum.sort_values(by="average",ascending=False)
sort_average['average'][:10].plot(kind="bar")
plt.title('订单消费单价前10')
plt.xlabel('订单ID')
plt.ylabel('消费单价')
sort_average
```
<img width="606" height="509" alt="image" src="https://github.com/user-attachments/assets/52029380-904b-423e-9374-59685f047bcf" />
<img width="891" height="711" alt="image" src="https://github.com/user-attachments/assets/a3b0ecb6-2df9-4e08-9812-c9a37fba81b0" />


#一天当中什么时间段，点菜量比较集中（hour）
```python
data['hourcount'] = 1
data['time'] = pd.to_datetime(data['place_order_time'])
data['hour'] = data['time'].map(lambda x:x.hour)
gp_by_hour = data.groupby(by='hour').count()['hourcount']
gp_by_hour.plot(kind='bar')
plt.title('点菜数量与小时的关系图')
plt.xlabel('8月份小时')
plt.ylabel('点菜数量')
```
<img width="906" height="702" alt="image" src="https://github.com/user-attachments/assets/35a5739b-346a-4503-b021-ed468ddc48e6" />

#哪一天订餐数量最多
```python
data['daycount'] = 1
data['day']=data['time'].map(lambda x:x.day)
gp_by_day = data.groupby(by='day').count()['daycount']
gp_by_day.plot(kind='bar')
plt.title('点菜数量与日期的关系图')
plt.xlabel('8月份日期')
plt.ylabel('点菜数量')
#排序，取点菜数量最大的前5天
# day_sort = gp_by_day.sort_values(ascending=False)[:10]
# day_sort.plot(kind='bar')
# plt.title('点菜数量与日期的关系图')
# plt.xlabel('8月份日期')
# plt.ylabel('点菜数量')
```
<img width="918" height="695" alt="image" src="https://github.com/user-attachments/assets/fa8480ab-2218-46ca-a7a0-ec2cb9f2094f" />

#查看星期几人数最多，订餐数最多，映射数据到星期
```python
data['weekcount'] = 1
week_map = {
    0:'星期一',
    1:'星期二',
    2:'星期三',
    3:'星期四',
    4:'星期五',
    5:'星期六',
    6:'星期日',
}
data['weekday'] = data['time'].map(lambda x:x.weekday())
data['weekdayzh'] = data['weekday'].map(week_map)
gp_by_weekday = data.groupby(by='weekday').count()['weekcount']
gp_by_weekday.index = gp_by_weekday.index.map(week_map)
gp_by_weekday.plot(kind='bar')
plt.title('点菜数量与星期的关系图')
plt.xlabel('8月份每星期')
plt.ylabel('点菜数量')
```
<img width="873" height="729" alt="image" src="https://github.com/user-attachments/assets/42c4e126-552d-4bc8-9168-f34727ca97d1" />

```python
plt.figure(figsize=(10,4))
plt.subplot(1,2,1)
plt.plot(gp_by_weekday,color="red",marker="o",label="数据A")
plt.title('')
plt.legend()
plt.subplot(1,2,2)
gp_by_hour.plot(kind="bar")
```
<img width="1328" height="567" alt="image" src="https://github.com/user-attachments/assets/32bbabea-292a-465a-ab88-759558caba2a" />

# 讲分好数据进行单独的工作表存储，合并到一个excel中进行导出。
```python
df_week = data.groupby(['weekday','weekdayzh']).count()['weekcount'].reset_index(name="星期统计量")
df_hour = gp_by_hour.reset_index(name="小时统计量")
df_day = gp_by_day.reset_index(name="日统计量")
df_sort_average = sort_average.reset_index()
df_sort_total_amounts = sort_total_amounts.reset_index()
df_sort_counts =sort_counts.reset_index()
df_data_group = data['order_id'].value_counts().reset_index(name="菜量种类")
df_data_group.rename(columns={"index":"order_id"},inplace=True)
df_dishes_count = data['dishes_name'].value_counts().reset_index(name="菜量计量")
df_dishes_count.rename(columns={"index":"order_id"},inplace=True)
data['time'] = pd.to_datetime(data['time'],errors="coerce")
data['datetime'] = data['time'].dt.normalize()
data['datetime'] = data['datetime'].dt.strftime("%Y-%m-%d")
filepath = r"D:\Users\84462\Desktop\数据分析工作\餐饮销售数据\餐饮销售数据源1.xlsx"
with pd.ExcelWriter(filepath, engine="openpyxl") as writer:
    # Sheet 1: 原始明细数据（如果量大，方便后续处理）
    data.to_excel(writer, sheet_name="原始数据", index=False)
    # Sheet 2: 按星期计算结果
    df_week.to_excel(writer, sheet_name="按星期统计", index=False)
    
    # Sheet 3: 按小时计算结果
    df_hour.to_excel(writer, sheet_name="按小时统计", index=False)
     # Sheet 4: 按小时计算结果
    df_day.to_excel(writer, sheet_name="按日期统计", index=False)
     # Sheet 5: #哪个订单ID平均消费最贵
    df_sort_average.to_excel(writer, sheet_name="订单ID平均消费最贵", index=False)
    #哪个订单ID吃的钱最多（排序）
    df_sort_total_amounts.to_excel(writer, sheet_name="订单ID吃的钱最多", index=False)
    #订单ID点菜数量TOP10
    df_sort_counts.to_excel(writer, sheet_name="订单ID点菜数量", index=False)
    #订单点菜的种类最多（）
    df_data_group.to_excel(writer, sheet_name="订单点菜的种类最多", index=False)
    #什么菜最受欢迎（对菜名进行频数统计，取最大前10名）
    df_dishes_count.to_excel(writer, sheet_name="什么菜最受欢迎", index=False)
    
print("✅ 文件导出成功！")
```
#导出的excel数据，在powerBI中，进行做日报和月报，方便进行数据分析以及数据汇报。

###模型关系
<img width="1989" height="1218" alt="image" src="https://github.com/user-attachments/assets/fbdefb45-bb9b-4942-b6bc-4de3f85d7c01" />

###日报
<img width="2202" height="1230" alt="image" src="https://github.com/user-attachments/assets/268b6473-224b-475e-bec5-458bd6d10717" />

###月报
<img width="2199" height="1233" alt="image" src="https://github.com/user-attachments/assets/0ac70a2f-ecbf-47d5-ae0d-2033ba2e314b" />

###Power BI 看板配套 DAX 公式全集
1. 核心指标
```python
当月销售额(元) =  SUM('原始数据'[total_amounts])
当月订单总数 = DISTINCTCOUNT('原始数据'[order_id])
当月客单价_按订单 = 
VAR TotalSales = [当月销售额(元)]       
VAR OrderCnt = DISTINCTCOUNT('原始数据'[order_id])  
RETURN
DIVIDE(TotalSales, OrderCnt, 0)
当月达成率 = DIVIDE([当月销售额(元)],[当月目标值],0)
```

2.营业时段（计算列）
```python
营业时段 = 
VAR HourNum = SELECTEDVALUE('原始数据'[hour])
RETURN
IF(
    HourNum >= 10 && HourNum < 17, "午市",
    IF(HourNum >= 17 && HourNum < 23, "晚市",
     "其他时段"
    )
)
```

3. 时段销售额
```python
销售额_午市 = 
CALCULATE([当月销售额(元)],FILTER('原始数据',HOUR('原始数据'[time])>=10&&HOUR('原始数据'[time])<=14))
销售额_晚市 = 
CALCULATE([当月销售额(元)],FILTER('原始数据',HOUR('原始数据'[time])>=17&&HOUR('原始数据'[time])<=22))    
午市占比 = 
DIVIDE(
    [销售额_午市],
    [销售额_午市] + [销售额_晚市],
    0
)         
晚市占比 = 
DIVIDE(
    [销售额_晚市],
    [销售额_午市] + [销售额_晚市],
    0
)
```

4.日期维度（日期表）
```python
销售额_上旬 = CALCULATE([当月销售额(元)],FILTER('原始数据',DAY('原始数据'[day])<=10&&DAY('原始数据'[day])>0))
销售额_中旬 = CALCULATE([当月销售额(元)],FILTER('原始数据',DAY('原始数据'[day])<=20&&DAY('原始数据'[day])>10))
销售额_下旬 = CALCULATE([当月销售额(元)],FILTER('原始数据',DAY('原始数据'[day])>=20)) 
```
5. 点菜量相关
 ```python
总菜量 = SUM('原始数据'[dishes_name])
日均点菜量 = DIVIDE([总菜量],DISTINCTCOUNT('原始数据'[weekdayzh])) 
```
