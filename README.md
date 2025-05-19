# Home Advantage in Spanish La-Liga (2012-2023)
*How much does playing at home really matter in football? This project analyses over a decade of Spanish top-flight football to uncover the impact of crowd presence, formations, squad age and coaching on match outcomes.*

## Project Objective
This analysis investigates **home advantage** in Spanish La Liga by examining over 4,000 matches between 2012 and 2023. By leveraging player, match and club-level datasets, the project explores:

- How home advantage manifests across clubs
- Whether **larger stadiums** and **higher attendance** improve home performance
- Which **managers consistently deliver** at home
- How **age and team composition** affect results

## Why Does This Matter?

As a passionate football fan and follower of the Spanish La Liga, I have often heard the saying that "it’s harder to win away". It is a common culture in football— but **how true is that** statistically? This project gives me a chance to explore that question through data, turning a familair football narrative into a measurable, data-driven investigation.

## Datasets Used

All datasets were primarily sourced from the publicly available CSV file **'Football Data from Transfermarkt'** on Kaggle, these were structured, cleaned, and filtered specifically for **Spanish top-tier football** (`competition_id = 'ES1'`). Additional context and supplementary data were gathered from other reputable web sources to fill in gaps not covered by the original dataset.

## Technologies Used
- Python (Pandas, NumPy, datetime)
- Data Visualizations: Matplotlib and Seaborn
- NoteBook: Jupyter Laboratory

## Methods

#### Filtering for La Liga
```python
games_data_ESP = games_data[games_data['competition_id'] == 'ES1']
clubs_data_ESP = clubs_data[clubs_data['domestic_competition_id'] == 'ES1']
appearances_data_ESP = appearances_data[appearances_data['competition_id'] == 'ES1']
```

#### Data Cleaning and Feature Engineering

Key transformations done on this work included:

- **Filling missing attendance**
using home club averages:
```python
games_data_ESP['attendance'] = games_data_ESP.groupby('home_club_id')['attendance'].transform(
lambda x: x.fillna(x.mean())
```
- **Standardizing match formations** and correcting wrong entries:
```python
def fix_formation(x):
    if '/' in x and len(x.split('/')[-1]) == 4:  
        formation_parts = x.split('/')[:-1] 
        year_last_digit = x.split('/')[-1][-1] 
        formation_parts.append(year_last_digit)
        return '-'.join(formation_parts)
    return x
games_data_ESP['home_club_formation'] = games_data_ESP['home_club_formation'].apply(fix_formation)
```
- **Deriving performance metrics** like:
  - home points, away points
  - home advantage ratio
  - attendance percentage etc.
```python
games_data_ESP['home_points'] = games_data_ESP.apply(lambda row: 3 if row['home_club_goals'] > row['away_club_goals'] else 1 if row['home_club_goals'] == row['away_club_goals'] else 0, axis=1)
```
#### Key Questions Explored
- Which club maximizes **home advantage** ?
- Does larger stadium mean **larger attendance percentage**?
- Does **larger crowd** really improve team performance?
- Do **younger squads** perform better at home?
- How do **discipline patterns(cards)** differ with crowd size?
- Do **certain managers** consistently deliver at home?

#### Analysis Highlights
- **Larger stadiums attract bigger crowds** and clubs with **higher attendance typically perform better at home**.
- **Home teams receive fewer cards when attendance is high** suggesting crowd induced discipline and confidence.
- **Younger squads** not only score more goals but also **attract more fans**.
- Surprisingly, **spending more on transfers does not strongly correlate with home performance**.
- **Home advantage ratio** reveals teams that dominate at home but underperform away.

#### Visualizations and Insight
This project includes many Matplotlib/Seaborn Visuals which include:
1. **Correlation Matrix**
   
   This visualizes the relationship between the various factors in the dataset.
   ```python
   plt.figure(figsize=(8, 8))
   sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', square=True, fmt='.2f', linewidths=0.5)
   plt.show()
   ```
![Corr Matrix](https://github.com/user-attachments/assets/06549ef3-a32a-4ccc-a828-9a6fa10da0ff)
*fig 1: Correlation Analysis*


    The heatmap above shows some correlations. There's a strong positive correlation between stadium capacity and attendance, then between attendance and team performance.      However, attendance negatively correlates with home yellow cards and shows a slight negative trend with away yellow cards. There is also a slight negative relationship      between average age and both attendance and overall performance. Interestingly, high attendance percentages(filling up stadiums) don’t necessarily lead to better team       performance, and factors like squad size and transfer spending don’t show a strong connection to performance either.

2. **Scatter Plots**
   
   To further illustrate the various relationships shown on the correlationship matrix, the scatter plot is used. This showed the relationships between
   - Home Performance(goals and points) and attendance
   - Home Yellow cards and attendance
   - Attendance Percentage and Home Performance.

![ScatterPlot](https://github.com/user-attachments/assets/a9404b3c-8560-4c4f-b5c8-ba077029d6e6)
*fig 2: Scatter plots*

    The scatter plots further confirm the findings of the correlation analysis; that home team performance is highly directly impacted by stadium capacity and average           attendance while the inverse is the case for the away team. It further shows that filling up smaller stadiums to the 'brim' does not clearly impact home team                performance.

3. **Home Adavantage Ratio and Home Team Performance**
   
   Due to the established correlation between Home Team performance and Average attendance I visualized the top ten performing teams by home points and the top ten teams by
   attendance. This is to get a visual presentation of the top performing teams on both metrics and compare where they fall on both. Also, to better quantify home advantage
   on team performance in the Spanish football league, I calculated and visualized the **Home Advantage Ratio**, a metric got by dividing each team's home points by their
   total points. This brings to light how some teams capitalize majorly on home support but falter away from home.
   ```python
   # Home Advantage Ratio
   games_data_ESP['total_points'] = games_data_ESP['home_points'] + games_data_ESP['away_points']
   home_advantage_ratio = games_data_ESP.groupby('home_club_name')[['home_points', 'total_points']].sum().reset_index()
   home_advantage_ratio['home_advantage_ratio'] = home_advantage_ratio['home_points'] / home_advantage_ratio['total_points']
   ```
   ![Bar chart Home adv](https://github.com/user-attachments/assets/f8fdedab-dd98-4c06-ba58-855fd9e9fbc2)
   *fig 3: Bar Charts(Home advantage ratio)*

    ![Attendance %](https://github.com/user-attachments/assets/0a10f74b-275b-4319-b277-2ffd8502dee2)
   
   *fig 4: Bar Chart(Home attendance percentage)*


   The bar charts above show that 8/10 teams with the highest average attendance have the highest points gathered in home matches. Also, 8/10 teams with highest average
   attendance have over 0.55 home advantage ratio. **This shows significant reliance of these teams with high home attendance on home support to win matches**.                 Comparitively, only 4/10 teams with the highest home attendance percentage have high home points, this shows that **home attendance percentage is not a metric that          significantly impacts home team performance**.

 5. **Coach Impact Analysis(Home vs Away)**
    
    No football performance analysis in football is complete without considering the impact of coaches. Therefore, to better understand the dynamics of home advantage, I        conducted an analysis of coach performances based on average points won in home and away games, focusing on managers with a minimum of 9 home games managed. This            ensures that the insights drawn are based on consistent, long-term trends rather than short-term fluctuations.

    ```python
    manager_team_matrix_home = team_manager_performance_home_filtered.pivot_table(index='home_club_manager_name', columns='home_club_name', values='home_points')
    plt.figure(figsize=(5,5))
    sns.heatmap(manager_team_matrix_home, annot=True, cmap='coolwarm', square=True, linewidths=0.5, linecolor='black')
    plt.title('Top 20 Manager-Team Performance (Home Games)')
    plt.show()
    ```


     ![Manager by team(home)](https://github.com/user-attachments/assets/511b68ed-d211-4b82-aff1-f936ca7cbf8e)
    
    *fig 5: Heatmap(Coach performance Home)*
    
     ![Manager by team(away)](https://github.com/user-attachments/assets/f3ce0dda-cb12-43fb-9254-43d28b5554e4)
    
    *fig 6: Heatmap(Coach performance Away)*

    The Heatmap above shows that managers/coaches also influence home team performance in the league to varying extents, highlighting the need for thorough profiling by         teams before recruitment. Some managers are seen to perform exceedingly well in home games but not away, some kept fair home records across many teams, few performed        better away from home while others did poorly at home in one team and well in another.
    
## Conclusion

This analysis confirms that **home advantage in Spanish La Liga is real and measurable**. Clubs with larger stadiums and higher attendance tend to earn more points and concede fewer goals and cards at home, while younger squads boost both goal output and fan turnout. Although big transfer spends and squad size don’t guarantee success, tactical approach, often driven by certain managers play important role.

By combining metrics like **home advantage ratio**, **attendance percentage**, and **manageral points per game**, I’ve built a framework that can inform coaching strategies, match-day engagement, and long-term squad planning. With richer data (xG, tactical events, referee profiling), this foundation could grow into a powerful tool for clubs, analysts, and fans alike.

## Recommendations
Based on the findings in this project, here are a few suggestions for clubs, analysts, and decision-makers in Spanish football:

1. **Prioritize Attendance Growth**
Teams that consistently draw large crowds perform better at home. Clubs should invest in fan engagement, ticket incentives, local outreach to increase turnout and subsequently invest in larger stadiums.

2. **Use Youth Strategically**
Younger squads showed stronger attacking output and drew bigger crowds. Clubs should consider balancing experience with younger, more dynamic players, especially in home matches.

3. **Don’t Rely on Big Transfers Alone**
Transfer spending didn’t directly correlate with better results. Clubs should focus on tactical cohesion and fit especially with coach recruitments rather than simply spending more in the market.


## Data Limitations and Opportunities for Expansion
While the current dataset gives valuable insights, more enriched data could enhance future versions of this project:
- Expected Goals and Shots data (xG)
  This dataset lacks expected goals, possession or shot quality metrics, these could have seperated lucky wins from actual incredible home performances.
- Atmosphere and Fan Influence
  While attendance figures capture qauntity, they don't capture quality or intensity. The psychological effects of crowd energy, chants and noise levels is unaccounted for.
  Integrating sentiment data, crowd noise measured in decibels or fan behavior would be a good step forward.
- Referee Behavior
  Athough referee names are captured, their match behavior could also be added and analysed to account for penalties and cards.

## Data Source & Licensing

The data used in this project was sourced from the following Kaggle dataset:

**[Player Scores and Match Events Dataset by David Cariboo](https://www.kaggle.com/datasets/davidcariboo/player-scores)**

> This dataset includes player appearances, match outcomes, clubs, stadiums, managers, events, and more from European football leagues between 2012 and 2023. The data is freely available for analysis and educational purposes under the [Kaggle Datasets License](https://www.kaggle.com/datasets/davidcariboo/player-scores/license).

Please refer to the original Kaggle page for attribution, updates, or further details.

## References

- David Cariboo (Kaggle Dataset Author):
[Player Scores Dataset](https://www.kaggle.com/datasets/davidcariboo/player-scores)

- La Liga Historical Fixtures and Results:
Used indirectly for performance context and club references.


## Author 

### Micah Ndubuisi Ezirim 
Data Analyst | Lagos, Nigeria

[linkedIn](https://www.linkedin.com/in/ezirim-micah-268955241)




      
   



