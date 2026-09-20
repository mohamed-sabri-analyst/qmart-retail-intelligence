# QMart Retail Intelligence — Enterprise BI Platform — Key DAX Measures

Organized by the same Display Folders used inside Power BI Desktop — 159 measures across 13 categories.

---

## Core Calculations

**Total Customers PY**

```dax
CALCULATE([Total Customers], SAMEPERIODLASTYEAR(Dim_Date[Date]))
```

## KPI & Executive Summary Measures

**KPI Revenue Target**

```dax
2100000000
```

**Revenue vs Target %**

```dax
DIVIDE([Total Revenue], [KPI Revenue Target])
```

**KPI Status Icon**

```dax
VAR Achievement = [Revenue vs Target %]
RETURN
    SWITCH(TRUE(),
        Achievement >= 1, "🟢",
        Achievement >= 0.85, "🟡",
        "🔴"
    )
```

**Profit Margin Target %**

```dax
0.30
```

**Margin vs Target**

```dax
[Gross Margin %] - [Profit Margin Target %]
```

**YTD vs Annual Target Pace %**

```dax
VAR DaysElapsed = DATEDIFF(DATE(YEAR(TODAY()),1,1), TODAY(), DAY) + 1
VAR ExpectedPace = DIVIDE(DaysElapsed, 365)
RETURN DIVIDE([Revenue YTD], [KPI Revenue Target]) - ExpectedPace
```

**Overall Health Score**

```dax
VAR RevScore = MIN([Revenue vs Target %], 1) * 40
VAR MarginScore = MIN(DIVIDE([Gross Margin %], 0.35), 1) * 30
VAR ReturnScore = (1 - MIN([Return Rate % (by Value)] / 0.1, 1)) * 30
RETURN RevScore + MarginScore + ReturnScore
```

**Total Measures Count Check**

```dax
1
```

## Sales & Revenue

**Total Revenue**

```dax
SUM(Fact_Sales[NetAmount])
```

**Total Gross Sales**

```dax
SUM(Fact_Sales[GrossAmount])
```

**Total Discount Given**

```dax
SUM(Fact_Sales[DiscountAmount])
```

**Total Quantity Sold**

```dax
SUM(Fact_Sales[Quantity])
```

**Total Transactions**

```dax
DISTINCTCOUNT(Fact_Sales[TransactionID])
```

**Total Line Items**

```dax
COUNTROWS(Fact_Sales)
```

**Average Order Value**

```dax
DIVIDE([Total Revenue], [Total Transactions])
```

**Average Selling Price**

```dax
DIVIDE([Total Revenue], [Total Quantity Sold])
```

**Average Basket Size (Items)**

```dax
DIVIDE([Total Line Items], [Total Transactions])
```

**Revenue per Store**

```dax
DIVIDE([Total Revenue], DISTINCTCOUNT(Fact_Sales[StoreKey]))
```

**Revenue per Customer**

```dax
DIVIDE([Total Revenue], DISTINCTCOUNT(Fact_Sales[CustomerKey]))
```

**Discount Rate %**

```dax
DIVIDE([Total Discount Given], [Total Gross Sales])
```

**In-Store Revenue**

```dax
CALCULATE([Total Revenue], Dim_Customer[PreferredChannel] = "In-Store")
```

**Online Revenue (App+Web)**

```dax
CALCULATE([Total Revenue],
    Dim_Customer[PreferredChannel] IN {"Mobile App", "Website"})
```

**Weekend Revenue**

```dax
CALCULATE([Total Revenue], Dim_Date[IsWeekend] = TRUE)
```

**Weekday Revenue**

```dax
CALCULATE([Total Revenue], Dim_Date[IsWeekend] = FALSE)
```

**Ramadan Revenue**

```dax
CALCULATE([Total Revenue], Dim_Date[IsRamadan] = TRUE)
```

**Holiday Season Revenue**

```dax
CALCULATE([Total Revenue], Dim_Date[IsHolidaySeason] = TRUE)
```

**Revenue Rank by Store**

```dax
RANKX(ALL(Dim_Store[StoreName]), [Total Revenue], , DESC)
```

**% of Total Revenue**

```dax
DIVIDE([Total Revenue], CALCULATE([Total Revenue], ALL(Dim_Store)))
```

**Revenue Contribution by Department**

```dax
DIVIDE([Total Revenue], CALCULATE([Total Revenue], ALL(Dim_Product[Department])))
```

## Profitability

**Total COGS**

```dax
SUM(Fact_Sales[COGS])
```

**Total Profit**

```dax
SUM(Fact_Sales[ProfitAmount])
```

**Gross Margin %**

```dax
DIVIDE([Total Profit], [Total Revenue])
```

**Markup %**

```dax
DIVIDE([Total Profit], [Total COGS])
```

**Profit per Transaction**

```dax
DIVIDE([Total Profit], [Total Transactions])
```

**Profit per Unit**

```dax
DIVIDE([Total Profit], [Total Quantity Sold])
```

**Profit per Store**

```dax
DIVIDE([Total Profit], DISTINCTCOUNT(Fact_Sales[StoreKey]))
```

**Electronics Profit**

```dax
CALCULATE([Total Profit], Dim_Product[Department] = "Electronics")
```

**Fashion Profit**

```dax
CALCULATE([Total Profit], Dim_Product[Department] = "Fashion")
```

**Grocery Profit**

```dax
CALCULATE([Total Profit], Dim_Product[Department] = "Grocery")
```

**High Margin Sales (>40%)**

```dax
CALCULATE([Total Revenue],
    FILTER(Fact_Sales, DIVIDE(Fact_Sales[ProfitAmount], Fact_Sales[NetAmount]) > 0.4))
```

**Low Margin Sales (<15%)**

```dax
CALCULATE([Total Revenue],
    FILTER(Fact_Sales, DIVIDE(Fact_Sales[ProfitAmount], Fact_Sales[NetAmount]) < 0.15))
```

**Profit Rank by Product**

```dax
RANKX(ALL(Dim_Product[ProductName]), [Total Profit], , DESC)
```

**Contribution Margin**

```dax
[Total Revenue] - [Total COGS]
```

**Break-even Units**

```dax
VAR FixedCosts = 50000000  -- ضع تقديرك السنوي بالريال القطري
VAR ContributionMarginPerUnit = DIVIDE([Total Profit], [Total Quantity Sold])
RETURN
DIVIDE(FixedCosts, ContributionMarginPerUnit, 0)
```

## Customer Analytics

**Total Customers**

```dax
DISTINCTCOUNT(Fact_Sales[CustomerKey])
```

**Guest Checkout Transactions**

```dax
CALCULATE([Total Transactions], Fact_Sales[CustomerKey] = 0)
```

**Guest Checkout %**

```dax
DIVIDE([Guest Checkout Transactions], [Total Transactions])
```

**Repeat Customers**

```dax
COUNTROWS(
    FILTER(VALUES(Dim_Customer[CustomerKey]),
        CALCULATE(DISTINCTCOUNT(Fact_Sales[TransactionID])) > 1)
)
```

**Repeat Rate**

```dax
DIVIDE([Repeat Customers], [Total Customers])
```

**CLV**

```dax
DIVIDE([Total Revenue], [Total Customers])
```

**Purchase Frequency**

```dax
DIVIDE([Total Transactions], [Total Customers])
```

**Platinum Tier Revenue**

```dax
CALCULATE([Total Revenue], Dim_Customer[MembershipTier] = "Platinum")
```

**Gold Tier Revenue**

```dax
CALCULATE([Total Revenue], Dim_Customer[MembershipTier] = "Gold")
```

**Silver Tier Revenue**

```dax
CALCULATE([Total Revenue], Dim_Customer[MembershipTier] = "Silver")
```

**Bronze Tier Revenue**

```dax
CALCULATE([Total Revenue], Dim_Customer[MembershipTier] = "Bronze")
```

**Qatari Customer Revenue**

```dax
CALCULATE([Total Revenue], Dim_Customer[Nationality] = "Qatari")
```

**Average Age of Customers**

```dax
CALCULATE(AVERAGE(Dim_Customer[BirthYear]))
```

**Active Customers**

```dax
CALCULATE(DISTINCTCOUNT(Fact_Sales[CustomerKey]), Dim_Customer[IsActive] = TRUE)
```

**New Customers (First Purchase in Period)**

```dax
VAR CustomersFirstEver =
    FILTER(
        VALUES(Dim_Customer[CustomerKey]),
        CALCULATE(MIN(Fact_Sales[DateKey]), ALL(Dim_Date)) = CALCULATE(MIN(Fact_Sales[DateKey]))
    )
RETURN COUNTROWS(CustomersFirstEver)
```

**Expat Customer Revenue**

```dax
CALCULATE([Total Revenue], Dim_Customer[Nationality] <> "Qatari")
```

**Average Revenue per Loyal Customer**

```dax
CALCULATE([CLV],
    Dim_Customer[MembershipTier] IN {"Gold", "Platinum"})
```

**Top 20% Share**

```dax
DIVIDE(
    SUMX(
        TOPN(
            ROUNDUP(DISTINCTCOUNT(Fact_Sales[CustomerKey]) * 0.2, 0), 
            ADDCOLUMNS(VALUES(Dim_Customer[CustomerKey]), "@CustRev", [Total Revenue]), 
            [@CustRev], 
            DESC
        ), 
        [@CustRev]
    ), 
    [Total Revenue]
)
```

**Customer Churn Risk (No Purchase 90+ Days)**

```dax
COUNTROWS(
    FILTER(
        VALUES(Dim_Customer[CustomerKey]),
        VAR LastDateKey = CALCULATE(MAX(Fact_Sales[DateKey]))
        VAR LastPurchaseDate =
            DATE(
                INT(LastDateKey / 10000),
                INT(MOD(LastDateKey, 10000) / 100),
                MOD(LastDateKey, 100)
            )
        RETURN
            NOT ISBLANK(LastDateKey) && DATEDIFF(LastPurchaseDate, TODAY(), DAY) > 90
    )
)
```

## Product Analytics

**Total Products Sold (Distinct)**

```dax
DISTINCTCOUNT(Fact_Sales[ProductKey])
```

**Total Active SKUs**

```dax
CALCULATE(DISTINCTCOUNT(Dim_Product[ProductKey]), Dim_Product[Status] = "Active")
```

**Best Selling Product (Qty)**

```dax
CALCULATE([Total Quantity Sold],
    TOPN(1, VALUES(Dim_Product[ProductName]), [Total Quantity Sold], DESC))
```

**Top Product Revenue Rank**

```dax
RANKX(ALL(Dim_Product[ProductName]), [Total Revenue], , DESC)
```

**Top 10 Products Revenue %**

```dax
VAR ProdTable = ADDCOLUMNS(VALUES(Dim_Product[ProductKey]), "PRev", [Total Revenue])
VAR Top10 = TOPN(10, ProdTable, [PRev])
RETURN DIVIDE(SUMX(Top10, [PRev]), [Total Revenue])
```

**Electronics Revenue**

```dax
CALCULATE([Total Revenue], Dim_Product[Department] = "Electronics")
```

**Fashion Revenue**

```dax
CALCULATE([Total Revenue], Dim_Product[Department] = "Fashion")
```

**Grocery Revenue**

```dax
CALCULATE([Total Revenue], Dim_Product[Department] = "Grocery")
```

**Electronics Revenue Share %**

```dax
DIVIDE([Electronics Revenue], [Total Revenue])
```

**Fashion Revenue Share %**

```dax
DIVIDE([Fashion Revenue], [Total Revenue])
```

**Grocery Revenue Share %**

```dax
DIVIDE([Grocery Revenue], [Total Revenue])
```

**Slow Moving Products (Below Avg Qty)**

```dax
VAR AvgQty = AVERAGEX(VALUES(Dim_Product[ProductKey]), [Total Quantity Sold])
RETURN
    COUNTROWS(FILTER(VALUES(Dim_Product[ProductKey]), [Total Quantity Sold] < AvgQty))
```

**New Product Revenue (Launched This Year)**

```dax
CALCULATE([Total Revenue], Dim_Product[LaunchYear] = YEAR(TODAY()))
```

**Discontinued Product Revenue**

```dax
CALCULATE([Total Revenue], Dim_Product[Status] = "Discontinued")
```

## Store & Regional Performance

**Active Stores**

```dax
DISTINCTCOUNT(Dim_Store[StoreKey])
```

**Top Store**

```dax
CALCULATE(
    SELECTEDVALUE(Dim_Store[StoreName]),
    TOPN(1, ALL(Dim_Store[StoreName]), [Total Revenue], DESC)
)
```

**Store Rank (Revenue)**

```dax
RANKX(ALL(Dim_Store), [Total Revenue], , DESC)
```

**Store Rank (Profit)**

```dax
RANKX(ALL(Dim_Store[StoreName]), [Total Profit], , DESC)
```

**Store Transaction Share %**

```dax
DIVIDE([Total Transactions], CALCULATE([Total Transactions], ALL(Dim_Store)))
```

**Avg Revenue/Store**

```dax
AVERAGEX(VALUES(Dim_Store[StoreKey]), [Total Revenue])
```

**Stores Above Average Revenue**

```dax
VAR AvgRev = [Avg Revenue/Store]
RETURN COUNTROWS(FILTER(VALUES(Dim_Store[StoreKey]), [Total Revenue] > AvgRev))
```

**Underperforming Stores (Below 80% of Avg)**

```dax
VAR AvgRev = [Avg Revenue/Store]
RETURN COUNTROWS(FILTER(VALUES(Dim_Store[StoreKey]), [Total Revenue] < AvgRev * 0.8))
```

**New Stores Revenue (Opened Last 12 Months)**

```dax
CALCULATE([Total Revenue],
    FILTER(Dim_Store, DATEDIFF(Dim_Store[OpenDate], TODAY(), MONTH) <= 12))
```

**Rev/Sqm**

```dax
DIVIDE([Total Revenue], SUM(Dim_Store[SizeSqm]))
```

**Doha Revenue**

```dax
CALCULATE([Total Revenue], Dim_Store[Municipality] = "Doha")
```

**Al Rayyan Revenue**

```dax
CALCULATE([Total Revenue], Dim_Store[Municipality] = "Al Rayyan")
```

**Flagship Store Revenue**

```dax
CALCULATE([Total Revenue], Dim_Store[StoreType] = "Flagship Mega Store")
```

**Express Store Revenue**

```dax
CALCULATE([Total Revenue], Dim_Store[StoreType] = "Express Store")
```

## Inventory Management

**Total Stock on Hand**

```dax
SUM(Fact_Inventory[StockOnHand])
```

**Total Inventory Value**

```dax
SUM(Fact_Inventory[InventoryValue])
```

**Total Units Received**

```dax
SUM(Fact_Inventory[UnitsReceived])
```

**Low Stock Items (Count)**

```dax
CALCULATE(DISTINCTCOUNT(Fact_Inventory[ProductKey]), Fact_Inventory[IsLowStock] = TRUE)
```

**Low Stock Rate %**

```dax
DIVIDE([Low Stock Items (Count)], DISTINCTCOUNT(Fact_Inventory[ProductKey]))
```

**Inventory Turnover Ratio**

```dax
DIVIDE([Total COGS], AVERAGE(Fact_Inventory[InventoryValue]))
```

**Average Stock per Product**

```dax
AVERAGE(Fact_Inventory[StockOnHand])
```

**Sell-Through Rate %**

```dax
DIVIDE(SUM(Fact_Inventory[UnitsSoldEstimate]),
    SUM(Fact_Inventory[UnitsSoldEstimate]) + [Total Stock on Hand])
```

**Out of Stock Risk Products**

```dax
CALCULATE(DISTINCTCOUNT(Fact_Inventory[ProductKey]), Fact_Inventory[StockOnHand] = 0)
```

**Inventory Value by Department**

```dax
CALCULATE([Total Inventory Value], ALLEXCEPT(Dim_Product, Dim_Product[Department]))
```

## Returns Analysis

**Total Returns (Count)**

```dax
COUNTROWS(Fact_Returns)
```

**Total Refund Amount**

```dax
SUM(Fact_Returns[RefundAmount])
```

**Return Rate % (by Line Items)**

```dax
DIVIDE([Total Returns (Count)], [Total Line Items])
```

**Return Rate % (by Value)**

```dax
DIVIDE([Total Refund Amount], [Total Revenue])
```

**Net Revenue After Returns**

```dax
[Total Revenue] - [Total Refund Amount]
```

**Fashion Return Rate %**

```dax
VAR FashionReturns =
    CALCULATE([Total Returns (Count)], Dim_Product[Department] = "Fashion")
VAR FashionLines =
    CALCULATE([Total Line Items], Dim_Product[Department] = "Fashion")
RETURN DIVIDE(FashionReturns, FashionLines)
```

**Top Return Reason**

```dax
CALCULATE(
    SELECTEDVALUE(Fact_Returns[ReturnReason]),
    TOPN(1, ALL(Fact_Returns[ReturnReason]),
        CALCULATE(COUNTROWS(Fact_Returns)), DESC)
)
```

**Returns by Defective Product %**

```dax
DIVIDE(
    CALCULATE([Total Returns (Count)], Fact_Returns[ReturnReason] = "Defective Product"),
    [Total Returns (Count)]
)
```

**Store with Highest Return Rate**

```dax
CALCULATE(
    SELECTEDVALUE(Dim_Store[StoreName]),
    TOPN(1, ALL(Dim_Store[StoreName]), [Total Returns (Count)], DESC)
)
```

**Average Days to Return**

```dax
AVERAGEX(
    Fact_Returns,
    VAR SaleDateKey = Fact_Returns[OriginalSaleDateKey]
    VAR ReturnDateKey = Fact_Returns[ReturnDateKey]
    VAR SaleDate =
        DATE(
            INT(SaleDateKey / 10000),
            INT(MOD(SaleDateKey, 10000) / 100),
            MOD(SaleDateKey, 100)
        )
    VAR ReturnDate =
        DATE(
            INT(ReturnDateKey / 10000),
            INT(MOD(ReturnDateKey, 10000) / 100),
            MOD(ReturnDateKey, 100)
        )
    RETURN
        DATEDIFF(SaleDate, ReturnDate, DAY)
)
```

## Promotions & Marketing

**Revenue with Promotion**

```dax
CALCULATE([Total Revenue], Fact_Sales[PromotionKey] <> 0)
```

**Revenue without Promotion**

```dax
CALCULATE([Total Revenue], Fact_Sales[PromotionKey] = 0)
```

**Promotion Penetration %**

```dax
DIVIDE(
    CALCULATE([Total Transactions], Fact_Sales[PromotionKey] <> 0),
    [Total Transactions]
)
```

**Total Active Promotions**

```dax
CALCULATE(DISTINCTCOUNT(Dim_Promotion[PromotionKey]), Dim_Promotion[PromotionKey] <> 0)
```

**Average Discount % per Promo**

```dax
CALCULATE(AVERAGE(Dim_Promotion[DiscountPercent]), Dim_Promotion[PromotionKey] <> 0)
```

**Best Performing Promotion (Revenue)**

```dax
CALCULATE(
    SELECTEDVALUE(Dim_Promotion[PromotionName]),
    TOPN(1, FILTER(ALL(Dim_Promotion[PromotionName]), TRUE), [Revenue with Promotion], DESC)
)
```

**Ramadan Promo Revenue**

```dax
CALCULATE([Total Revenue], Dim_Promotion[PromotionType] = "Ramadan Special")
```

**National Day Promo Revenue**

```dax
CALCULATE([Total Revenue], Dim_Promotion[PromotionType] = "National Day Special")
```

**Incremental Revenue from Promotions**

```dax
VAR AvgOrderNoPromo = DIVIDE([Revenue without Promotion],
    CALCULATE([Total Transactions], Fact_Sales[PromotionKey] = 0))
VAR PromoTxns = CALCULATE([Total Transactions], Fact_Sales[PromotionKey] <> 0)
RETURN [Revenue with Promotion] - (AvgOrderNoPromo * PromoTxns)
```

**Promo Profit Impact %**

```dax
VAR ProfitWithPromo = CALCULATE([Total Profit], Fact_Sales[PromotionKey] <> 0)
VAR ProfitNoPromo = CALCULATE([Total Profit], Fact_Sales[PromotionKey] = 0)
RETURN DIVIDE(ProfitWithPromo - ProfitNoPromo, ProfitNoPromo)
```

## Payment & Channel Analysis

**Cash Payment Revenue**

```dax
CALCULATE([Total Revenue], Dim_PaymentMethod[PaymentCategory] = "Cash")
```

**Card Payment Revenue**

```dax
CALCULATE([Total Revenue], Dim_PaymentMethod[PaymentCategory] = "Card")
```

**Digital Wallet Revenue**

```dax
CALCULATE([Total Revenue], Dim_PaymentMethod[PaymentCategory] = "Digital Wallet")
```

**Cashless Transaction Rate %**

```dax
DIVIDE(
    CALCULATE([Total Transactions], Dim_PaymentMethod[PaymentCategory] <> "Cash"),
    [Total Transactions]
)
```

**Most Used Payment Method**

```dax
CALCULATE(
    SELECTEDVALUE(Dim_PaymentMethod[PaymentMethod]),
    TOPN(1, ALL(Dim_PaymentMethod[PaymentMethod]), [Total Transactions], DESC)
)
```

**Installment Plan Revenue**

```dax
CALCULATE([Total Revenue], Dim_PaymentMethod[PaymentMethod] = "Installment Plan")
```

## Employee Performance

**Total Active Employees**

```dax
CALCULATE(DISTINCTCOUNT(Dim_Employee[EmployeeKey]), Dim_Employee[IsActive] = TRUE)
```

**Revenue per Employee**

```dax
DIVIDE([Total Revenue], DISTINCTCOUNT(Fact_Sales[EmployeeKey]))
```

**Top Performing Employee (Revenue)**

```dax
CALCULATE(
    SELECTEDVALUE(Dim_Employee[EmployeeName]),
    TOPN(1, ALL(Dim_Employee[EmployeeName]), [Total Revenue], DESC)
)
```

**Employee Sales Rank**

```dax
RANKX(ALL(Dim_Employee[EmployeeName]), [Total Revenue], , DESC)
```

**Transactions per Employee**

```dax
DIVIDE([Total Transactions], DISTINCTCOUNT(Fact_Sales[EmployeeKey]))
```

**Average Basket Value per Employee**

```dax
DIVIDE([Total Revenue], [Total Transactions])
```

**Electronics Dept Employee Revenue**

```dax
CALCULATE([Total Revenue], Dim_Employee[Department] = "Electronics")
```

**Store Manager Count**

```dax
CALCULATE(DISTINCTCOUNT(Dim_Employee[EmployeeKey]), Dim_Employee[JobRole] = "Store Manager")
```

## Time Intelligence

**Revenue PY (Prior Year)**

```dax
CALCULATE([Total Revenue], SAMEPERIODLASTYEAR(Dim_Date[Date]))
```

**Revenue YoY Growth %**

```dax
DIVIDE([Total Revenue] - [Revenue PY (Prior Year)] , [Revenue PY (Prior Year)])
```

**Revenue PM (Prior Month)**

```dax
CALCULATE([Total Revenue], DATEADD(Dim_Date[Date], -1, MONTH))
```

**Revenue PQ (Prior Quarter)**

```dax
CALCULATE([Total Revenue], DATEADD(Dim_Date[Date], -1, QUARTER))
```

**Revenue YTD**

```dax
TOTALYTD([Total Revenue], Dim_Date[Date])
```

**Revenue QTD**

```dax
TOTALQTD([Total Revenue], Dim_Date[Date])
```

**Revenue MTD**

```dax
TOTALMTD([Total Revenue], Dim_Date[Date])
```

**Revenue YTD PY**

```dax
CALCULATE([Revenue YTD], SAMEPERIODLASTYEAR(Dim_Date[Date]))
```

**Revenue YTD Growth %**

```dax
DIVIDE([Revenue YTD] - [Revenue YTD PY], [Revenue YTD PY])
```

**Rolling 3-Month Revenue**

```dax
CALCULATE([Total Revenue],
    DATESINPERIOD(Dim_Date[Date], MAX(Dim_Date[Date]), -3, MONTH))
```

**Rolling 12-Month Revenue**

```dax
CALCULATE([Total Revenue],
    DATESINPERIOD(Dim_Date[Date], MAX(Dim_Date[Date]), -12, MONTH))
```

**Revenue Last 7 Days**

```dax
CALCULATE([Total Revenue],
    DATESINPERIOD(Dim_Date[Date], MAX(Dim_Date[Date]), -7, DAY))
```

**Revenue Last 30 Days**

```dax
CALCULATE([Total Revenue],
    DATESINPERIOD(Dim_Date[Date], MAX(Dim_Date[Date]), -30, DAY))
```

**Profit YoY Growth %**

```dax
VAR CurrProfit = [Total Profit]
VAR PrevProfit = CALCULATE([Total Profit], SAMEPERIODLASTYEAR(Dim_Date[Date]))
RETURN DIVIDE(CurrProfit - PrevProfit, PrevProfit)
```

**Transactions YoY Growth %**

```dax
VAR Curr = [Total Transactions]
VAR Prev = CALCULATE([Total Transactions], SAMEPERIODLASTYEAR(Dim_Date[Date]))
RETURN DIVIDE(Curr - Prev, Prev)
```

**Same Store Sales Growth %**

```dax
VAR StoresActiveBothPeriods =
    FILTER(
        VALUES(Dim_Store[StoreKey]),
        CALCULATE([Total Transactions]) > 0 &&
        CALCULATE([Total Transactions], SAMEPERIODLASTYEAR(Dim_Date[Date])) > 0
    )
VAR CurrRevComparable = CALCULATE([Total Revenue], StoresActiveBothPeriods)
VAR PrevRevComparable = CALCULATE([Total Revenue], StoresActiveBothPeriods, SAMEPERIODLASTYEAR(Dim_Date[Date]))
RETURN DIVIDE(CurrRevComparable - PrevRevComparable, PrevRevComparable)
```

**Days with Sales**

```dax
CALCULATE(DISTINCTCOUNT(Fact_Sales[DateKey]))
```

**Average Daily Revenue**

```dax
DIVIDE([Total Revenue], [Days with Sales])
```

**Best Day Revenue**

```dax
MAXX(VALUES(Dim_Date[Date]), [Total Revenue])
```

**Cumulative Revenue (Running Total)**

```dax
CALCULATE([Total Revenue],
    FILTER(ALLSELECTED(Dim_Date[Date]), Dim_Date[Date] <= MAX(Dim_Date[Date])))
```

**Revenue YoY Growth (Value)**

```dax
[Total Revenue] - [Revenue PY (Prior Year)]
```

**Revenue MoM Growth %**

```dax
DIVIDE([Total Revenue] - [Revenue PM (Prior Month)], [Revenue PM (Prior Month)])
```

**Revenue QoQ Growth %**

```dax
DIVIDE([Total Revenue] - [Revenue PQ (Prior Quarter)], [Revenue PQ (Prior Quarter)])
```
