# World Bank development indicators data in `worlddevrds`

This file is an R serialized object (`.RDS`) created from the World Bank World Development Indicators (WDI) dataset. I inspected it with R and found that it loads as a single `data.frame`.

## 1. Overall structure

- Object class: `data.frame`
- Rows: 396,970
- Columns: 70
- Countries represented: 265
- Indicators represented: 1,498
- Year columns: 1960 through 2025, inclusive, for 66 year fields

The dataset is a wide table in which each row corresponds to one country-indicator pair, not a single country-year observation.

This is clear from the row count:

- `265 countries * 1,498 indicators = 396,970 rows`

So every row is effectively:

- one country
- one indicator
- many yearly values stored in separate columns

## 2. Column layout

The first four columns are metadata fields:

1. `Country Name`
2. `Country Code`
3. `Indicator Name`
4. `Indicator Code`

After that, there are 66 numeric columns for years 1960 to 2025:

- `1960`, `1961`, ..., `2025`

These columns contain the indicator values for that country/indicator pair in each year.

## 3. Example record

A sample row looks like this:

- `Country Name`: Africa Eastern and Southern
- `Country Code`: AFE
- `Indicator Name`: Access to clean fuels and technologies for cooking (% of population)
- `Indicator Code`: EG.CFT.ACCS.ZS
- `1960`: NA
- `1961`: NA
- `1962`: NA
- ...
- `2025`: NA

The values are numeric and represent the reported World Bank indicator for each year. Many years are missing (`NA`) because not every indicator was reported for every country in every year.

## 4. What each row represents

Each row is a country-specific indicator record.

For example, one row might be:

- Country: `United States`
- Indicator: `GDP per capita (current US$)`
- Values: one number for each year from 1960 to 2025

Another row might be:

- Country: `United States`
- Indicator: `Population, total`
- Values: a different numeric series for those same years

This means the table is organized as a country-by-indicator matrix with annual values spread across columns.

## 5. Data types and missingness

- `Country Name`, `Country Code`, `Indicator Name`, and `Indicator Code` are character strings.
- The year columns are numeric.
- Missing values are common, especially in early years and for some indicators.

The `summary()` output shows many years have very high proportions of `NA`, which is expected for an international indicator database where coverage varies widely by country and variable.

## 6. Why this structure matters

This is a wide-format data frame, not a long tidy format.

It is convenient for:

- country-by-indicator comparison
- quick access to an indicator across many years
- table-like inspection in R

It is less convenient for:

- plotting with a tidy long-format workflow
- filtering by year and indicator in a single column structure

## 7. Example R code to load and inspect

```r
x <- readRDS("worlddevrds")

str(x)
summary(x[, c("Country Name", "Country Code", "Indicator Name", "Indicator Code")])

# number of rows and columns
dim(x)

# number of unique countries/indicators
length(unique(x$`Country Code`))
length(unique(x$`Indicator Code`))
```

## 8. Conclusion

The `worlddevrds` object is a compact, wide-format World Bank development indicators table. It contains one row per country-indicator combination and one column per year. The dataset is well suited for cross-sectional data exploration and indicator comparisons, but it would typically be reshaped into tidy long form before advanced time-series analysis or plotting.
