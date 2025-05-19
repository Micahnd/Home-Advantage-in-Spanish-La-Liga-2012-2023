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

### Filtering for La Liga
```python
games_data_ESP = games_data[games_data['competition_id'] == 'ES1']
clubs_data_ESP = clubs_data[clubs_data['domestic_competition_id'] == 'ES1']
appearances_data_ESP = appearances_data[appearances_data['competition_id'] == 'ES1']
```

### Data Cleaning and Feature Engineering

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
### Key Questions Explored
- Which club maximizes **home advantage** ?
- Does larger stadium mean **larger attendance percentage**?
- Does **larger crowd** really improve team performance?
- Do **younger squads** perform better at home?
- How do **discipline patterns(cards)** differ with crowd size?
- Do **certain managers** consistently deliver at home?

### Analysis Highlights
- **Larger stadiums attract bigger crowds** and clubs with **higher attendance typically perform better at home**.
- **Home teams receive fewer cards when attendance is high** suggesting crowd induced discipline and confidence.
- **Younger squads** not only score more goals but also **attract more fans**.
- Surprisingly, **spending more on transfers does not strongly correlate with home performance**.
- **Home advantage ratio** reveals teams that dominate at home but underperform away.

### Visualizations
This project includes many Matplotlib/Seaborn Visuals which include:
1. **Correlation Matrix**
   This visualizes the relationship between the various factors in the dataset.
   ```python
   plt.figure(figsize=(8, 8))
   sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', square=True, fmt='.2f', linewidths=0.5)
   plt.show()
   ```
![Corr Matrix](https://github.com/user-attachments/assets/06549ef3-a32a-4ccc-a828-9a6fa10da0ff)


The heatmap above shows several key correlations within the data. There is a strong positive correlation between stadium capacity (number of seats) and attendance. Conversely, a strong negative correlation exists between attendance and the number of home yellow cards, while a slight negative correlation also exists with away yellow cards. There is a slight negative correlation between average age and attendance+overall performance and high attendance percentage does not appear to correlate with improved team performance. Interestingly, squad size and higher transfer spending do not show a significant correlation with team performance. 

