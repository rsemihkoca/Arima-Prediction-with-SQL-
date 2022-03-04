# The Arima+ model I created using SQL over BigQuery for Single Value Time Series Prediction.

The purpose of the model is to: Predict the number of couriers required 3 months in advance by predicting the number of orders to come over the next 96 days.

Results:
In total over the next 96 days with 0.9 confidence:

     Minimum,    39,308 (13% error)
     Forecasted,  47,228 (12% error)
     Maximum   55,149 (31% error) 
     Actual 42,764

 #of cargos needs to be delivered.

# Results
<img src="https://raw.githubusercontent.com/rsemihkoca/Arima-Prediction-with-SQL-/main/Results.png">
