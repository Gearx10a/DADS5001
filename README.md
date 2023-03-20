# DADS5001 พลังงานแห่งอนาคต (Future Energy)

#Question
1. ความสัมพันธ์ของ พลังงาน กับ CO2
2. สถานการณ์ตลาดซื้อขายพลังงานสะอาด
3. คำนวน ความคุ้มค่าของการลงทุนใน Solar Rooftop อย่างไร

# ความสัมพันธ์ของ พลังงาน กับ CO2 
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
	
จากกราฟของปริมาณ CO2 ที่เกิดขึ้น จำแนกตามแหล่งเชื้อเพลิง (Fuel) อันได้แก่ Coal/Lignite, Natural Gas, Oil ในปี 1997 – 2022 พบว่า CO2 ที่เกิดจาก Natural Gas และ Coal/Lignite มีแนวโน้มที่เพิ่มสูงขึ้น ขณะที่ CO2 ที่เกิดจาก Oil มีแนวโน้มที่สูงขึ้นจนถึงปี 1997 หลังจากนั้นจนถึงปี 2022 เป็นการทรงตัวอยู่ในช่วง 20,000 - 25,000 KT 

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

จากกราฟของปริมาณ CO2 ที่เกิดขึ้น จำแนกตามประเภทกิจกรรม อันได้แก่ Power Generation, Industry, Transport, Other ในปี 1997 – 2022 พบว่า CO2 ที่เกิดจากกิจกรรม Power Generation, Industry, Transport มีแนวโน้มที่เพิ่มสูงขึ้น โดยกิจกรรม Power Generation มีแนวโน้มที่เพิ่มสูงขึ้นจนถึงปี 2012 หลังจากนั้นมีแนวโน้มที่ทรงตัวจนลดลงมาในปี 2022 คาดว่าเป็นผลจากการสนับสนุนให้ใช้พลังงานสะอาดในการผลิตไฟฟ้าแทนการใช้แหล่งเชื้อเพลิงที่ปล่อย CO2 ลดปริมาณการปล่อย CO2 ที่เกิดขึ้นจากกิจกรรม Power Generation อันจะเห็นได้จากรูปที่ 8

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

จากกราฟของสัดส่วน CO2 ต่อ หน่วยไฟฟ้า(kwh) ในปี 1996 – 2022 พบว่า สัดส่วน CO2 ต่อ หน่วยไฟฟ้า (kwh) มีแนวโน้มที่ลดลง กล่าวได้ว่า การผลิตพลังงานไฟฟ้า 1 kwh ปล่อย CO2 ได้น้อยลง ทำให้ประสิทธิภาพการผลิตพลังงานไฟฟ้าสูงขึ้น โดยในอนาคตทางหน่วยงานภาครัฐอย่าง กระทรวงพลังงาน ได้มีการตั้งเป้าหมายที่จะทำให้ สัดส่วน CO2 ต่อ หน่วยไฟฟ้า (kwh) มีค่าเป็น 0 ด้วยการเน้นผลิตพลังงานไฟฟ้าจากพลังงานสะอาดทดแทนการผลิตพลังงานไฟฟ้าจากแหล่งเชื้อเพลิงที่ปล่อย CO2  

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
![image](https://user-images.githubusercontent.com/119307197/226206342-f38274d3-df19-4611-8137-2a448edecb7d.png)

รูปที่ 4 ตาราง Heatmap แสดงค่า Correlation 

แหล่งที่มา: กระทรวงพลังงาน

ตาราง Heatmap ที่แสดงค่า Correlation ความสัมพันธ์ของคู่ตัวแปร โดยเป็นค่าที่จะบ่งชี้ถึงทิศทางความสัมพันธ์และระดับความสัมพันธ์ ซึ่งค่า Correlation มีค่าอยู่ระหว่าง -1 ถึง 1 นั้นมีความหมายดังนี้
-	ค่าเข้าใกล้ 0 คู่ตัวแปรมีแนวโน้มที่จะไม่มีความสัมพันธ์ต่อกัน
-	ค่าเข้าใกล้ 1 คู่ตัวแปรมีแนวโน้มที่จะมีความสัมพันธ์ต่อกันสูง
-	ค่าบวก คู่ตัวแปรมีแนวโน้มที่จะมีความสัมพันธ์กันในทิศทางเดียวกัน
-	ค่าลบ คู่ตัวแปรมีแนวโน้มที่จะมีความสัมพันธ์กันในทิศทางตรงกันข้าม

จากค่า Correlation เห็นได้ว่า ตัวแปร CO2 (สัดส่วนของ CO2 ต่อ 1 kwh) ที่เป็นตัวบ่งชี้ถึงประสิทธิภาพการผลิตพลังงาน มีความสัมพันธ์ในทิศทางตรงกันข้าม (Negative) กันอย่างสูงกับ แหล่งเชื้อเพลิงที่ปล่อย CO2 อย่าง  Coal/Lignite, Natural Gas และประเภทกิจกรรมที่ปล่อย CO2 อย่าง Power Generation, Industry, Transport คาดว่าเป็นผลจากการที่ประชาชนและหน่วยงานที่เกี่ยวข้องตะหนักถึงผลกระทบของ CO2 จากการเพิ่มขึ้นของ CO2 จึงได้มีการเร่งพัฒนาเทคโนโลยีและสนับสนุนการใช้พลังงานสะอาด มีการใช้พลังงานสะอาดกันมากขึ้น ลดการปล่อย CO2 ทำให้ประสิทธิภาพการผลิตพลังงานเพิ่มสูงขึ้น นอกจากนี้ยิ่งเวลาผ่านไป เทคโนโลยียิ่งมีการพัฒนากันมากขึ้น ทำให้ประสิทธิภาพในการผลิตพลังงานเพิ่มสูงขึ้น เห็นได้จากค่า Correlation ของ CO2 กับ Year นั้นเอง

ทั้งนี้การคาดการณ์ดังกล่าว เป็นข้อสรุปเบื้องต้นที่ได้มาจากค่า Correlation ในตาราง Heatmap จำเป็นที่จะด้องมีการศึกษาค้นคว้าต่อไปในอนาคต เพื่อยืนยั่นในสมมุติฐานดังกล่าว


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

จากกราฟ จำนวนผู้ใช้ไฟฟ้าในปี 2564 แสดงให้เห็นได้ว่า ผู้ใช้ไฟฟ้าส่วนใหญ่พักอาศัยอยู่ที่ภาคกลาง เป็นจำนวน 10,248,225 ราย คิดเป็นร้อยละ 41 ของทั้งประเทศ รองลงมาจะเป็นภาคตะวันออกเฉียงเหนือเป็นจำนวน 7,029,429 ราย คิดเป็นร้อยละ 28 ภาคเหนือเป็นจำนวน 4,455,876 ราย คิดเป็นร้อยละ 18 ภาคใต้เป็นคิดเป็นจำนวน 3361232 ราย คิดเป็นร้อยละ 13 ตามลำดับ

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

โดยพื้นที่ที่เราจะทำการศึกษาเรื่อง พลังงานสะอาด ได้แก่จังหวัดชลบุรี ฉะเชิงเทรา และระยอง เนื่องจากเป็นพื้นสำคัญทางเศรษฐกิจอันเป็นพื้นที่ในโครงการเศรษฐกิจพิเศษภาคตะวันออก (EEC) แล้วยังเป็นจังหวัดที่อยู่ในภาคกลางที่มีผู้ใช้ไฟฟ้ามากสุดในประเทศ ซึ่งจังหวัดชลบุรี ฉะเชิงเทรา และระยอง ต่างมีแนวโน้มของจำนวนผู้ใช้ไฟฟ้าที่เพิ่มสูงขึ้น โดยเฉพาะจังหวัดชลบุรี นอกจากนี้จำนวนผู้ใช้ไฟฟ้ารวมทั้ง 3 จังหวัด คิดเป็นร้อยละ 15 ของภาคกลาง ในปี 2564

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

ในพื้นที่จังหวัดดังกล่าว ได้มีการผลิตพลังงานสะอาดจากแหล่งที่มา 4 ประเภท ได้แก่ 
-	Solar เป็นพลังงานจากแสงอาทิตย์ มาใช้ในการผลิตกระแสไฟฟ้าผ่านสิ่งประดิษฐ์ อย่าง Solar Cells
-	Biogas เป็นพลังงานที่ได้จากการแปรรูปวัสดุเหลือใช้จากการเกษตร เช่น หญ้าเนเปียร์ ข้าวโพด ชานอ้อย ฟางข้าว เป็นต้น
-	Biomass เป็นพลังงานที่ได้จากการแปรรูปวัสดุเหลือใช้จากภาคปศุสัตว์ เช่น มูลสัตว์ 
-	Garbage เป็นพลังงานที่ได้จากขยะ ขยะชุมชน และขยะมูลฝอยจากการอุปโภค กระบวนการผลิต และกิจกรรมต่างๆ ทั้งในภาคครัวเรือนและภาคอุตสาหกรรม

จากกราฟจำนวนผู้ผลิตพลังงานสะอาดในปี 2564 จะเห็นได้ว่า จำนวนผู้ผลิตพลังงานสะอาดจาก Solar มีจำนวนมากที่สุด อันเนื่องมาจากความยืดหยุ่นในการลงทุนที่มากกว่า ที่มีทั้งการติดตั้ง Solar Rooftop ในระดับภาคครัวเรือนและระดับภาคอุตสาหกรรม เช่น การทำ Solar Form เป็นต้น ทำให้ผู้ผลิต Solar มีความหลากหลายและมีจำนวนที่มากตามไปด้วย ขณะที่การผลิตพลังงานสะอาดจาก Biogas Biomass และ Garbage จำเป็นที่จะต้องเงินลงทุนที่สูง และข้อจำกัดด้านพื้นที่และสิ่งแวดล้อม ทำให้ผู้ผลิตพลังงานประเภทเหล่านี้ต้องเป็นผู้ผลิตไฟฟ้ารายใหญ่ระดับภาคอุตสาหกรรม เป็นเหตุให้มีผู้ผลิตพลังงานน้อยราย โดยที่ผู้ผลิตพลังงานสะอาดจาก Solar คิดเป็นร้อยละ 97 ขณะที่จำนวนผู้ผลิตพลังงานสะอาดจาก Biogas Biomass และ Garbage รวมกันมีเพียงร้อยละ 3 ซึ่งสามารถสังเกตผู้ผลิตพลังงานรายเล็กและรายใหญ่ ได้จากปริมาณการรับซื้อพลังงาน ที่จะแสดงต่อไปได้จากรูปที่ 9

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

จากกราฟปริมาณการรับซื้อพลังงานสะอาดจากผู้ผลิตพลังงาน ตั้งแต่ปี 2550 ถึง 2564 เห็นได้ว่า การปริมาณการผลิตพลังงานสะอาดมีแนวโน้มที่เพิ่มสูงขึ้น โดยเฉพาะตั้งแต่ในปี 2562 ที่เริ่มมีนโยบายให้รับซื้อพลังงานสะอาดจากผู้ผลิตพลังงานจาก Solar ได้เป็นปีแรก ซึ่งก่อนหน้านี้เป็นพลังงานสะอาดจาก Biomass ของภาคการเกษตรเป็นสำคัญ 
นอกจากนี้แล้ว เมื่อพิจารณาที่สัดส่วนปริมาณการรับซื้อพลังงานสะอาด ในปี 2564 เห็นได้ว่า พลังงานสะอาดจาก Solar มีสัดส่วนเป็นร้อยละ 57 ของปริมาณการรับซื้อพลังงานสะอาด ที่มีสัดส่วนผู้ผลิตพลังงานเป็นร้อยละ 97 ขณะที่พลังงานสะอาดจาก Biogas Biomass และ Garbage คิดรวมกันเป็นสัดส่วนร้อยละ 43 ของปริมาณการรับซื้อพลังงานสะอาด ที่มีสัดส่วนผู้ผลิตพลังงานเพียงแค่ร้อยละ 3 เนื่องจากผู้ผลิตพลังงานสะอาดจาก Solar มีทั้งรายย่อยและรายใหญ่ โดยส่วนใหญ่เป็นรายย่อย ที่มีกำลังการผลิตไฟฟ้าไม่สูงมากหนัก ขณะที่ผู้ผลิตพลังงานสะอาดจาก Biogas Biomass และ Garbage เป็นรายใหญ่ที่มีกำลังการผลิตไฟฟ้าที่สูงมาก ส่งผลทำให้ผู้ผลิตพลังงานที่มีเพียงจำนวนร้อยละ 3 สามารถมีกำลังการผลิตไฟฟ้าได้ถึงร้อยละ 43 ของปริมาณการรับซื้อพลังงานสะอาด

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

จากกราฟความสัมพันธ์ของค่าไฟฟ้ากับหน่วยพลังงาน จำแนกตามแหล่งการผลิตพลังงานสะอาด พบว่า แต่ละพลังงานมีความสัมพันธ์เป็นเส้นตรง เนื่องจากราคาของพลังงานที่ขายนั้นเป็นการคิดราคาแบบต่อหน่วยด้วยอัตราคงที่และก้าวหน้า ขึ้นอยู่กับประเภทพลังงานและโครงการรับซื้อ โดยจะเป็นได้ว่า Solar มีเส้นความสัมพันธ์อยู่หลายเส้น เนื่องจากในส่วนของ Solar มีโครงการรับซื้อไฟฟ้าอยู่หลายโครงการ โดยแต่ละโครงการต่างมีการคิดอัตราค่าไฟฟ้าที่แตกต่างกันไป จึงทำให้เส้นความชันของ Solar นั้นแตกต่างกันไปด้วย

ซึ่งผู้ผลิตพลังงานรายเล็กและรายใหญ่นั้น สามารถดูได้จากปริมาณการผลิตพลังงาน โดยที่ทางด้านซ้ายจะเป็นรายเล็ก เนื่องจากมีปริมาณการผลิตพลังงานน้อย ขณะที่่ทางด้านขวาจะเป็นรายใหญ่ เนื่องจากมีปริมาณผลิตพลังงานที่สูง อันการยืนยันสมมุติฐานในรูปที่ 7 และ 8

# คำนวน ความคุ้มค่าในการลงทุนใน Solar Rooftop อย่างไร

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

แต่ในช่วงปี 2022 ที่อัตราค่า Ft มีค่าเป็นบวก เห็นได้ว่า เส้นอัตราค่าไฟฟ้าต่อหน่วยไฟฟ้ากับอัตราค่า Ft มีการเคลื่อนไหวในแนวโน้มที่ไปในทิศทางเดียวกัน กล่าวคือ หากอัตรา Ft ปรับค่าเป็นสูงขึ้น จะส่งผลให้ค่าไฟฟ้าสูงขึ้น แล้วทำให้อัตราค่าไฟฟ้าต่อหน่วยไฟฟ้าสูงขึ้น ขณะที่พฤติกรรมการใช้ไฟฟ้ายังคงเหมือนเดิม ฉะนั้นการเข้ามาลงทุนติดตั้ง Solar Rooftop เพื่อลดภาระค่าไฟฟ้า จึงเป็นคำถามที่ใครหลายคนสงสัยถึงค่าคุ้มค่าในการลงทุน ทางเราจึงได้นำข้อมูลสถิติการใช้ไฟฟ้าข้างต้นมาเป็นกรณีศึกษาให้เห็นภาพถึงความคุ้มค่าในการลงทุนดังตารางนี้

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

จากสถิติการใช้ไฟฟ้า นำมาคำนวณหาค่าหน่วยการใช้ไฟฟ้าต่อวันในช่วงที่มีแสงอาทิตย์ เป็น 4.15 kw/day เนื่องจาก Solar Rooftop จะผลิตพลังงานไฟฟ้าได้เฉพาะในช่วงที่มีแสงอาทิตย์เท่านั้น ต่อมาเป็นการเลือกขนาดกำลังการผลิตของ Solar Rooftop ซึ่งควรเลือกให้มากกว่าค่าหน่วยการใช้ไฟฟ้าต่อวันในช่วงที่มีแสงอาทิตย์ ซึ่งเราได้เลือกมาที่กำลังการผลิต 5 kw/day ที่มีราคาของอุปกรณ์รวมค่าบริการติดตั้งอยู่ที่ประมาณ 200,000 บาท ซึ่งอายุของแผง Solar Panel อยู่ที่ประมาณ 20 ปี อันเป็นระยะเวลาในการลงทุนของเรา
ผลประโยชน์ในการติดตั้ง Solar Rooftop ในแบบ Hybrid แบ่งออกเป็น 2 ส่วน ได้แก่ การลดหน่วยการใช้ไฟฟ้าในช่วงเวลากลางวัน ที่อัตรา 4.48 บาทต่อหน่วย (เป็นอัตราที่คำนวนจากสถิติการใช้ไฟฟ้า) และนำหน่วยที่เหลือจากการผลิตไฟฟ้าไปขายให้การไฟฟ้า ที่อัตรา 2.2 บาทต่อหน่วย

ตัวชี้วัดทางการเงินที่เรานำมาใช้ในการวิเคราะห์ความคุ้มค่าในการลงทุน ได้แก่ NPV, IRR, Payback Period นั้นเอง
-	Net Present Value (NPV) มูลค่าปัจจุบันสุทธิ เป็นผลตอบแทนที่เราคาดว่า จะได้รับจากการลงทุนในโครงการนี้ โดยที่หากค่า NPV เป็นค่าบวก แสดงว่าผลตอบแทนที่ได้จากโครงการนี้มากกว่าเงินลงทุน มีความคุ้มค่าในการลงทุน ขณะที่หากค่า NPV เป็นค่าลบ แสดงว่าผลตอบแทนที่ได้จากโครงการนี้น้อยกว่าเงินลงทุน ไม่มีความคุ้มค่าในการลงทุน
-	Internal Rate of Return (IRR) อัตราผลตอบแทนภายใน เป็นอัตราคิดลดที่ทำให้ NPV มีค่าเป็น 0 โดยที่หากค่า IRR มีค่ามากกว่า ต้นทุนของเงินลงทุนในโครงการนี้ แสดงว่า มีความคุ้มค่าในการลงทุน ขณะที่หากค่า IRR มีค่าน้อยกว่า ต้นทุนของเงินลงทุนในโครงการนี้ แสดงว่า ไม่มีความคุ้มค่าในการลงทุน
-	Payback Period ระยะเวลาการคืนทุน เป็นค่าที่บอกว่ากี่ปีจึงได้เงินลงทุนคืน โดยที่หากค่า Payback Period มีค่าน้อยกว่า ระยะการลงทุนของโครงการนี้ แสดงว่า มีความคุ้มค่าในการลงทุน ขณะที่หากค่า Payback Period มีค่ามากกว่า ระยะการลงทุนของโครงการนี้ แสดงว่า ไม่มีความคุ้มค่าในการลงทุน

จากตารางข้างต้น เห็นได้ว่าค่า NPV มีค่าเป็นบวก และค่า IRR มีค่ามากกว่า ต้นทุนของเงินลงทุนในโครงการ (Inflation Rate) แล้วก็ค่า Payback Period ที่มีค่าน้อยกว่า ระยะการลงทุนของโครงการ นั้น แสดงให้เห็นได้ว่า การลงทุนในการติดตั้ง Solar Rooftop นั้นมีความคุ้มค่าน่าลงทุน


