
import matplotlib.pyplot as plt
import pandas as pd
import scipy.stats as st
import numpy as np
from sklearn.linear_model import LinearRegression
from sklearn.svm import SVR

%load_ext lab_black
import matplotlib.pyplot as plt
import pandas as pd
import scipy.stats as st
import numpy as np
from sklearn.linear_model import LinearRegression
from sklearn.svm import SVR
​
%load_ext lab_black
mouse_metadata_path = "Mouse_metadata.csv"
study_results_path = "Study_results.csv"
mouse_metadata = pd.read_csv(mouse_metadata_path)
study_results = pd.read_csv(study_results_path)
DF (Mouse_ Metadata) Overview
mouse_metadata
Mouse ID	Drug Regimen	Sex	Age_months	Weight (g)
0	k403	Ramicane	Male	21	16
1	s185	Capomulin	Female	3	17
2	x401	Capomulin	Female	16	15
3	m601	Capomulin	Male	22	17
4	g791	Ramicane	Male	11	16
...	...	...	...	...	...
244	z314	Stelasyn	Female	21	28
245	z435	Propriva	Female	12	26
246	z581	Infubinol	Female	24	25
247	z795	Naftisol	Female	13	29
248	z969	Naftisol	Male	9	30
249 rows × 5 columns

DF Types (Mouse_metadata)
mouse_metadata.dtypes
Mouse ID        object
Drug Regimen    object
Sex             object
Age_months       int64
Weight (g)       int64
dtype: object
DF Describe (Mouse_metadata)
mouse_metadata.describe()
Age_months	Weight (g)
count	249.000000	249.000000
mean	12.730924	26.116466
std	7.228687	3.697003
min	1.000000	15.000000
25%	6.000000	25.000000
50%	13.000000	27.000000
75%	19.000000	29.000000
max	24.000000	30.000000
DF Unique values [Mouse ID]
mouse_metadata["Mouse ID"].value_counts()
g316    1
h531    1
i635    1
p981    1
p136    1
       ..
u327    1
r850    1
c458    1
l897    1
s166    1
Name: Mouse ID, Length: 249, dtype: int64
DF (study_results) Overview
study_results
Mouse ID	Timepoint	Tumor Volume (mm3)	Metastatic Sites
0	b128	0	45.000000	0
1	f932	0	45.000000	0
2	g107	0	45.000000	0
3	a457	0	45.000000	0
4	c819	0	45.000000	0
...	...	...	...	...
1888	r944	45	41.581521	2
1889	u364	45	31.023923	3
1890	p438	45	61.433892	1
1891	x773	45	58.634971	4
1892	b879	45	72.555239	2
1893 rows × 4 columns

DF Types (study_results)
study_results.dtypes
Mouse ID               object
Timepoint               int64
Tumor Volume (mm3)    float64
Metastatic Sites        int64
dtype: object
DF Describe (study_results)
study_results.describe()
Timepoint	Tumor Volume (mm3)	Metastatic Sites
count	1893.000000	1893.000000	1893.000000
mean	19.572108	50.448381	1.021659
std	14.079460	8.894722	1.137974
min	0.000000	22.050126	0.000000
25%	5.000000	45.000000	0.000000
50%	20.000000	48.951474	1.000000
75%	30.000000	56.292200	2.000000
max	45.000000	78.567014	4.000000
DF Unique values [Mouse ID]
study_results["Mouse ID"].value_counts()
g989    13
g316    10
q511    10
g296    10
a492    10
        ..
v199     1
u153     1
l872     1
x336     1
b447     1
Name: Mouse ID, Length: 249, dtype: int64
Single Dataset Display
Mouse = pd.merge(mouse_metadata, study_results, how="outer", on="Mouse ID")
Mouse
Mouse ID	Drug Regimen	Sex	Age_months	Weight (g)	Timepoint	Tumor Volume (mm3)	Metastatic Sites
0	k403	Ramicane	Male	21	16	0	45.000000	0
1	k403	Ramicane	Male	21	16	5	38.825898	0
2	k403	Ramicane	Male	21	16	10	35.014271	1
3	k403	Ramicane	Male	21	16	15	34.223992	1
4	k403	Ramicane	Male	21	16	20	32.997729	1
...	...	...	...	...	...	...	...	...
1888	z969	Naftisol	Male	9	30	25	63.145652	2
1889	z969	Naftisol	Male	9	30	30	65.841013	3
1890	z969	Naftisol	Male	9	30	35	69.176246	4
1891	z969	Naftisol	Male	9	30	40	70.314904	4
1892	z969	Naftisol	Male	9	30	45	73.867845	4
1893 rows × 8 columns

Clean DataFrame - dropping duplicate Timepoint
Clean_Mouse = Mouse.drop_duplicates()
Clean_Mouse
Mouse ID	Drug Regimen	Sex	Age_months	Weight (g)	Timepoint	Tumor Volume (mm3)	Metastatic Sites
0	k403	Ramicane	Male	21	16	0	45.000000	0
1	k403	Ramicane	Male	21	16	5	38.825898	0
2	k403	Ramicane	Male	21	16	10	35.014271	1
3	k403	Ramicane	Male	21	16	15	34.223992	1
4	k403	Ramicane	Male	21	16	20	32.997729	1
...	...	...	...	...	...	...	...	...
1888	z969	Naftisol	Male	9	30	25	63.145652	2
1889	z969	Naftisol	Male	9	30	30	65.841013	3
1890	z969	Naftisol	Male	9	30	35	69.176246	4
1891	z969	Naftisol	Male	9	30	40	70.314904	4
1892	z969	Naftisol	Male	9	30	45	73.867845	4
1892 rows × 8 columns

Clean_Mouse.count()
Mouse ID              1892
Drug Regimen          1892
Sex                   1892
Age_months            1892
Weight (g)            1892
Timepoint             1892
Tumor Volume (mm3)    1892
Metastatic Sites      1892
dtype: int64
Summary Statistics
#DF(Clean_Mouse) Summary Statistics

Clean_Mouse.describe()
Age_months	Weight (g)	Timepoint	Tumor Volume (mm3)	Metastatic Sites
count	1892.000000	1892.000000	1892.000000	1892.000000	1892.000000
mean	12.810254	25.662262	19.582452	50.451260	1.022199
std	7.189027	3.922652	14.075984	8.896191	1.138032
min	1.000000	15.000000	0.000000	22.050126	0.000000
25%	7.000000	25.000000	5.000000	45.000000	0.000000
50%	13.000000	27.000000	20.000000	48.954697	1.000000
75%	20.000000	29.000000	30.000000	56.298917	2.000000
max	24.000000	30.000000	45.000000	78.567014	4.000000
#DF(Drug_Regimen) - (each)Tumor Volume / Drug_Regimen

Drug_Regimen = Clean_Mouse.sort_values("Tumor Volume (mm3)", ascending=False)[
    ["Drug Regimen", "Tumor Volume (mm3)"]
]
Drug_Regimen
Drug Regimen	Tumor Volume (mm3)
1277	Ketapril	78.567014
1152	Naftisol	76.668817
1354	Ketapril	75.294936
1179	Stelasyn	75.123690
1571	Naftisol	75.113288
...	...	...
7	Ramicane	26.546993
18	Capomulin	25.472143
8	Ramicane	24.365505
19	Capomulin	23.343598
9	Ramicane	22.050126
1892 rows × 2 columns

(Mean) Tumor Volume / Drug_Regimen
Drug_Regimen_Mean = Drug_Regimen.groupby(["Drug Regimen"])
Drug_Regimen_Mean.mean()
Tumor Volume (mm3)
Drug Regimen	
Capomulin	40.675741
Ceftamin	52.591172
Infubinol	52.884795
Ketapril	55.235638
Naftisol	54.331565
Placebo	54.033581
Propriva	52.368318
Ramicane	40.216745
Stelasyn	54.233149
Zoniferol	53.236507
# Create a bar chart based off of the group series from before
Mean_chart = Drug_Regimen_Mean.mean().plot(kind="bar")
​
# Set the xlabel and ylabel using class methods
plt.xlabel("Drug Regimen")
plt.ylabel("Tumor Volume (mm3)")
plt.show()

(Median) Tumor Volume / Drug_Regimen
Drug_Regimen_Median = Drug_Regimen.groupby(["Drug Regimen"])
Drug_Regimen_Median.median()
Tumor Volume (mm3)
Drug Regimen	
Capomulin	41.557809
Ceftamin	51.776157
Infubinol	51.820584
Ketapril	53.698743
Naftisol	52.509285
Placebo	52.288934
Propriva	50.909965
Ramicane	40.673236
Stelasyn	52.431737
Zoniferol	51.818479
# Create a bar chart based off of the group series from before
Median_chart = Drug_Regimen_Median.median().plot(kind="bar")
​
# Set the xlabel and ylabel using class methods
plt.xlabel("Drug Regimen")
plt.ylabel("Tumor Volume (mm3)")
plt.show()

(STD) Tumor Volume / Drug_Regimen
Drug_Regimen_STD = Drug_Regimen.groupby(["Drug Regimen"])
Drug_Regimen_STD.std()
Tumor Volume (mm3)
Drug Regimen	
Capomulin	4.994774
Ceftamin	6.268188
Infubinol	6.567243
Ketapril	8.279709
Naftisol	8.134708
Placebo	7.821003
Propriva	6.502160
Ramicane	4.846308
Stelasyn	7.710419
Zoniferol	6.966589
# Create a bar chart based off of the group series from before
STD_chart = Drug_Regimen_Mean.std().plot(kind="bar")
​
# Set the xlabel and ylabel using class methods
plt.xlabel("Drug Regimen")
plt.ylabel("Tumor Volume (mm3)")
plt.show()

(Min) Tumor Volume / Drug_Regimen
Drug_Regimen_min = Drug_Regimen.groupby(["Drug Regimen"])
Drug_Regimen_min.min()
Tumor Volume (mm3)
Drug Regimen	
Capomulin	23.343598
Ceftamin	45.000000
Infubinol	36.321346
Ketapril	45.000000
Naftisol	45.000000
Placebo	45.000000
Propriva	45.000000
Ramicane	22.050126
Stelasyn	45.000000
Zoniferol	45.000000
# Create a bar chart based off of the group series from before
Min_chart = Drug_Regimen_min.min().plot(kind="bar")
​
# Set the xlabel and ylabel using class methods
plt.xlabel("Drug Regimen")
plt.ylabel("Tumor Volume (mm3)")
plt.show()

(Max) Tumor Volume / Drug_Regimen
Drug_Regimen_max = Drug_Regimen.groupby(["Drug Regimen"])
Drug_Regimen_max.max()
Tumor Volume (mm3)
Drug Regimen	
Capomulin	48.158209
Ceftamin	68.923185
Infubinol	72.226731
Ketapril	78.567014
Naftisol	76.668817
Placebo	73.212939
Propriva	72.455421
Ramicane	47.622816
Stelasyn	75.123690
Zoniferol	73.324432
# Create a bar chart based off of the group series from before
Max_chart = Drug_Regimen_max.max().plot(kind="bar")
​
# Set the xlabel and ylabel using class methods
plt.xlabel("Drug Regimen")
plt.ylabel("Tumor Volume (mm3)")
plt.show()

(Variance) Tumor Volume / Drug_Regimen
Drug_Regimen_var = Drug_Regimen.groupby(["Drug Regimen"])
Drug_Regimen_var.var()
Tumor Volume (mm3)
Drug Regimen	
Capomulin	24.947764
Ceftamin	39.290177
Infubinol	43.128684
Ketapril	68.553577
Naftisol	66.173479
Placebo	61.168083
Propriva	42.278090
Ramicane	23.486704
Stelasyn	59.450562
Zoniferol	48.533355
# Create a bar chart based off of the group series from before
Var_chart = Drug_Regimen_var.var().plot(kind="bar")
​
# Set the xlabel and ylabel using class methods
plt.xlabel("Drug Regimen")
plt.ylabel("Tumor Volume (mm3)")
plt.show()

(SEM) Tumor Volume / Drug_Regimen
Drug_Regimen_sem = Drug_Regimen.groupby(["Drug Regimen"])
Drug_Regimen_sem.sem()
Tumor Volume (mm3)
Drug Regimen	
Capomulin	0.329346
Ceftamin	0.469821
Infubinol	0.492236
Ketapril	0.603860
Naftisol	0.596466
Placebo	0.581331
Propriva	0.514041
Ramicane	0.320955
Stelasyn	0.573111
Zoniferol	0.516398
# Create a bar chart based off of the group series from before
Sem_chart = Drug_Regimen_sem.sem().plot(kind="bar")
​
# Set the xlabel and ylabel using class methods
plt.xlabel("Drug Regimen")
plt.ylabel("Tumor Volume (mm3)")
plt.show()

Bar Charts
Capomulin = Clean_Mouse.loc[
    Clean_Mouse["Drug Regimen"] == "Capomulin", "Mouse ID"
].count()
Capomulin
230
Ceftamin = Clean_Mouse.loc[
    Clean_Mouse["Drug Regimen"] == "Ceftamin", "Mouse ID"
].count()
Ceftamin
178
Infubinol = Clean_Mouse.loc[
    Clean_Mouse["Drug Regimen"] == "Infubinol", "Mouse ID"
].count()
Infubinol
178
Ketapril = Clean_Mouse.loc[
    Clean_Mouse["Drug Regimen"] == "Ketapril", "Mouse ID"
].count()
Ketapril
188
Naftisol = Clean_Mouse.loc[
    Clean_Mouse["Drug Regimen"] == "Naftisol", "Mouse ID"
].count()
Naftisol
186
Placebo = Clean_Mouse.loc[Clean_Mouse["Drug Regimen"] == "Placebo", "Mouse ID"].count()
Placebo
181
Propriva = Clean_Mouse.loc[
    Clean_Mouse["Drug Regimen"] == "Propriva", "Mouse ID"
].count()
Propriva
160
Ramicane = Clean_Mouse.loc[
    Clean_Mouse["Drug Regimen"] == "Ramicane", "Mouse ID"
].count()
Ramicane
228
Stelasyn = Clean_Mouse.loc[
    Clean_Mouse["Drug Regimen"] == "Stelasyn", "Mouse ID"
].count()
Stelasyn
181
Zoniferol = Clean_Mouse.loc[
    Clean_Mouse["Drug Regimen"] == "Zoniferol", "Mouse ID"
].count()
Zoniferol
182
Mice_Summary = pd.DataFrame(
    [
        {"Drug Regimen": "Capomulin", "Mice Number": Capomulin},
        {"Drug Regimen": "Ceftamin", "Mice Number": Ceftamin},
        {"Drug Regimen": "Infubinol", "Mice Number": Infubinol},
        {"Drug Regimen": "Ketapril", "Mice Number": Ketapril},
        {"Drug Regimen": "Naftisol", "Mice Number": Naftisol},
        {"Drug Regimen": "Placebo", "Mice Number": Placebo},
        {"Drug Regimen": "Propriva", "Mice Number": Propriva},
        {"Drug Regimen": "Ramicane", "Mice Number": Ramicane},
        {"Drug Regimen": "Stelasyn", "Mice Number": Stelasyn},
        {"Drug Regimen": "Zoniferol", "Mice Number": Zoniferol},
    ]
)
Mice_Summary
Drug Regimen	Mice Number
0	Capomulin	230
1	Ceftamin	178
2	Infubinol	178
3	Ketapril	188
4	Naftisol	186
5	Placebo	181
6	Propriva	160
7	Ramicane	228
8	Stelasyn	181
9	Zoniferol	182
Bar Charts
# Set x axis and tick locations
x_axis = np.arange(len(Mice_Summary))
tick_locations = [value for value in x_axis]
plt.figure(figsize=(20, 3))
plt.bar(x_axis, Mice_Summary["Mice Number"], color="b", alpha=0.5, align="center")
plt.xticks(tick_locations, Mice_Summary["Drug Regimen"], rotation="vertical")
plt.title("Total number of mice for each treatment")
plt.xlabel("Drug Regimen")
plt.ylabel("Mice NUmbers")
Text(0, 0.5, 'Mice NUmbers')

Pie Charts
Mice_Male = Clean_Mouse.loc[Clean_Mouse["Sex"] == "Male", "Mouse ID"].count()
Mice_Male
958
Mice_Female = Clean_Mouse.loc[Clean_Mouse["Sex"] == "Female", "Mouse ID"].count()
Mice_Female
934
Mice_Distribution = pd.DataFrame(
    [
        {"Sex": "Male", "Mice Number": Mice_Male},
        {"Sex": "Female", "Mice Number": Mice_Female},
    ]
)
Mice_Distribution
Sex	Mice Number
0	Male	958
1	Female	934
# Labels for the sections of our pie chart
labels = ["Mice Male", "Mice Female"]
​
# The values of each section of the pie chart
sizes = [958, 934]
​
# The colors of each section of the pie chart
colors = ["#4285f4", "orange"]
​
# Tells matplotlib to seperate the "MIce Male" section from Female
explode = (0.1, 0)
# Automatically finds the percentages of each part of the pie chart
plt.pie(
    sizes,
    explode=explode,
    labels=labels,
    colors=colors,
    autopct="%1.1f%%",
    shadow=True,
    startangle=140,
)
([<matplotlib.patches.Wedge at 0x7ff425d83550>,
  <matplotlib.patches.Wedge at 0x7ff425d91210>],
 [Text(-0.7528766827041941, -0.9344392439534677, 'Mice Male'),
  Text(0.6901370393433148, 0.8565692423420561, 'Mice Female')],
 [Text(-0.4391780649107799, -0.545089558972856, '50.6%'),
  Text(0.37643838509635347, 0.46721958673203057, '49.4%')])

Boxplots
Clean_Mouse["Drug Regimen"].value_counts(dropna=False)
Capomulin    230
Ramicane     228
Ketapril     188
Naftisol     186
Zoniferol    182
Placebo      181
Stelasyn     181
Ceftamin     178
Infubinol    178
Propriva     160
Name: Drug Regimen, dtype: int64
​
Capomulin = Clean_Mouse.loc[
    Clean_Mouse["Drug Regimen"] == "Capomulin", "Tumor Volume (mm3)"
].values
Ramicane = Clean_Mouse.loc[
    Clean_Mouse["Drug Regimen"] == "Ramicane", "Tumor Volume (mm3)"
].values
Infubinol = Clean_Mouse.loc[
    Clean_Mouse["Drug Regimen"] == "Infubinol", "Tumor Volume (mm3)"
].values
Ceftamin = Clean_Mouse.loc[
    Clean_Mouse["Drug Regimen"] == "Ceftamin", "Tumor Volume (mm3)"
].values
​
​
plt.title("Tumor Volume (mm3)")
plt.grid()
plt.boxplot(
    [Capomulin, Ramicane, Infubinol, Ceftamin],
    labels=["Capomulin", "Ramicane", "Infubinol", "Ceftamin"],
)
{'whiskers': [<matplotlib.lines.Line2D at 0x7ff425d5bb50>,
  <matplotlib.lines.Line2D at 0x7ff425d5bf90>,
  <matplotlib.lines.Line2D at 0x7ff425d6da90>,
  <matplotlib.lines.Line2D at 0x7ff425d6de90>,
  <matplotlib.lines.Line2D at 0x7ff425d02890>,
  <matplotlib.lines.Line2D at 0x7ff425d02d10>,
  <matplotlib.lines.Line2D at 0x7ff425d16750>,
  <matplotlib.lines.Line2D at 0x7ff425d16b90>],
 'caps': [<matplotlib.lines.Line2D at 0x7ff425d63410>,
  <matplotlib.lines.Line2D at 0x7ff425d63850>,
  <matplotlib.lines.Line2D at 0x7ff425d78310>,
  <matplotlib.lines.Line2D at 0x7ff425d78750>,
  <matplotlib.lines.Line2D at 0x7ff425d0d190>,
  <matplotlib.lines.Line2D at 0x7ff425d0d610>,
  <matplotlib.lines.Line2D at 0x7ff425d16fd0>,
  <matplotlib.lines.Line2D at 0x7ff425d20450>],
 'boxes': [<matplotlib.lines.Line2D at 0x7ff425d5b790>,
  <matplotlib.lines.Line2D at 0x7ff425d6d650>,
  <matplotlib.lines.Line2D at 0x7ff425d02450>,
  <matplotlib.lines.Line2D at 0x7ff425d162d0>],
 'medians': [<matplotlib.lines.Line2D at 0x7ff425d63cd0>,
  <matplotlib.lines.Line2D at 0x7ff425d78b90>,
  <matplotlib.lines.Line2D at 0x7ff425d0da50>,
  <matplotlib.lines.Line2D at 0x7ff425d20890>],
 'fliers': [<matplotlib.lines.Line2D at 0x7ff425d6d150>,
  <matplotlib.lines.Line2D at 0x7ff425d78fd0>,
  <matplotlib.lines.Line2D at 0x7ff425d0de90>,
  <matplotlib.lines.Line2D at 0x7ff425d20cd0>],
 'means': []}

Quartiles, Outliers
#Quartiles

Quartiles = Clean_Mouse.sort_values("Tumor Volume (mm3)", ascending=False)[
    ["Drug Regimen", "Mouse ID", "Tumor Volume (mm3)"]
]
Quartiles
Drug Regimen	Mouse ID	Tumor Volume (mm3)
1277	Ketapril	o331	78.567014
1152	Naftisol	l725	76.668817
1354	Ketapril	p189	75.294936
1179	Stelasyn	m269	75.123690
1571	Naftisol	t724	75.113288
...	...	...	...
7	Ramicane	k403	26.546993
18	Capomulin	s185	25.472143
8	Ramicane	k403	24.365505
19	Capomulin	s185	23.343598
9	Ramicane	k403	22.050126
1892 rows × 3 columns

Camopulin Quartiles and Outliers
Capomulin_Quartiles = Quartiles.loc[
    Quartiles["Drug Regimen"] == "Capomulin"
].sort_values("Drug Regimen", ascending=False)["Tumor Volume (mm3)"]
Capomulin_Quartiles
332    48.158209
293    39.524843
339    39.202129
296    39.167949
22     39.113891
         ...    
376    44.006257
435    43.967895
11     43.878496
303    43.784893
19     23.343598
Name: Tumor Volume (mm3), Length: 230, dtype: float64
Capomulin_Quartiles.describe()
count    230.000000
mean      40.675741
std        4.994774
min       23.343598
25%       37.685933
50%       41.557809
75%       45.000000
max       48.158209
Name: Tumor Volume (mm3), dtype: float64
#Capomulin Quartiles

Capomulin_Q1 = Capomulin_Quartiles.quantile(0.25)
Capomulin_Q3 = Capomulin_Quartiles.quantile(0.75)
Capomulin_Q1, Capomulin_Q3
(37.685932865, 45.0)
#Capomulin_IQR

Capomulin_IQR = Capomulin_Q3 - Capomulin_Q1
Capomulin_IQR
7.314067135000002
#Capomulin Lower and Upper Limits

Capomulin_lower_limit = Capomulin_Q1 - 1.5 * Capomulin_IQR
Capomulin_upper_limit = Capomulin_Q3 - 1.5 * Capomulin_IQR
Capomulin_lower_limit, Capomulin_upper_limit
(26.714832162499995, 34.0288992975)
#Capomulin Outliers

Capomulin_Quartiles[
    (Capomulin_Quartiles < Capomulin_lower_limit)
    | (Capomulin_Quartiles > Capomulin_upper_limit)
]
332    48.158209
293    39.524843
339    39.202129
296    39.167949
22     39.113891
         ...    
376    44.006257
435    43.967895
11     43.878496
303    43.784893
19     23.343598
Name: Tumor Volume (mm3), Length: 202, dtype: float64
Ramicane Quartiles and Outliers
Ramicane_Quartiles = Quartiles.loc[Quartiles["Drug Regimen"] == "Ramicane"].sort_values(
    "Drug Regimen", ascending=False
)["Tumor Volume (mm3)"]
Ramicane_Quartiles
407    47.622816
82     38.504824
289    38.342008
271    38.265404
213    38.259725
         ...    
447    43.419381
417    43.339161
398    43.220276
449    43.166373
9      22.050126
Name: Tumor Volume (mm3), Length: 228, dtype: float64
Ramicane_Quartiles.describe()
count    228.000000
mean      40.216745
std        4.846308
min       22.050126
25%       36.674635
50%       40.673236
75%       45.000000
max       47.622816
Name: Tumor Volume (mm3), dtype: float64
#Ramicane Quartiles

Ramicane_Q1 = Ramicane_Quartiles.quantile(0.25)
Ramicane_Q3 = Ramicane_Quartiles.quantile(0.75)
Ramicane_Q1, Ramicane_Q3
(36.67463458499999, 45.0)
#Ramicane IQR

Ramicane_IQR = Ramicane_Q3 - Ramicane_Q1
Ramicane_IQR
8.325365415000007
#Ramicane Lower and Upper Limits

Ramicane_lower_limit = Ramicane_Q1 - 1.5 * Ramicane_IQR
Ramicane_upper_limit = Ramicane_Q3 - 1.5 * Ramicane_IQR
Ramicane_lower_limit, Ramicane_upper_limit
(24.18658646249998, 32.51195187749999)
#Ramicane Outliers

Ramicane_Quartiles[
    (Ramicane_Quartiles < Ramicane_lower_limit)
    | (Ramicane_Quartiles > Ramicane_upper_limit)
]
407    47.622816
82     38.504824
289    38.342008
271    38.265404
213    38.259725
         ...    
447    43.419381
417    43.339161
398    43.220276
449    43.166373
9      22.050126
Name: Tumor Volume (mm3), Length: 216, dtype: float64
Infubinol Quartiles and Outliers
Infubinol_Quartiles = Quartiles.loc[
    Quartiles["Drug Regimen"] == "Infubinol"
].sort_values("Drug Regimen", ascending=False)["Tumor Volume (mm3)"]
Infubinol_Quartiles
637     72.226731
1722    47.298508
1071    49.471244
793     49.431685
1682    49.385574
          ...    
1311    54.832629
1223    54.822521
1048    54.778961
1397    54.656549
669     36.321346
Name: Tumor Volume (mm3), Length: 178, dtype: float64
Infubinol_Quartiles.describe()
count    178.000000
mean      52.884795
std        6.567243
min       36.321346
25%       47.312353
50%       51.820584
75%       57.314444
max       72.226731
Name: Tumor Volume (mm3), dtype: float64
#Infubinol Quartiles

Infubinol_Q1 = Infubinol_Quartiles.quantile(0.25)
Infubinol_Q3 = Infubinol_Quartiles.quantile(0.75)
Infubinol_Q1, Infubinol_Q3
(47.3123533, 57.314443967500004)
#Infubinol IQR

Infubinol_IQR = Infubinol_Q3 - Infubinol_Q1
Ramicane_IQR
8.325365415000007
#Infubinol Lower and Upper Limits

Infubinol_lower_limit = Infubinol_Q1 - 1.5 * Infubinol_IQR
Infubinol_upper_limit = Infubinol_Q3 - 1.5 * Infubinol_IQR
Infubinol_lower_limit, Infubinol_upper_limit
(32.309217298749985, 42.31130796625)
#Infubinol Outiliers

Infubinol_Quartiles[
    (Infubinol_Quartiles < Infubinol_lower_limit)
    | (Infubinol_Quartiles > Infubinol_upper_limit)
]
637     72.226731
1722    47.298508
1071    49.471244
793     49.431685
1682    49.385574
          ...    
945     55.087724
1311    54.832629
1223    54.822521
1048    54.778961
1397    54.656549
Name: Tumor Volume (mm3), Length: 177, dtype: float64
Ceftamin Quartiles and Outliers
Ceftamin_Quartiles = Quartiles.loc[Quartiles["Drug Regimen"] == "Ceftamin"].sort_values(
    "Drug Regimen", ascending=False
)["Tumor Volume (mm3)"]
Ceftamin_Quartiles
1033    68.923185
1765    47.196927
1790    48.983717
840     48.722078
1254    48.679453
          ...    
1404    54.952226
1158    54.951668
1029    54.613973
1375    54.510575
484     45.000000
Name: Tumor Volume (mm3), Length: 178, dtype: float64
Ceftamin_Quartiles.describe()
count    178.000000
mean      52.591172
std        6.268188
min       45.000000
25%       47.208427
50%       51.776157
75%       56.801438
max       68.923185
Name: Tumor Volume (mm3), dtype: float64
#Ceftamin Quartiles

Ceftamin_Q1 = Ceftamin_Quartiles.quantile(0.25)
Ceftamin_Q3 = Ceftamin_Quartiles.quantile(0.75)
Ceftamin_Q1, Ceftamin_Q3
(47.208427112500004, 56.801437570000004)
#Ceftamin IQR

Ceftamin_IQR = Ceftamin_Q3 - Ceftamin_Q1
Ceftamin_IQR
9.5930104575
#Ceftamin Lower and Upper Limits

Ceftamin_lower_limit = Ceftamin_Q1 - 1.5 * Ceftamin_IQR
Ceftamin_upper_limit = Ceftamin_Q3 - 1.5 * Ceftamin_IQR
Ceftamin_lower_limit, Ceftamin_upper_limit
(32.818911426250004, 42.411921883750004)
# Ceftamin Outliers
Ceftamin_Quartiles[
    (Ceftamin_Quartiles < Ceftamin_lower_limit)
    | (Ceftamin_Quartiles > Ceftamin_upper_limit)
]
1033    68.923185
1765    47.196927
1790    48.983717
840     48.722078
1254    48.679453
          ...    
1404    54.952226
1158    54.951668
1029    54.613973
1375    54.510575
484     45.000000
Name: Tumor Volume (mm3), Length: 178, dtype: float64
Line and Scatter Plots
#Plot of time point versus tumor volume for all mouses

Timepoint = Clean_Mouse.sort_values("Drug Regimen", ascending=False)[
    ["Drug Regimen", "Mouse ID", "Timepoint", "Tumor Volume (mm3)"]
]
Timepoint
Drug Regimen	Mouse ID	Timepoint	Tumor Volume (mm3)
813	Zoniferol	f129	10	48.179719
752	Zoniferol	d133	0	45.000000
1325	Zoniferol	o926	45	62.327966
1335	Zoniferol	p136	0	45.000000
1336	Zoniferol	p136	5	49.273971
...	...	...	...	...
224	Capomulin	w914	45	36.041047
223	Capomulin	w914	40	35.611309
222	Capomulin	w914	35	41.664916
221	Capomulin	w914	30	40.993110
303	Capomulin	b128	15	43.784893
1892 rows × 4 columns

# Create a scatter plot which compares MPG to horsepower
Timepoint.plot(
    kind="scatter",
    x="Tumor Volume (mm3)",
    y="Timepoint",
    grid=True,
    figsize=(8, 8),
    title="Timepoint Vs. Tumor Volume (mm3)",
)
plt.show()

#Generate a line plot of time point versus tumor volume for a mouse treated with Capomulin

Timepoint_Capomulin = Timepoint.loc[
    Timepoint["Drug Regimen"] == "Capomulin"
].sort_values("Drug Regimen", ascending=False)[
    ["Drug Regimen", "Mouse ID", "Timepoint", "Tumor Volume (mm3)"]
]
Timepoint_Capomulin
Drug Regimen	Mouse ID	Timepoint	Tumor Volume (mm3)
115	Capomulin	y793	0	45.000000
356	Capomulin	g316	25	46.809225
354	Capomulin	g316	15	45.216743
353	Capomulin	g316	10	44.502042
313	Capomulin	j246	15	43.312654
...	...	...	...	...
39	Capomulin	m601	45	28.430964
38	Capomulin	m601	40	31.378045
37	Capomulin	m601	35	30.834357
36	Capomulin	m601	30	31.758275
303	Capomulin	b128	15	43.784893
230 rows × 4 columns

# Create a scatter plot which compares MPG to horsepower
Timepoint_Capomulin.plot(
    kind="scatter",
    x="Tumor Volume (mm3)",
    y="Timepoint",
    grid=True,
    figsize=(8, 8),
    title="Capomulin - Timepoint Vs. Tumor Volume (mm3)",
)
plt.show()

#scatter plot of mouse weight versus average tumor volume for the Capomulin regimen

Mouse_Weight = Clean_Mouse.loc[Clean_Mouse["Drug Regimen"] == "Capomulin"].sort_values(
    "Tumor Volume (mm3)", ascending=False
)[["Drug Regimen", "Mouse ID", "Weight (g)", "Tumor Volume (mm3)"]]
Mouse_Weight
Drug Regimen	Mouse ID	Weight (g)	Tumor Volume (mm3)
332	Capomulin	j119	23	48.158209
375	Capomulin	l509	21	48.070452
440	Capomulin	i557	24	47.685963
374	Capomulin	l509	21	47.488599
384	Capomulin	r944	25	47.373423
...	...	...	...	...
39	Capomulin	m601	17	28.430964
17	Capomulin	s185	17	28.328531
28	Capomulin	x401	15	28.167397
18	Capomulin	s185	17	25.472143
19	Capomulin	s185	17	23.343598
230 rows × 4 columns

Capomulin_Tumor_Mean = Mouse_Weight.loc[
    Mouse_Weight["Drug Regimen"] == "Capomulin", "Tumor Volume (mm3)"
].mean()
Capomulin_Tumor_Mean
40.67574114099999
Mouse_Weight.plot(
    kind="scatter",
    x="Tumor Volume (mm3)",
    y="Weight (g)",
    grid=True,
    figsize=(8, 8),
    title="Capomulin - Weight (g) Vs. Tumor Volume (mm3)",
)
plt.show()

Correlation and Regression
​