# DADS5001 พลังงานแห่งอนาคต (Future Energy)

#Question
1. ความสัมพันธ์ของ พลังงาน กับ CO2
2. สถานการณ์ตลาดซื้อขายพลังงานสะอาด
3. คำนวณ ความคุ้มค่าของการลงทุนใน Solar Rooftop อย่างไร

# ความสัมพันธ์ของ พลังงาน กับ CO2 

![image](https://user-images.githubusercontent.com/119307197/226912477-852bd0e9-5f4f-400f-8bff-e459305d054b.png)

รูปที่ 1 อัตราการเปลี่ยนแปลงของปริมาณการผลิตพลังงานจาก Natural Gas กับ Renewable 

แหล่งที่มา: กระทรวงพลังงาน

จากกราฟจะเห็นได้ว่า ปริมาณการผลิตพลังงานจาก Renewable มีแนวโน้มที่เพิ่มขึ้นสูงและเป็นอัตราการเพิ่มขึ้นที่สูงกว่าของ Natural Gas เนื่องจาก เส้น Renewable มีการแกว่งตัวสูงอยู่ในโซนบวกเป็นสำคัญ และส่วนใหญ่เส้น Renewable ก็อยู่เหนือเส้น Natural Gas

ขณะเดียวกันปริมาณการผลิตพลังงานจาก Natural Gas ก็มีการเพิ่มขึ้นในอัตราที่ต่ำ เห็นได้จาก เส้น Natural Gas ที่ไม่ได้แกว่งตัวมาก 

นอกจากนี้การจะคาดการณ์ปริมาณการผลิตพลังงานจาก Renewable ทำได้ยากกว่าของ Natural Gas เนื่องจาก เส้น Renewable มีการแกว่งตัวที่สูงกว่าของเส้น Natural Gas

```Python
#dataset 01
data_01_01 = pd.read_csv('01_CO2_Emission_Sector.csv') #by กระทรวงพลังงาน
data_01_01 = data_01_01.fillna(0)
data_01_02 = pd.read_csv('01_CO2_Emission_kWh.csv', index_col = 'Year', parse_dates = True) #by กระทรวงพลังงาน

display(data_01_01)
data_01_01.Year = data_01_01.Year.astype('object')
#data_01_01.info() 
display(data_01_02)
#data_01_02.info()

%matplotlib inline
df_res = data_01_01.pivot_table(index = ['Year'], values = ['Oil', 'Natural Gas', 'Coal/Lignite'])
display(df_res)

plt.figure(figsize=(15,5),dpi=150)
ax = plt.gca()
df_res.iloc[::5, :].plot( kind='bar', ax=ax,
         title='CO2 By Fuel', ylabel='1000 Tons')

```
![image](https://user-images.githubusercontent.com/119307197/226206181-6304743f-6ce2-4e21-854c-c7c627cab9c6.png)

รูปที่ 1 ปริมาณ CO2 ที่เกิดขึ้น จำแนกตามแหล่งเชื้อเพลิง (Fuel)

แหล่งที่มา: กระทรวงพลังงาน
	
จากกราฟของปริมาณ CO2 ที่เกิดขึ้น จำแนกตามแหล่งเชื้อเพลิง (Fuel) อันได้แก่ Coal/Lignite, Natural Gas, Oil ในปี 1997 – 2022 พบว่า CO2 ที่เกิดจาก Natural Gas และ Coal/Lignite มีแนวโน้มที่เพิ่มสูงขึ้น ขณะที่ CO2 ที่เกิดจาก Oil มีแนวโน้มที่สูงขึ้นจนถึงปี 1997 หลังจากนั้นก็มีแนวโน้มเพิ่มขึ้น-ลดลงอยู่ประมาณ 20,000 - 25,000 KT แต่ถึงอย่างนั้น Oil ก็เป็นแหล่งเชื้อเพลิงที่ทำให้เกิด CO2 มากที่สุด

```Python
data_01_01['Total_Sector'] = data_01_01.drop('Total', axis =1).apply(lambda q : q['Oil'] + q['Coal/Lignite'] + q['Natural Gas'], axis = 1)
df_sect = data_01_01.loc[: , ['Year','Sector', 'Total_Sector']]
df_sect['Sector'].unique()
df_sect['Sector'] = df_sect.apply(lambda x: x['Sector'].strip(), axis = 1)
df_sect["Power Generation"] = df_sect.apply(lambda a: a["Total_Sector"] if a["Sector"] == "Power Generation" else 0, axis = 1)
df_sect["Transport"] =df_sect.apply(lambda a: a["Total_Sector"] if a["Sector"] == "Transport" else 0, axis = 1)
df_sect["Industry"] = df_sect.apply(lambda a: a["Total_Sector"] if a["Sector"] == "Industry" else 0, axis = 1)
df_sect["Other"] = df_sect.apply(lambda a: a["Total_Sector"] if a["Sector"] == "Other" else 0, axis = 1)
df_sect = df_sect.drop(['Sector', 'Total_Sector'], axis = 1)
df_sect = df_sect.pivot_table(index = ['Year'], values = ['Power Generation', 'Transport', 'Industry', 'Other'], aggfunc = 'sum')
display(df_sect)

plt.figure(figsize=(15,5),dpi=150)
ax = plt.gca()
df_sect.iloc[::5, :].plot( kind='bar', ax=ax,
         title='CO2 By Activity', ylabel='1000 Tons' )

```
![image](https://user-images.githubusercontent.com/119307197/226206263-fc2f990c-5558-4f95-a6e3-18c16e8d3150.png)

รูปที่ 2 ปริมาณ CO2 ที่เกิดขึ้น จำแนกตามประเภทกิจกรรม

แหล่งที่มา: กระทรวงพลังงาน

จากกราฟของปริมาณ CO2 ที่เกิดขึ้น จำแนกตามประเภทกิจกรรม อันได้แก่ Power Generation, Industry, Transport, Other ในปี 1997 – 2022 พบว่า CO2 ที่เกิดจาก Power Generation, Industry, Transport มีแนวโน้มที่เพิ่มสูงขึ้น โดย Power Generation มีแนวโน้มที่เพิ่มสูงขึ้นจนถึงปี 2012 หลังจากนั้นมีแนวโน้มที่ทรงตัวจนลดลงมาในปี 2022 แต่อย่างไร Power Generation ก็ยังคงเป็นกิจกรรมที่ทำให้เกิด CO2 สูงที่สุด

```Python
sns.set(rc={'figure.dpi':150})
sns.relplot( kind='line',
             data=data_01_02, 
             x='Year', y='CO2', 
             height=5, aspect=2.5,
             alpha=0.6,)

ax = plt.gca()
data_01_02.plot( kind='line', ax=ax,
         title='kg-CO2/Kwh', ylabel='')

```
![image](https://user-images.githubusercontent.com/119307197/226206310-4903bedf-0230-4a7a-9eaa-a54655703ac3.png)

รูปที่ 3 สัดส่วน CO2 ต่อ หน่วยไฟฟ้า(kwh)

แหล่งที่มา: กระทรวงพลังงาน

จากกราฟของสัดส่วน CO2 ต่อ หน่วยไฟฟ้า(kwh) ในปี 1996 – 2022 พบว่า สัดส่วน CO2 ต่อ หน่วยไฟฟ้า (kwh) มีแนวโน้มที่ลดลง กล่าวได้ว่า การผลิตพลังงานไฟฟ้า 1 หน่วย ปล่อย CO2 ได้น้อยลง แสดงให้เห็นถึงประสิทธิภาพการผลิตพลังงานไฟฟ้าที่สูงขึ้นในด้านการลดมลพิษจาก CO2  

```Python
df_res = df_res.reset_index()
df_sect = df_sect.reset_index()
df_co2 = data_01_02.reset_index()
#display(df_res, df_sect, df_co2)

df_merg_co2 = pd.merge(df_res, df_sect, left_on = 'Year', right_on = 'Year').reset_index()
df_merg_co2 = df_merg_co2.iloc[10:,].reset_index().drop('index', axis= 1).drop('level_0', axis = 1)
df_merg_co2['CO2'] = df_co2.CO2
display(df_merg_co2)

#feature scaling
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
column_co2 = ['Coal/Lignite', 'Natural Gas', 'Oil', 'Power Generation', 'Transport', 'Industry', 'Other']
df_merg_co2[column_co2] = scaler.fit_transform(df_merg_co2[column_co2])
display(df_merg_co2)

corr_CO2 = df_merg_co2.corr().round(2)
sns.heatmap(corr_CO2, annot = True) 

```
![image](https://user-images.githubusercontent.com/119307197/226932426-346bf556-3b59-4c50-bba3-fcf5130b0e46.png)

รูปที่ 4 Correlation ของปริมาณพลังงานกับปริมาณ CO2 ที่จำแนกตามประเภทกิจกรรม

แหล่งที่มา: กระทรวงพลังงาน

ตาราง Heatmap ที่แสดงค่า Correlation ความสัมพันธ์ของคู่ตัวแปร โดยเป็นค่าที่จะบ่งชี้ถึงทิศทางความสัมพันธ์และระดับความสัมพันธ์ ซึ่งค่า Correlation มีค่าอยู่ระหว่าง -1 ถึง 1 นั้นมีความหมายดังนี้
-	ค่าเข้าใกล้ 0 คู่ตัวแปรมีแนวโน้มที่จะไม่มีความสัมพันธ์ต่อกัน
-	ค่าเข้าใกล้ 1 คู่ตัวแปรมีแนวโน้มที่จะมีความสัมพันธ์ต่อกันสูง
-	ค่าบวก คู่ตัวแปรมีแนวโน้มที่จะมีความสัมพันธ์กันในทิศทางเดียวกัน
-	ค่าลบ คู่ตัวแปรมีแนวโน้มที่จะมีความสัมพันธ์กันในทิศทางตรงกันข้าม

จากค่า Correlation จะเห็นได้ว่า ปริมาณพลังงานกับปริมาณ CO2 ที่เกิดจาก Industry, Power Generation, Transport มีความสัมพันธ์กันสูงและทิศทางเดียวกันด้วย จากค่า Correlation ที่ 0.98, 0.96, 094 ตามลำดับ นอกจากนี้ ปริมาณ CO2 ที่เกิดจาก Industry, Power Generation, Transport ต่างมีความสัมพันธ์กันเองในทิศทางเดียวกันอีกเช่นกัน 


# สถานการณ์ของตลาดพลังงานสะอาด 

```Python
#dataset2
data_02_01 = pd.read_csv('02_Province_Oil.csv')
data_02_01['ภาค'].fillna(method='ffill', inplace = True)
data_02_01['จังหวัด'].fillna(method='ffill', inplace = True)
data_02_01.fillna(0, inplace= True) 

data_02_01 = pd.read_csv('02_Province_elec.csv')
data_02_01['Region'].fillna(method='ffill', inplace = True)
data_02_01['จังหวัด'].fillna(method='ffill', inplace = True)
data_02_01['จำนวนผู้ใช้และการจำหน่าย'].fillna(method='ffill', inplace = True)
data_02_01.dropna(subset = ['ประเภทผู้ใช้'], inplace = True)
data_02_01.fillna(0, inplace= True)

p0 = data_02_01[data_02_01['Region'].str.match('ภาค', case = False)]
p0 = p0[p0['จังหวัด'].str.match('ภาค', case = False)]
p0 = p0[p0['ประเภทผู้ใช้'].str.match('จำนวนผู้ใช้ไฟฟ้า', case = False)]
p0.drop(['จังหวัด', 'จำนวนผู้ใช้และการจำหน่าย', 'ประเภทผู้ใช้'], axis = 1, inplace = True)
#display(p0)

#p0.columns = p0.columns.str.replace('ภาค', 'Region')

p0 = p0.groupby('Region').sum().T
p0.columns = p0.columns.str.replace('ภาคกลาง', 'Central')
p0.columns = p0.columns.str.replace('ภาคตะวันออกเฉียงเหนือ', 'Northeast')
p0.columns = p0.columns.str.replace('ภาคเหนือ', 'North')
p0.columns = p0.columns.str.replace('ภาคใต้', 'South')
p0 = pd.DataFrame(p0)
#p0 = p0.set_index(['Region'])
display(p0)

reg_name = ['Central', 'Northeast',	'North', 'South']
reg_volume = np.array([10.248225, 7.029429, 4.455876, 3.361232])
df_reg = pd.DataFrame(reg_name, columns= ['Region'])
df_reg['volume'] = reg_volume
df_reg.sort_values('volume', inplace = True)
#display(df_reg)
plt.barh(df_reg['Region'], df_reg['volume'])
plt.legend('')          # Add legend to the graph
plt.xlabel("Number of House (Million)")                  
plt.title("Electricity Consumer in 2564 by Region")
```
![image](https://user-images.githubusercontent.com/119307197/226206439-f8c74eca-7ca8-4575-9cd5-ac22622045ac.png)

รูปที่ 5 จำนวนผู้ใช้ไฟฟ้าในปี 2564

แหล่งที่มา: สำนักงานสถิติแห่งชาติ

จากกราฟ จำนวนผู้ใช้ไฟฟ้าในปี 2564 แสดงให้เห็นได้ว่า ผู้ใช้ไฟฟ้าส่วนใหญ่พักอาศัยอยู่ที่ภาคกลาง เป็นจำนวน 10,248,225 ราย คิดเป็นร้อยละ 41 ของทั้งประเทศ รองลงมาจะเป็นภาคตะวันออกเฉียงเหนือเป็นจำนวน 7,029,429 ราย คิดเป็นร้อยละ 28 ภาคเหนือเป็นจำนวน 4,455,876 ราย คิดเป็นร้อยละ 18 ภาคใต้เป็นคิดเป็นจำนวน 3,361,232 ราย คิดเป็นร้อยละ 13 

```Python
p1 = data_02_01[data_02_01['Region'].str.match('ภาค', case = False)]
p1 = p1[p1['ประเภทผู้ใช้'].str.match('จำนวนผู้ใช้ไฟฟ้า', case = False)]
p1 = p1[p1['จังหวัด'].isin(['ชลบุรี','ฉะเชิงเทรา', 'ระยอง'])]
p1.rename(columns = {"จังหวัด":"Year"}, inplace = True)
p1.drop(['Region', 'จำนวนผู้ใช้และการจำหน่าย', 'ประเภทผู้ใช้'], axis = 1, inplace = True)

p1 = p1.set_index('Year').T
p1.rename(columns = {'ชลบุรี':'Chonburi', 'ฉะเชิงเทรา':'Chachoengsao', 'ระยอง':'Rayong'}, inplace = True)
display(p1)

fig, ax = plt.subplots(1,3, sharex = True, sharey = True, figsize = (10,2)) #grap2

Year = []
for i in range(2555,2565):
    Year.append(str(i))

p1.loc[Year, 'Chonburi'].plot(kind = 'bar', color = 'gold', ax = ax[0], title = 'Chonburi', ylabel='Number of House')
p1.loc[Year, 'Chachoengsao'].plot(kind = 'bar', color = 'blue', ax = ax[1], title = 'Chachoengsao')
p1.loc[Year, 'Rayong'].plot(kind = 'bar', color = 'green', ax = ax[2], title = 'Rayong')

```
![image](https://user-images.githubusercontent.com/119307197/226206459-bc96139d-8196-4909-a4af-0eb272790b07.png)

รูปที่ 6 จำนวนผู้ใช้ไฟฟ้าของจังหวัด ชลบุรี ฉะเชิงเทรา และระยอง

แหล่งที่มา: สำนักงานสถิติแห่งชาติ

โดยพื้นที่ที่เราจะทำการศึกษาเรื่อง พลังงานสะอาด ได้แก่จังหวัดชลบุรี ฉะเชิงเทรา และระยอง เนื่องจากเป็นพื้นสำคัญทางเศรษฐกิจอันเป็นพื้นที่ในโครงการเศรษฐกิจพิเศษภาคตะวันออก (EEC) ซึ่งจังหวัดชลบุรี ฉะเชิงเทรา และระยอง ต่างมีแนวโน้มของจำนวนผู้ใช้ไฟฟ้าที่เพิ่มสูงขึ้น โดยเฉพาะอย่างยิ่งจังหวัดชลบุรี

```Python
#dataset 03
data_03_01 = pd.read_csv('03_GreenEnergy_01.csv')
data_03_02 = pd.read_csv('03_GreenEnergy_02.csv')
#data_03_01.info()
#data_03_02.info()
data_03_01.คู่ค้าทางธุรกิจ = data_03_01.คู่ค้าทางธุรกิจ.astype('str')
data_03_01.บัญชีผู้ผลิตไฟฟ้า = data_03_01.บัญชีผู้ผลิตไฟฟ้า.astype('str')
data_03_01.บัญชีผู้ใช้ไฟฟ้า = data_03_01.บัญชีผู้ใช้ไฟฟ้า.astype('object')
data_03_01.VENDOR = data_03_01.VENDOR.astype('str')
data_03_01.year = data_03_01.year.astype('str')
#display(data_03_01)

pvt = data_03_01.pivot_table(index = ['year'], columns = ['Code_Fuel_Type'], values = ['VENDOR'], aggfunc = 'count',fill_value = 0)
#display(pvt)
prod = pvt['VENDOR']
prod = prod[['bg','bm', 'gb', 'sl']].cumsum()
display(prod)
#prod.plot.bar()

plt.figure(figsize=(15,5),dpi=150)
reg_name = ['bg', 'bm',	'gb', 'sl']
reg_volume = np.array([7, 6, 3, 462])
df_reg = pd.DataFrame(reg_name, columns= ['Region'])
df_reg['volume'] = reg_volume
df_reg.sort_values('volume', inplace = True)
#display(df_reg)
plt.barh(df_reg['Region'], df_reg['volume'])
plt.title('Green Energy Producer')
plt.xlabel("Number of Producer")                 
plt.ylabel("")

```
![image](https://user-images.githubusercontent.com/119307197/226206520-d322bc95-9586-4c2b-a7f2-3a04e78a705d.png)

รูปที่ 7 จำนวนผู้ผลิตพลังงานสะอาดในปี 2564

แหล่งที่มา: การไฟฟ้าส่วนภูมิภาค 

ในพื้นที่จังหวัดดังกล่าว ได้มีการผลิตพลังงานสะอาดจากแหล่งพลังงาน 4 ประเภท ได้แก่ 
-	Solar (sl) เป็นพลังงานจากแสงอาทิตย์ มาใช้ในการผลิตกระแสไฟฟ้าผ่านสิ่งประดิษฐ์ อย่าง Solar Cells
-	Biogas (bg) เป็นพลังงานที่ได้จากการแปรรูปวัสดุเหลือใช้จากการเกษตร เช่น หญ้าเนเปียร์ ข้าวโพด ชานอ้อย ฟางข้าว เป็นต้น
-	Biomass (bm) เป็นพลังงานที่ได้จากการแปรรูปวัสดุเหลือใช้จากภาคปศุสัตว์ เช่น มูลสัตว์ 
-	Garbage (gb) เป็นพลังงานที่ได้จากขยะ ขยะชุมชน และขยะมูลฝอยจากการอุปโภค กระบวนการผลิต และกิจกรรมต่างๆ ทั้งในภาคครัวเรือนและภาคอุตสาหกรรม

จากกราฟจำนวนผู้ผลิตพลังงานสะอาดในปี 2564 จะเห็นได้ว่า จำนวนผู้ผลิตพลังงานสะอาดจาก Solar มีจำนวนมากที่สุด โดยที่ผู้ผลิตพลังงานสะอาดจาก Solar คิดเป็นร้อยละ 97 ขณะที่จำนวนผู้ผลิตพลังงานสะอาดจาก Biogas Biomass และ Garbage รวมกันมีเพียงร้อยละ 3 

```Python
pvt = data_03_01.pivot_table(index = ['year'], columns = ['Code_Fuel_Type'], values = ['ปริมาณเสนอขาย(MW)'], aggfunc = 'sum',fill_value = 0)
volume_mw = pvt['ปริมาณเสนอขาย(MW)']
volume_mw = volume_mw[['bg','bm', 'gb', 'sl']].cumsum()
display(volume_mw)

plt.figure(figsize=(15,5),dpi=150)
ax = plt.gca()
volume_mw.plot(kind = 'bar', stacked =True, ax=ax,
                        title='Volume of Green Energy', ylabel='(MW)')

```
![image](https://user-images.githubusercontent.com/119307197/226206530-d8bd545d-3c82-4735-8084-59b39eb6efe5.png)

รูปที่ 8 ปริมาณการรับซื้อพลังงานสะอาดจากผู้ผลิตพลังงาน

แหล่งที่มา: การไฟฟ้าส่วนภูมิภาค 

จากกราฟปริมาณการรับซื้อพลังงานสะอาดจากผู้ผลิตพลังงาน ตั้งแต่ปี 2550 ถึง 2564 เห็นได้ว่า การปริมาณรับซื้อพลังงานสะอาดมีแนวโน้มที่เพิ่มสูงขึ้น โดยเฉพาะตั้งแต่ในปี 2562 ที่เริ่มมีการรับซื้อพลังงานสะอาดจากผู้ผลิตพลังงาน Solar ซึ่งก่อนหน้านี้มีพลังงานสะอาดจาก Biomass เป็นสำคัญ 

เมื่อพิจารณาที่สัดส่วนปริมาณการรับซื้อพลังงานสะอาด ในปี 2564 เห็นได้ว่า พลังงานสะอาดจาก Biogas Biomass และ Garbage คิดรวมกันเป็นสัดส่วนร้อยละ 43 ของปริมาณการรับซื้อพลังงานสะอาด ซึ่งเป็นสัดส่วนที่สูง แม้จะมีจำนวนผู้ผลิตพลังงานอยู่น้อยรายก็ตาม

```Python
display(data_03_02)
sns.set(rc={'figure.dpi':250})
sns.relplot( kind='scatter',          # default:'scatter'
             data=data_03_02,            # Data to plot
             x='Total_Unit', y='Price', # Positions of x and y axes
             hue='Energy',            # Grouping variable that will produce elements with different colors
             height=5, aspect=2.5,    # Figure size must be set in the figure-level function
             alpha=0.6,
            ) 
```
![image](https://user-images.githubusercontent.com/119307197/226206545-9368a682-e398-4806-a54e-a9148ab59943.png)
 
รูปที่ 9 ความสัมพันธ์ของค่าไฟฟ้ากับหน่วยพลังงาน จำแนกตามแหล่งการผลิตพลังงานสะอาด

แหล่งที่มา: การไฟฟ้าส่วนภูมิภาค 

จากกราฟ Scatter plot ของ ราคาขาย (Price) กับ ปริมาณของการผลิตพลังงานของผู้ผลิต (Unit) พบว่า แต่ละพลังงานมีความสัมพันธ์เป็นลักษณะเส้นตรงลาดขึ้น (Linear) ซึ่งสามารถนำไปประยุกต์ขาดการณ์รายได้และกำไรจากการผลิตพลังงานได้ 

ซึ่งเราสามารถจำแนกผู้ผลิตพลังงานรายเล็กและรายใหญ่เบื้องต้นได้ ผ่านการดูปริมาณของการผลิตพลังงาน โดยที่ทางด้านซ้ายจะเป็นรายเล็ก เนื่องจากมีปริมาณการผลิตพลังงานน้อยกว่า ขณะที่ทางด้านขวาจะเป็นรายใหญ่ เนื่องจากมีปริมาณผลิตพลังงานที่สูงกว่า 

การที่พลังงานสะอาดจาก Biogas Biomass และ Garbage แม้จะมีสัดส่วนจำนวนผู้ผลิตที่น้อย แต่สามารถมีกำลังการผลิตที่สูงได้นั้นเป็นเพราะผู้ผลิตพลังงานจาก Biomass และ Garbage เป็นผู้ผลิตพลังงานรายใหญ่มีกำลังการผลิตที่สูง เห็นได้จาก จุด plot ที่เรียงกันอยู่ทางด้านขวาของรูป ซึ่งหากนักลงทุนรายใหญ่มีความต้องการที่จะลงทุนในพลังงานสะอาด ควรที่ลงทุนในพลังงานที่มาจาก Garbage เนื่องจากเส้นของ Garbage มีความชัน (Slope) ที่สูงกว่าเส้นของ Biomass แสดงถึงผลตอบแทนที่สูงกว่า ภายใต้ข้อจำกัดที่ว่าต้นทุนในการผลิตพลังงานต่อหน่วยนั้นเท่ากัน

# คำนวณ ความคุ้มค่าในการลงทุน Solar Rooftop อย่างไร

การศึกษาความคุ้มค่าในการลงทุน Solar Rooftop ครั้งนี้ เป็นเพียงกรณีศึกษาผ่านผู้ใช้ไฟฟ้ารายหนึ่งเท่านั้น ซึ่งอาจมิได้นำไปอนุมานแทนกลุ่มประชากรผู้ใช้ไฟฟ้าได้ เป็นเพียงการอธิบายให้เห็นถึงแนวทางการคำนวญความคุ้มค่าในการลงทุนเท่านั้น

```Python
#dataset 04

#กราฟแท่ง ค่าไฟไม่รวมFt กับค่า Ft
data_04_01 = pd.read_csv('04_my_home_01.csv')
data_04_01.รหัสอัตรา = data_04_01.รหัสอัตรา.astype('str')
data_04_01.วันที่อ่าน = pd.to_datetime(data_04_01.วันที่อ่าน)
data_04_01.rename(columns = {'วันที่อ่าน': 'Date'}, inplace= True)
data_04_01.rename(columns = {'KWH รวม': 'KWH'}, inplace= True)
data_04_01.rename(columns = {'ค่า FT': 'Ft_Price'}, inplace= True)
data_04_01.rename(columns = {'FT / หน่วย': 'Ft_Rate'}, inplace= True)
data_04_01.rename(columns = {'ค่าไฟฟ้ารวมภาษี': 'Price_Bt'}, inplace= True)
data_04_01.rename(columns = {'เฉลี่ย/หน่วย': 'Price/Unit'}, inplace= True)
data_04_01.set_index('Date', inplace = True)
data_04_01.info()
display(data_04_01)

data_04_01[['Price/Unit', 'Ft_Rate']].loc['2016':'2023'].plot(subplots= True, grid = True)
#plt.ylabel('Bath/Unit');
plt.show()
```
![image](https://user-images.githubusercontent.com/119307197/226206646-5027daa5-bb68-4814-a167-ad2ff23143a5.png)

รูปที่ 10 อัตราค่าไฟฟ้าต่อหน่วยไฟฟ้ากับอัตราค่า Ft ตั้งแต่ปี 2016 - 2023

แหล่งที่มา: การไฟฟ้าส่วนภูมิภาค 

เพื่อเป็นกรณีศึกษา เราได้นำสถิติการใช้ไฟฟ้าของบ้านพักที่อยู่อาศัยแห่งหนึ่ง มาศึกษาหาความคุ้มค่าด้านการลงทุนติดตั้งใน Solar Rooftop โดยจากกราฟ อัตราค่าไฟฟ้าต่อหน่วยไฟฟ้ากับอัตราค่า Ft ตั้งแต่ปี 2016 ถึง 2021 ที่ อัตราค่า Ft มีค่าเป็นลบ เห็นได้ว่า เส้นอัตราค่าไฟฟ้าต่อหน่วยไฟฟ้ากับเส้นอัตราค่า Ft เคลื่อนไหวไม่ได้สอดล้องกัน โดยที่อัตราค่า Ft จะมีการปรับทุกๆ 4 เดือน

```Python

data_04_01[['Price/Unit', 'Ft_Rate']].loc['2022':'2023'].plot(subplots= True, grid = True)
#plt.ylabel('Bath/Unit');
plt.show()

```
![image](https://user-images.githubusercontent.com/119307197/226206835-0193e5fb-f292-4b4f-9627-1b56fb7004f4.png)

รูปที่ 11 อัตราค่าไฟฟ้าต่อหน่วยไฟฟ้ากับอัตราค่า Ft ตั้งแต่ปี 2022 - 2023

แหล่งที่มา: การไฟฟ้าส่วนภูมิภาค

แต่ในช่วงปี 2022 ที่อัตราค่า Ft มีค่าเป็นบวกและปรับสูงขึ้น เห็นได้ว่า เส้นอัตราค่าไฟฟ้าต่อหน่วยไฟฟ้ากับอัตราค่า Ft มีการเคลื่อนไหวในแนวโน้มที่ไปในทิศทางเดียวกัน กล่าวคือ หากอัตรา Ft ปรับค่าสูงขึ้น ผู้ใช้ไฟฟ้ารายนี้มีแนวโน้มที่จะจ่ายค่าไฟฟ้าสูงขึ้น ฉะนั้นการเข้ามาลงทุนติดตั้ง Solar Rooftop จึงอาจเป็นแนวทางในการแก้ปัญหาได้

```Python
#Investment #ความคุ้มค่าด้านการลงทุน NPV IRR

#display(data_04_01['Price/Unit'][84:96]) 
power = 5
use = round(data_04_01['KWH'][84:96].mean()/30/4.2/0.7,2) 
price_h = round(data_04_01['Price/Unit'][84:96].mean(),2) #
price_s = 2.20
y0 = -200000
yn = (use*4.2*365*price_h)+((power-use)*4.2*365*price_s)
age_s = 20

cashflows = [y0]

for i in range(1,(age_s)+1):
    cashflows.append(yn)
#print(cashflows)
rate = 0.05

npv_sl = npf.npv(rate, cashflows)
irr_sl = npf.irr(cashflows)

def dpp(rate, cash_flows=list()):
    #from pandas import Series, DataFrame
    cf_df = pd.DataFrame(cash_flows, columns=['UndiscountedCashFlows'])
    cf_df.index.name = 'Year'
    cf_df['DiscountedCashFlows'] = npf.pv(rate=rate, pmt=0, nper=cf_df.index, fv=-cf_df['UndiscountedCashFlows'])
    cf_df['CumulativeDiscountedCashFlows'] = np.cumsum(cf_df['DiscountedCashFlows'])
    final_full_year = cf_df[cf_df.CumulativeDiscountedCashFlows < 0].index.values.max()
    fractional_yr = -cf_df.CumulativeDiscountedCashFlows[final_full_year ]/cf_df.DiscountedCashFlows[final_full_year + 1]
    payback_period = final_full_year + fractional_yr
    return payback_period
dpp_sl = dpp(rate, cashflows)

#print(npv_sl, irr_sl, dpp_sl)

from tabulate import tabulate

head = ['Parameter', 'Quantity', 'Unit']
data = [('Consumption', use, 'Kw/Day'),
        ('Capacity', power, 'Kw/Day'),
        ('Elec Price of House', price_h, 'Baht/KwH'),
        ('Elec Price of Solar', price_s, 'Baht/KwH'),
        ('Age of Solar Panel', age_s, 'Year'),
        ('', '', ''),
        ('Cost', cashflows[0], 'Baht'),
        ('Benefit', round(cashflows[1],2), 'Baht/Year'),
        ('Inflation Rate', rate*100, '%'),
        ('', '', ''),
        ('NPV', round(npv_sl, 2), 'Baht'),
        ('IRR', round(irr_sl*100, 2), '%'),
        ('Payback Period', round(dpp_sl, 2), 'Year')]
print(tabulate(data, head, tablefmt = 'psql', floatfmt = ',.0f', numalign = 'right'))
```	

![image](https://user-images.githubusercontent.com/119307197/226206759-6413c68d-ef1b-4fd7-90d9-87f1d7927501.png)

รูปที่่ 12 ตัวชี้วัดทางการเงิน

ข้อมูลที่จำเป็นต่อประเมินความคุ้มค่าในการลงทุน Solar Rooftop มีรายละเอียดดังนี้ 
1. ค่าหน่วยการใช้ไฟฟ้าต่อวันในช่วงที่มีแสงอาทิตย์ เนื่องจาก Solar Rooftop จะผลิตพลังงานไฟฟ้าได้เฉพาะในช่วงที่มีแสงอาทิตย์เท่านั้น

ค่าหน่วยการใช้ไฟฟ้าต่อวันในช่วงที่มีแสงอาทิตย์ = ค่าเฉลี่ยหน่วยการใช้ไฟฟ้ารายเดือน / จำนวนวันใน 1 เดือน * ค่า PSH
	
หมายเหตุ ค่า PSH: Peak Sun Hour เป้นค่าเฉลี่ยแสงแดดต่อวัน ซึ่งในแต่ละช่วงเวลาจะมีความเข้มของแสงอาทิตย์ที่ไม่เท่ากัน จะมีค่าอยู่ที่ประมาณ 4.2 ชั่วโมงต่อวัน

โดยที่ผู้ใช้ไฟฟ้ารายนี้ มีค่าหน่วยการใช้ไฟฟ้าต่อวันในช่วงที่มีแสงอาทิตย์เป็น 4.15 kw/day 

2. ขนาดกำลังการผลิตของ Solar Rooftop ซึ่งควรเลือกให้มากกว่าค่าหน่วยการใช้ไฟฟ้าต่อวันในช่วงที่มีแสงอาทิตย์ ซึ่งเราได้เลือกมาที่กำลังการผลิต 5 kw/day ที่มีราคาของอุปกรณ์รวมค่าบริการติดตั้งอยู่ที่ประมาณ 200,000 บาท ซึ่งอายุของแผง Solar Panel อยู่ที่ประมาณ 20 ปี อันเป็นระยะเวลาในการลงทุนของเรา

3. ค่าเฉลี่ยอัตราค่าไฟฟ้า โดยที่ผู้ใข้ไฟฟ้ารายนี้ มีค่าเฉลี่ยอัตราค่าไฟฟ้าอยู่ที่ 4.48 บาทต่อหน่วย

4. คำนวณผลประโยชน์ที่จะได้ต่อปี ทั้งนี้ผลประโยชน์ในการติดตั้ง Solar Rooftop แบบ Hybrid แบ่งออกเป็น 2 ส่วน ได้แก่ การลดหน่วยการใช้ไฟฟ้าในช่วงเวลากลางวัน และการนำหน่วยที่เหลือจากการผลิตไฟฟ้าไปขาย มีอัตรารับซื้อไฟฟ้าที่ 2.2 บาทต่อหน่วย 

	ผลประโยชน์ = (หน่วยการใช้ไฟฟ้าต่อวันในช่วงที่มีแสงอาทิตย์*ค่าเฉลี่ยอัตราค่าไฟฟ้า) + (หน่วยเหลือจากการใช้ไฟฟ้าต่อวันในช่วงที่มีแสงอาทิตย์*อัตรารับซื้อไฟฟ้า)
	
ตัวชี้วัดทางการเงินที่เรานำมาใช้ในการวิเคราะห์ความคุ้มค่าในการลงทุน ได้แก่ NPV, IRR, Payback Period นั้นเอง
-	Net Present Value (NPV) มูลค่าปัจจุบันสุทธิ เป็นผลตอบแทนที่เราคาดว่า จะได้รับจากการลงทุนในโครงการนี้ โดยที่หากค่า NPV เป็นค่าบวก แสดงว่าผลตอบแทนที่ได้จากโครงการนี้มากกว่าเงินลงทุน มีความคุ้มค่าในการลงทุน ขณะที่หากค่า NPV เป็นค่าลบ แสดงว่าผลตอบแทนที่ได้จากโครงการนี้น้อยกว่าเงินลงทุน ไม่มีความคุ้มค่าในการลงทุน
-	Internal Rate of Return (IRR) อัตราผลตอบแทนภายใน เป็นอัตราคิดลดที่ทำให้ NPV มีค่าเป็น 0 โดยที่หากค่า IRR มีค่ามากกว่า ต้นทุนของเงินลงทุนในโครงการนี้ แสดงว่า มีความคุ้มค่าในการลงทุน ขณะที่หากค่า IRR มีค่าน้อยกว่า ต้นทุนของเงินลงทุนในโครงการนี้ แสดงว่า ไม่มีความคุ้มค่าในการลงทุน
-	Payback Period ระยะเวลาการคืนทุน เป็นค่าที่บอกว่ากี่ปีจึงได้เงินลงทุนคืน โดยที่หากค่า Payback Period มีค่าน้อยกว่า ระยะการลงทุนของโครงการนี้ แสดงว่า มีความคุ้มค่าในการลงทุน ขณะที่หากค่า Payback Period มีค่ามากกว่า ระยะการลงทุนของโครงการนี้ แสดงว่า ไม่มีความคุ้มค่าในการลงทุน

จากตารางข้างต้น เห็นได้ว่า
- NPV = 190,917.68 บาท #มีความคุ้มค่าในการลงทุน
- IRR = 14.67 % ซึ่งมีค่ามากกว่า ต้นทุนของเงินลงทุนในโครงการ (Inflation Rate) ที่ 5.00 % #มีความคุ้มค่าในการลงทุน
- Payback Period = 7.87 ปี ซึ่งมีค่าน้อยกว่า ระยะเวลาการลงทุนของโครงการ ที่ 20 ปี #มีความคุ้มค่าในการลงทุน

แสดงให้เห็นได้ว่า การลงทุนในการติดตั้ง Solar Rooftop ของผู้ใช้ไฟฟ้ารายนี้นั้นมีความคุ้มค่าน่าลงทุน ทั้งนี้ข้อมูลนี้เป็นการคำนวณของผู้ใช้ไฟฟ้ารายนี้เท่านั้น ซึ่งอาจมิได้นำไปอนุมาณแทนกลุ่มประชากรผู้ใช้ไฟฟ้าได้ เป็นเพียงแนวทางให้เห็นภาพการลงทุนสำหรับคนที่ต้องการเข้ามาเป็นผู้ผลิตพลังงานสะอาดรายเล็กเท่านั้น


