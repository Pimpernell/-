餐饮店铺销售数据分析
1、首选拿到数据 python进行数据

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
data
data.info()

#频数统计，什么菜最受欢迎（对菜名进行频数统计，取最大前10名）
dishes_count = data['dishes_name'].value_counts()[:10]
#3数据可视化matplotlib
dishes_count.plot(kind="line",color=['r'])
dishes_count.plot(kind="bar",fontsize=16)
for x,y in enumerate(dishes_count):
    print(x,y)
    plt.text(x,y+3,y,ha="center",fontsize=12)
<img width="864" height="869" alt="image" src="https://github.com/user-attachments/assets/9e4fc6f1-e062-42ed-a73a-aacf83138fd6" />

#订单点菜的种类最多（）
data_group = data['order_id'].value_counts()[:10]
data_group.plot(kind="bar",fontsize=16,color=['r','m','b','y','g'])
plt.title('订单点菜的种类top10')
plt.xlabel('订单ID',fontsize=16)
plt.ylabel('点菜种类',fontsize=16)
#8月份餐厅点单点菜种类前10名，平均点菜25个菜品

<img width="957" height="719" alt="image" src="https://github.com/user-attachments/assets/97ab9df2-596c-4262-b3a1-1cab16559520" />


#订单ID点菜数量Top10（分组order_id,counts求和，排序，前十）
data['total_amounts'] = data['counts']*data['amounts'] #统计单道菜消费总额
dataGroup = data[['order_id','counts','amounts','total_amounts']].groupby(by="order_id")
Group_sum = dataGroup.sum() #分组求和
Group_sum
sort_counts = Group_sum.sort_values(by='counts',ascending=False)
sort_counts['counts'][:10].plot(kind="bar",fontsize=16)
plt.title('订单ID点菜数量TOP10')
plt.xlabel('订单ID')
plt.ylabel('点菜数量')

<img width="912" height="732" alt="image" src="https://github.com/user-attachments/assets/9dc12980-3a58-4578-b4e9-b0ee1c85188b" />

#哪个订单ID吃的钱最多（排序）
sort_total_amounts = Group_sum.sort_values(by="total_amounts",ascending=False)
sort_total_amounts['total_amounts'][:10].plot(kind="bar")
plt.xlabel('订单ID')
plt.ylabel('消费金额')
plt.title('消费金额前10')

<img width="909" height="717" alt="image" src="https://github.com/user-attachments/assets/c663c69e-3004-480e-9086-dea551d08f97" />


#哪个订单ID平均消费最贵
Group_sum['average'] = Group_sum['total_amounts']/Group_sum['counts']
sort_average = Group_sum.sort_values(by="average",ascending=False)
sort_average['average'][:10].plot(kind="bar")
plt.title('订单消费单价前10')
plt.xlabel('订单ID')
plt.ylabel('消费单价')
sort_average

<img width="606" height="509" alt="image" src="https://github.com/user-attachments/assets/52029380-904b-423e-9374-59685f047bcf" />
<img width="891" height="711" alt="image" src="https://github.com/user-attachments/assets/a3b0ecb6-2df9-4e08-9812-c9a37fba81b0" />


#一天当中什么时间段，点菜量比较集中（hour）
data['hourcount'] = 1
data['time'] = pd.to_datetime(data['place_order_time'])
data['hour'] = data['time'].map(lambda x:x.hour)
gp_by_hour = data.groupby(by='hour').count()['hourcount']
gp_by_hour.plot(kind='bar')
plt.title('点菜数量与小时的关系图')
plt.xlabel('8月份小时')
plt.ylabel('点菜数量')

<img width="906" height="702" alt="image" src="https://github.com/user-attachments/assets/35a5739b-346a-4503-b021-ed468ddc48e6" />

#哪一天订餐数量最多
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

<img width="918" height="695" alt="image" src="https://github.com/user-attachments/assets/fa8480ab-2218-46ca-a7a0-ec2cb9f2094f" />

#查看星期几人数最多，订餐数最多，映射数据到星期
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

<img width="873" height="729" alt="image" src="https://github.com/user-attachments/assets/42c4e126-552d-4bc8-9168-f34727ca97d1" />

plt.figure(figsize=(10,4))
plt.subplot(1,2,1)
plt.plot(gp_by_weekday,color="red",marker="o",label="数据A")
plt.title('')
plt.legend()
plt.subplot(1,2,2)
gp_by_hour.plot(kind="bar")

<img width="1328" height="567" alt="image" src="https://github.com/user-attachments/assets/32bbabea-292a-465a-ab88-759558caba2a" />

# 讲分好数据进行单独的工作表存储，合并到一个excel中进行导出。
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





