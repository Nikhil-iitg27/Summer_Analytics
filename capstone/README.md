# 🚗 Dynamic Parking Price Optimization using Pathway & Bokeh

This project implements real-time, demand-driven dynamic pricing for parking spaces using [Pathway](https://pathway.com/) and [Bokeh](https://bokeh.org/) for visualization.

## 🔧 Tools & Technologies Used

| Tool/Library | Purpose |
|--------------|---------|
| **Pathway**  | Real-time data streaming, windowed aggregations, and reactive computations |
| **Bokeh**    | Interactive, real-time visualizations (pricing graphs, trends) |
| **Pandas**   | Initial CSV data cleaning and formatting |
| **Google Colab** | Final interactive execution environment for notebook-style demo |
| **Panel (optional)** | GUI dashboard for embedding Bokeh plots (if used) |

---

## 📊 Models Implemented

### Model 1: **Occupancy-Based Smooth Baseline**

- **Formula Used**:  
  \[
  \text{price}_t = 8 + \frac{12}{1 + \left( \frac{\text{Capacity}}{\text{Average Occupancy} / 2} \right)}
  \]

- **Interpretation**:
  - As **Occupancy increases**, price increases.
  - The non-linear sigmoid-like shape smooths the growth and prevents abrupt jumps.
  - Caps and floors are built-in via the formula bounds.

### Model 2: **Demand-Based Dynamic Pricing**
A more advanced pricing mechanism that integrates multiple features.

#### 🔑 Demand Function
\[
\text{Demand} = \alpha \cdot \frac{\text{Occupancy}}{\text{Capacity}} + 
\beta \cdot \text{QueueLength} - 
\gamma \cdot \text{Traffic} +
\delta \cdot \text{IsSpecialDay} +
\epsilon \cdot \text{VehicleTypeWeight}
\]

| Variable | Weight |
|----------|--------|
| α (Occupancy Rate) | 1.0 |
| β (Queue Length) | 0.05 |
| γ (Traffic Level) | 0.1 |
| δ (Special Day) | 0.2 |
| ε (Vehicle Type Weight) | 1.0 |

- **Normalized demand** computed and used to scale the price:
  \[
  \text{Price} = \text{BasePrice} \cdot (1 + \lambda \cdot \text{NormalizedDemand})
  \]
  with price clipped to **[5, 20]** range.

---

## 📈 Real-Time Visualizations (Bokeh)

We used Bokeh to generate:
- **Live pricing plot** for a given parking location.
- **Comparison** between Model 1 and Model 2 prices over time (if competitor data is simulated).
- **Price trends with occupancy/demand overlays** (if desired).

---

## 📁 Dataset

We use a cleaned CSV file containing:

| Column Name | Description |
|-------------|-------------|
| `Timestamp` | DateTime of observation |
| `Occupancy`, `Capacity` | Parking utilization |
| `QueueLength`, `TrafficConditionNearby` | Indicators of congestion |
| `IsSpecialDay` | Binary flag |
| `VehicleType` | Categorical type (car, bike, truck, cycle, etc.) |

---

## 🧪 Assumptions

- Traffic level is mapped to numeric values:
  - `"low"` = 1.0, `"average"` = 2.5, `"high"` = 5.0
- Vehicle type is mapped to weights:
  - `"car"` = 1.0, `"bike"` = 0.5, `"truck"` = 1.5, `"cycle"` = 0.2
- Each day is treated as a tumbling window.
- Missing or unknown values default to neutral (e.g., weight = 1.0).

---

## 📝 Submission Includes

- ✅ Well-documented **Google Colab notebook**
- ✅ Embedded **Bokeh plots** (interactive or screenshots)
- ✅ **README.md** (this file)

---

## 🚀 To Run

```python
# Load CSV into Pathway
data = pw.io.csv.read("parking_stream_2.csv", schema=ParkingSchema)

# Compute real-time pricing using demand model
pw.run()
