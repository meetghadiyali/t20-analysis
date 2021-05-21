# T20 Cricket Game Analysis

What is Cricket? [Short video by International Cricket Council (ICC)](https://www.youtube.com/watch?v=g-beFHld19c)

* Analysis the Indian Cricket Team's 2018 and 2019 International 20-over Cricket matches. 
* Used a different approach of analyzing commentary (word descriptions) instead of plain numerical data. 
* Plot graphs to answer various questions pertaning to the matches and made the appropriate intferences. 
* Used Jupyter Notebook for the analysis. 


## Why using commentary (word description of the game) instead of numerical data as input? 

* Commentary about the game gives lot of interesting insights otther than the usual runs and wickets. 
* This was a desciptive sports analysis.
* I summarized the sports data to come up with important statistics. 






# ANALYSIS

### Total number of 20-over (T20) matches India played in 2018 and 2019
```python 
batting_df=df[df['batting_team']=='ind']
num = len(batting_df['match'].unique())
print("Total no. of matches India played:",num)
```
###### Output:
Total no. of matches India played: 28


---

### No of T20s India played each year
```python
num_of_matches=batting_df.groupby(['year']).apply(lambda x:x['match'].nunique()).reset_index(name='No. of Matches')
fig = num_of_matches.plot.bar(x="year", y="No. of Matches", rot=0, title="No. of T20s India Played",figsize=(10,8)).get_figure()
```
###### Output: 
![](https://github.com/meetghadiyali/t20-analysis/blob/main/t20_played.png)

*Inference:*
1. India played more T20's in 2018 than compared to 2019. This is due to the ICC Cricket World Cup in 2019. 

---

### Team Average Score (Batting first and batting second) 
```python 
score = batting_df.groupby(['year','match','innings_no']).apply(lambda x:x['runs'].sum()).reset_index(name='score')
print("Batting First Average score:",np.median(score[score['innings_no']==1]['score'].values))
print("Batting Second Average score:",np.median(score[score['innings_no']==2]['score'].values))
```
###### Output:
Batting First Average score: 183.0
Batting Second Average score: 157.5

---

### Team Average Innings wise. 
```python
#split into innings
first_innings_df=score[score['innings_no']==1]
second_innings_df=score[score['innings_no']==2]

df1=first_innings_df.groupby('year').apply(lambda x:np.median(x['score'].values)).reset_index(name='score')
df2=second_innings_df.groupby('year').apply(lambda x:np.median(x['score'].values)).reset_index(name='score')

data = {"Batting First": df1['score'].values,
"Batting Second":df2['score'].values
}
index = df1['year'].values

# Dictionary loaded into a DataFrame 
dataFrame = pd.DataFrame(data=data, index=index);

# Draw a vertical bar chart
fig= dataFrame.plot.bar(rot=0, title="Team Average",figsize=(10,8)).get_figure()
```
###### Output:
![](https://github.com/meetghadiyali/t20-analysis/blob/main/team_average.png)

*Inference:*
1. The highest batting first average was close to 180 in 2018. On an average, India scored 180+ runs while batting first    in 2018.  So, we can infer that India had the **best batting** line-up during 2018.
2. Batting Second average is always less than the batting first average over all the years. From this, we can infer that    the target set by the Indian team is higher than the opponents.

---

### Overall Winning % (Batting first and Batting second): 
```python
grp = batting_df.groupby('match').first().reset_index()
matches_won = grp[grp['innings_no'] == grp['result']]
print("Over all Winning %:",(matches_won.shape[0]/num)*100)

#Batting first & second
first  = grp[grp['innings_no'] == 1]
second = grp[grp['innings_no'] == 2]

#Batting first winning %
won1 = first[first['innings_no'] == first['result']]
print("Batting First Winning % :",(won1.shape[0]/first.shape[0])*100)

#Batting secong winning %
won2 = second[second['innings_no'] == second['result']]
print("Batting Second Winning % :",(won2.shape[0]/second.shape[0])*100)
```
###### Output:
Over all Winning %: 67.85714285714286 
Batting First Winning % : 50.0
Batting Second Winning % : 85.71428571428571

---

### Winning % against different teams: 
```python
df1 = grp.groupby('bowling_team').apply(lambda x:x.shape[0]).reset_index(name='no')
df2=grp.groupby('bowling_team').apply(lambda x: x[x['innings_no']==x['result']].shape[0]).reset_index(name='won')
df2['Win %']=df2['won']/df1['no']
fig=df2.plot.bar(x="bowling_team", y="Win %", rot=0, title="Win % against different teams",figsize=(10,8)).get_figure() #weighted
```
###### Output:
![](https://github.com/meetghadiyali/t20-analysis/blob/main/win_diff_temas.png)

*Inference:*
1. India wins almost every match when playing against **Bangladesh.**
2. India won **ZERO** matches against **Australia** in 2018 and 2019.

---

### Overall Batting Strike Rate 
```python
batting_df=df[df['batting_team']=='ind']
bowling_df=df[df['bowling_team']=='ind']

#strike rate of indian team
sr=(np.sum(batting_df['runs'].values)/batting_df.shape[0])*100
print("Strike rate of Indian team:",sr)
```
###### Output:
Strike rate of Indian team: 141.7989417989418

---

### Batting Strike Rate in 2018 and 2019. 
```python
sr=batting_df.groupby('year').apply( lambda x: (np.sum(x['runs'].values)/x.shape[0])*100  ).reset_index(name='strike rate')
fig=sr.plot.bar(x="year", y="strike rate", rot=0, title="Team Batting performance over the years",figsize=(10,8)).get_figure()
```
###### Output:
![](https://github.com/meetghadiyali/t20-analysis/blob/main/batting_performance.png)

*Inference:*
1. India had a much better strike rate in 2018. 

---

### Batting Strike Rate across different phases of a match: 
```python
powerplay_df = df[df['over_number']<=5.6]
middle_df = df[(df['over_number']>=6.1) & (df['over_number']<=14.6)]
last_df = df[(df['over_number']>=15.1) & (df['over_number']<=19.6)]

powerplay_batting_df = powerplay_df[powerplay_df['batting_team']=='ind']
middle_batting_df = middle_df[middle_df['batting_team']=='ind']
last_batting_df = last_df[last_df['batting_team']=='ind']

sr1=(np.sum(powerplay_batting_df['runs'].values)/powerplay_batting_df.shape[0])*100
sr2=(np.sum(middle_batting_df['runs'].values)/middle_batting_df.shape[0])*100
sr3=(np.sum(last_batting_df['runs'].values)/last_batting_df.shape[0])*100

#plot
data = {"Strike rate":[sr1,sr2,sr3]
        };

index  = ["Powerplay", "Middle", "Last 5 overs"];

# Dictionary loaded into a DataFrame       
dataFrame = pd.DataFrame(data=data, index=index);

# Draw a vertical bar chart
axes = dataFrame.plot.bar(rot=0, title="Team India Batting Strike (Overs wise)").get_figure()
```
###### Output:
![](https://github.com/meetghadiyali/t20-analysis/blob/main/batting_sr.png) 

*Inference:*
1. The strike rate of the Indian team reaches around **160+** in the last 5 overs. And around **130+** in power play and middle overs.

---

### Batting Strike Rate over different phases in 2018 and 2019:
```python
#Strike rate 
score = powerplay_batting_df.groupby(['year','match']).apply(lambda x:x['runs'].sum()).reset_index(name='score') 
pp=powerplay_batting_df.groupby("year").apply(lambda x:(x['runs'].sum()/x.shape[0])*100).reset_index(name='strike rate')
middle=middle_batting_df.groupby("year").apply(lambda x:(x['runs'].sum()/x.shape[0])*100).reset_index(name='strike rate') 
last=last_batting_df.groupby("year").apply(lambda x:(x['runs'].sum()/x.shape[0])*100).reset_index(name='strike rate')

data = {"Powerplay":pp['strike rate'].values,
        "Middle overs":middle['strike rate'].values,
        "Last 5 overs":last['strike rate'].values
        };

index  = last['year'].values
# Dictionary loaded into a DataFrame       
dataFrame = pd.DataFrame(data=data, index=index);

# Draw a vertical bar chart
axes = dataFrame.plot.bar(rot=0, title="Team India batting strike rate")
#axes[1].legend(loc=2)  

fig=axes.get_figure()
```
###### Output:
![](https://github.com/meetghadiyali/t20-analysis/blob/main/batting_sr_phases.png)

*Inference:*
1. In 2018, India recorded the highest batting strike rate across **all the 3 phases** (Powerplay, middle overs, and      the last 5 overs)

