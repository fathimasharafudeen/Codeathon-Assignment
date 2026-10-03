# Codeathon-Assignment
# 🚚 Fleet Performance & Delivery Efficiency Dashboard

A Power BI dashboard for **Logistics & Transportation** that analyses fleet performance, fuel usage, delivery punctuality and route-level efficiency, built as a module-end Power BI assignment.

> **Goal:** give transport operations a single view to optimise routes and fleet usage.

![Dashboard preview](images/dashboard.png)
<!-- Add a screenshot of your final dashboard page at images/dashboard.png -->

---

## 📌 Key Insights (Jan–Feb 2023, 50 trips)

| KPI | Value |
|---|---|
| Total trips | 50 |
| Total distance | 52,941 km |
| On-Time Delivery % | **60%** (30 on-time, 20 late) |
| Fuel Efficiency | **≈ 11.43 km/L** |
| Cost per km | **≈ ₹10.18** |
| Avg. Delivery Time (estimated) | **≈ 21.18 hrs** |

---

## 📂 Dataset

The Excel workbook contains two sheets:

| Sheet | Description |
|---|---|
| `Trip_Data` | 50 trips: vehicle, driver, origin, destination, distance (km), fuel consumed (L), delivery status (On-Time / Late), delivery date |
| `Vehicle_Master` | 7 vehicles: vehicle type and maintenance cost |

- **Cities:** Bangalore, Chennai, Delhi, Hyderabad, Kolkata, Mumbai, Pune
- **Period:** 1 Jan 2023 – 28 Feb 2023

---

## 🧹 Data Preparation (Power Query)

Issues found in the raw data and how they were handled:

- **Wrong header:** the first column was named `Projects`; renamed to `Trip_ID`.
- **Corrupted IDs:** the first 12 trip IDs contained unrelated text; restored as `T001`–`T012`.
- **Bad distance value:** trip `T043` had `116+666` (text), which forced the column to Text. Set to **116 km**, since its fuel use (8.71 L) matches ~13 km/L like other trips, while 782 km would imply an unrealistic ~90 km/L.
- **Data types:** distance → Whole Number, fuel → Decimal Number, date → Date.
- **Missing fuel values:** none found in this dataset; the approach for filling them is the average fuel consumption per vehicle type.

---

## 🗂️ Data Model

`Vehicle_Master` (1) ──► (many) `Trip_Data`, joined on `Vehicle_ID` (one-to-many, single direction).

---

## 🧮 DAX Measures

> Column names may differ slightly in your file (e.g. spaces instead of underscores).

```DAX
Total Trips = COUNTROWS(Trip_Data)

On-Time Trips = CALCULATE(COUNTROWS(Trip_Data), Trip_Data[Delivery_Status] = "On-Time")

On-Time Delivery % = DIVIDE([On-Time Trips], [Total Trips])

Fuel Efficiency = DIVIDE(SUM(Trip_Data[Distance_km]), SUM(Trip_Data[Fuel_Consumed_L]))

Total Fuel Cost = SUM(Trip_Data[Fuel_Consumed_L]) * 100

Total Maintenance Cost =
SUMX(
    VALUES(Trip_Data[Vehicle_ID]),
    LOOKUPVALUE(Vehicle_Master[Maintenance_Cost],
                Vehicle_Master[Vehicle_ID], Trip_Data[Vehicle_ID])
)

Cost per km =
DIVIDE([Total Fuel Cost] + [Total Maintenance Cost], SUM(Trip_Data[Distance_km]))

Avg Delivery Time (hrs) =
DIVIDE(SUM(Trip_Data[Distance_km]) / 50, COUNTROWS(Trip_Data))

Route = Trip_Data[Origin] & " → " & Trip_Data[Destination]   -- calculated column
```

---

## 📊 Dashboard Visuals

| Visual | Purpose |
|---|---|
| **KPI visuals / cards** | Avg. Delivery Time, Cost per km, On-Time Delivery %, Fuel Efficiency |
| **Clustered bar chart** | On-Time Delivery % by Route (Top 10 by trips) |
| **Line chart** | Fuel Efficiency trend by month |
| **Map** | Delivery performance by route (Origin → Destination), bubbles by origin city, split by destination |
| **Slicers** | Vehicle Type, Delivery Status, Delivery Date |

---

## ⚠️ Assumptions & Limitations

- **Fuel price:** not in the dataset; assumed **₹100 per litre**.
- **Avg. Delivery Time:** the dataset has no delivery-time column, so it is **estimated** as `Distance ÷ 50 km/h ÷ trips` (assumed average speed of 50 km/h).
- **Maintenance cost** is counted once per vehicle, not once per trip, to avoid double counting.
- **KPI visuals** with a Month trend axis show the latest month (Feb 2023); the cards show the overall values.
- Data covers only **two months**, so the monthly trend has two points.
- Power BI's built-in Map visual cannot draw origin-to-destination lines; routes are shown via origin-city bubbles split by destination.

---

## 🛠️ Tools Used

- Microsoft Power BI Desktop (Power Query, DAX, data modelling, visuals)
- Microsoft Excel (source data)

---

## ▶️ How to Open

1. Clone or download this repository.
2. Open `Module_2_Codeathon_assignment.pbix` in **Power BI Desktop**.
3. If prompted, update the data source path to the Excel file in this repo (`Transform data → Data source settings`).

---

## 📁 Repository Structure

```
├── Module_2_Codeathon_assignment.pbix   # Power BI report
├── logistics_project_dataset.xlsx       # Source data
├── images/
│   └── dashboard.png                    # Dashboard screenshot
└── README.md
```

---

## 👤 Author

**[Your Name]** · [LinkedIn / GitHub profile link]
