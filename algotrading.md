
## Algorithmic Trading Strategy

## Introduction

In this assignment, you will develop an algorithmic trading strategy by incorporating financial metrics to evaluate its profitability. This exercise simulates a real-world scenario where you, as part of a financial technology team, need to present an improved version of a trading algorithm that not only executes trades but also calculates and reports on the financial performance of those trades.

## Background

Following a successful presentation to the Board of Directors, you have been tasked by the Trading Strategies Team to modify your trading algorithm. This modification should include tracking the costs and proceeds of trades to facilitate a deeper evaluation of the algorithm’s profitability, including calculating the Return on Investment (ROI).

After meeting with the Trading Strategies Team, you were asked to include costs, proceeds, and return on investments metrics to assess the profitability of your trading algorithm.

## Objectives

1. **Load and Prepare Data:** Open and run the starter code to create a DataFrame with stock closing data.

2. **Implement Trading Algorithm:** Create a simple trading algorithm based on daily price changes.

3. **Customize Trading Period:** Choose your entry and exit dates.

4. **Report Financial Performance:** Analyze and report the total profit or loss (P/L) and the ROI of the trading strategy.

5. **Implement a Trading Strategy:** Implement a trading strategy and analyze the total updated P/L and ROI. 

6. **Discussion:** Summarise your finding.


## Instructions

### Step 1: Data Loading

Start by running the provided code cells in the "Data Loading" section to generate a DataFrame containing AMD stock closing data. This will serve as the basis for your trading decisions. First, create a data frame named `amd_df` with the given closing prices and corresponding dates. 

```r
# Check current working directory
print(getwd())
# Set the correct path where AMD.csv is located
setwd("/Users/soroobinirajendran/Desktop/ACTL1101")

# Load data from CSV file
amd_df <- read.csv("AMD.csv")
# Convert the date column to Date type and Adjusted Close as numeric
amd_df$date <- as.Date(amd_df$Date)
amd_df$close <- as.numeric(amd_df$Adj.Close)
amd_df <- amd_df[, c("date", "close")]
```
Explanations: 
1. Prior to reading the file, it is essential to verify that the location of the file “AMD.csv” is in fact in the
directory from which I am running my R script. I did this by using the ‘print(getwd())’ command.
2. Then I correctly set the working directory to the directory containing the file “AMD.csv” with the
‘setwd()’ function.
3. Having ensured that the working directory is correctly set, I ran the starter code. In step 1, I loaded the
historical stock price data of AMD from a CSV file named AMD.csv and this file contains information
such as the date and closing price of AMD stock.
The ‘read.csv()’ function is used to read the data from the CSV file and store it in a dataframe
named ‘amd_df’.

#### Plotting the Data
Plot the closing prices over time to visualize the price movement.
```r
plot(amd_df$date, amd_df$close,'l')
```
Explanations:
The above code plots the closing prices of AMD stock over time using the ‘plot()’ function. The code specifies
that the x-axis should represent the dates (amd_df$date) whilst the y-axis represents the closing prices
(amd_df$close).
I used the argument ‘l’ to specify that the plot should be a line plot.

### Step 2: Trading Algorithm
Implement the trading algorithm as per the instructions. You should initialize necessary variables, and loop through the dataframe to execute trades based on the set conditions.

- Initialize Columns: Start by ensuring dataframe has columns 'trade_type', 'costs_proceeds' and 'accumulated_shares'.
- Change the algorithm by modifying the loop to include the cost and proceeds metrics for buys of 100 shares. Make sure that the algorithm checks the following conditions and executes the strategy for each one:
  - If the previous price = 0, set 'trade_type' to 'buy', and set the 'costs_proceeds' column to the current share price multiplied by a `share_size` value of 100. Make sure to take the negative value of the expression so that the cost reflects money leaving an account. Finally, make sure to add the bought shares to an `accumulated_shares` variable.
  - Otherwise, if the price of the current day is less than that of the previous day, set the 'trade_type' to 'buy'. Set the 'costs_proceeds' to the current share price multiplied by a `share_size` value of 100.
  - You will not modify the algorithm for instances where the current day’s price is greater than the previous day’s price or when it is equal to the previous day’s price.
  - If this is the last day of trading, set the 'trade_type' to 'sell'. In this case, also set the 'costs_proceeds' column to the total number in the `accumulated_shares` variable multiplied by the price of the last day.



```r
# Initialize columns for trade type, costs/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA # Corrected column name
amd_df$accumulated_shares <- 0 # Initialize if needed for tracking
# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0
# Loop through the rows of amd_df to implement the trading algorithm
for (i in 1:nrow(amd_df)) {
 current_price <- amd_df$close[i] # Get the closing price for the current day

 if (previous_price == 0) {
 # If the previous price is 0, perform a buy
 amd_df$trade_type[i] <- 'buy'
 amd_df$costs_proceeds[i] <- -current_price * share_size
 accumulated_shares <- accumulated_shares + share_size
 } else if (current_price < previous_price) {
 # If the current price is less than the previous day's price, perform a buy
 amd_df$trade_type[i] <- 'buy'
 amd_df$costs_proceeds[i] <- -current_price * share_size
 accumulated_shares <- accumulated_shares + share_size
 } else if (i == nrow(amd_df)) {
 # If this is the last day of trading, perform a sell
 amd_df$trade_type[i] <- 'sell'
 amd_df$costs_proceeds[i] <- accumulated_shares * current_price
 }
 # Update accumulated shares for each row
 amd_df$accumulated_shares[i] <- accumulated_shares

 # Update previous_price for the next iteration
 previous_price <- current_price
}
# After the loop, check if there are accumulated shares to sell
if (accumulated_shares > 0) {
 amd_df$trade_type[nrow(amd_df)] <- 'sell'
 amd_df$costs_proceeds[nrow(amd_df)] <- accumulated_shares * amd_df$close[nrow(amd_d
f)]
# Leave accumulated_shares as is to reflect the number of shares before the sell
}
# Print the last few rows of the data frame to see the results
print(tail(amd_df, 10)) # Display the last 10 rows for better inspection
```
Explanations:
Step 2 involved implementing the trading algorithm based on specific conditions.
1. First I initialised the columns for trade type, costs/proceeds, and accumulated shares in the AMD
dataframe.
The column for trade Type (‘trade_type’) represents the type of trade executed on a particular day
and since in the initial row no trades have been executed yet, it is initialised with NA (Not
Available) to indicate that no trade type has been assigned to that day.
Similarly the column for ‘costs_proceeds’ is initialised to NA as costs proceeds records the
financial impact of each trade, presenting money spent on buys as negative values and money
gained from sells as positive values. Since no trades have occured yet, there are no costs or
proceeds to record and thus it is initialised to NA.
The ‘accumulated_shares’ column maintains a cumulative track of the total number of shares
over time and since initially no shares have been accumulated yet, it is initialised to 0.

2. Then I used the ‘for’ loop to iterate through each row of the data frame ‘amd_df’ to analyze the price
data and execute trades accordingly.
The ‘for’ loop checks the ‘current_price’ against the ‘previous_price’ to determine whether to buy
or sell.
The ‘i’ variable takes on values of the rows from row 1 to the total number of rows in the
dataframe which is given by ‘nrow(amd_df)’.
The function ‘nrow(amd_df)’ in R returns the number of rows in a dataframe named ‘amd_df’.
At each iteration, ‘current_price’ is assigned the closing price of the stock for the current day,
which is derived from the ‘close’ column of the dataframe. If the ‘previous_price’ is 0, this
indicates the first day of trading. Therefore in this case, the algorithm executes the ‘buy’
condition and assigns a ‘buy’ value to the ‘trade_type’ column for the current row.
Then it calculates the cost of buying shares by (‘-current_price * share_size’), which is a negative
value, and then updates the total accumulated shares (‘accumulated_shares’).
The same method is executed for if the ‘current_price’ is less than the ‘previous_price’ indicating
that the price has decreased and thus following through with the ‘buy’ trade.

3. On the last day of trading, which is where the loop reaches the end of the data frame, the algorithm
executes a sell trade to liquidate any remaining shares.
Here I have specified that ’ else if (i == nrow(amd_df)) ’ then we execute a ‘sell’, which means that
if the row ‘i’ is equal to the last row of the dataframe then it is a sell condition.

4. In Step 2 I have utilised the ‘if’ and ‘else if’ statements. These are conditional statements which I
integrated to define the logic for buying, selling, or holding stocks based on certain conditions, which
were the stock price movements, such as the ‘buy’ condition for when the current price is less than the
previous price.
The ‘if’ statement evaluates a condition and executes a block of code if the condition is true.
However if the condition is false, the code block associated with the if statement is skipped.
Likewise the ‘else if’ statement allowed me to check additional conditions where the previous if
statement(s) evaluated to false. This statement is used when multiple conditions need to be
checked sequentially, which for me were ‘current_price < previous_price’ and ‘i ==
nrow(amd_df)’.

5. After each trade, I updated the total number of accumulated shares to be recorded in the
‘accumulated_shares’ column for the current row. Also I updated the ‘previous_price’ variable with the
current price at the end of each iteration so that it can be referenced in the next iteration.

### Step 3: Customize Trading Period
- Define a trading period you wanted in the past five years 
```r
# Define the start and end dates for the period
start_date <- as.Date("01-01-2022", format="%m-%d-%Y")
end_date <- as.Date("12-31-2022", format="%m-%d-%Y")
# Print the start and end dates to verify them
print(start_date)
print(end_date)

# Create a new data frame with data for the specified date range
trading_period_df <- amd_df[amd_df$date >= start_date & amd_df$date <= end_date, ]
# Print the first few rows of the filtered dataframe
print(head(trading_period_df))

# Initialize columns for trade type, costs/proceeds, and accumulated shares in amd_df
trading_period_df$trade_type <- NA
trading_period_df$costs_proceeds <- NA # Corrected column name
trading_period_df$accumulated_shares <- 0 # Initialize if needed for tracking
# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0
# Loop through the rows of trading_period_df to implement the trading algorithm
for (i in 1:nrow(trading_period_df)) {
 current_price <- trading_period_df$close[i] # Get the closing price for the curren
t day

 if (previous_price == 0) {
 # If the previous price is 0, perform a buy
 trading_period_df$trade_type[i] <- 'buy'
 trading_period_df$costs_proceeds[i] <- -current_price * share_size
 accumulated_shares <- accumulated_shares + share_size
 } else if (current_price < previous_price) {
 # If the current price is less than the previous day's price, perform a buy
 trading_period_df$trade_type[i] <- 'buy'
 trading_period_df$costs_proceeds[i] <- -current_price * share_size
 accumulated_shares <- accumulated_shares + share_size
 } else if (i == nrow(trading_period_df)) {
 # If this is the last day of trading, perform a sell
 trading_period_df$trade_type[i] <- 'sell'
 trading_period_df$costs_proceeds[i] <- accumulated_shares * current_price
 } else {
 # No trade
 trading_period_df$trade_type[i] <- NA
 trading_period_df$costs_proceeds[i] <- 0
 }
 # Update accumulated shares for each row
 trading_period_df$accumulated_shares[i] <- accumulated_shares

 # Update previous_price for the next iteration
 previous_price <- current_price
}
# After the loop, check if there are accumulated shares to sell
if (accumulated_shares > 0) {
 trading_period_df$trade_type[nrow(trading_period_df)] <- 'sell'
 trading_period_df$costs_proceeds[nrow(trading_period_df)] <- accumulated_shares * t
rading_period_df$close[nrow(trading_period_df)]
 # Leave accumulated_shares as is to reflect the number of shares before the sell
}
```
Explanations:
1. I used the ‘as.Date()’ function to define the start and end dates for my customised trading period
because this code converts a character string, factor, or numeric value to a Date object. I set the start
and end dates for the trading period as January 1st, 2022, to December 31st, 2022, from within the
original amd dataframe.
2. Then, I created a new dataframe named ‘trading_period_df’ which contained only the rows of ‘amd_df’
that were within this specified date range, that is, “01/01/2022” to “12/31/2022”.
3. I formatted mine as per month-date-year format and therefore I specified this using the code
‘format=“%m/%d/%Y”’.

### Step 4: Run Your Algorithm and Analyze Results
After running your algorithm, check if the trades were executed as expected. Calculate the total profit or loss and ROI from the trades.

- Total Profit/Loss Calculation: Calculate the total profit or loss from your trades. This should be the sum of all entries in the 'costs_proceeds' column of your dataframe. This column records the financial impact of each trade, reflecting money spent on buys as negative values and money gained from sells as positive values.
- Invested Capital: Calculate the total capital invested. This is equal to the sum of the 'costs_proceeds' values for all 'buy' transactions. Since these entries are negative (representing money spent), you should take the negative sum of these values to reflect the total amount invested.
- ROI Formula: $$\text{ROI} = \left( \frac{\text{Total Profit or Loss}}{\text{Total Capital Invested}} \right) \times 100$$

```r
# Calculate total profit or loss
total_pl <- sum(trading_period_df$costs_proceeds, na.rm = TRUE)
print(total_pl)
# Calculate total capital invested (sum of costs_proceeds for 'buy' transactions)
total_invested <- -sum(trading_period_df$costs_proceeds[trading_period_df$trade_type
== 'buy'], na.rm = TRUE)
print(total_invested)

# Calculate ROI
ROI <- (total_pl / (total_invested)) * 100
# Print the results
cat("Total Profit/Loss:", total_pl, "\n")
cat("ROI:", ROI, "\n")
```
Explanations:
The code for Step 4 calculates the total profit or loss, total capital invested, and ROI for the customised
trading period.
1. I used the ‘sum()’ code to sum up the costs/proceeds column to calculate the total profit or loss, and
then to calculate the total capital invested which is the sum of the costs/proceeds for ‘buy’
transactions.
2. Then I calculated the ROI using the formula of (total_pl / (total_invested)) * 100.
3. I used the ‘cat’ function to display the output of the total profit/loss and ROI to the console.
4. I used the ‘’ function at the end of each line as it is a newline character, which would move the cursor to
the next line after printing

### Step 5: Profit-Taking Strategy or Stop-Loss Mechanisum (Choose 1)
- Option 1: Implement a profit-taking strategy that you sell half of your holdings if the price has increased by a certain percentage (e.g., 20%) from the average purchase price.
- Option 2: Implement a stop-loss mechanism in the trading strategy that you sell half of your holdings if the stock falls by a certain percentage (e.g., 20%) from the average purchase price. You don't need to buy 100 stocks on the days that the stop-loss mechanism is triggered.


```r
#Profit-Taking Strategy, In this step, we will implement a profit-taking strategy. If
the price has increased by 20% from the average purchase price, we will sell half of
the holdings.
# Define the start and end dates for the period
start_date <- as.Date("01-01-2022", format="%m-%d-%Y")
end_date <- as.Date("12-31-2022", format="%m-%d-%Y")
# Create a new data frame with data for the specified date range
trading_period_df <- amd_df[amd_df$date >= start_date & amd_df$date <= end_date, ]
# Set profit-taking threshold (e.g., 20%)
profit_threshold <- 0.2
# Initialize columns for trade type, cost/proceeds, and accumulated shares in trading
_period_df
trading_period_df$trade_type <- NA
trading_period_df$costs_proceeds <- 0
trading_period_df$accumulated_shares <- 0
# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0
total_cost <- 0
average_purchase_price <- 0
# Loop through the rows of trading_period_df to implement the profit taking strategy
for (i in 1:nrow(trading_period_df)) {
 current_price <- trading_period_df$close[i]

 if (previous_price == 0 || current_price < previous_price) {
 # Buy condition
 trading_period_df$trade_type[i] <- "Buy"
 trade_cost <- -current_price * share_size
 trading_period_df$costs_proceeds[i] <- trade_cost
 accumulated_shares <- accumulated_shares + share_size
 total_cost <- total_cost + trade_cost
 average_purchase_price <- ((average_purchase_price * (accumulated_shares - share_
size)) + (current_price * share_size)) / accumulated_shares
 } else if (current_price >= 1.2 * average_purchase_price && accumulated_shares > 0)
{
 # Profit-taking condition (sell half of the holdings)
 trading_period_df$trade_type[i] <- "Sell"
 shares_to_sell <- accumulated_shares / 2
 trade_proceeds <- current_price * shares_to_sell
 trading_period_df$costs_proceeds[i] <- trade_proceeds
 accumulated_shares <- accumulated_shares - shares_to_sell
 }
 trading_period_df$accumulated_shares[i] <- accumulated_shares

 # Update previous_price for the next iteration
 previous_price <- current_price
}
# After the loop, check if there are accumulated shares to sell
if (accumulated_shares > 0) {
 final_day <- nrow(trading_period_df)
 trading_period_df$trade_type[final_day] <- "Sell"
 trading_period_df$costs_proceeds[final_day] <- trading_period_df$close[final_day] *
accumulated_shares
 trading_period_df$accumulated_shares[final_day] <- 0
}
```
Explanations:
Step 5 replicates the code for Step 3 except with the integration of the average purchase price and
implementation of the profit taking strategy.
1. I gave a profit threshold of 20% as defined in ‘profit_threshold <- 0.2’.
2. I calculated the average purchase, which I did as follows:
1. I calculated the total value of shares purchased before the current transaction.
This was done by (average_purchase_price * (accumulated_shares - share_size)) where the
shares purchased in the current transaction, represented by ‘share_size’, are subtracted
from total accumulated shares and then multiplies it by the previous average purchase
price.
2. The next component was to calculate the value of the shares purchased in the current
transaction which is equal to (current_price * share_size).
This is done by multiplying the current share price (current_price) by the number of shares
bought in the current transaction (share_size).
3. Thus the total value of shares is given by sum of the total value of shares purchased before the
current transaction and the value of the shares purchased in the current transaction, that is:
((average_purchase_price * (accumulated_shares - share_size)) + (current_price *
share_size)).
3. Therefore the ‘average_purchase_price’ is given by the total value of shares divided by the accumulated
shares.

### Step 6: Summarize Your Findings
- Did your P/L and ROI improve over your chosen period?
- Relate your results to a relevant market event and explain why these outcomes may have occurred.


```r
# Fill your code here and Discuss
# Calculate Total P/L and ROI
total_profit_loss_after_profit_taking_strategy <- sum(trading_period_df$costs_proceed
s, na.rm = TRUE)
total_invested_capital <- -sum(trading_period_df$costs_proceeds[trading_period_df$tra
de_type == "Buy"], na.rm = TRUE)
ROI_after_profit_taking_strategy <- (total_profit_loss_after_profit_taking_strategy /
total_invested_capital) * 100
# Print Results
cat("Total Profit/Loss:", total_profit_loss_after_profit_taking_strategy, "\n")
cat("ROI:", ROI_after_profit_taking_strategy, "\n")
# Add the necessary code to save the resulting data frame to a CSV file for verificat
ion in Excel
write.csv(trading_period_df, "trading_period_results.csv")
```
Explanations:
In Step 6 the same code was used as Step 4 however I assigned new names to differentiate between before and after the implementation of the profit taking strategy. These names are ‘total_profit_loss_after_profit_taking_strategy’ and ‘ROI_after_profit_taking_strategy’.


DISCUSSION:
Analysing the trading period I have chosen, January 1st 2022 to 31st December 2022, with respect to specific market events explains the consistent ROI and profit/loss observed above in Step 5, where despite implementing a profit-taking strategy with a threshold of 20% equal to or greater than the average purchase price, my P/L and ROI remained the same.The company AMD is in the semiconductor industry. This industry encountered a significant global chip shortage in 2022, which was driven by the supply chain disruptions resulting from the ongoing COVID-19 pandemic since 2020 that is the primary reason for accelerating shortages. As a result, AMD, being in this industry, has also been negatively impacted by these supply chain disruptions, in particular the wafer and substrates shortage. This has thus led to increased demand for electronics and thereby logistical challenges, as such supply constraints highly likely affected AMD’s capabilities for optimal production and in turn their stock prices throughout the year. An increase in remote work and schooling caused a sudden surge in demand for consumer electronics, specifically those requiring chips such as computers and network peripherals. However due to limited production facilities, there was a depletion in inventories. Examining historical stock price data, there is clearly a constant fluctuation throughout the 2022 period and ultimately an overall decline in stock prices from January to December 2022. Market conditions, including volatility, is a significant contributor to stock price movements, and this is evident with AMD in 2022, whereby such volatility could result in a profit threshold too high such that the stock could not sustain a significant increase to meet or exceed the profit-taking threshold, meaning that the profit taking strategy was not triggered and no sales would occur. Justifiably, AMD’s price drops have triggered multiple buy conditions. Geopolitical tensions also affected market conditions and in the case of the 2022 period, this includes the Russian-Ukraine war. The conflict between Russia and Ukraine which intensified in February 2022 induced widespread economic implications as sanctions on Russia and disruptions in supply chains contributed to market volatility. Specifically the supply of specific raw materials used in semiconductor manufacturing, such as neon and palladium, has been exacerbated by this war as Ukraine produces approximately half of the global neon supply and 90% of the semiconductor-grade neon used in the United States, whilst Russia exports about 40% of the global supply of the metal palladium, which could be further affected by sanctions imposed by Western governments. Additionally, the war’s impact on energy prices and inflation had ripple effects across global markets, including the semiconductor sector. Due to the rising tensions between the United States (US) and China, especially in the technology sector, in regard to technology and trade policies, AMD has faced obstacles in accessing the Chinese market. This has been exacerbated by the US government’s implementation of stricter export controls in October 2022, aimed at restricting China’s procurement of semiconductor technology, so as to safeguard against potential bolstering of Beijing’s military capabilities, particularly in the development of AI-powered military technologies, using AMD’s specific AI chip. These restrictions on technology exports and imports, as well as tariffs, could have impacted AMD’s business operations and stock performance. Therefore by examining the trading data from the algorithm against these market events, mainly impacting the supply chain, you can see patterns in how the algorithm executed trades in response to market conditions and its movements, where specifically the continual price drops and fluctuation led to frequent buy conditions during this period and the profit threshold could not be reached with only selling on the last day. My first strategy in the trading algorithm yielded a P/L of -335456 dollars which remained consistent after implementing the profit-taking strategy utilising a 20% profit threshold, therefore providing a consistent ROI of -27.43332% before and after the profit taking strategy.



