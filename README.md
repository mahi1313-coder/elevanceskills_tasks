# elevanceskills_tasks
Build real-time Google Play Store analytics using Python. This project features a live pipeline that scrapes app data via google-play-scraper, processes it with Pandas, and performs NLP sentiment analysis on reviews. Visualize dynamic insights and trends through an interactive Streamlit dashboard for data-driven decisions.


import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime
import pytz

# Sample data simulation (replace with actual data source)
data = {
    'Category': ['Games', 'Social', 'Productivity', 'Tools', 'Entertainment', 'Education', 'Health', 'Finance', 'Shopping', 'Travel', 'Music', 'Books', 'News', 'Weather', 'Lifestyle'],
    'Avg_Rating': [4.2, 4.1, 4.3, 3.9, 4.0, 4.4, 4.1, 4.2, 3.8, 4.3, 4.0, 4.1, 3.9, 4.2, 4.0],
    'Total_Reviews': [5000000, 3000000, 2000000, 1500000, 4000000, 1000000, 800000, 1200000, 600000, 900000, 700000, 500000, 400000, 300000, 200000],
    'Installs': [100000000, 80000000, 60000000, 50000000, 40000000, 30000000, 25000000, 20000000, 15000000, 10000000, 8000000, 6000000, 5000000, 4000000, 3000000],
    'Size_MB': [15, 12, 20, 8, 18, 25, 14, 16, 9, 22, 11, 13, 7, 10, 19],
    'Last_Update': ['2023-01-15', '2023-01-20', '2023-02-10', '2023-01-05', '2023-01-25', '2023-01-30', '2023-03-01', '2023-01-10', '2023-01-12', '2023-01-18', '2023-02-05', '2023-01-22', '2023-01-08', '2023-01-14', '2023-01-28']
}

df = pd.DataFrame(data)
df['Last_Update'] = pd.to_datetime(df['Last_Update'])

# Filters
df_filtered = df[(df['Avg_Rating'] >= 4.0) & (df['Size_MB'] >= 10) & (df['Last_Update'].dt.month == 1)]

# Top 10 by Installs
df_top10 = df_filtered.nlargest(10, 'Installs')

# Time check for IST (Indian Standard Time, UTC+5:30)
ist = pytz.timezone('Asia/Kolkata')
now = datetime.now(ist)
current_hour = now.hour

if 15 <= current_hour < 17:  # 3PM to 5PM IST
    # Create grouped bar chart
    fig, ax = plt.subplots(figsize=(12, 6))
    bar_width = 0.35
    index = range(len(df_top10))
    
    bars1 = ax.bar(index, df_top10['Avg_Rating'], bar_width, label='Average Rating', color='skyblue')
    bars2 = ax.bar([i + bar_width for i in index], df_top10['Total_Reviews'] / 1000000, bar_width, label='Total Reviews (in millions)', color='salmon')
    
    ax.set_xlabel('Category')
    ax.set_ylabel('Values')
    ax.set_title('Top 10 App Categories by Installs: Average Rating and Total Reviews')
    ax.set_xticks([i + bar_width / 2 for i in index])
    ax.set_xticklabels(df_top10['Category'], rotation=45)
    ax.legend()
    
    plt.tight_layout()
    plt.show()
else:
    print("The graph is only available between 3PM IST and 5PM IST. Current time:", now.strftime('%H:%M %Z'))
