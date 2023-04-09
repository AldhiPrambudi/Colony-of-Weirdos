# 4. Bussiness Insight
Pada Business Insight ini kami membuat beberapa variabel dummy dan sedikit merubah bentuk data yang seharusnya terjadi pada stage preprocessing.

## 4.1. Pengaruh "MIS_Status" (Status Pinjaman) terhadap "ApprovalFY" (Tahun diterimanya Pinjaman)


```python
plt.figure(figsize=(15,5))
sns.countplot(data=df,x='ApprovalFY',hue='MIS_Status',   palette = 'afmhot_r', 
            lw = 1, ec = 'k')
plt.xticks(rotation=90, ha='right')
plt.show()
```


    
![png](output_2_0.png)
    


**Keterangan :**<br>
Dari Informasi diatas dapat dilihat bahawa tingkat gagal bayar (CHGOFF) paling banyak terletak pada tahun "2007" dan hal ini terjadi pada saat dimulainya "great recession 2007 -2009". jadi masuk akal jika tingkat gagal bayar (CHGOFF) tinggi.
- Sumber : https://www.bls.gov/spotlight/2012/recession/

#### Mari kita lihat tingkat gagal bayar berdasarkan State(Negara Bagian) selama tahun "2007"


```python
plt.figure(figsize=(25,8))
sns.countplot(data=df[df['ApprovalFY'] == 2007],x='State',hue='MIS_Status',  palette = 'afmhot_r', 
            lw = 1, ec = 'k')
plt.xticks(rotation=90, ha='right')
plt.show()
```


    
![png](output_5_0.png)
    


**Keterangan :**<br>
Ternyata terdapat **"State"** (Negara Bagian) yang tingkat gagal bayarnya **(CHGOFF)** cukup signifikan melebihi tingkat berhasil bayar **(P I F)**, terlihat dari plot diatas yaitu state **"CA", "FL", "GA" & "MD"**

#### Mari kita lihat tingkat gagal bayar dari sektor industri selama tahun "2007"


```python
plt.figure(figsize=(15,5))
sns.countplot(data=df[df['ApprovalFY'] == 2007],x='NAICS',hue='MIS_Status',  palette = 'afmhot_r', 
            lw = 1, ec = 'k')
plt.xticks(rotation=90, ha='right')
plt.show()
```


    
![png](output_8_0.png)
    


Ada hal yang menarik disini, terlihat bahwa sector industri **"Healt Care/Social Assistance"** memiliki tingkat gagal bayar yang kecil, yang berarti dari **"sektor industri"** tersebut tidak terlalu banyak mempengaruhi resiko gagal bayar selama resesi ekonomi.

#### Dari penjelasan diatas kita dapat memberikan rekomendasi business jika terjadi resesi: <br>
- SBA perlu menambahkan persyaratan kredit yang lebih ketat, SBA dapat menetapkan standar kredit yang lebih ketat untuk meminimalkan risiko gagal bayar. Persyaratan kredit yang ketat dapat memastikan bahwa hanya bisnis terbaik dari **"State" & "Sektor Industri"** terbaiklah yang memenuhi syarat untuk mendapatkan pinjaman dan lebih mampu membayar kembali pinjaman selama masa resesi ekonomi.
- Sebagai lembaga pemerintah SBA juga berperan untuk membantu memulihkan ekonomi selama resesi yaitu dengan meningkatkan edukasi keuangan melalui program konseling SBA, seingga dapat meningkatkan edukasi keuangan bagi bisnis kecil untuk membantu mereka memahami risiko dan cara mengelola keuangan mereka selama masa resesi ekonomi. Edukasi keuangan dapat membantu bisnis kecil mengambil keputusan yang lebih baik dalam pengelolaan keuangan mereka dan meminimalkan risiko gagal bayar.

## 4.2. Pengaruh "MIS_Status" (Status Pinjaman) terhadap "Term" (Jangka Waktu Pinjaman)



```python
#create column
CHGOFF = df[df['MIS_Status'] == 'CHGOFF']
PIF = df[df['MIS_Status'] == 'P I F']

fig, ax = plt.subplots(1, 1, figsize = (14, 6))

sns.kdeplot(x = 'Term', data = CHGOFF, label = 'Status : Gagal Bayar', 
            color = '#E49393', fill = True, multiple = 'stack', ax = ax)

sns.kdeplot(x = 'Term', data = PIF, label = 'Status : Lunas',
            color = '#408E91', fill = True, multiple = 'stack', ax = ax)

fig.suptitle("Pengaruh Jangka Waktu Pinjaman terhadap Status Pinjaman", fontsize=16, fontweight='bold', family='sansserif', color='#323232')
plt.title('Banyak Perusahaan berpotensi gagal membayar pinjaman di jangka waktu yang pendek (kecil)', fontsize=13, fontweight='bold', family='monospace', color='grey', loc ='left')
plt.annotate('tingkat gagal\nbayar meningkat', fontstyle='italic',  fontname='monospace', color ='grey',fontweight='bold', fontsize=9,xy=(50,0.014),arrowprops=dict(arrowstyle='->',connectionstyle='arc3,rad=.2'),xytext=(-10,0.018))
ax.grid(c = 'black', lw = 1, axis = 'y', alpha = 0.1)
ax.set_xlabel('Jangka Waktu',  weight = 'bold')
ax.legend()

plt.show()
```


    
![png](output_12_0.png)
    


**Keterangan :**<br>
dari plot diatas terlihat bahwa tingkat resiko gagal bayar banyak terjadi dijangka waktu **(Term)** yang pendek < 100. hal ini juga menyatakan bahwa semakin lama jangka waktu yang diberikan, semakin besar juga kemungkinan bisnis dapat berkembang dan hal ini dapat mengurangi resiko gagal bayar.

#### Akan tetapi kami menemukan sumber terkait Jangka Waktu **"(Term)"** 
> sumber : https://www.sba.gov/partners/lenders/7a-loan-program/terms-conditions-eligibility
- pinjaman yang didukung/dijamin oleh properti biasanya memiliki durasi 25 tahun atau lebih (300 bulan) dan merupakan satu-satunya pinjaman yang diberikan untuk jangka waktu yang begitu lama, sedangkan pinjaman yang tidak dijamin oleh properti biasanya memiliki durasi kurang dari 25 tahun (<300 bulan).sehingga kami melakukan segmentasi terhadap variabel **"Term"** dengan membuat varibael baru bernama **RealEstate**, di mana **"RealEstate" = 1 jika "Term" (≥ 300 bulan) dan "RealEstate" = 0 jika "Term" < 300 bulan.**


```python
#membuat kolom realestate
df['RealEstate'] = 0
df.loc[df['Term'] >= 300, 'RealEstate'] = 1
```


```python
#cek kolom apakah sudah sesuai
re = df[['Term', 'RealEstate']]
re[re['RealEstate']==1].sample(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Term</th>
      <th>RealEstate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>558438</th>
      <td>300</td>
      <td>1</td>
    </tr>
    <tr>
      <th>334511</th>
      <td>306</td>
      <td>1</td>
    </tr>
    <tr>
      <th>78096</th>
      <td>300</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
df1 = df.groupby(['RealEstate', 'MIS_Status']).agg(Jumlah_Peminjam=('LoanNr_ChkDgt', 'count')).reset_index()
df2 =  df.groupby('RealEstate').agg(Jumlah_Total_Peminjam=('MIS_Status', 'count')).reset_index()
df_merge = df1.merge(df2, on = 'RealEstate')

# Menambah Kolom Rasio
df_merge['Rasio'] = round (df_merge['Jumlah_Peminjam'] / df_merge['Jumlah_Total_Peminjam'] * 100,2)

#ubah nama data
df_merge['RealEstate'] = df_merge['RealEstate'].replace({0: 'Lainnya', 1: 'Real Estate'})

df_merge
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>RealEstate</th>
      <th>MIS_Status</th>
      <th>Jumlah_Peminjam</th>
      <th>Jumlah_Total_Peminjam</th>
      <th>Rasio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Lainnya</td>
      <td>CHGOFF</td>
      <td>145171</td>
      <td>666802</td>
      <td>21.77</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Lainnya</td>
      <td>P I F</td>
      <td>521631</td>
      <td>666802</td>
      <td>78.23</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Real Estate</td>
      <td>CHGOFF</td>
      <td>36</td>
      <td>30414</td>
      <td>0.12</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Real Estate</td>
      <td>P I F</td>
      <td>30378</td>
      <td>30414</td>
      <td>99.88</td>
    </tr>
  </tbody>
</table>
</div>




```python
import seaborn as sns
# Membuat plot
plt.figure (figsize = (8, 10))
sns.barplot(x ='RealEstate', y = 'Rasio', hue = 'MIS_Status', data = df_merge, palette = 'afmhot_r', lw = 1, ec = 'k')

# Memberi judul pada plot dan sumbu-sumbunya
plt.title('Pengaruh Resiko Gagal Bayar terhadap RealEstate', fontsize=15, fontweight='bold', pad = 15, family='sansserif')
plt.xlabel('Real Estate', fontsize = 12, fontname='monospace')
plt.ylabel('Rasio Persentase(%)', fontsize = 12,fontname='monospace')
plt.text(x=-0.20, y=22.77, s='21.77%', ha='center', va='center', fontsize=10)
plt.text(x=0.20, y=79.23, s='78.23%', ha='center', va='center', fontsize=10)
plt.text(x=0.80, y=1.12, s='0.12%', ha='center', va='center', fontsize=10)
plt.text(x=1.20, y=100.88, s='99.88%', ha='center', va='center', fontsize=10)
plt.yticks(range(0, 120, 10)) 


# Menampilkan plot
plt.show()
```


    
![png](output_18_0.png)
    


**Keterangan:** <br>
terlihat bahwa "RealEstate"(Kepemilikan Properti) memiliki tingkat gagal bayar sangat rendah yaitu hanya sebesar 0.13%, ini menandakan bahwa nilai dari properti tersebut seringkali cukup besar untuk menutupi jumlah pokok yang masih belum dibayar, sehingga mengurangi kemungkinan terjadinya gagal bayar.

#### Dari penjelasan diatas kita dapat memberikan rekomendasi business: <br>
- Perusahaan yang dijamin oleh properti (real estate) dengan jangka waktu lebih dari 300 bulan memiliki tingkat gagal bayar yang sangat rendah, hal ini menandakan SBA dapat memberikan dukungan dalam bentuk penjaminan pinjaman untuk membantu perusahaan tersebut. Namun, SBA tetap perlu memonitor kinerja perusahaan dan mengawasi penggunaan dana pinjaman dengan baik. SBA juga dapat memberikan bimbingan dan konseling kepada perusahaan untuk membantu mereka meningkatkan kinerja dan pertumbuhan bisnis mereka.

## 4.3. Pengaruh State (Negara Bagian) terhadap tingkat gagal bayar (CHGOFF)


```python
# -- import csv that contain state name
states = pd.read_csv('states.csv')
states.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State</th>
      <th>Abbrev</th>
      <th>Code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>Ala.</td>
      <td>AL</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>Alaska</td>
      <td>AK</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>Ariz.</td>
      <td>AZ</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>Ark.</td>
      <td>AR</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>Calif.</td>
      <td>CA</td>
    </tr>
  </tbody>
</table>
</div>




```python
# -- percentage of CHGOFF based on state
gb1 = df[df['MIS_Status'] == 'CHGOFF'].groupby('State').count()[['MIS_Status']].reset_index()
gb2 = df[df['MIS_Status'] == 'P I F'].groupby('State').count()[['MIS_Status']].reset_index()
gb_state = pd.merge(gb1,gb2,on='State')
gb_state.rename(columns={'MIS_Status_x':'CHGOFF','MIS_Status_y':'P I F'},inplace=True)

def percent(x):
    a = x['CHGOFF'] / (x['P I F'] + x['CHGOFF'])
    return round(a,3)
gb_state['Percent'] = gb_state.apply(percent,axis=1)


# -- Input State Name based on state code
def apply_name_states(x) :
    return states[states['Code'] == x['State']]['State'].values[0]

gb_state['StateName'] = gb_state.apply(apply_name_states,axis=1)
df_state_MIS = gb_state.copy()


# -- Chloropleth
fig = go.Figure(data=go.Choropleth(
    locations=df_state_MIS['State'], # Spatial coordinates
    z = df_state_MIS['Percent'].astype(float), # Data to be color-coded
    locationmode = 'USA-states', # set of locations match entries in `locations`
    colorscale = 'Reds',
    text = df_state_MIS['StateName'],
    colorbar_title = "Persentase",
))

fig.update_layout(
    title_text = 'Persentase CHGOFF Per State',
    geo_scope='usa', # limite map scope to USA
)

fig.show()
```


<div>                            <div id="ea122f3c-b4b1-4107-8dc4-c956c344a04b" class="plotly-graph-div" style="height:525px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("ea122f3c-b4b1-4107-8dc4-c956c344a04b")) {                    Plotly.newPlot(                        "ea122f3c-b4b1-4107-8dc4-c956c344a04b",                        [{"colorbar":{"title":{"text":"Persentase"}},"colorscale":[[0.0,"rgb(255,245,240)"],[0.125,"rgb(254,224,210)"],[0.25,"rgb(252,187,161)"],[0.375,"rgb(252,146,114)"],[0.5,"rgb(251,106,74)"],[0.625,"rgb(239,59,44)"],[0.75,"rgb(203,24,29)"],[0.875,"rgb(165,15,21)"],[1.0,"rgb(103,0,13)"]],"locationmode":"USA-states","locations":["AK","AL","AR","AZ","CA","CO","CT","DC","DE","FL","GA","HI","IA","ID","IL","IN","KS","KY","LA","MA","MD","ME","MI","MN","MO","MS","MT","NC","ND","NE","NH","NJ","NM","NV","NY","OH","OK","OR","PA","RI","SC","SD","TN","TX","UT","VA","VT","WA","WI","WV","WY"],"text":["Alaska","Alabama","Arkansas","Arizona","California","Colorado","Connecticut","District of Columbia","Delaware","Florida","Georgia","Hawaii","Iowa","Idaho","Illinois","Indiana","Kansas","Kentucky","Louisiana","Massachusetts","Maryland","Maine","Michigan","Minnesota","Missouri","Mississippi","Montana","North Carolina","North Dakota","Nebraska","New Hampshire","New Jersey","New Mexico","Nevada","New York","Ohio","Oklahoma","Oregon","Pennsylvania","Rhode Island","South Carolina","South Dakota","Tennessee","Texas","Utah","Virginia","Vermont","Washington","Wisconsin","West Virginia","Wyoming"],"z":[0.094,0.219,0.195,0.245,0.228,0.211,0.16,0.31,0.22,0.32,0.292,0.185,0.134,0.167,0.258,0.199,0.139,0.214,0.218,0.15,0.244,0.112,0.26,0.141,0.179,0.178,0.073,0.244,0.075,0.127,0.125,0.231,0.116,0.284,0.233,0.188,0.178,0.181,0.164,0.129,0.249,0.088,0.261,0.217,0.203,0.231,0.08,0.172,0.145,0.185,0.068],"type":"choropleth"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"title":{"text":"Persentase CHGOFF Per State"},"geo":{"scope":"usa"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('ea122f3c-b4b1-4107-8dc4-c956c344a04b');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })                };                });            </script>        </div>


**Keterangan**:<br>
Berdasarkan visualisasi mapping diatas dapat disimpulkan bahwa setiap **State** (Negara Bagian) memiliki tingkat resiko gagal bayar **(CHGOFF)** yang berbeda, hal ini juga menandakan bahwa ekonomi masing masing **State** berbeda.

#### Dari penjelasan diatas kita dapat memberikan rekomendasi business: <br>
- Mempertimbangkan kondisi ekonomi setiap state. SBA dapat mempertimbangkan kondisi ekonomi setiap state ketika menetapkan persyaratan dan kriteria untuk program penjaminan pinjaman. Ini dapat membantu SBA mengidentifikasi risiko kredit yang berbeda-beda antara state.
- Membuat laporan risiko kredit berbasis state. SBA dapat membuat laporan risiko kredit berbasis state untuk membantu lender dan pemohon pinjaman memahami risiko kredit yang berbeda-beda antara state. Ini dapat membantu lender dan pemohon pinjaman dalam mengambil keputusan yang lebih baik dan meminimalkan risiko gagal bayar.
