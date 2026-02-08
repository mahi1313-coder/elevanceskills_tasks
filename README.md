# Use a grouped bar chart to compare the average rating and total review count for the top 10 app categories by number of installs. Filter out any categories where the average rating is below 4.0 and size below 10 M and last update should be Jan month . this graph should work only between 3PM IST to 5 PM IST apart from that time we should not show this graph in dashboard itself.

# code

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

# Time check for IST
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




    # 2
    # Create an interactive Choropleth map using Plotly to visualize global installs by Category. Apply filters to show data for only the top 5 app categories and highlight category where the number of installs exceeds 1 million. The app category should not start with the characters “A,” “C,” “G,” or “S.” This graph should work only between 6 PM IST and 8 PM IST; apart from that time, we should not show it in the dashboard itself.


    # code
    import pandas as pd
import plotly.express as px
from datetime import datetime
import pytz

# Sample data simulation
data = {
    'Country': ['USA', 'India', 'China', 'Brazil', 'Russia', 'USA', 'India', 'China', 'Brazil', 'Russia', 'USA', 'India', 'China', 'Brazil', 'Russia', 'USA', 'India', 'China', 'Brazil', 'Russia', 'USA', 'India', 'China', 'Brazil', 'Russia'],
    'Category': ['Games', 'Games', 'Games', 'Games', 'Games', 'Social', 'Social', 'Social', 'Social', 'Social', 'Productivity', 'Productivity', 'Productivity', 'Productivity', 'Productivity', 'Tools', 'Tools', 'Tools', 'Tools', 'Tools', 'Entertainment', 'Entertainment', 'Entertainment', 'Entertainment', 'Entertainment'],
    'Installs': [50000000, 30000000, 40000000, 20000000, 15000000, 40000000, 25000000, 35000000, 18000000, 12000000, 30000000, 20000000, 25000000, 15000000, 10000000, 25000000, 18000000, 22000000, 12000000, 8000000, 35000000, 22000000, 30000000, 16000000, 11000000]
}

df = pd.DataFrame(data)

# Filters
df_filtered = df[~df['Category'].str.startswith(('A', 'C', 'G', 'S'))]

# Top 5 categories
category_totals = df_filtered.groupby('Category')['Installs'].sum().reset_index()
category_totals = category_totals.sort_values(by='Installs', ascending=False)
top5_categories = category_totals.head(5)['Category'].tolist()
df_top5 = df_filtered[df_filtered['Category'].isin(top5_categories)]

# Highlight
category_totals['Highlight'] = category_totals['Installs'] > 1000000
highlight_dict = dict(zip(category_totals['Category'], category_totals['Highlight']))
df_top5['Highlight'] = df_top5['Category'].map(highlight_dict)

# Time check
ist = pytz.timezone('Asia/Kolkata')
now = datetime.now(ist)
current_hour = now.hour

if 18 <= current_hour < 20:  # 6PM to 8PM IST
    fig = px.choropleth(df_top5, 
                        locations='Country', 
                        locationmode='country names',
                        color='Installs',
                        hover_name='Country',
                        animation_frame='Category',
                        title='Global Installs by Top 5 App Categories (Filtered)',
                        color_continuous_scale='Viridis')
    fig.update_traces(marker=dict(opacity=0.7 if not df_top5['Highlight'].any() else 1.0))
    fig.show()
else:
    print("The graph is only available between 6PM IST and 8PM IST. Current time:", now.strftime('%H:%M %Z'))


    # 3
    # Create a dual-axis chart comparing the average installs and revenue for free vs. paid apps within the top 3 app categories. Apply filters to exclude apps with fewer than 10,000 installs and revenue below $10,000 and android version should be more than 4.0 as well as size should be more than 15M and content rating should be Everyone and app name should not have more than 30 characters including space and special character .this graph should work only between 1 PM IST to 2 PM IST apart from that time we should not show this graph in dashboard itself.

    # code
    import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime
import pytz

# Sample data simulation
data = {
    'App_Name': ['App1', 'App2', 'App3', 'App4', 'App5', 'App6', 'App7', 'App8', 'App9', 'App10', 'App11', 'App12', 'App13', 'App14', 'App15'],
    'Category': ['Games', 'Games', 'Games', 'Social', 'Social', 'Social', 'Productivity', 'Productivity', 'Productivity', 'Entertainment', 'Entertainment', 'Entertainment', 'Tools', 'Tools', 'Tools'],
    'Type': ['Free', 'Paid', 'Free', 'Free', 'Paid', 'Free', 'Paid', 'Free', 'Paid', 'Free', 'Paid', 'Free', 'Free', 'Paid', 'Free'],
    'Installs': [500000, 20000, 300000, 400000, 15000, 250000, 10000, 350000, 5000, 450000, 8000, 200000, 300000, 12000, 150000],
    'Revenue': [50000, 5000, 40000, 60000, 5000, 30000, 5000, 70000, 2000, 80000, 3000, 25000, 45000, 4000, 20000],
    'Android_Version': [5.0, 4.1, 5.1, 4.2, 4.0, 5.2, 4.1, 5.0, 4.0, 5.1, 4.0, 5.0, 4.2, 4.1, 5.0],
    'Size_MB': [20, 16, 18, 22, 14, 19, 12, 25, 10, 21, 11, 17, 23, 13, 16],
    'Content_Rating': ['Everyone', 'Everyone', 'Everyone', 'Everyone', 'Everyone', 'Everyone', 'Everyone', 'Everyone', 'Everyone', 'Everyone', 'Everyone', 'Everyone', 'Everyone', 'Everyone', 'Everyone']
}

df = pd.DataFrame(data)

# Filters
df_filtered = df[
    (df['Installs'] >= 10000) &
    (df['Revenue'] >= 10000) &
    (df['Android_Version'] > 4.0) &
    (df['Size_MB'] > 15) &
    (df['Content_Rating'] == 'Everyone') &
    (df['App_Name'].str.len() <= 30)
]

# Top 3 categories
category_totals = df_filtered.groupby('Category')['Installs'].sum().reset_index()
top3_categories = category_totals.nlargest(3, 'Installs')['Category'].tolist()
df_top3 = df_filtered[df_filtered['Category'].isin(top3_categories)]

# Group and pivot
grouped = df_top3.groupby(['Category', 'Type']).agg({'Installs': 'mean', 'Revenue': 'mean'}).reset_index()
pivot_installs = grouped.pivot(index='Category', columns='Type', values='Installs')
pivot_revenue = grouped.pivot(index='Category', columns='Type', values='Revenue')

# Time check
ist = pytz.timezone('Asia/Kolkata')
now = datetime.now(ist)
current_hour = now.hour

if 13 <= current_hour < 14:  # 1PM to 2PM IST
    fig, ax1 = plt.subplots(figsize=(10, 6))
    pivot_installs.plot(kind='bar', ax=ax1, position=0, width=0.4, color=['skyblue', 'lightcoral'], alpha=0.7)
    ax1.set_ylabel('Average Installs', color='blue')
    ax1.tick_params(axis='y', labelcolor='blue')
    
    ax2 = ax1.twinx()
    pivot_revenue.plot(kind='bar', ax=ax2, position=1, width=0.4, color=['green', 'orange'], alpha=0.7)
    ax2.set_ylabel('Average Revenue ($)', color='red')
    ax2.tick_params(axis='y', labelcolor='red')
    
    ax1.set_xlabel('Category')
    ax1.set_title('Average Installs and Revenue for Free vs. Paid Apps in Top 3 Categories')
    ax1.legend(title='Installs', loc='upper left')
    ax2.legend(title='Revenue', loc='upper right')
    
    plt.tight_layout()
    plt.show()
else:
    print("The graph is only available between 1PM IST and 2PM IST. Current time:", now.strftime('%H:%M %Z'))


    #4
    # Plot a time series line chart to show the trend of total installs over time, segmented by app category. Highlight periods of significant growth by shading the areas under the curve where the increase in installs exceeds 20% month-over-month and app name should not starts with x, y ,z and app category should start with letter " E " or " C " or " B " and We have to translate the Beauty category in Hindi and Business category in Tamil and Dating category in German while showing it on Graph. reviews should be more than 500 the app name should not contain letter "S" as well as this graph should work only between 6 PM IST to 9 PM IST apart from that time we should not show this graph in dashboard itself
    # code

    import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime
import pytz

# Sample data simulation (replace with actual data source)
# Assuming time series data with monthly installs
dates = pd.date_range(start='2023-01-01', periods=12, freq='M')
categories = ['Entertainment', 'Communication', 'Business', 'Beauty']  # Filtered to start with E, C, B
data = []
for cat in categories:
    for date in dates:
        installs = 100000 + (hash(cat + str(date)) % 50000)  # Simulated installs
        data.append({'Date': date, 'Category': cat, 'Installs': installs, 'Reviews': 600, 'App_Name': 'SampleApp'})  # Reviews >500, app name not starting with x,y,z and not containing S

df = pd.DataFrame(data)

# Filters
df_filtered = df[
    (~df['App_Name'].str.startswith(('x', 'y', 'z'))) &
    (~df['App_Name'].str.contains('S')) &
    (df['Reviews'] > 500) &
    (df['Category'].str.startswith(('E', 'C', 'B')))
]

# Translate categories for display
translation_dict = {
    'Beauty': 'सौंदर्य',  # Hindi
    'Business': 'வணிகம்',  # Tamil
    'Dating': 'Dating'  # German, but Dating not in filtered categories
}
df_filtered['Category_Display'] = df_filtered['Category'].map(lambda x: translation_dict.get(x, x))

# Aggregate total installs per category per month
df_agg = df_filtered.groupby(['Date', 'Category_Display'])['Installs'].sum().reset_index()

# Calculate month-over-month growth
df_agg = df_agg.sort_values(['Category_Display', 'Date'])
df_agg['Prev_Installs'] = df_agg.groupby('Category_Display')['Installs'].shift(1)
df_agg['Growth'] = (df_agg['Installs'] - df_agg['Prev_Installs']) / df_agg['Prev_Installs'] * 100
df_agg['Significant_Growth'] = df_agg['Growth'] > 20

# Time check for IST (Indian Standard Time, UTC+5:30)
ist = pytz.timezone('Asia/Kolkata')
now = datetime.now(ist)
current_hour = now.hour

if 18 <= current_hour < 21:  # 6PM to 9PM IST
    # Create time series line chart
    fig, ax = plt.subplots(figsize=(12, 6))
    
    for cat in df_agg['Category_Display'].unique():
        cat_data = df_agg[df_agg['Category_Display'] == cat]
        ax.plot(cat_data['Date'], cat_data['Installs'], label=cat, marker='o')
        
        # Shade areas where growth >20%
        growth_periods = cat_data[cat_data['Significant_Growth']]
        if not growth_periods.empty:
            for _, row in growth_periods.iterrows():
                ax.fill_between([row['Date'], row['Date']], 0, row['Installs'], alpha=0.3, color='yellow')
    
    ax.set_xlabel('Date')
    ax.set_ylabel('Total Installs')
    ax.set_title('Trend of Total Installs Over Time by Category (with Significant Growth Highlighted)')
    ax.legend()
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()
else:
    print("The graph is only available between 6PM IST and 9PM IST. Current time:", now.strftime('%H:%M %Z'))

    # 5
    # Plot a bubble chart to analyze the relationship between app size (in MB) and average rating, with the bubble size representing the number of installs. Include a filter to show only apps with a rating higher than 3.5 and that belong to the Game, Beauty ,business , commics , commication , Dating , Entertainment , social and event categories. Reviews should be greater than 500 and the app name should not contain letter "S" and sentiment subjectivity should be more than 0.5 and highlight the Game Category chart in Pink color. We have to translate the Beauty category in Hindi and Business category in Tamil and Dating category in German while showing it on Graphs. Installs should be more than 50k as well as this graph should work only between 5 PM IST to 7 PM IST apart from that time we should not show this graph in dashboard itself.

    # code

    import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime
import pytz

data = {
    'App_Name': ['GameApp1', 'BeautyApp1', 'BusinessApp1', 'ComicsApp1', 'CommunicationApp1', 'DatingApp1', 'EntertainmentApp1', 'SocialApp1', 'EventApp1', 'GameApp2', 'BeautyApp2'],
    'Category': ['Game', 'Beauty', 'Business', 'Comics', 'Communication', 'Dating', 'Entertainment', 'Social', 'Event', 'Game', 'Beauty'],
    'Size_MB': [20, 15, 25, 18, 22, 16, 19, 21, 17, 23, 14],  # Some below/above for filter
    'Avg_Rating': [4.0, 4.2, 3.8, 4.1, 3.9, 4.3, 4.0, 3.7, 4.2, 4.5, 3.6],  # Some <=3.5 to filter
    'Installs': [100000, 60000, 80000, 70000, 90000, 55000, 75000, 65000, 85000, 120000, 45000],  # Some <=50k to filter
    'Reviews': [600, 700, 550, 800, 650, 750, 600, 550, 700, 900, 400],  # Some <=500 to filter
    'Sentiment_Subjectivity': [0.6, 0.7, 0.5, 0.8, 0.6, 0.9, 0.7, 0.5, 0.8, 0.6, 0.4]  # Some <=0.5 to filter
}

df = pd.DataFrame(data)


df_filtered = df[
    (df['Avg_Rating'] > 3.5) &
    (df['Category'].isin(['Game', 'Beauty', 'Business', 'Comics', 'Communication', 'Dating', 'Entertainment', 'Social', 'Event'])) &
    (df['Reviews'] > 500) &
    (~df['App_Name'].str.contains('S')) &
    (df['Sentiment_Subjectivity'] > 0.5) &
    (df['Installs'] > 50000)
]

translation_dict = {
    'Beauty': 'सौंदर्य',  
    'Business': 'வணிகம்', 
    'Dating': 'Verabredung' 
}
df_filtered['Category_Display'] = df_filtered['Category'].map(lambda x: translation_dict.get(x, x))

list = pytz.timezone('Asia/Kolkata')
now = datetime.now(ist)
current_hour = now.hour

if 17 <= current_hour < 19:  
    fig, ax = plt.subplots(figsize=(10, 6))
    

    colors = df_filtered['Category'].apply(lambda x: 'pink' if x == 'Game' else 'blue')
    
    
    scatter = ax.scatter(df_filtered['Size_MB'], df_filtered['Avg_Rating'], 
                         s=df_filtered['Installs']/1000,  # Scale bubble size
                         c=colors, alpha=0.6, edgecolors='w', linewidth=0.5)
    
 
    for i, row in df_filtered.iterrows():
        ax.annotate(row['Category_Display'], (row['Size_MB'], row['Avg_Rating']), 
                    textcoords="offset points", xytext=(0,10), ha='center', fontsize=8)
    
    ax.set_xlabel('App Size (MB)')
    ax.set_ylabel('Average Rating')
    ax.set_title('Bubble Chart: App Size vs. Average Rating (Bubble Size = Installs)')
    
    
    handles = [plt.Line2D([0], [0], marker='o', color='w', markerfacecolor='pink', markersize=10, label='Game'),
               plt.Line2D([0], [0], marker='o', color='w', markerfacecolor='blue', markersize=10, label='Other Categories')]
    ax.legend(handles=handles)
    
    plt.tight_layout()
    plt.show()
else:
    print("The graph is only available between 5PM IST and 7PM IST. Current time:", now.strftime('%H:%M %Z'))

    #6
    # You are required to create a stacked area chart to visualize the cumulative number of installs over time for each app category, with each category represented as a separate color band in the chart. Apply the following filters before plotting: include only apps with an average rating of at least 4.2, app names that do not contain any numbers, app categories that start with the letter “T” or “P,” reviews greater than 1,000, and app sizes between 20 MB and 80 MB. In the chart legend, translate “Travel & Local” into French, “Productivity” into Spanish, and “Photography” into Japanese. Highlight by increasing the color intensity for any month where total installs increased by more than 25% month-over-month for any category. This visualization must only be displayed between 4 PM IST and 6 PM IST, and it should not appear on the dashboard outside this time window.

    #code

    import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime
import pytz


dates = pd.date_range(start='2023-01-01', periods=12, freq='M')
categories = ['Travel & Local', 'Productivity', 'Photography', 'Tools']  # Filtered to start with T or P
data = []
for cat in categories:
    for date in dates:
        installs = 50000 + (hash(cat + str(date)) % 100000)  # Simulated installs
        data.append({
            'Date': date, 
            'Category': cat, 
            'Installs': installs, 
            'Avg_Rating': 4.3,  
            'App_Name': 'SampleApp',
            'Reviews': 1500,  
            'Size_MB': 30  
        })

df = pd.DataFrame(data)

df_filtered = df[
    (df['Avg_Rating'] >= 4.2) &
    (~df['App_Name'].str.contains(r'\d')) &  # No numbers in app name
    (df['Category'].str.startswith(('T', 'P'))) &
    (df['Reviews'] > 1000) &
    (df['Size_MB'].between(20, 80))
]


translation_dict = {
    'Travel & Local': 'Voyage et Local', 
    'Productivity': 'Productividad', 
    'Photography': '写真', 
}
df_filtered['Category_Display'] = df_filtered['Category'].map(lambda x: translation_dict.get(x, x))


df_agg = df_filtered.groupby(['Date', 'Category_Display'])['Installs'].sum().reset_index()


pivot_df = df_agg.pivot(index='Date', columns='Category_Display', values='Installs').fillna(0)

cumulative_df = pivot_df.cumsum()


total_installs = cumulative_df.sum(axis=1)
total_installs_prev = total_installs.shift(1)
growth = (total_installs - total_installs_prev) / total_installs_prev * 100
highlight_months = growth > 25


ist = pytz.timezone('Asia/Kolkata')
now = datetime.now(ist)
current_hour = now.hour

if 16 <= current_hour < 18:  
    fig, ax = plt.subplots(figsize=(12, 6))
    
   
    cumulative_df.plot.area(ax=ax, stacked=True, alpha=0.7)
    
   
    for i, (date, is_highlight) in enumerate(highlight_months.items()):
        if is_highlight:
           
            ax.fill_between([date, date], 0, cumulative_df.loc[date].sum(), alpha=1.0, color='gray')
    
    ax.set_xlabel('Date')
    ax.set_ylabel('Cumulative Installs')
    ax.set_title('Cumulative Installs Over Time by Category (Stacked Area)')
    ax.legend(title='Category')
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()
else:
    print("The graph is only available between 4PM IST and 6PM IST. Current time:", now.strftime('%H:%M %Z'))
