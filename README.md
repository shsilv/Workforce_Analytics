# Workforce Management Analytics: Forecasting Staffing Needs & Optimizing Service Levels

## Problem Statement

Airlines, like customer support teams, must balance unpredictable demand with limited staffing resources. Flight delays can cascade into operational bottlenecks, negatively impacting passenger experience — similar to how unresolved support tickets affect satisfaction in a call center. Each delayed flight can be thought of as a “support ticket,” with delay minutes representing ticket complexity and passenger volume representing customer demand. Staffing (crew or gate agents) must be optimized to handle this workload efficiently.

**Project Goal:**  
This project applies workforce management analytics to airline delay data to forecast operational impact, estimate staffing requirements, and simulate surge scenarios (e.g., a +30% spike in delays). The analysis uses R for data processing and modeling and Tableau for the interactive dashboard, translating outputs into actionable workforce-planning recommendations.

**Personal Connection:**  
As a former flight attendant, I’ve seen firsthand how delays affect both passengers and staff. This experience inspired the analogy of flights as support tickets and informed my focus on modeling the operational impact of delays. 

---

## Data

- **Source:** [Bureau of Transportation Statistics – On-Time Performance Data](https://www.transtats.bts.gov/OT_Delay/ot_delaycause1.asp?qv52ynB=qn6n&20=E) 
- **Dataset:** `Airline_Delay_Cause.csv` (January-June 2025)
- **Processed output (exported to Tableau):** 'carrier_summary.csv'

### Limitations

- **Scope:** Data covers only January–June 2025, so results may not capture full-year or seasonal patterns.
- **Passenger estimates:** Counts were estimated (180 mainline, 70 regional per flight); actual volumes may differ.
- **Delay categories:** Broad classifications don’t capture multi-factor causes.
- **Agent efficiency assumptions:** Mainline agents modeled at 70 delay-minutes/hour, regional at 50; real performance may vary.
- **SLA modeling:** SLA capped at 100% using fixed staffing assumptions, which may not reflect carrier-specific targets.
- **Operational simplification:** Model doesn’t account for shift overlaps, labor laws, or dynamic reallocation.
- **Analogy scope:** Flights as “support tickets” is a simplification and omits complexities of real airline ops.

---

## Methodology

1. Load and clean January–June 2025 delay data in R; replace NA values with 0 for numeric delay/flight fields.
2. Classify carriers as mainline or regional.
3. Estimate passengers affected and compute an impact score:
```r
flight_data <- flight_data %>%
  mutate(carrier_type = if_else(carrier_name %in% mainline_carriers, "mainline", "regional"),
         avg_passengers = if_else(carrier_type == "mainline", 180, 70),
         estimated_passengers = arr_flights * avg_passengers,
         impact_score = arr_delay * avg_passengers,
         agent_capacity_minutes_per_hour = if_else(carrier_type == "mainline", 70, 50))
```
4. Aggregate totals by carrier (total_delay_minutes, passengers_affected, total_impact_score).
5. Model staffing & SLA under fixed staffing assumptions:
```r
agent_capacity_per_shift <- agent_capacity_minutes_per_hour * 8
fixed_agents <- if_else(carrier_type == "mainline", 200, 50)
agent_capacity_total <- fixed_agents * agent_capacity_per_shift * 22
sla_estimated <- pmin(1, agent_capacity_total / total_delay_minutes)
```
6. Simulate +30% delay scenario (recompute delay minutes, agents required, SLA).
7. Export carrier_summary.csv for Tableau dashboarding.

---

## Key Visualizations (R)

### Top 10 Carriers by Operational Impact
Impact score was used to identify carriers with the greatest operational disruption.

<img width="1344" height="960" alt="download" src="https://github.com/user-attachments/assets/4f2d424e-dcf7-4fbd-9013-8364074a1fdd" />


### Carrier Workload: Delay Minutes vs. Passengers Affected
This scatter plot compares total delay minutes to passengers affected per carrier.

<img width="1344" height="960" alt="download" src="https://github.com/user-attachments/assets/3996e43d-f328-4b7d-b370-f5a98d5dbab1" />


### Top 10 Carriers: Estimated Agents Required
This chart shows the estimated number of agents required by carrier, highlighting differences in staffing needs across airline types.

<img width="1344" height="960" alt="download" src="https://github.com/user-attachments/assets/0df06a0a-a548-45e9-986c-788e1cba2d0e" />


### Staffing Needs & SLA Under Normal vs. +30% Delay Scenario
This comparison illustrates how staffing requirements shift under normal conditions versus a simulated 30% increase in delays.

<img width="1344" height="960" alt="download" src="https://github.com/user-attachments/assets/f1c21a45-dd07-4513-84ab-ecfe04977480" />

---

## Interactive Dashboard

Explore the data and scenarios in greater detail through the interactive [Tableau dashboard](https://public.tableau.com/app/profile/sarah.hannah.silverstein/viz/AirlineWorkforcePlanningAnalysis/AirlineWorkforcePlanningAnalysis?publish=yes).

It includes the following key components:

- **KPIs:** Total Passengers Affected, Total Delay Minutes, and Average SLA Compliance
- **Charts:** Top Carriers by Operational Impact, Delay Minutes vs Passengers Affected, and Staffing Needs vs SLA under Normal and +30% Delay Scenarios

This allows for dynamic exploration beyond the static visualizations presented above.

---

## Insights and Recommendations

1. **Operational impact isn’t proportional to airline size:** Both regional and mainline carriers can generate significant disruption depending on delays and passenger volumes. Staffing should prioritize carriers based on impact scores, not just fleet size.
2. **Focus staffing on high-impact carriers:** Airlines with the largest total delay minutes and highest estimated passenger impact require targeted workforce allocation to maintain SLA performance.
3. **Scenario modeling highlights surge risk:** The +30% delay simulation shows that fixed staffing may be insufficient during spikes. Proactive adjustments or contingency staffing plans are critical to prevent SLA degradation.
4. **Consistent and accurate data drives better forecasts:** Uniform delay and cancellation reporting improves the precision of agent requirement estimates and SLA calculations, enabling more informed operational decisions.
5. **SLA estimates reflect fixed staffing limits:** With fixed agent allocations per carrier type, some airlines hit the maximum 100% estimated SLA. This indicates overstaffing relative workload for certain carriers under normal conditions and potential underperformance under surge scenarios.

---

### Full Code

The full R script used for data processing, modeling, and visualization is available [here](https://github.com/shsilv/Workforce_Analytics/blob/main/R%20Code).
