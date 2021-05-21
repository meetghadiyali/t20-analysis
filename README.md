# T20 Cricket Game Analysis

* Analysis the Indian Cricket Team's 2018 and 2019 International 20-over Cricket matches. 
* Used a different approach of analyzing commentary (word descriptions) instead of plain numerical data. 
* Plot graphs to answer various questions pertaning to the matches and made the appropriate intferences. 
* Used Jupyter Notebook for the analysis. 


## Why using commentary (word description of the game) instead of numerical data as input? 

* Commentary about the game gives lot of interesting insights otther than the usual runs and wickets. 
* This was a desciptive sports analysis.
* I summarized the sports data to come up with important statistics. 






# ANALYSIS

#### Total number of 20-over (T20) matches India played in 2018 and 2019
```python 
batting_df=df[df['batting_team']=='ind']
num = len(batting_df['match'].unique())
print("Total no. of matches India played:",num)
```
###### Output:
Total no. of matches India played: 28


---

#### No of T20s India played each year
```python
num_of_matches=batting_df.groupby(['year']).apply(lambda x:x['match'].nunique()).reset_index(name='No. of Matches')
fig = num_of_matches.plot.bar(x="year", y="No. of Matches", rot=0, title="No. of T20s India Played",figsize=(10,8)).get_figure()
```
###### Output: 
![](https://github.com/meetghadiyali/t20-analysis/blob/main/t20_played.png)

*Inference:*
India played more T20's in 2018 than compared to 2019. This is due to the ICC Cricket World Cup in 2019. 

---

#### Team Average Score (Batting first and batting second) 
```python 
score = batting_df.groupby(['year','match','innings_no']).apply(lambda x:x['runs'].sum()).reset_index(name='score')
print("Batting First Average score:",np.median(score[score['innings_no']==1]['score'].values))
print("Batting Second Average score:",np.median(score[score['innings_no']==2]['score'].values))
```
###### Output:
Batting First Average score: 183.0
Batting Second Average score: 157.5

---

#### Team Average Innings wise. 
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



