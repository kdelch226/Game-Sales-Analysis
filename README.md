What are the top 5 platforms with the highest global sales?
top_platforms = clean_df.groupby('Platform')['Global_Sales'].sum().sort_values(ascending=True).tail(5)
top_platforms
Platform
DS       817.89
Wii      909.20
PS3      949.39
X360     969.30
PS2     1233.56
Name: Global_Sales, dtype: float64
# Plot the top 5 Platforms with the highest global sales as a bar chart
top_platforms.plot(kind='bar', figsize=(10, 6),color='coral')
plt.title('Top 5 Platforms with Highest Global Sales')
plt.ylabel('Average Sales (in millions)')
plt.xlabel('Platforms')
plt.show()

What time period between 1980 and 2020 saw the highest global sales?
# Filter data between 1980 et 2020
df_filtered = clean_df[(clean_df['Year'] >= 1980) & (clean_df['Year'] <= 2020)]

# Groupe by year
region_sales_sum = df_filtered.groupby('Year')['Global_Sales'].sum()

fig, ax1 = plt.subplots(figsize=(25,7))
ax1.set_xticks(region_sales_sum.index)  
ax1.errorbar(region_sales_sum.index,region_sales_sum, yerr=region_sales_sum/5)
plt.ylabel('Average Sales (in millions)')
plt.xlabel('Years')
plt.show()

we can see that the time period between 2005 and 2009 saw the highest global sales, peaking in 2008 with total sales of 678.49 million. This 5-year range represents the golden era of the video game industry, where sales consistently grew and remained at their highest levels.


How do sales trends vary across the four major regions (NA, EU, JP, Other) from 1980 to 2020?
regions_df = df_filtered.groupby('Year')[['NA_Sales','EU_Sales','JP_Sales','Other_Sales']].mean()  

fig, ax2 = plt.subplots(figsize=(15,7))
ax2.errorbar(regions_df.index,regions_df['NA_Sales'],color='black',label='NA_Sales')
ax2.errorbar(regions_df.index,regions_df['JP_Sales'],color='red',label='JP_Sales')
ax2.errorbar(regions_df.index,regions_df['EU_Sales'],color='blue',label='EU_Sales')
ax2.errorbar(regions_df.index,regions_df['Other_Sales'],color='green',label='Other_Sales')
plt.ylabel('Total Sales (in millions)')
plt.xlabel('Regions')
plt.legend()
plt.show()


North America has consistently led in sales since 1980. However, since 1995, sales across all regions have declined to around 0.25 million units, likely due to the rise of online gaming and digital platforms

How have platform and genre sales evolved over time, and which combinations perform best?
evolution by genre
# prepare data by genre
genre_sales_over_time = clean_df.groupby(['Year', 'Genre'])['Global_Sales'].sum().reset_index()

# Identify the top  5 and 3 worst genres
top_genres_first_7 = genre_sales_over_time.groupby('Genre')['Global_Sales'].sum().nlargest(7).index
top_genres_last_3 = genre_sales_over_time.groupby('Genre')['Global_Sales'].sum().nsmallest(3).index

top_genres_combined = top_genres_first_7.append(top_genres_last_3)

# Filtrer les données pour ne conserver que les genres les plus populaires
filtered_data_genre_sales_over_time = genre_sales_over_time[genre_sales_over_time['Genre'].isin(top_genres_combined)]
plt.figure(figsize=(30,10))

# a line plot for each genre ()easier with seaborn
sns.lineplot(data=filtered_data_genre_sales_over_time, x='Year', y='Global_Sales', hue='Genre')
plt.title('Évolution des ventes par genre', fontsize=16)
plt.xlabel('Année', fontsize=14)
plt.ylabel('Ventes globales (en millions)', fontsize=14)

# Afficher la légende et le graphique
plt.legend(title='Genre')
plt.show()

Global video game sales experienced significant growth starting from the 2000s, reaching peaks between 2010 and 2015, with genres such as Action, Shooter, and Role-Playing dominating the sales charts.

n contrast, genres like Puzzle and Strategy showed relatively low sales throughout the period. However, after 2015, a decline in sales was observed across almost all genres, which could indicate a shift in consumer preferences, possibly linked to the rise of mobile games or market saturation. For better readability, the analysis focused on the top 7 most popular and top 3 worst genres.

# prepare data by Platform
platform_sales_over_time = clean_df.groupby(['Year','Platform'])['Global_Sales'].sum().reset_index()

# Identify the top  5 and 3 worst platform
top_platforms_first_7 = platform_sales_over_time.groupby('Platform')['Global_Sales'].sum().nlargest(7).index
top_platforms_last_3 = platform_sales_over_time.groupby('Platform')['Global_Sales'].sum().nsmallest(3).index
top_platforms_combined = top_platforms_first_7.append(top_platforms_last_3)

filtered_data_platform_sales_over_time = platform_sales_over_time[platform_sales_over_time['Platform'].isin(top_platforms_combined)]

plt.figure(figsize=(30,10))

# a line plot for each genre ()easier with seaborn
sns.lineplot(data=filtered_data_platform_sales_over_time, x='Year', y='Global_Sales', hue='Platform')
plt.title('Évolution des ventes par Platform', fontsize=16)
plt.xlabel('Année', fontsize=14)
plt.ylabel('Ventes globales (en millions)', fontsize=14)

# Afficher la légende et le graphique
plt.legend(title='Genre')
plt.show()

Between 1985 and 2020, many gaming platforms emerged, with some quickly disappearing due to factors like high production costs and limited game libraries. Platforms like the 3DO and Sega Saturn struggled to compete with giants like Sony's PlayStation and Microsoft's Xbox, leading to their decline.

In contrast, consoles launched between 2000 and 2010, such as the Xbox 360, PlayStation 3, and Nintendo Wii, succeeded due to their innovative features like online play and motion controls. However, platforms like the PSP and Wii U, while initially popular, faded may be due to issues like limited game support and competition from newer technologies.

This highlights the fast-changing nature of the gaming industry, where only the most adaptable platforms survive.

best_combinations = df.groupby(['Platform', 'Genre'])['Global_Sales'].sum().sort_values(ascending=False).reset_index()
best_combinations.head()
Platform	Genre	Global_Sales
0	PS3	Action	307.88
1	Wii	Sports	292.06
2	X360	Shooter	278.55
3	PS2	Sports	273.41
4	PS2	Action	272.76
The top platform-genre combinations highlight key industry trends.

The PS3 with Action games leads sales, reflecting its dominance in action titles. The Wii excelled in the Sports genre, benefiting from its motion controls and casual gaming appeal. The X360 thrived with Shooter games, emphasizing its role in popularizing the genre. Lastly, the PS2 was strong in both Sports and Action, showing its broad appeal across genres.

These combinations illustrate how certain platforms were key to the success of specific game genres.

Which variable among 'Genre', 'Publisher', and 'Platform' most influences high global sales?
Encoding categorical variables
# Encode categorical variables (Genre, Publisher, Platform)
df_encoded = pd.get_dummies(df_filtered[['Genre', 'Publisher', 'Platform']], drop_first=True)

# Add global sales to the encoded DataFrame to calculate correlation
df_encoded['Global_Sales'] = df_filtered['Global_Sales']
df_encoded.head()
Genre_Adventure	Genre_Fighting	Genre_Misc	Genre_Platform	Genre_Puzzle	Genre_Racing	Genre_Role-Playing	Genre_Shooter	Genre_Simulation	Genre_Sports	...	Platform_SCD	Platform_SNES	Platform_TG16	Platform_WS	Platform_Wii	Platform_WiiU	Platform_X360	Platform_XB	Platform_XOne	Global_Sales
0	False	False	False	False	False	False	False	False	False	True	...	False	False	False	False	True	False	False	False	False	82.74
1	False	False	False	True	False	False	False	False	False	False	...	False	False	False	False	False	False	False	False	False	40.24
2	False	False	False	False	False	True	False	False	False	False	...	False	False	False	False	True	False	False	False	False	35.83
3	False	False	False	False	False	False	False	False	False	True	...	False	False	False	False	True	False	False	False	False	33.00
4	False	False	False	False	False	False	True	False	False	False	...	False	False	False	False	False	False	False	False	False	31.38
5 rows × 617 columns

Calculate the Correlation Matrix
correlation_matrix = df_encoded.corr()

# Display the correlation between the encoded variables and global sales
correlation_matrix['Global_Sales'].sort_values(ascending=False).head()
Global_Sales          1.000000
Publisher_Nintendo    0.272650
Platform_GB           0.102821
Platform_NES          0.100312
Genre_Platform        0.061796
Name: Global_Sales, dtype: float64
Specific Visualization of Correlation with Global Sales
# Display the correlation with global sales
correlation_with_sales = correlation_matrix['Global_Sales'].drop('Global_Sales')  # Remove 'Global_Sales' from the list

plt.figure(figsize=(10, 6))
correlation_with_sales.sort_values(ascending=True).tail(15).plot(kind='barh', color='skyblue')
plt.title('Correlation of Variables with Global Sales')
plt.xlabel('Correlation Coefficient')
plt.ylabel('Variables')
plt.show()

Among the Genre, Publisher, and Platform variables, Publisher_Nintendo shows the highest correlation with global sales (0.272). However, this doesn't fully determine which variable has the greatest impact, as correlation alone doesn’t imply direct influence.

More analysis is needed to identify the variable with the most significant effect.

Calculate the total correlation for each category (Publisher, Genre, Platform)
Selection deleted
# Calculate the average correlation for each group (Publisher, Platform, Genre)
# extracts the correlations between 'Global_Sales' and all columns containing 'Publisher' in their names, 
# then calculates the mean of these correlations.
publisher_corr = correlation_matrix['Global_Sales'][df_encoded.columns.str.contains('Publisher')].mean()
platform_corr = correlation_matrix['Global_Sales'][df_encoded.columns.str.contains('Platform')].mean()
genre_corr = correlation_matrix['Global_Sales'][df_encoded.columns.str.contains('Genre')].mean()



# Create a series to compare the mean correlation for Publisher, Genre, and Platform
mean_corr = pd.Series({
    'Publisher': publisher_corr,
    'Genre': genre_corr,
    'Platform': platform_corr
})
# Visualize the mean correlations with a bar plot
plt.figure(figsize=(8, 6))
mean_corr.sort_values(ascending=True).plot(kind='barh', color=['lightcoral', 'lightgreen', 'skyblue'])
plt.title('Average correlations between each group and Global Sales')
plt.xlabel('average correlation')
plt.ylabel('Variables')
plt.show()

mean_corr
Publisher   -0.003353
Genre       -0.000233
Platform     0.005524
dtype: float64
Among the Publisher, Genre, and Platform variables, Platform shows the highest correlation with global sales, followed by Genre and Publisher. Although Platform has the highest correlation, these values are relatively low, indicating that none of these variables have a strong direct impact on global sales. maybe other factors

Predict Sales for top 5 publishers
