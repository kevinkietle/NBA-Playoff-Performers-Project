NBA Playoff Performers Project
==============================

In this project, I had the goal of researching players that have elevated or shrunk their performance in the postseason compared to the regular season. I looked at win shares per 48 minutes as the averaged all in performance metric. A positive performer would mean a higher WS/48 in the playoffs than in the regular season.  I performed my research using Python, Pandas, Matplotlib, Seaborn, Tableau, and Sci-kit Learn.

Summary
-------

Here is a visualization I put together in Tableau as a one pager summarizing the results.

This project is broken down into four parts, in which you can find the links to below for full code and project. The rest of this readme will summarize key activities and findings from each section.

-   [1\. Data Scraping and Cleaning](https://github.com/kevinkietle/NBA-Playoff-Performers-Project/blob/main/1.%20NBA%20Playoff%20Performers%20pt.%201.ipynb)

-   [2\. EDA](https://github.com/kevinkietle/NBA-Playoff-Performers-Project/blob/main/2.%20NBA%20Playoff%20Performers%20pt.%202.ipynb)

-   [3\. Regression](https://github.com/kevinkietle/NBA-Playoff-Performers-Project/blob/main/3.%20NBA%20Playoff%20Performers%20pt.%203.ipynb)

-   [4\. Clustering](https://github.com/kevinkietle/NBA-Playoff-Performers-Project/blob/main/4.%20NBA%20Playoff%20Performers%20pt.%204.ipynb)

**Main takeaways:**

-   The majority of players actually see a decrease in WS/48 from regular to postseason as the game becomes more serious and teams hone in on one opponent.

-   Perimeter players (PG, SG, SF) have the best differentials but also the largest variance in their differentials. This is because they are the highest usage, which provides more opportunity to elevate your game or shrink in the moment.

-   The stat features that have the most statistically significant correlations to WS/48 difference are True Shooting %, REB %, AST %, and TOV %. These are significant because they have correlations to usage - higher rebounders typically correlate to lower usage percentage for example. With exception to TOV %, these have negative correlation (higher TS leads to worse WS/48 difference).

-   Some models also identified age/minutes played as relevant predictors. The idea is that older players are more experienced and can draw from that in the postseason while potentially saving themselves in the regular season. This trend did not really exist for the best players.

-   In identifying clusters within the top 10 percentile of players who elevated their games, as well as the clusters within the bottom 10 percentile of players, versatility seems to be the trait to look for in your roster. A few clusters present in the top 10 percentile included big men who could shoot and wings who have good size and can defend. A few clusters present in the bottom 10 percentile included effective big men without shooting range and perimeter players known as offensive players who are defensive liabilities. These clusters make sense in the context of the predictors mentioned above. Big men who don't shoot jumpers have higher TS% and REB%, and offensive minded players have higher AST % and lower TOV %, all of which correlated to lower WS/48 difference.

Data Scraping and Cleaning
-----------

Using Beautiful Soup, I scraped the advanced stats page from Basketball Reference for both regular season and playoffs for the years 2019-2023. I merged all these dataframes on the player name and year.

For cleaning, I consolidated extra rows for players that played on multiple teams in one year. I created a few calculated columns such as minutes per game and the WS/48 difference that we are targeting (playoff WS/48 minus regular season WS/48).

I made a main dataframe called 'combine' that had all players that played at least 19 minutes per game. Then I made another dataframe called 'best_players' that had all players that played at least 30 minutes per game.

EDA
-----------

I made a function that gave me the top WS/48 difference values for each year to validate with the eye test. From the most recent year 2023, it seems to be solid with guys like Anthony Edwards, Devin Booker, Jamal Murray, and Caleb Martin making the list.

In looking at positions, it seems perimeter players (PG, SG, SF) have the best differentials and the largest standard deviations. They have more opportunity to control the game (higher usage percentage). T tests between the position groups support this statement.

![position ws48 diff table](https://github.com/kevinkietle/NBA-Playoff-Performers-Project/assets/82183590/09056d42-e085-4f3a-bd82-6dcbc2ec89f6)

![position ws48 diff charts](https://github.com/kevinkietle/NBA-Playoff-Performers-Project/assets/82183590/7d9cab61-aee3-4846-a540-5261a1fb9ad8)

![positions and usage](https://github.com/kevinkietle/NBA-Playoff-Performers-Project/assets/82183590/a0e5300b-6470-4b86-9420-d138aafe4700)

In looking at age, there is a slight positive trend on the dataframe with a 19 MPG threshold, suggesting that older players draw on their experience to elevate their games in the postseason. This or their regular season production is depressed a bit due to lack of urgency.

![age and diff combined](https://github.com/kevinkietle/NBA-Playoff-Performers-Project/assets/82183590/de172a0d-8cde-49b2-8ad6-c238ba131d6c)

When looking at the dataframe with a 30 MPG threshold, however, this trend is not present. The best players are extremely effective in the regular season even if they are older.

![age and diff best players](https://github.com/kevinkietle/NBA-Playoff-Performers-Project/assets/82183590/940dbec5-7bb0-4415-95e8-a8eee5e1a2e7)

The point of older age playing better in the playoffs led me to explore the idea of urgency/resting. The bottom chart supports the theory older players save themselves more in the regular season as there is a slight negative correlation between games played and WS/48 difference.

![games played and diff](https://github.com/kevinkietle/NBA-Playoff-Performers-Project/assets/82183590/6c11c9f8-24ee-4202-a0e0-a7b194233f49)

Regressions
-----------

I started with a basic multiple linear regression that gave a negative R squared, meaning it was not interpretable. I then did backward elimination to select features as a way of eliminating noise from the model. The statistically significant features were TS%, REB%, AST%, and TOV%. All of these had a negative correlation to WS/48 difference except TOV%.

I also tried forward selection, which gave a lower R squared score and gave a different predictor, USG%, but we have discussed how the previous predictors are already correlated to USG%.

Lastly, I took a different approach to developing training and test splits. Instead of randomizing a test split, I took the data for years 2019-2022 as the training data and the data for 2023 as the test set. By doing this and performing Ridge regression instead of basic linear regression, we get our best R squared of the project at 0.0869. This model gave similar results as to relevant predictors: TS%, AST%, REB%, and MPG.

Clustering
----------

As for clustering, I filtered the best_players dataframe into two: one comprising the top 10 percentile of WS/48 difference and one comprising the bottom 10 percentile of WS/48 difference.

I ran a principal components analysis to shrink the dimensions of the data down to 10 before feeding that into a K Means Clustering model. I charted silhouette scores to determine the optimal number of clusters before continuing with the process.

From the clustering analysis of the top 10 percentile, a few trend groups stood out: floor spacing big men with paint protection and wings with good size/defense. Guys like Kristaps Porzingis, Marc Gasol, Anthony Edwards, and Caleb Martin comprise the two groups I'm talking about. Overall, the main theme is versatility. If your big clogs up the floor or your offensive guard can't defend switches you get your weaknesses exploited.

From the analysis of the bottom 10 percentile, the trend groups that exist are big men who can't space the floor (guys who are usually really good too) and offensive value adds who are thought of as defensive liabilities. This is essentially the inverse of groups from the top 10 percentile. Guys like Jarrett Allen, Domantas Sabonis, CJ McCollum, and Kemba Walker comprise the two groups I'm talking about.

&nbsp;
&nbsp;

Hope you enjoyed reading through this project!

Feel free to contact me with any questions or inquiries.

LinkedIn - <https://www.linkedin.com/in/kevinkietle/>

Email - <kevinkietle@gmail.com>
