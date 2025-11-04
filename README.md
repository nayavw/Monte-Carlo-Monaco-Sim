# Monte-Carlo-Monaco-Sim
Objective
This project simulates alternative race strategies for a backmarker car in the 2025 Monaco Grand Prix using the FastF1 API.
Specifically, it evaluates Bernie Collins’s “two very early stops” strategy: pitting on laps 1 and 2 under an early safety car, versus the actual strategy executed by the selected car in the race.

## Methodology
### Data Source
Race session data was loaded via the FastF1 API (get_session(2025, 'Monaco', 'R')), which provides:
- Lap times
- Tyre compounds and stint lengths
- Race control messages (to detect safety car periods)
- Starting grid and final classification
- Driver Selection
The simulation automatically selects the rearmost driver on the starting grid (a “backmarker”) to test recovery potential from alternate strategies.

Lap-Time Model
A simple linear tyre-degradation model was fitted per compound:
LapTime = Base + Slope × TireLife + 𝜖
where: 𝜖∼𝑁(0,𝜎^2) adds lap-to-lap noise.

Base pace and degradation slope were sampled with slight random variation for each Monte Carlo run.

### Race Simulation Logic
Each simulated race runs for the total number of laps (Monaco ≈ 78).
Pit-stop time losses are normally distributed (μ = 19 s, σ = 1.5 s) and reduced by ≈ 10 s if a safety car is active.
A random early safety car occurs with configurable probability (default 35%).
Overtaking constraints: passes can only occur if the trailing car is faster by > 1.2 s / lap on average (very restrictive to reflect Monaco’s narrow circuit).

## Strategies Compared
Actual-like strategy: The real pit sequence extracted from FastF1 stint data.
Collins' two-early-stop: Pit lap 1: Soft, lap 2: Hard, then run to the end.

### Monte Carlo Sampling
1,000 simulations were run for each strategy. Each run produces total race time, lap-by-lap times, and simulated finishing position (relative to field).

## Results Summary
Actual-like	
  - Avg Finish Pos: 19.0
  - P(top 10): 0 %
  - P(better than grid): 79 %
  - Avg Race Time (s): 6154 s

Collins early 2-stop
  - Avg Finish Pos: 20.0
  - P(top 10): 0 %
  - P(better than grid): 0 %
  - Avg Race Time (s): 7226 s

## Interpretation:
The actual strategy was significantly more time-efficient (~70 s faster on average).
The two-early-stop approach consistently finished last in every simulation, mainly due to:
  - Losing ~38 seconds in two early stops before tyre degradation had any benefit.
  - Lack of overtaking opportunities prevents recovery.
  - Early safety car not providing sufficient advantage to offset track-position loss.

## Visual Insights:
The finishing-position histogram shows that both strategies are clustered at the back, but “Actual-like” occasionally finishes one position higher.
The race-time distribution is clearly bimodal, with “Collins” running approximately 115 seconds slower on average.
The race trace plot shows the clear difference between the two strategies, with Collins' being obviously slower.
