
# CAPM Analysis

## Introduction

In this assignment, you will explore the foundational concepts of the Capital Asset Pricing Model (CAPM) using historical data for AMD and the S&P 500 index. This exercise is designed to provide a hands-on approach to understanding how these models are used in financial analysis to assess investment risks and returns.

## Background

The CAPM provides a framework to understand the relationship between systematic risk and expected return, especially for stocks. This model is critical for determining the theoretically appropriate required rate of return of an asset, assisting in decisions about adding assets to a diversified portfolio.

## Objectives

1. **Load and Prepare Data:** Import and prepare historical price data for AMD and the S&P 500 to ensure it is ready for detailed analysis.
2. **CAPM Implementation:** Focus will be placed on applying the CAPM to examine the relationship between AMD's stock performance and the overall market as represented by the S&P 500.
3. **Beta Estimation and Analysis:** Calculate the beta of AMD, which measures its volatility relative to the market, providing insights into its systematic risk.
4. **Results Interpretation:** Analyze the outcomes of the CAPM application, discussing the implications of AMD's beta in terms of investment risk and potential returns.

## Instructions

### Step 1: Data Loading

- We are using the `quantmod` package to directly load financial data from Yahoo Finance without the need to manually download and read from a CSV file.
- `quantmod` stands for "Quantitative Financial Modelling Framework". It was developed to aid the quantitative trader in the development, testing, and deployment of statistically based trading models.
- Make sure to install the `quantmod` package by running `install.packages("quantmod")` in the R console before proceeding.

```r
# Set start and end dates
start_date <- as.Date("2019-05-20")
end_date <- as.Date("2024-05-20")

# Load data for AMD, S&P 500, and the 1-month T-Bill (DTB4WK)
amd_data <- getSymbols("AMD", src = "yahoo", from = start_date, to = end_date, auto.assign = FALSE)
gspc_data <- getSymbols("^GSPC", src = "yahoo", from = start_date, to = end_date, auto.assign = FALSE)
rf_data <- getSymbols("DTB4WK", src = "FRED", from = start_date, to = end_date, auto.assign = FALSE)

# Convert Adjusted Closing Prices and DTB4WK to data frames
amd_df <- data.frame(Date = index(amd_data), AMD = as.numeric(Cl(amd_data)))
gspc_df <- data.frame(Date = index(gspc_data), GSPC = as.numeric(Cl(gspc_data)))
rf_df <- data.frame(Date = index(rf_data), RF = as.numeric(rf_data[,1]))  # Accessing the first column of rf_data

# Merge the AMD, GSPC, and RF data frames on the Date column
df <- merge(amd_df, gspc_df, by = "Date")
df <- merge(df, rf_df, by = "Date")
```

#### Data Processing 
```r
colSums(is.na(df))
# Fill N/A RF data
df <- df %>%
  fill(RF, .direction = "down") 
```

### Step 2: CAPM Analysis

The Capital Asset Pricing Model (CAPM) is a financial model that describes the relationship between systematic risk and expected return for assets, particularly stocks. It is widely used to determine a theoretically appropriate required rate of return of an asset, to make decisions about adding assets to a well-diversified portfolio.

#### The CAPM Formula
The formula for CAPM is given by:

$$
E(R_i) = R_f + \beta_i (E(R_m) - R_f)
$$

Where:

- $E(R_i)$ is the expected return on the capital asset,
- $R_f$ is the risk-free rate,
- $\beta_i$ is the beta of the security, which represents the systematic risk of the security,
- $E(R_m)$ is the expected return of the market.



#### CAPM Model Daily Estimation

- **Calculate Returns**: First, we calculate the daily returns for AMD and the S&P 500 from their adjusted closing prices. This should be done by dividing the difference in prices between two consecutive days by the price at the beginning of the period.
  
$$
\text{Daily Return} = \frac{\text{Today's Price} - \text{Previous Trading Day's Price}}{\text{Previous Trading Day's Price}}
$$

```r
# Calculate daily returns using for loop
df$Return_AMD <- 0.0
df$Return_GSPC <- 0.0
for(i in 2:nrow(df)) {
df$Return_AMD[i] <- (df$AMD[i] - df$AMD[i-1]) / df$AMD[i-1]
df$Return_GSPC[i] <- (df$GSPC[i] - df$GSPC[i-1]) / df$GSPC[i-1]
}
# Remove rows with NA values created by the lag function
df <- df %>% drop_na(Return_AMD, Return_GSPC)
```

- **Calculate Risk-Free Rate**: Calculate the daily risk-free rate by conversion of annual risk-free Rate. This conversion accounts for the compounding effect over the days of the year and is calculated using the formula:
  
$$
\text{Daily Risk-Free Rate} = \left(1 + \frac{\text{Annual Rate}}{100}\right)^{\frac{1}{360}} - 1
$$

```r
# Calculate daily risk-free rate for each row
df$Daily_RF <- ((1 + df$RF / 100)ˆ(1/360)) - 1
```


- **Calculate Excess Returns**: Compute the excess returns for AMD and the S&P 500 by subtracting the daily risk-free rate from their respective returns.

```r
# Initialise the new column for excess AMD returns
df$Excess_Return_AMD <- 0.0
# Initialise the new column for excess GSPC returns
df$Excess_Return_GSPC <- 0.0
#Calculate the Excess returns using a for loop
for (i in 2:nrow(df)) {
df$Excess_Return_AMD[i] <- df$Return_AMD[i] - df$Daily_RF[i]
df$Excess_Return_GSPC[i] <- df$Return_GSPC[i] - df$Daily_RF[i]
}
# Display the data frame to verify the calculations
head(df)

# Remove rows with NA values created by the lag function
df <- df %>% drop_na(Return_AMD, Return_GSPC)
# Display the first few rows of the data frame to verify the changes
head(df)
```


- **Perform Regression Analysis**: Using linear regression, we estimate the beta (\(\beta\)) of AMD relative to the S&P 500. Here, the dependent variable is the excess return of AMD, and the independent variable is the excess return of the S&P 500. Beta measures the sensitivity of the stock's returns to fluctuations in the market.

```r
#Perform Linear Regression
capm_model <- lm(Excess_Return_AMD ~ Excess_Return_GSPC, data = df)
summary_capm_model<-summary(capm_model)```


#### Interpretation

What is your \(\beta\)? Is AMD more volatile or less volatile than the market?

```r
# Extract beta value
beta <- coef(capm_model)["Excess_Return_GSPC"]
beta
```

Answer: 
Beta, β, is a measure of a stock’s volatility in relation to the overall market. It indicates how much the
stock’s price is expected to move relative to movements in the market. It is a crucial metric in financial
analysis and investment decision-making, as it helps investors understand and manage the risk associated
with individual stocks and portfolios.
The lm function performs linear regression to estimate the relationship between AMD’s excess returns and
the S&P 500’s excess returns. The coefficients (alpha and beta) are extracted from the model.
A β of 1 indicates that the stock’s price is expected to move with the market, whereas if β is greater than 1
then this suggests that the stock is more volatile than the market and the opposite, meaning less volatile, for
values of β < 1. A negative β indicates that the stock moves in the opposite direction to the market.
From the linear regression, I have extracted β as 1.570001. This is the AMD stock’s beta. Since beta is equal
to 1.570001 which is greater than 1 then this indicates, as defined above, that AMD is more volatile than the
market. More specifically, this means that AMD’s stock is 1.570001 times more volatile than the market.
Comparing to every 1% change in the market’s excess return, AMD’s excess return is expected to change by
1.570001%. If the market increases by 1% then AMD’s stock will parallel this motion, but it will increase by
1.570001%. Likewise when the market goes down by 1%, AMD’s stock will decrease by 1.570001%. This
indicates a higher risk and potential for higher returns compared to the market.
Analysing from an investor perspective, due to AMD’s higher beta, the stock might be more attractive to
an investor who is looking for higher returns. However at the same time, they must be aware of the higher
volatility and potential losses associated with possible higher returns from a high beta.

#### Plotting the CAPM Line
Plot the scatter plot of AMD vs. S&P 500 excess returns and add the CAPM regression line.

```r
#Plot the scatter plot and regression line
ggplot(df, aes(x = Excess_Return_GSPC, y = Excess_Return_AMD)) +
geom_point(color = "blue") +
geom_smooth(method = "lm", color = "red") +
labs(title = "Relationship between Excess Returns of AMD and Excess Returns of S&P 500",
x = "Excess Return of S&P 500", y = "Excess Return of AMD")
```

### Step 3: Predictions Interval
Suppose the current risk-free rate is 5.0%, and the annual expected return for the S&P 500 is 13.3%. Determine a 90% prediction interval for AMD's annual expected return.



**Answer:**

```r
# Calculate the daily standard error of the forecast
sf <- summary(capm_model)$sigma
print(paste("Daily Standard Error:", sf))

# Annual standard error for prediction
annual_sf <- sf * sqrt(252)
print(paste("Annual Standard Error:", annual_sf))

#Current Risk-Free rate and expected market return
current_rf <- 0.05
expected_market_return <- 0.133

# Calculate the expected annual return of AMD using CAPM formula
expected_annual_return_AMD <- current_rf + (beta)*(expected_market_return - current_rf)
print(paste("Expected Annual Return AMD:", expected_annual_return_AMD))

# Calculate z value for 90% confidence level (two-tailed test)
z_value_90 <- qnorm(0.95) # 0.95 corresponds to (1 - (1 - confidence level)/2)
# Print or display the z value
print(paste("Z Value for 90% CI:", z_value_90))

# Calculate the 90% prediction interval
prediction_interval <- c(expected_annual_return_AMD - (z_value_90)*(annual_sf),
expected_annual_return_AMD + (z_value_90)*(annual_sf))
# Display the 90% prediction interval as a percentage
prediction_interval<- sprintf("%0.1f%%", prediction_interval * 100)
# Print the prediction interval
cat("90% Prediction Interval for AMD's Annual Expected Return:", prediction_interval, "%")
```
Interpretation:
Based on the Capital Asset Pricing Model (CAPM) analysis, the 90% prediction interval for AMD’s expected
annual return provides a range from -49% to 85%, which are the lower and upper bounds within we can
expect the actual return to fall with a certain level of confidence. A wide prediction interval, such as -49
to 85, indicates a high level of uncertainty and volatility in AMD’s returns and therefore reflects the risk
associated with investing in AMD. The substantial spread suggests to investors that the actual returns can
vary significantly from the expected value. Such an interval means that in the worst scenario, AMD’s annual
return could decrease by up to 49%, however in the best-case scenario, AMD’s annual return could increase
by up to 85%. Thus investors must be aware of the high risk of substantial losses that come associated with
the potential high returns. This interval can help investors decide whether AMD fits their risk tolerance and
investment strategy.
Conservative investors might find this range too risky, while aggressive investors might see the potential for
high returns as an opportunity. This information is critical for evaluating the suitability of AMD within a
diversified investment strategy, balancing the potential for significant returns against the risk of considerable
losses.
