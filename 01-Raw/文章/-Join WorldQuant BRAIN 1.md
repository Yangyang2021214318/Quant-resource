Unlock more complex operators at Expert, Master and Grandmaster Genius levels.

## Arithmetic

Operator

Description

definition

description

abs(x)

base

Returns the absolute value of a number, removing any negative sign.

The absolute value is often used to ensure that only the size of a value is considered, not its direction.

**Examples:**

abs(close - open)

This expression will output the absolute difference of the daily price change

add(x, y, filter = false), x + y

base

Adds two or more inputs element wise. Set filter=true to treat NaNs as 0 before summing.

The add operator performs element-wise addition on two or more inputs. If the optional filter parameter is set to true, any NaN values in the inputs are treated as zero before the addition.

**Example calculations**

Suppose you have two vectors:

- x = \[1, NaN, 3\]
- y = \[4, 5, NaN\]
- add(x, y) returns \[1+4, NaN+5, 3+NaN\] = \[5, NaN, NaN\]
- add(x, y, filter=true) returns \[1+4, 0+5, 3+0\] = \[5, 5, 3\]

**Tips**

Use filter=true to treat NaNs as zeros. This can improve coverage and performance without the need to backfill the data.

densify(x)

base

Converts a grouping field of many buckets into lesser number of only available buckets so as to make working with grouping fields computationally efficient

divide(x, y), x / y

base

x / y

inverse(x)

base

1 / x

log(x)

base

Calculates the natural logarithm of the input value. Commonly used to transform data that has positive values.

max(x, y,..)

base

Maximum value of all inputs. At least 2 inputs are required

min(x, y..)

base

Minimum value of all inputs. At least 2 inputs are required

multiply(x,y,..., filter=false), x \* y

base

Multiplies two or more inputs element wise. Set filter=true to treat NaNs as 0 before multiplication

power(x, y)

base

x ^ y

reverse(x)

base

\- x

sign(x)

base

Returns the sign of a number: +1 for positive, -1 for negative, and 0 for zero. If the input is NaN, returns NaN. Input: Value of 7 instruments at day t: (2, -3, 5, 6, 3, NaN, -10) Output: (1, -1, 1, 1, 1, NaN, -1)

signed\_power(x, y)

base

x raised to the power of y such that final result preserves sign of x

sqrt(x)

base

Returns the non negative square root of x. Equivalent to power(x, 0.5); for signed roots use signed\_power(x, 0.5).

subtract(x, y, filter=false), x - y

base

Subtracts inputs left to right: x? y? … Supports two or more inputs. Set filter=true to treat NaNs as 0 before subtraction.

## Logical

Operator

Description

definition

description

and(input1, input2)

base

Returns 1 ('true') if both inputs are 1 ('true'). Otherwise, returns 0 ('false').

if\_else(input1, input2, input 3)

base

The if\_else operator returns one of two values based on a condition. If the condition is true, it returns the first value; if false, it returns the second value.

input1 < input2

base

Returns 1 ('true') if input1 is a smaller than input2. Otherwise, returns 0 ('false').

input1 <= input2

base

Returns 1 ('true') if input1 is a smaller or the same as input2. Otherwise, returns 0 ('false').

input1 == input2

base

Returns 1 ('true') if input1 and input2 are the same. Otherwise, returns 0 ('false').

input1 > input2

base

Returns 1 ('true') if input1 is a larger than input2. Otherwise, returns 0 ('false').

input1 >= input2

base

Returns 1 ('true') if input1 is a larger or the same as input2. Otherwise, returns 0 ('false').

input1!= input2

base

Returns 1 ('true') if input1 and input2 are different numbers. Otherwise, returns 0 ('false').

is\_nan(input)

base

If (input == NaN) return 1 else return 0

not(x)

base

Returns the logical negation of x. Returns 0 when x is 1 (‘true’) and 1 when x is 0 (‘false’).

or(input1, input2)

base

Returns 1 if either input is true (either input1 or input2 has a value of 1), otherwise it returns 0.

## Time Series

Operator

Description

definition

description

days\_from\_last\_change(x)

base

Calculates the number of days since the last change in the value of a given variable.

hump(x, hump = 0.01)

base

Limits amount and magnitude of changes in input (thus reducing turnover)

kth\_element(x, d, k, ignore=“NaN”)

base

Returns the K-th value from a time series by looking back over a specified number of (‘d’) days, with the option to ignore certain values. Commonly used for backfilling missing data.

last\_diff\_value(x, d)

base

Returns the most recent value of x from the past d days that is different from the current value of x.

ts\_arg\_max(x, d)

base

Returns the number of days since the maximum value occurred in the last d days of a time series. If today's value is the maximum, returns 0; if it was yesterday, returns 1, and so on.

ts\_arg\_min(x, d)

base

Returns the number of days since the minimum value occurred in a time series over the past d days. If today's value is the minimum, returns 0; if it was yesterday, returns 1, and so on.

ts\_av\_diff(x, d)

base

Calculates the difference between a value and its mean over a specified period, ignoring NaN values in the mean calculation. In short, it returns x – ts\_mean(x, d) with NaNs ignored.

ts\_backfill(x,lookback = d, k=1)

base

Replaces missing (NaN) values in a time series with the most recent valid value from a specified lookback window, improving data coverage and reducing risk from missing data.

ts\_corr(x, y, d)

base

Calculates the Pearson correlation between two variables, x and y, over the past d days, showing how closely they move together.

ts\_count\_nans(x,d)

base

Counts the number of missing (NaN) values in a data series over a specified number of days.

ts\_covariance(y, x, d)

base

Calculates the covariance between two time-series variables, y and x, over the past d days. Useful for measuring how two variables move together within a specified historical window.

ts\_decay\_linear(x, d, dense = false)

base

Applies a linear decay to time-series data over a set number of days, smoothing the data by averaging recent values and reducing the impact of older or missing data.

ts\_delay(x, d)

base

Returns the value of a variable x from d days ago. Use this operator to access historical data points by specifying the desired time lag in days.

ts\_delta(x, d)

base

Calculates the difference between a value and its delayed version over a specified period. Useful for measuring changes or momentum in time-series data.

ts\_mean(x, d)

base

Calculates the simple average (mean) value of a variable x over the past d days.

ts\_product(x, d)

base

Returns the product of the values of x over the past d days. Useful for calculating geometric means and compounding returns or growth rates.

ts\_quantile(x,d, driver="gaussian" )

base

Calculates the ts\_rank of the input and transforms it using the inverse cumulative distribution function (quantile function) of a specified probability distribution (default: Gaussian/normal). This helps to normalize or reshape the distribution of your data over a rolling window.

ts\_rank(x, d, constant = 0)

base

Ranks the value of a variable for each instrument over a specified number of past days, returning the rank of the current value (optionally adjusted by a constant). Useful for normalizing time-series data and highlighting relative performance over time.

ts\_scale(x, d, constant = 0)

base

Scales a time series to a 0–1 range based on its minimum and maximum values over a specified period, with an optional constant shift.

ts\_std\_dev(x, d)

base

Calculates the standard deviation of a data series x over the past d days, measuring how much the values deviate from their mean during that period.

ts\_step(1)

base

Returns a counter of days, incrementing by one each day.

ts\_sum(x, d)

base

Sum values of x for the past d days.

ts\_zscore(x, d)

base

Calculates the Z-score of a time series, showing how far today's value is from the recent average, measured in standard deviations. Useful for standardizing and comparing values over time.

## Cross Sectional

Operator

Description

definition

description

normalize(x, useStd = false, limit = 0.0)

base

Centers a daily cross section by subtracting the market mean; optionally divide by the cross sectional standard deviation and clamp the result to \[?limit, +limit\]. NaNs are ignored in mean/std.

quantile(x, driver = gaussian, sigma = 1.0)

base

Ranks and shifts a vector of Alpha values, then applies a chosen statistical distribution (gaussian, cauchy, or uniform) to reduce outliers. The sigma parameter controls the scale of the output.

rank(x, rate=2)

base

Ranks the values of the input x among all instruments, returning numbers evenly spaced between 0.0 and 1.0. Useful for normalizing data and reducing the impact of outliers.

scale(x, scale=1, longscale=1, shortscale=1)

base

Scales the input so that the sum of absolute values across all instruments equals a specified book size. Allows separate scaling for long and short positions using optional parameters.

winsorize(x, std=4)

base

Winsorize limits values in a data to within a specified number of standard deviations from the mean, reducing the impact of extreme outliers.

zscore(x)

base

Z-score is a numerical measurement that describes a value's relationship to the mean of a group of values. Z-score is measured in terms of standard deviations from the mean

## Vector

Operator

Description

definition

description

vec\_avg(x)

base

Calculates the mean (average) of all elements in a vector field for each instrument and date, converting vector data to a single matrix value.

vec\_sum(x)

base

Calculates the sum of all values in a vector field.

## Transformational

Operator

Description

definition

description

bucket(rank(x), range=“0, 1, 0.1”, skipBoth=False, NaNGroup=False) or bucket(rank(x), buckets = “2,5,6,7,10”, skipBoth=False, NaNGroup=False)

base

The bucket operator creates custom groups by dividing data into buckets (ranges) based on ranked values of any data field. These buckets can then be used with group operators like group\_neutralize, group\_rank, group\_zscore etc.

trade\_when(x, y, z)

base

The trade\_when operator changes Alpha values only when a specific condition is met, keeps previous values otherwise, and can close positions by assigning NaN under an exit condition. It is useful for reducing turnover and controlling when trades are executed.

## Group

Operator

Description

definition

description

group\_backfill(x, group, d, std = 4.0)

base

Fills missing (NaN) values for instruments within the same group by calculating a winsorized mean of all non-NaN values over the past d days. The winsorized mean is computed by trimming extreme values based on a specified standard deviation multiplier (std, default 4.0).

group\_mean(x, weight, group)

base

Calculates the harmonic mean of a data field within each specified group.

group\_neutralize(x, group)

base

Neutralizes Alpha values within each specified group by subtracting the group mean from each value. Groups can be industry, sector, country, or any custom grouping.

group\_rank(x, group)

base

Ranks each element within its group based on the input field, assigning a value between 0.0 and 1.0. This helps compare items within the same group, such as stocks in the same industry.

group\_scale(x, group)

base

Normalizes values within each group to a range between 0 and 1, making data comparable across different groups.

group\_zscore(x, group)

base

Calculates the Z-score of each value within its group, showing how far each value is from the group mean in terms of standard deviations. Useful for comparing values relative to their group.

No search results found for ''