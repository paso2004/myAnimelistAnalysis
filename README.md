# MyAnimeList Ratings Analysis


## Project Background




The realm of anime has captivated audiences worldwide, offering a diverse range of stories, characters, and genres. MyAnimeList (MAL) stands as a cornerstone platform for anime enthusiasts, providing a comprehensive database of anime titles, rankings, and community reviews. Analyzing data from MyAnimeList offers valuable insights into the trends, preferences, and dynamics within the anime community.

In this Python Data Analysis Project, we aim to delve into the extensive dataset provided by MyAnimeList. Our objective is to uncover patterns, trends, and correlations within the data, shedding light on various aspects of anime consumption and reception. By leveraging data analysis techniques and visualization tools, we seek to gain a deeper understanding of the following key areas:

## Problem Statement

- Understanding Viewer Preferences: Identifying the genres, themes, and characteristics that resonate most with anime viewers.

- Assessing Content Reception: Examining the impact of content warnings and broadcast details on viewer ratings and reviews.

- Identifying Top Performers: Determining the top-rated anime titles .

- Exploring Industry Trends: Analyzing release frequencies, seasonal patterns, and viewer engagement metrics to understand the broader trends shaping the anime industry.

- Identifying low performing Animes : Finding the least performing anime.


### Steps followed 

#### Data Loading and Cleaning

- Step 1 : Load the following libraries for Data analysis in Jupyter Notebook :

            import numpy as np
            import pandas as pd
            import matplotlib.pyplot as plt
            import seaborn as sns

- Step 2 : Load the CSV File in the Notebook :
            
            df = pd.read_csv(r'C:\Users\ASUS\Downloads\New Data Sets\Anime.csv')
            
- Step 3 : Check the column names in the dataset :

            df.head()

Snapshot:

![Capture1](https://github.com/paso2004/myAnimelistAnalysis/assets/161154534/3e5b0799-8b05-41da-8353-f70861a42a0a)

- Step 4 : Check the Data type of each column and information as well :

            df.info()

Snapshot :

![Capture2](https://github.com/paso2004/myAnimelistAnalysis/assets/161154534/11924810-0103-46f2-aebb-7092b691f9e8)

- Step 5 : Deleting columns that are not required for analysis. In our case we deleted Description, Related Manga, Related Anime, Voice Actors, Staff :

            df.drop(['Description','Related_Mange','Related_anime','Voice_actors','staff'],axis=1,inplace = True)
            df.head()


Snapshot :

![Capture3](https://github.com/paso2004/myAnimelistAnalysis/assets/161154534/78fb829e-6ecf-446b-b675-6bd990c1f839)


        
- Step 6 : For Animes that do not have Japanese name replace the Japanese name by corresponding English name:
                
                df['Japanese_name'] = df['Japanese_name'].fillna(df['Name'])




- Step 7 : The columns Tag and Content warnings has a lot of inputs . To effectively find the genre just the first two tags is needed. The rest of the tags are sub genres. The same goes for the tags in Content Warning. We are Going to use a lambda function with the condition that if the columns have only one tag in any row it will just return the tag itself and not run the Split function :

                df['Tags'] = df['Tags'].apply(lambda x: ' ,'.join(x.split(',')[:2]) if pd.notnull(x) else x)
               
                df['Content_Warning'] = df['Content_Warning'].apply(lambda x: ','.join(x.split(',,')[:3]) if pd.notnull(x) else x)

Snapshot:

![Capture4](https://github.com/paso2004/myAnimelistAnalysis/assets/161154534/b3e3780d-42f5-412c-8aff-2fc8bb5de2ed)


- Step 8 : We need to replace the null values from the Content Warning column with 'Kids Friendly' :

                df['Content_Warning'] = df['Content_Warning'].fillna('Kids Friendly')

Snapshot:

![Capture5](https://github.com/paso2004/myAnimelistAnalysis/assets/161154534/d831c649-6071-4724-a13e-73e12dec5325)


#### Data Analysis

- Step 1 : The best way to find the top 10 Animes we need to utilise a bar chart :

                sorted_data = df.sort_values(by ='Rating',  ascending = False)
                top_10 = sorted_data.head(10)

                #ploting the barplot
                plt.figure(figsize=(10,6))
                ax = sns.barplot( x='Name', y='Rating', data =top_10)
                plt.xlabel('Titles')
                plt.ylabel('Rating')
                plt.title('Top 10 animes with highest Rating')
                plt.xticks(rotation=45, ha='right')

                #Adjusting Y axis limit
                plt.ylim(top = top_10['Rating'].max() + 0.5)
                plt.tight_layout()
                for bars in ax.containers :
                    ax.bar_label(bars)
                plt.show()

Output :

![Capture6](https://github.com/paso2004/myAnimelistAnalysis/assets/161154534/5fbd5864-aa9d-472f-aecf-ad9b34d3ff25)

- Step 2: Similar code can be used o find the least popular anime as well:

            desc_sorted_values = df.sort_values(by = 'Rating' , ascending = True)
            lowest_10 = desc_sorted_values.head(10)

            #plotting the bar plot

            plt.figure(figsize=(10,6))
            ax = sns.barplot(x= 'Name',y= 'Rating', data = lowest_10 )
            plt.ylabel('Rating')
            plt.xlabel('Titles')
            plt.title('Least rated Animes')
            plt.xticks(rotation=45, ha='right')

            #adjusting y axis limits
            plt.ylim( top = lowest_10['Rating'].max()+ 0.5)
            plt.tight_layout()

            for bars in ax.containers:
                ax.bar_label(bars)
                
            plt.show()

Output :

![Capture7](https://github.com/paso2004/myAnimelistAnalysis/assets/161154534/5996fba8-92d1-41ba-ae07-e8cb6b79def7)

- Step 3 : To show the popularity by Genre pie chart is used :

            type_count = df['Tags'].value_counts()

            #finding top 10 tags
            top_10_tags = type_count.head(10)

            #making all the other tags apart from top10 as Others

            other_than_top10 = top_10_tags[10:].sum()
            top_10_tags['Others'] = other_than_top10

            #plotting the bar plot
            plt.figure(figsize = (6,6))
            plt.pie(top_10_tags,labels=top_10_tags.index, autopct='%1.1f%%', startangle=140)
            plt.title('Anime Tags')
            plt.axis('equal')
            plt.show()

Output :

![Capture8](https://github.com/paso2004/myAnimelistAnalysis/assets/161154534/6c993ae7-9980-4a41-bbe6-fc1cde9190e7)

- Step 4: To find the thye trend of Animes with Content Warning ( not suitable for Children ) we are using a bar plot. We are grouping the data on the basis of Content_warning then performing Mean on rating to find average value of different tags:

            content_warning = df.groupby(['Content_Warning'],as_index= False)['Rating'].mean().sort_values(by = 'Rating', ascending= False).head(10)
            content_warning['Rating'] = content_warning['Rating'].round(2)
            # Ratings of Anime based on Content Warning
            plt.figure(figsize=(7,4))
            plt.xlabel('Content warning')
            plt.ylabel('Rating')
            plt.title('Popular Animes with Content Warning Tags')
            plt.xticks(rotation=45, ha='right')

            ax = sns.barplot(x='Content_Warning', y='Rating', data = content_warning)
            for bars in ax.containers:
                ax.bar_label(bars)
                
            plt.show()


Output:

![Capture9](https://github.com/paso2004/myAnimelistAnalysis/assets/161154534/7e859d5f-6c32-4fce-b313-daa44244ce28)

- Step 5 : Next we have to find the most profitable Anime Production studio. We can do this by applying group by function on studios column and finding the total number of Anime names associated with each studio:

            studio_success = df.groupby(['Studio'],as_index= False)['Name'].count().sort_values(by = 'Name', ascending= False).head(20)

            # Studios bar plot
            plt.figure(figsize=(8,4))
            plt.xlabel('Studio Name')
            plt.ylabel('Number of Animes')
            plt.title('Studios with most Production')
            plt.xticks(rotation=45, ha='right')

            ax = sns.barplot(x='Studio', y='Name', data = studio_success)
            for bars in ax.containers:
                ax.bar_label(bars)
                
            plt.show()

Output:

![Capture10](https://github.com/paso2004/myAnimelistAnalysis/assets/161154534/e7fecceb-12c6-429f-bd6b-0372a94cea9a)


Step 6 : Finally we have to find the most consistent production studio . We will follow the same code as Step 5 but isntead of Count() use Mean() function :

            studio_rating = df.groupby(['Studio'],as_index= False)['Rating'].mean().sort_values(by = 'Rating', ascending= False).head(20)

            studio_rating['Rating'] = studio_rating['Rating'].round(3)

            # Studios bar plot
            plt.figure(figsize=(10,6))
            plt.xlabel('Studio Name')
            plt.ylabel('Number of Animes')
            plt.title('Studios with most Production')
            plt.xticks(rotation=45, ha='right')

            ax = sns.barplot(x='Studio', y='Rating', data = studio_rating)
            for bars in ax.containers:
                ax.bar_label(bars)
                
            plt.show()

Output :

![Capture11](https://github.com/paso2004/myAnimelistAnalysis/assets/161154534/f8456140-bae7-4fbf-bab6-8b5b6df7b0f3)

#### Conclusion :

Most of the Top Rated Animes are from Action, Comedy, Adventure, Fantasy genre. For an Anime to be more successful it needs to be catered towards teenagers and above 18 + audience . Toei, Sunrise, J.C. Staff, The Entertainement and Madhouse are the 4 major leading anime production studios. But the most consistent studios are Studio Pivote , Tiger Animations , B.C. May Pictures meaning taking up a lot of projects doesn't guarantee quality work. 




 
