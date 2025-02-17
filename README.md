# Crime-Analysis-Report

## Project Overview 
I have developed a Crime Analysis Report analyzing total crimes by month, year, and time group, identifying high-crime areas, crime types, and revealing a 70% resolution rate alongside 29.96% unresolved incidents, offering actionable insights for crime prevention and resolution strategies.

## Objectives 

1. Total Crimes:
- Sum of all reported crimes in the dataset.

2. Crime Distribution by Year and Yearly Changes:
- Analysis of crimes categorized by year, including insights into the year-over-year changes.

3. Crimes by Time Range (e.g., 3:00 AM to 5:59 AM):
- Exploration of crime occurrences within specific time intervals, providing a detailed breakdown.

4. Hitmap Showing Crime Distribution by Weekdays and Months:
- Visualization using a hitmap to illustrate how crimes are distributed across weekdays and months.

5. Crimes by Country:
-Examination of crimes categorized by the country where they occurred

6. Total Resolved and Unresolved Crimes:
-Distinction between resolved and unresolved crimes, offering an overview of the overall resolution rate.

7. Monthly Crime Trend with Percentage Variance:
- Analysis of the monthly crime trend, accompanied by the percentage variance to highlight fluctuations.

8. Identification of the Most Dangerous Time of the Day:
=Exploration to pinpoint the specific time periods during the day associated with a higher frequency of crimes.

## Datasource 

Dataset used for this project was taken randomly from kaggle and Crime Analysis Report:[Click Here](https://github.com/Zaki1203/Crime-Analysis-Report/blob/main/Crime%20Dashboard.zip)

## Data Preparation
1. **Load "Crime Data"**  
   - Open Microsoft Power BI.  
   - Navigate to **Get Data** and select **Excel**.  
   - Locate and open the file named **"Crime Data"**.  
   - Once the data is loaded, click on **Transform Data** to access Power Query for basic data transformations.  

2. **Load "Other Data"**  
   - Click on **New Source** and select **Excel**.  
   - Browse and open the file named **"Other Data"**.  
   - Select the **Crime Type** column and load the data.  

3. **Inspect the Dataset**  
   - Upon reviewing the dataset, I identified **8 columns** in the **"Crime Data"** table and **2 columns** in the **"Other Data"** table.  
   - I verified the data types of all columns to ensure accuracy and consistency.  

## Dax 

-blank measure month = maxx(all(CalenderData[Month]), [total crime]/20

-blank measure year = 200

-blank measure(month) = 200

-cf(month) = 
var _Pct_momchange =
IF(
DIVIDE(
    [total crime]-[Crime Prev Month],[Crime Prev Month]
)
<>BLANK(),
DIVIDE(
    [total crime]-[Crime Prev Month],[Crime Prev Month]
),
BLANK()
)

var _negativePct=-0.01
RETURN
SWITCH(
    TRUE(),
    _Pct_momchange=0,"Grey",
    _Pct_momchange>=_negativePct,"Green",
    _Pct_momchange<_negativePct,"Red"
)


-cf(year) = 
        var prev=
            CALCULATE([total crime],SAMEPERIODLASTYEAR(CalenderData[Date])
            )
        VAR yoychange=
        IF(prev<>BLANK(),
          [total crime]-prev,
            BLANK()
        )
        RETURN
        SWITCH(
            TRUE(),
            yoychange=0,"Grey",
            yoychange>1,"Green",
            yoychange<1,"Red"
        )


-check = "✅"

-crime pre Year = 
        var prev=
            CALCULATE([total crime],SAMEPERIODLASTYEAR(CalenderData[Date])
            )
        VAR yoychange=
        IF(prev<>BLANK(),
          [total crime]-prev,
            BLANK()
        )
        RETURN
        SWITCH(
            TRUE(),
            yoychange=0,yoychange&"-",
            yoychange>1,"▲" &yoychange,
            yoychange<1,"▼" &yoychange
        )

-Crime Prev Month = 
            CALCULATE(
                [total crime],
                DATEADD(CalenderData[Date],-1,MONTH)
            )

-Crime Resolved = DIVIDE(CALCULATE([total crime],'Crime Data'[Resolved]=1),[total crime])

-Crime Unresolved = DIVIDE(CALCULATE([total crime],'Crime Data'[Resolved]=0),[total crime])

-cross = "❌"

-drillthrough = 
var _weekday=SELECTEDVALUE(CalenderData[Weekday])
var _timegroup=SELECTEDVALUE('Crime Data'[Time Group])
RETURN
"The entire chart on this page is associated with"&" "&
switch(
    TRUE(),
    _timegroup<>BLANK(),_timegroup,
    _weekday<>BLANK(),UPPER(_weekday)
)


-label(month) = 
var _Pct_momchange =
IF(
DIVIDE(
    [total crime]-[Crime Prev Month],[Crime Prev Month]
)
<>BLANK(),
DIVIDE(
    [total crime]-[Crime Prev Month],[Crime Prev Month]
),
BLANK()
)

var _negativePct=-0.01
RETURN
SWITCH(
    TRUE(),
    _Pct_momchange=0,_Pct_momchange&"-",
    _Pct_momchange>=_negativePct,"▲"& FORMAT(_Pct_momchange,"0%"),
    _Pct_momchange<_negativePct,"▼"&FORMAT(_Pct_momchange,"0%")
)


-total crime = COUNTROWS('Crime Data')

## Dax to Create Calender 

CalenderData = 
var _mindate=YEAR(MIN('Crime Data'[Crime Date]))
var _maxdate=YEAR(MAX('Crime Data'[Crime Date]))
RETURN
ADDCOLUMNS(
    FILTER(
        CALENDARAUTO(),
        YEAR([Date])>= _mindate &&
        YEAR([Date])<= _maxdate
    ),
    "Year",Year([Date]),
    "Month",FORMAT([Date],"mmm"),
    "MonthNUM",MONTH([Date]),
     "Weekday",FORMAT([Date],"ddd"),
    "weeknum",WEEKDAY([Date])
)

## Data Modeling

After creating a DAX-based calendar table and other calculation tables, we will establish relationships between the tables to enable seamless data analysis and visualization.

<img width="660" alt="image" src="https://github.com/user-attachments/assets/684d24ee-07b4-4550-a3c8-1b27f038852d" />

## Data Visualization

I have made two page namely:
-Crime Analysis Report 
-Detail Page 

The **Detail Page** serves as a **drill-through page** in the crime analysis report, allowing users to access in-depth information about specific crime incidents. By clicking on a data point (e.g., a specific crime type, location, or time), users can navigate to the Detail Page, which provides granular insights and additional context related to the selected crime. This feature enhances the interactivity and analytical depth of the report, enabling users to explore data more effectively.

![image](https://github.com/user-attachments/assets/c8f75655-0769-4b51-af0c-f3ae4df5362b)

![image](https://github.com/user-attachments/assets/952c0ed2-a8d3-431e-a45b-3a71205e4c1f)

## Analysis

1. Total Crime by Year
Highest Crimes were reported in the year 2022 with 1150 cases and lowest cases in 2021 with 405 crimes

2. Crimes on Dayweek
Most of the crimes are reported on sunday with 375,Friday with 368 ,Monday with 367 and lowest on tuesday with 327 

3. Total Crime by Time Group
The analysis revealed that the highest number of crimes were reported during the 12:00 AM - 2:59 AM time group with 638 crime reports. This was followed by the 9:00 AM - 11:59 AM time group with 607reports , which recorded the second-highest crime frequency. Conversely, the lowest number of crimes were reported during the 12:00 PM - 2:59 PM time group with 191 reports

4. Total Crime by Country
Highest crimes were reported from Austria with 879 cases followed by finland with 331 cases ,uk with 219 cases and switzerland,belgium  with lowest crime reported with 18 cases

5. Crime Type
-Highest crimes reported were of violence & sexual offence,anti-social behaviour,Public order and Criminal damage with lowest crime reports from possession of weapon


## Suggestions

Enhance Nighttime Policing
-Allocate additional resources and patrols during the 12:00 AM - 2:59 AM time group, which has the highest crime frequency. Implement community policing initiatives to deter late-night criminal activity.

Focus on High-Crime Days
-Increase law enforcement presence on Sundays, Fridays, and Mondays, which consistently report higher crime rates. Public awareness campaigns can also discourage criminal behavior during these days.

Regional Crime Prevention Strategies

Develop tailored crime prevention programs for high-crime regions like Austria and Finland. Collaborate with local authorities to address specific challenges and reduce crime rates.

Address Violent and Public Order Crimes
-Prioritize resources to combat Violence & Sexual Offences, Anti-Social Behaviour, and Public Order crimes. Strengthen community engagement programs to address root causes such as social inequality and substance abuse.

Improve Resolution Rates
-Invest in advanced investigative tools, training, and technology to improve the 29.96% unresolved crime rate. Streamline case management systems to ensure timely resolution of incidents.

Leverage Data-Driven Insights
-Use the Crime Analysis Report to identify emerging trends and allocate resources effectively. Regularly update the dataset and analysis to adapt to changing crime patterns.

Community Engagement and Awareness
-Launch public awareness campaigns to educate citizens about crime prevention measures, especially during high-risk periods. Encourage community reporting and cooperation with law enforcement.

Cross-Country Collaboration
Foster collaboration between countries with high crime rates (e.g., Austria, Finland) to share best practices, intelligence, and resources for combating transnational crimes.

