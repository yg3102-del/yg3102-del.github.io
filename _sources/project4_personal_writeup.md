# NYC Traffic Safety Intelligence Dashboard: Personal Writeup

For our final project, our team built the **NYC Traffic Safety Intelligence Dashboard**. It is a Streamlit app based on NYC Open Data. At first, our idea was simple. We wanted to use crash data to understand traffic safety in New York City. But this question was too broad. Almost any chart could answer it in some way.

Later, we changed the question. We started to ask: **what does the citywide average hide?** This became the main idea of our project. A total crash number can show the scale of the problem, but it cannot show who faces more danger. It also cannot show whether different boroughs have different crash patterns. So our dashboard moved from “how many crashes happened” to “where does risk become unequal.”

This change also shaped how we used the data. We did not want to use only one dataset. The crash-level data tells us where and when crashes happen. It also shows the reported causes. The person-level data tells us who was hurt or killed. By combining these two datasets, we could connect crash conditions with human outcomes. This made the project more useful than a basic crash-count dashboard.


![Dashboard home page](project4_files/dashboard_home.png)


The dashboard has four main pages. The first page gives the overall picture. The second page looks at road users. This is where we saw that pedestrians and cyclists face much higher fatality risk once they are involved in a crash. The third page looks at place and time. It compares crash causes by borough and by time of day. The last page connects these results to policy choices. For example, a late-night crash pattern may point to speed or alcohol. A daytime pattern may point more to distraction or failure to yield.


![Road user risk page](project4_files/dashboard_risk.png)

My main contribution was working on the data pipeline and dashboard structure. I helped retrieve data from NYC Open Data through the Socrata API, clean and organize the dataset, and connect the project to BigQuery so the app could load data more efficiently. I also worked on Streamlit pages that display live 2026 collision data and visualizations. Through this process, I learned that building a dashboard is not only about making charts. It also requires careful decisions about data quality, loading speed, column selection, and how to make the results understandable for users.

![Where and when analysis page](project4_files/dashboard_where_when.png)

One problem I learned from was the crash time field. The API does not store time in the clean way we expected. If we handled it like a normal string, the app could read the wrong part of the value. That would make the day and late-night analysis unreliable. Fixing this made me realize that small data cleaning choices can change the whole meaning of a chart.

The biggest thing I learned is that a dashboard should not just display data. It should help the user ask a better question. At the beginning, we could have made a page with crash counts by borough and stopped there. But that would mostly show volume. It would not show risk. By looking at fatality rates and pedestrian death share, the project became more about unequal harm. That is a stronger policy question.

This project also changed how I think about public data. Open data looks objective, but it still needs careful framing. If we only rank boroughs by total crashes, we may miss where pedestrians face the highest danger. If we only show citywide averages, we may miss local patterns. A useful dashboard should make those hidden differences easier to see.


![Policy recommendations page](project4_files/dashboard_policy.png)

## Links

**Live App:** [NYC Traffic Safety Intelligence Dashboard](https://sparkly-pickle-vluncnqsyee7xht3my7b7m.streamlit.app/)
**GitHub Repository:** [Project Repository](https://github.com/advanced-computing/sparkly-pickle)