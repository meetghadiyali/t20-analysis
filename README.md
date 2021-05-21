# T20 Cricket Game Analysis

What is Cricket? [Short fun video by International Cricket Council (ICC)](https://www.youtube.com/watch?v=g-beFHld19c)

* Analysis of the Indian Cricket Team's 2018 and 2019 International 20-over Cricket matches. 
* Used a different approach of analyzing commentary (word descriptions) instead of plain numerical data. 
* Plotted graphs to answer various questions pertaning to the matches and made the appropriate inferences. 
* Used Jupyter Notebook for the analysis. 
* Plotted Bar Graphs for the analysis. 


## Why using commentary (word description of the game) instead of numerical data as input? 

* Commentary about the game gives lot of interesting insights otther than the usual runs and wickets. 
* This was a desciptive sports analysis.
* Summarized the sports data to come up with important statistics. 






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

### No of T20s India played in 2018 and 2019:
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
1. The highest batting first average was close to 180 in 2018. On an average, India scored 180+ runs while batting        first in 2018.  So, we can infer that India had the **best batting** line-up during 2018.
2. Batting Second average is always less than the batting first average over all the years. From this, we can infer        that the target set by the Indian team is higher than the opponents.

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
1. India wins almost every match when playing against **Bangladesh and Ireland.**
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
2. HIGHER THE BETTER. 

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
1. The strike rate of the Indian team reaches around **160+** in the last 5 overs. And around **130+** in power play      and middle overs.
2. HIGHER THE BETTER. 

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
1. In 2018, India recorded the highest batting strike rate across **all the 3 phases** (Powerplay, middle overs, and      the last 5 overs). 
2. HIGHER THE BETTER. 

---

### Economy Rates while Bowling in different phases: 
```python
powerplay_bowling_df = powerplay_df[powerplay_df['bowling_team']=='ind']
middle_bowling_df = middle_df[middle_df['bowling_team']=='ind']
last_bowling_df = last_df[last_df['bowling_team']=='ind']

temp = powerplay_bowling_df.groupby('match').apply(lambda x:x['runs'].sum()).reset_index()
avg1=np.median(temp[0].values)
er1 = avg1/6.0

temp = middle_bowling_df.groupby('match').apply(lambda x:x['runs'].sum()).reset_index()
avg2=np.median(temp[0].values)
er2 = avg2/9.0

temp = last_bowling_df.groupby('match').apply(lambda x:x['runs'].sum()).reset_index()
avg3=np.median(temp[0].values)
er3 = avg3/5.0

data = {"Economy rate":[er1,er2,er3] };
index = ["Powerplay", "Middle", "Last 5 overs"];

# Dictionary loaded into a DataFrame 
dataFrame = pd.DataFrame(data=data, index=index);

# Draw a vertical bar chart
fig = dataFrame.plot.bar(rot=0, title="Team India bowling performance").get_figure()
```
###### Output:
![](https://github.com/meetghadiyali/t20-analysis/blob/main/bowling_performance.png)

*Inference:*
1. Indian bowlers concede around 7-9 runs per over. 
2. LOWER THE BETTER. 

---

### Bowling Performance phase wise in a match: 
```python
#bowling strike rate
sr1 = powerplay_bowling_df.shape[0]/(powerplay_bowling_df[powerplay_bowling_df['event']=='out'].shape[0])
sr2 = middle_bowling_df.shape[0]/(middle_bowling_df[middle_bowling_df['event']=='out'].shape[0])
sr3 = last_bowling_df.shape[0]/(last_bowling_df[last_bowling_df['event']=='out'].shape[0])

#bowling average
avg1=np.sum(powerplay_bowling_df['runs'].values)/(powerplay_bowling_df[powerplay_bowling_df['event']=='out'].shape[0])
avg2=np.sum(middle_bowling_df['runs'].values)/(middle_bowling_df[middle_bowling_df['event']=='out'].shape[0])
avg3=np.sum(last_bowling_df['runs'].values)/(last_bowling_df[last_bowling_df['event']=='out'].shape[0])

# A python dictionary
data = {"Strike rate":[sr1,sr2,sr3],
"Bowling average":[avg1,avg2,avg3]
}
index = ["Powerplay", "Middle", "Last 5 overs"];

# Dictionary loaded into a DataFrame 
dataFrame = pd.DataFrame(data=data, index=index);

# Draw a vertical bar chart
fig = dataFrame.plot.bar(rot=0, title="Team India bowling performance").get_figure()
```
###### Output:
![](https://github.com/meetghadiyali/t20-analysis/blob/main/bowling_sr_avg_phases.png)

*Inference:*
1. On an average, Indian bowlers pick 2 wickets in the last 5 overs as bowlers concede around 13 balls for each wicket. 
2. Indian bowlers concede around 27+ runs for a wicket in the middle overs which can be improved. 
3. LOWER THE NUMBER THE BETTER. 

---

### Bowling Performance in different phases in 2018 and 2019: 
```python
pp=powerplay_bowling_df.groupby('year').apply(lambda x: x.shape[0] / x[x['event']=='out'].shape[0] ).reset_index(name='strike rate')
middle=middle_bowling_df.groupby('year').apply(lambda x: x.shape[0] / x[x['event']=='out'].shape[0] ).reset_index(name='strike rate')
last=last_bowling_df.groupby('year').apply(lambda x: x.shape[0] / x[x['event']=='out'].shape[0] ).reset_index(name='strike rate')

# A python dictionary
data = {"Powerplay":pp['strike rate'].values,
"Middle overs":middle['strike rate'].values,
"Last 5 overs":last['strike rate'].values
}
index = last['year'].values
# Dictionary loaded into a DataFrame 
dataFrame = pd.DataFrame(data=data, index=index);

# Draw a vertical bar chart
fig = dataFrame.plot.bar(rot=0, title="Team India bowling performance (Strike rate)").get_figure()
```
###### Output:
![](https://github.com/meetghadiyali/t20-analysis/blob/main/bowling_sr_phase.png)

*Inference:*
1. Team India’s bowling performance was very poor in 2019 as the bowlers considered 33+ balls on an average to take a       wicket in the middle overs.

---

### Boundary Analysis: 
```python
print("Avg no. of balls to hit 4:",batting_df.shape[0]/batting_df['runs'].value_counts()[4])
print("Avg no. of balls to hit 6:",batting_df.shape[0]/batting_df['runs'].value_counts()[6])
```
###### Output:
 Avg no. of balls to hit 4: 9.120643431635388
 Avg no. of balls to hit 6: 16.84158415841584

---

### Average number of balls to score a boundary in 2018 and 2019: 
```python
df_4 = batting_df.groupby('year').apply(lambda x: x.shape[0] / x[x['runs']==4].shape[0] ).reset_index(name='num_of_balls')
df_6 = batting_df.groupby('year').apply(lambda x: x.shape[0] / x[x['runs']==6].shape[0] ).reset_index(name='num_of_balls')

# A python dictionary
data = {"4":df_4['num_of_balls'].values,
"6":df_6['num_of_balls'].values
}

index = df_4['year'].values
# Dictionary loaded into a DataFrame 
dataFrame = pd.DataFrame(data=data, index=index);

# Draw a vertical bar chart
fig = dataFrame.plot.bar(rot=0, title="Avg no. of balls to score boundary (year wise)",figsize=(10,8)).get_figure()
```
###### Output: 
![](https://github.com/meetghadiyali/t20-analysis/blob/main/avg_boundary_ball.png)


*Inference:*
1. Team India improved power hitting over the past few years. In 2019, the team cleared six for every 16 balls.

---

### Average number of balls to score a boundary in a match: 
```python
num1=powerplay_batting_df.shape[0]/powerplay_batting_df['runs'].value_counts()[4]
num2=middle_batting_df.shape[0]/middle_batting_df['runs'].value_counts()[4]
num3=last_batting_df.shape[0]/last_batting_df['runs'].value_counts()[4]

x_axis = ['PP','Middle','Last']
y_axis = [num1, num2, num3]
dataframe= pd.DataFrame({'overs':x_axis,'4':y_axis})

num1=powerplay_batting_df.shape[0]/powerplay_batting_df['runs'].value_counts()[6]
num2=middle_batting_df.shape[0]/middle_batting_df['runs'].value_counts()[6]
num3=last_batting_df.shape[0]/last_batting_df['runs'].value_counts()[6]
dataframe['6'] = [num1,num2,num3]

data = {"4":dataframe['4'].values,
"6":dataframe['6'].values
};

index = dataframe['overs'].values
# Dictionary loaded into a DataFrame 
dataFrame = pd.DataFrame(data=data, index=index);

# Draw a vertical bar chart
fig = dataFrame.plot.bar(rot=0, title="Avg no. of balls to score boundary(overs wise)",figsize=(10,8)).get_figure()
```
###### Output:
![](https://github.com/meetghadiyali/t20-analysis/blob/main/avg_boundary_phase.png)

*Inference:*
1. India clears 4 for every over in the power play and the last 5 overs. 
2. Team India smashes only 1 six in power play as the batsmen concede 24+ balls for a six. 


### Thank you. 
