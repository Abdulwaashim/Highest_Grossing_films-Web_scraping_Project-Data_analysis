# Highest-Grossing Movies Analysis using Python

## Project Overview
This project analyzes the highest-grossing movies using data scraped from Wikipedia. The analysis includes web scraping, data cleaning, and visualization using Python libraries such as BeautifulSoup, Pandas, Matplotlib, and Seaborn.

## Dataset Extraction (Web Scraping)
We used the `requests` and `BeautifulSoup` libraries to scrape movie data from Wikipedia.

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

url = "https://en.wikipedia.org/wiki/List_of_highest-grossing_films#Highest-grossing_films_by_year"

response = requests.get(url)
soup = BeautifulSoup(response.text, "html.parser")

tables = soup.find_all("table", {"class": "wikitable"})
highest_grossing_table = tables[2] 

rows = highest_grossing_table.find_all("tr")

movie_data = []

for row in rows[1:]: 
    cols = row.find_all(["th", "td"])
    if len(cols) < 5:
        continue  
    
    year = cols[0].text.strip()
    title = cols[1].text.strip() 
    worldwide_gross = cols[2].text.strip()
    budget = cols[3].text.strip() 

    movie_data.append([year, title, worldwide_gross, budget])

df = pd.DataFrame(movie_data, columns=["Year", "Title", "Worldwide Gross", "Budget"])
print(df.head())
```

## Data Cleaning
To prepare the data for analysis, we removed unwanted characters and converted monetary values to numerical format.

```python
df["Worldwide Gross"] = df["Worldwide Gross"].str.replace(r'[^\d.]', '', regex=True)
df["Budget"] = df["Budget"].str.replace(r'[^\d.]', '', regex=True)

df["Worldwide Gross"] = pd.to_numeric(df["Worldwide Gross"], errors='coerce')
df["Budget"] = pd.to_numeric(df["Budget"], errors='coerce')
```

## Data Analysis & Visualization
We analyzed trends in movie revenue and budget over time.

### Worldwide Gross vs Budget Scatter Plot
```python
import matplotlib.pyplot as plt
import seaborn as sns

plt.figure(figsize=(10, 6))
sns.scatterplot(x=df['Budget'], y=df['Worldwide Gross'])
plt.xlabel('Budget')
plt.ylabel('Worldwide Gross')
plt.title('Budget vs Worldwide Gross')
plt.show()
```

![Scatter Plot](https://github.com/Abdulwaashim/Highest_Grossing_films-Web_scraping_Project-Data_analysis/blob/main/Output/img-01.png)


### Highest Grossing Movies Over the Years
```python
plt.figure(figsize=(12, 6))
sns.lineplot(x=df['Year'], y=df['Worldwide Gross'], marker='o')
plt.xlabel('Year')
plt.ylabel('Worldwide Gross')
plt.title('Highest-Grossing Movies Over the Years')
plt.xticks(rotation=45)
plt.show()
```

![Line Chart]()

### Count of Highest Grossing Movies by Decade
```python
df['Decade'] = (df['Year'].astype(int) // 10) * 10
plt.figure(figsize=(10, 6))
sns.countplot(x=df['Decade'], palette='viridis')
plt.xlabel('Decade')
plt.ylabel('Count of Movies')
plt.title('Count of Highest Grossing Movies by Decade')
plt.show()
```

![Decade Count](https://github.com/Abdulwaashim/Highest_Grossing_films-Web_scraping_Project-Data_analysis/blob/main/Output/img-04.png)

### Top 10 Most Profitable Movies
```python
df['Profit'] = df['Worldwide Gross'] - df['Budget']
top_profitable_movies = df.sort_values(by='Profit', ascending=False).head(10)
plt.figure(figsize=(12, 6))
sns.barplot(y=top_profitable_movies['Title'], x=top_profitable_movies['Profit'], palette='Blues')
plt.xlabel('Profit')
plt.ylabel('Movie Title')
plt.title('Top 10 Most Profitable Movies')
plt.show()
```

![Profit Bar Chart](https://github.com/Abdulwaashim/Highest_Grossing_films-Web_scraping_Project-Data_analysis/blob/main/Output/img-05.png)

### Inflation-Adjusted Gross of Highest-Grossing Films
```python
inflation_rate = 1.5  # Example adjustment factor
df['Adjusted Gross'] = df['Worldwide Gross'] * inflation_rate
plt.figure(figsize=(12, 6))
sns.lineplot(x=df['Year'], y=df['Adjusted Gross'], marker='o')
plt.xlabel('Year')
plt.ylabel('Inflation-Adjusted Gross')
plt.title('Inflation-Adjusted Gross of Highest-Grossing Films')
plt.xticks(rotation=45)
plt.show()
```

![Inflation-Adjusted Gross](https://github.com/Abdulwaashim/Highest_Grossing_films-Web_scraping_Project-Data_analysis/blob/main/Output/img-06.png)

### Average Return on Investment per Decade
```python
df['ROI'] = (df['Worldwide Gross'] - df['Budget']) / df['Budget']
avg_roi_per_decade = df.groupby('Decade')['ROI'].mean()
plt.figure(figsize=(10, 6))
sns.barplot(x=avg_roi_per_decade.index, y=avg_roi_per_decade.values, palette='coolwarm')
plt.xlabel('Decade')
plt.ylabel('Average ROI')
plt.title('Average Return on Investment per Decade')
plt.show()
```

![ROI Chart](https://github.com/Abdulwaashim/Highest_Grossing_films-Web_scraping_Project-Data_analysis/blob/main/Output/img-07.png)

## Key Insights & Recommendations
1. **Increasing Budget Can Improve Revenue**: Movies with higher budgets tend to have higher worldwide gross revenue.
2. **Top Movies Dominate Revenue**: The top 10 highest-grossing movies contribute a significant percentage of total revenue.
3. **Revenue Growth Over Time**: There is a clear upward trend in gross revenue of top movies over the years.
4. **1990s and 2000s Dominated Box Office**: The highest count of top-grossing movies was released during these decades.
5. **High-Profit Movies Are Not Always High Budget**: Some lower-budget films have exceptionally high profit margins.
6. **Inflation-Adjusted Gross Shows Growth**: Accounting for inflation, older films still remain among the highest-grossing.
7. **ROI is Higher for Certain Decades**: The average return on investment was highest in earlier decades, suggesting lower production costs and high revenue margins.

## How to Run the Code
1. Install dependencies:
   ```bash
   pip install requests beautifulsoup4 pandas matplotlib seaborn
   ```
2. Run the Jupyter Notebook or Python script.

---
This project showcases data scraping, cleaning, visualization, and analysis to derive business insights for the film industry.

