# Total Profit
  Total Profit = SUM(Orders[Profit])


# Total Revenue
  Total Revenue = SUM(Orders[Sales])


# Profit Margin
  Profit Margin = 
  DIVIDE(
      SUM(Orders[Profit]),
      SUM(Orders[Sales])
  )


# Discount Amount
  Discount Amount = 
  SUMX(
      Orders,
      Orders[Sales] * Orders[Discount]
  )


# Average Discount
  Avg Discount = 
  DIVIDE(
      [Discount Amount],
      [Total Revenue]
  )


# Product Action 
  Product Action = 
  IF (
      HASONEVALUE ( Orders[Product Name] ),
      SWITCH (
          TRUE(),
          [Profit Margin] < 0, "Drop",
          [Profit Margin] < 0.10, "Fix",
          "Scale"
      ),
      BLANK()
  )


# Customer Action 
  Customer Action = 
  VAR AvgDisc = [Avg Discount]
  VAR Margin = [Profit Margin]
  RETURN
  IF (
      HASONEVALUE ( Orders[Customer ID] ),
      SWITCH(
          TRUE(),
          AvgDisc < 0.20 && Margin >= 0, "Protect",
          AvgDisc >= 0.20 && Margin >= 0, "Monitor",
          AvgDisc < 0.20 && Margin < 0, "Renegotiate",
          AvgDisc >= 0.20 && Margin < 0, "Drop"
      )
  )


# Customers in Profit Leakage Zone
  Customers in Profit Leakage Zone = 
  CALCULATE(
      DISTINCTCOUNT(Orders[Customer ID]),
      FILTER(
          Orders,
          [Customer Action] = "Drop"
      )
  )


# Revenue at Risk
  Revenue at Risk = 
  CALCULATE(
      [Total Revenue],
      FILTER(
          Orders,
          [Customer Action] = "Drop"
      )
  )


# Profit Leakage
  Profit Leakage = 
  CALCULATE(
      [Total Profit],
      FILTER(
          Orders,
          [Customer Action] = "Drop"
      )
  )

