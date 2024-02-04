# Nifty50

> Data Analysis of Nifty 50  Index over 10 years period

![Screenshot (75)](https://github.com/Ritik0109/Nifty50/assets/84517614/3c951b5a-2104-4731-9b28-a2457aff6f00)



### Objective:

The goal was to answer the following questions at the end of the project:
1. What does nifty comprise of? How has it changed over the past years?
2. What are the return in d/f time horizons such as monthly, yearly and CAGR?
3. How has it performed compared to different asset classes? Is it performing better?
4. How is it compared to global market ?

### WorkFlow:
Here, 10-years historical data has been utilized for the whole project. All this data was extracted from open data sources through Web scraping in Power Query. Details can be found in references section. After importing the data, various transformations & cleanings have been performed to make the data structure consistent for analysis. Data imputation have been performed on missing data, for this purpose median have been considered to minimize big changes over time. 

The data was next imported to Power BI Vertipaq engine and stored in the memory considering the size of the data. After this data modeling was conducted by having seperate for each asset class which was combined with a Master Date table on One-To-Many Relationship forming a Star Schema. After Data modeling & filter adjustments, various DAX measures were created for the calculation & analysis. The major DAX measures can be found in the next section. Finally, the visualizations were added resulting in the outcome of the analysis.

### Tools Used: 
Power BI, Power Query, Excel

### Important DAX Formulas: 

	
	MoM Returns = Nifty Monthly Returns = 
                    VAR CurrentPeriod = CALCULATE(AVERAGE(N_50_Data[Closing]), 
                                                    LASTNONBLANK(Dates[Date], 
                                                                COUNTROWS(RELATEDTABLE(N_50_Data))))

                    VAR PreviousPeriod = CALCULATE(AVERAGE(N_50_Data[Closing]), 
                                                    LASTNONBLANK(PREVIOUSMONTH(Dates[Date]), 
                                                                COUNTROWS(RELATEDTABLE(N_50_Data))))

                    VAR ChangeMoM = CurrentPeriod - PreviousPeriod

                    VAR ChangePrcnt = DIVIDE(ChangeMoM, 
                                                PreviousPeriod)

                    RETURN ChangePrcnt


	Absolute Returns = Absolute Returns = 
                    VAR MinValue = CALCULATE(MIN(N_50_Data[Closing]), FILTER(ALL(Dates),MIN(Dates[Date])))
                    
                    VAR MaxCurrentValue = CALCULATE(MAX(N_50_Data[Closing]),
                                            Dates[Date] <= MAX(Dates[Date]))
                    VAR ChangePrcnt = ((MaxCurrentValue - MinValue) / MinValue)
                    RETURN ChangePrcnt
					
	Nifty-50 CAGR = Nifty CAGR = 
            VAR NiftyMin =  
				MINX(FILTER(ALL(Dates[Date]), 
				YEAR(Dates[Date]) >= 2014), MIN(N_50_Data[Closing]))
				
            VAR NiftyMax =  
				MAXX(ALL(Dates[Date]), MAX(N_50_Data[Closing]))
				
            VAR ChangeinNifty = 
				NiftyMax - NiftyMin
				
            VAR TotalYears = 
				MAX(N_50_Data[Year]) - MIN(N_50_Data[Year])    
				
            VAR ChangeinPrcnt = 
			((NiftyMax / NiftyMin) ^ (1 / TotalYears )) - 1
            
			RETURN ChangeinPrcnt

	MVA 60 days = Nifty_MVA_60 = 
                        VAR LastDateofMonth = MAX(Dates[Date])
                        VAR TimeHorizon = 60

                        VAR PeriodTable = FILTER(ALL(Dates),
                                            Dates[Date] <= LastDateofMonth
                                            &&
                                            Dates[Date] > LastDateofMonth - TimeHorizon)


                        VAR MVA_30 = CALCULATE(AVERAGEX(N_50_Data, [Nifty Monthly Returns]),
                                                            PeriodTable
                        )

                        RETURN MVA_30
						
						
### Results:

From the detailed analysis & benchmarking of Nifty 50 and various other asset classs. We can say that one would have expected about 14% average return yearly since last 10 yrs and 263% in absolute terms. A simple example would be, if one has invested ₹100 in 2013 it would have become ₹263 by today. 

When benchmarking with other asset classes, Nifty has performed strongly and has stayed at top in terms of return most of the time except during major crisis when the whole economy is affected, that is where gold has shined. So, Nifty performance has a very plus corelation with country's economy.

When Nifty is compared with other INDEXES, either globally or with SENSEX, it has performed well while during an event of crisis, it has stayed comparatively resilient and has been able to bounced back quickly to recver its lost gains. 

### References:

The real estate INDEX does not exist as of now, in order to have a fair comparison, INDEX data from Housing.com & ISB has been used, which is publicly available. Data for Forex USD vs INR, Nifty daily 50, Gold return rate have been scraped from Investing.com which is a reliable data source.  Govt 10Y bonds data have been scraped from RBI database. SENSEX, NASDAQ, S&P 500 data have been obtained from their respective websites. Nifty 50 industry weightage has been extracted from NSE website.
