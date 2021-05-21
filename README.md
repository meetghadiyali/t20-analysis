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
### Output:
Total no. of matches India played: 28


