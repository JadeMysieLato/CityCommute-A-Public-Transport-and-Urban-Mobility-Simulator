# 🚊 CityCommute: A Public Transport and Urban Mobility Simulator

> A Midterm Project by Jada Angela Mysie Lato


**CityCommute** is an educational Java console application designed to simulate and compare two different urban transportation models: a **car-dependent city** and a **public-transport-focused city**. By running both scenarios using the exact same underlying commuter population baseline, the system isolates how configuration changes across transit modes dynamically alter urban infrastructure indicators.

The application translates user-configured distributions, speed boundaries, operational overheads, and vehicle constraints into explicit reports detailing travel times, financial costs, congestion utilization brackets, fleet tailpipe emissions, parking space demands, and transit capacity bottlenecks.

---

## ⚙️ Core Calculation Mechanics

The simulation strictly enforces population constraints, integer-based routing choices, and physical resource ceilings using a structured set of mathematical calculation behaviors:

### 1. Commuter Allocation
To avoid fractional occupants and ensure total counts perfectly match the user-configured baseline population, the system drops fractional remains for the first two categories and routes the leftover remainder directly to the train network:
*   **Car Commuters** = Rounded Down ( Population * (Car Percentage / 100) )
*   **Bus Commuters** = Rounded Down ( Population * (Bus Percentage / 100) )
*   **Train Commuters** = Population - (Car Commuters + Bus Commuters)

### 2. Vehicle Dispatching & Service Shortages
Transit fleets calculate vehicle requirements based on physical operational trips rather than assuming boundless capacity:
*   **Cars Required** = Rounded Up ( Car Commuters / Average Occupants Per Car )
*   **Required Transit Trips** = Rounded Up ( Passenger Demand / Capacity Per Trip )
*   **Operating Trips** = Minimum Value Between ( Required Trips , Available Trips )
*   **Passengers Served** = Minimum Value Between ( Passenger Demand , Available Trips * Capacity Per Trip )
*   **Unmet Passengers** = Passenger Demand - Passengers Served

### 3. Financial & Environmental Impact Metrics
*   **Travel Time (minutes)** = ( Distance in km / Average Speed in km/h ) * 60
*   **Total Car Operating Cost** = Number of Cars * Distance * Operating Cost per Vehicle-km
*   **Per-Person Car Cost** = Total Car Operating Cost / Car Commuters
*   **Transit Cost (Bus/Train)** = Passenger Fare * Passengers Served
*   **Road Utilization (%)** = ( Number of Cars / Road Capacity ) * 100
*   **Parking Shortage** = Maximum Value Between 0 and ( Parking Demand - Parking Capacity ) *(where Parking Demand = Number of Cars)*

---

## 🏛️ Architecture & Object-Oriented Design

The codebase relies heavily on foundational object-oriented programming (OOP) principles to maintain a clean separation of concerns and avoid bloating a single file:

### Class Structure & Relationships

*   **`CitySimulation`**: Coordinates the primary interface control flow, handles deep keyboard input scans, evaluates configuration data ranges, triggers parallel simulations, and prints the finished side-by-side matrices.
*   **`City` (Abstract)**: Serves as the structural parent class holding shared urban properties (population, infrastructure ceilings, and percentage splits). It defines the polymorphic behavioral contract `public abstract SimulationResult simulateTravel()`.
    *   **`CarDependentCity`**: Overrides the parent contract to compile calculations matching a highway-centric, single-occupant suburban scenario.
    *   **`TransitFocusedCity`**: Overrides the parent contract to compile calculations mapping out a dense, mass-transit urban framework.
*   **`TransportMode` (Abstract)**: Encapsulates generic transportation metrics including average velocities and chemical emission rates.
    *   **`Car`, `Bus`, `Train`**: Specialized subclasses that extend `TransportMode` to embed unique localized variables (such as car occupancy rates, operational trip costs, bus limits, or ticket fares).
*   **`TravelCalculator` (Interface)**: Declares the computational methods `calculateTravelTime(double distance)` and `calculateEmissions(double distance, int vehicles)`. This interface is implemented by `TransportMode` to enforce uniform mathematical capabilities across all vehicle types.
*   **`SimulationResult`**: A lightweight Data Transfer Object (DTO) that holds computed metrics from a single city run to facilitate side-by-side printing.

---

## 🛡️ Input Validation Boundaries

To protect calculations from arithmetic failures (such as errors caused by unexpected division by zero), the interface enforces strict structural rules before running data through formulas:
*   **Strictly Positive (> 0):** Total population, journey travel distance, average vehicle speeds, transit vehicle capacities, and total road system capacities.
*   **Non-Negative (>= 0):** Mode allocation percentages, transit passenger fares, vehicle distance running costs, vehicle emission rates, scheduled transit trips, and destination parking slots.
*   **Mathematical Constraints:** The three distribution values (Car % + Bus % + Train %) must sum to **exactly 100%**. When the system checks an empty transit link with zero scheduled trips, it handles the exception gracefully by returning **`N/A`** for capacity utilization instead of dividing by zero.

---

## 🚀 Getting Started

### Prerequisites
*   **Java Development Kit (JDK)**: Version 8 or higher.
*   An IDE (such as NetBeans, IntelliJ IDEA, Eclipse) or a terminal command line interface.

### Running via Command Line
1. Clone or download the repository files into your working environment.
2. Navigate into the source directory containing the `.java` files:
   ```bash
   cd CityCommute_Java_Source
   ```
3. Compile all the application source modules together:
   ```bash
   javac *.java
   ```
4. Fire up the primary simulation execution controller loop:
   ```bash
   java CitySimulation
   ```

---

## 📋 Example Simulation Workflow

1.  **Collect Configurations**: The interactive application prompts for data inputs and scans them against safety verification baselines.
2.  **Initialize City Blueprints**: The factory logic spins up one `CarDependentCity` instance and one `TransitFocusedCity` instance using matching population baselines.
3.  **Execute Parallel Runs**: Each city entity processes its internal routing choices, dispatches the required vehicle counts, identifies unmet passenger bottlenecks, and estimates local emission rates.
4.  **Populate Results**: Computational outcomes are captured into distinct `SimulationResult` tracking objects.
5.  **Print Comparison Matrix**: The app formats an aligned side-by-side terminal report detailing the structural trade-offs between both city configurations alongside capacity shortage warnings.

---

## 📖 Educational Disclaimer
This simulator uses simplified mathematical assumptions and population-averaged calculations. It is explicitly designed for personal experimentation, conceptual systems training, and academic computer science coursework. It does not model individual route tracking, live traffic flow physics, micro-congestion delay scaling, or real-world traffic engineering statistics.
