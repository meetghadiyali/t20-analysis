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


```python 
batting_df=df[df['batting_team']=='ind']
num = len(batting_df['match'].unique())
print("Total no. of matches India played:",num)
```
###### Output:
Total no. of matches India played: 28


---


```python
num_of_matches=batting_df.groupby(['year']).apply(lambda x:x['match'].nunique()).reset_index(name='No. of Matches')
fig = num_of_matches.plot.bar(x="year", y="No. of Matches", rot=0, title="No. of T20s India Played",figsize=(10,8)).get_figure()
```
###### Output: 
![](https://github.com/meetghadiyali/t20-analysis/blob/main/t20_played.png)
######## Inference: 
India played more T20's in 2018 than compared to 2019. This is due to the ICC Cricket World Cup in 2019. 
