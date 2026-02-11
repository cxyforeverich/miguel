# Unit Convention (Power/Energy)

To keep calculations consistent across components, MiGUEL uses the following convention:

- **Instantaneous power inside simulation core:** `W`
- **Energy over time:** `Wh` / `kWh`
- **Economic specific costs for generators/converters:** mostly per `kW`

## Where power-unit conversion is applied

### 1) GUI input layer (user-facing values in kW)
- GUI forms collect rated power in **kW**.
- Before calling `Environment.add_*`, GUI converts to **W** (`* 1000`) for internal simulation.

Main locations:
- `modern_gui.py::add_pv`
- `modern_gui.py::add_wind_turbine`
- `modern_gui.py::add_battery`
- `modern_gui.py::add_electrolyser`
- `modern_gui.py::add_fuelcell`

### 2) Environment/component simulation layer (internal power in W)
- Component nominal power fields (`p_n`, `max_power`) are used as **W** for dispatch and balance equations.
- Dispatch dataframe columns such as `P [W]`, `Power Output [W]`, `P_Res [W]` are all **W**.

Main locations:
- `environment.py` (`add_pv`, `add_wind_turbine`, `add_storage`, `add_electrolyser`, `add_fuel_cell`)
- `operation.py` dispatch logic

### 3) Economic calculations (convert W -> kW when using specific costs)
When specific CAPEX/OPEX is given in `USD/kW` or `USD/kW/a`, cost computation converts rated power from `W` to `kW` via `/1000`.

Examples:
- PV: `components/pv.py`
- Wind turbine: `components/windturbine.py`
- Electrolyser: `components/electrolyser.py`
- Fuel cell: `components/fuel_cell.py` (`max_power_kw = max_power / 1000` used for CAPEX/OPEX/CO2)

## Rule of thumb
- **Never mix W and kW in the same formula without explicit conversion.**
- If a variable ends with `_kw`, it is in kW.
- If a dataframe column contains `[W]`, it must remain in W.
