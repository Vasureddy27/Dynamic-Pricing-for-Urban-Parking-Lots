# Capstone Project Report: Dynamic Pricing for Urban Parking Lots

**Program:** Summer Analytics 2025
**Organized by:** Consulting & Analytics Club × Pathway
**Team Member:** Madderi Vasu

---

## 1. Introduction

Urban parking lots are high-demand resources that often suffer from inefficiencies due to static pricing. This project aims to solve that by implementing a dynamic pricing system that adapts in real-time using live data and machine learning logic. The final solution uses Pathway to simulate streaming data and updates parking prices based on demand and competition.

---

## 2. Dataset Overview

The dataset includes 14 parking spaces observed over 73 days at 18 time points per day (8:00 AM to 4:30 PM). Each record includes:

* **Location:** Latitude, Longitude
* **Parking Info:** Capacity, Occupancy, Queue Length
* **Vehicle Info:** Type of incoming vehicle (car, bike, truck)
* **External Info:** Traffic condition, Special day indicator
* **Time:** LastUpdatedDate and LastUpdatedTime (combined into `timestamp`)

---

## 3. Data Preprocessing Steps

1. **Combine Date & Time**: Created a `timestamp` column by merging `LastUpdatedDate` and `LastUpdatedTime`.
2. **Rename Columns**: Renamed original columns to standardized names like `lot_id`, `occupancy`, `queue_length`, etc.
3. **Cleaned CSV**: Saved cleaned data as `cleaned_dataset.csv` for Pathway streaming.

---

## 4. Model Implementation

### Model 1: Baseline Linear Pricing

**Formula:**

```
price_t+1 = price_t + α × (occupancy / capacity)
```

* Base price = \$10
* α = 2.0 (tunable factor)

**Justification:**
This simple model increases price linearly with occupancy, giving a reference point. Useful for benchmarking more intelligent models.

---

### Model 2: Demand-Based Pricing

**Demand Function:**

```
demand = α × occ_ratio + β × queue_norm - γ × traffic_norm + δ × is_special_day + ε × vehicle_weight
```

Where:

* Vehicle weights: Car = 1.0, Bike = 0.7, Truck = 1.5
* All inputs are normalized

**Price Formula:**

```
price = base_price × (1 + λ × normalized_demand)
```

* Clipped between \$5 and \$20

**Justification:**
Captures real-world influences on demand like traffic, event days, vehicle type, and queue. Provides a smooth and realistic price adjustment.

---

### Model 3: Competitive Pricing Model

**Additional Logic:**

* Calculates geographical distance between parking lots using `geopy`.
* If lot is full and nearby lots are cheaper → lower price or reroute.
* If nearby lots are expensive → increase price.

**Adjustment:**

```
price += adjustment_factor × competitor_signal
```

**Justification:**
Simulates real-world competition. Encourages optimal utilization and business-aware pricing.

---

## 5. Real-Time Streaming with Pathway

* Used `pathway.io.csv.read()` to stream `cleaned_dataset.csv`.
* Defined a schema with `pw.Schema` for structured input.
* Applied pricing logic as a UDF using `@pw.udf`.
* Emitted results to `output.jsonl` using `pathway.io.jsonlines.write()`.
* Suppressed verbose output using IPython capture.

---

## 6. Visualization

Used Bokeh to create a real-time pricing line plot for each parking lot:

* Price over time (x = timestamp, y = price)
* Simulated real-time stream using Bokeh's `ColumnDataSource.stream()`

---

## 7. Summary

| Model | Key Feature                 | Output Style         |
| ----- | --------------------------- | -------------------- |
| 1     | Linear price vs occupancy   | Simple benchmark     |
| 2     | Multi-variable demand model | Smooth dynamic price |
| 3     | Competitive geo-pricing     | Smart rerouting      |

This project showcases how data-driven dynamic pricing can be built from scratch, streamed in real-time, and visualized effectively using only Python, Pandas, NumPy, Pathway, and Bokeh.

---

## 8. Future Work

* Integrate real-time rerouting suggestions
* Enhance competitive model with distance-weighted pricing
* Deploy using Pathway's cloud API for real deployments

---

## 9. References

* Pathway Developer Docs: [https://pathway.com/developers/](https://pathway.com/developers/)
* Summer Analytics 2025 Problem Statement
* Bokeh Docs: [https://docs.bokeh.org/](https://docs.bokeh.org/)


