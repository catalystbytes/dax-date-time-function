# dax-date-time-function
DAX - Date &amp; Time Function

## Mastering Date & Time Functions in DAX: A Comprehensive Guide
### Store Open Summary (DAX with Date & Time Intelligence)
This DAX measure calculates and presents a detailed summary of each store's opening data — including its formatted open date, age in years, time of opening, week number, and whether it falls within the current Year-to-Date (YTD) or Month-to-Date (MTD) period. Also includes a forecast date one year after the original opening.

Useful for Power BI reports focused on timeline analysis, operational tracking, or performance reviews based on store openings.

https://www.tiktok.com/@catalystbytes/video/7500634077616213256

```dax
Store Open Summary = 
-- ===========================
-- 1. Get Store's Opening Date and Related Information
-- ===========================
VAR OpenDate = MAX(store_dim[date])  -- Get the store's opening date (as a scalar value)
VAR FullDate = FORMAT(OpenDate, "DD-MMM-YYYY")  -- Format the OpenDate as "DD-MMM-YYYY"
VAR WeekDay = FORMAT(OpenDate, "dddd")  -- Get the weekday name (e.g., "Monday")
VAR WeekNum = WEEKNUM(OpenDate)  -- Get the week number of the year
VAR HourOpened = HOUR(OpenDate)  -- Extract the hour the store opened
VAR MinuteOpened = MINUTE(OpenDate)  -- Extract the minute the store opened
VAR SecondOpened = SECOND(OpenDate)  -- Extract the second the store opened
VAR TimeOfDay = FORMAT(OpenDate, "hh:mm:ss AM/PM")  -- Format the opening time as "hh:mm:ss AM/PM"

-- ===========================
-- 2. Define Today's Date and Calculate Store Age
-- ===========================
VAR TodayDate = TODAY()  -- Get the current date for comparisons
VAR NowTime = NOW()  -- Get the current timestamp (including time)
VAR YearAge = DATEDIFF(OpenDate, TodayDate, YEAR)  -- Calculate the store's age in years
VAR PreciseAge = YEARFRAC(OpenDate, TodayDate)  -- Calculate the store's precise age in fractional years

-- ===========================
-- 3. Calculate One Year Later Date
-- ===========================
-- Calculate the exact same calendar day one year later from OpenDate
VAR OneYearLaterDate = DATE(YEAR(OpenDate) + 1, MONTH(OpenDate), DAY(OpenDate))

-- ===========================
-- 4. Check if Store Opened in Current Year (YTD)
-- ===========================
-- YTD (Year-to-Date) Check: If the store opened in the current year
VAR IsYTD = 
    IF(
        YEAR(OpenDate) = YEAR(TodayDate),  -- Check if the OpenDate is in the current year
        "Yes",  -- Return "Yes" if in the current year
        "No"  -- Return "No" if in any other year
    )

-- ===========================
-- 5. Check if Store Opened in Current Month (MTD)
-- ===========================
-- MTD (Month-to-Date) Check: If the store opened in the current month
VAR IsMTD = 
    IF(
        YEAR(OpenDate) = YEAR(TodayDate) && MONTH(OpenDate) = MONTH(TodayDate),  -- Check if the OpenDate is in the current month of the current year
        "Yes",  -- Return "Yes" if in the current month
        "No"  -- Return "No" if in any other month
    )

-- ===========================
-- 6. Final Output: Return Store Information with Calculations
-- ===========================
RETURN 
"📅 Opened on " & FullDate & 
" (" & WeekDay & 
") | Week #: " & WeekNum &  -- Display the week number of the store's opening
" | Age: " & YearAge & " yrs (" & ROUND(PreciseAge, 2) & " exact) " &  -- Display the store's age (both in full years and precise fractional years)
"⏱️ Opened at " & HourOpened & "h " & MinuteOpened & "m " & SecondOpened & "s" &  -- Display the exact time the store opened (in hours, minutes, and seconds)
" | 1 Yr Later: " & FORMAT(OneYearLaterDate, "DD-MMM-YYYY") &  -- Display the date one year after the store's opening
" | In YTD: " & IsYTD &  -- Show whether the store's opening is in the current year's range (YTD)
" | In MTD: " & IsMTD &  -- Show whether the store's opening is in the current month's range (MTD)
" | Report Time: " & FORMAT(NowTime, "DD-MMM-YYYY hh:mm:ss AM/PM")  -- Display the time the report was generated

```
