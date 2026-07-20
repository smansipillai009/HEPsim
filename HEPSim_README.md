# HEPSim

**Hybrid-Electric Propulsion Simulator** — an open-source platform for the
conceptual design and optimization of hybrid-electric aircraft propulsion systems.

Give it a mission. It returns the best hybrid-electric propulsion design — and shows its working.

---

## What it does

HEPSim automates the slow, manual trial-and-error of conceptual propulsion design.
Instead of an engineer guessing an architecture, guessing component sizes, simulating,
failing, and repeating, HEPSim does it automatically at scale:

```
MISSION → GENERATE candidates → SIZE from real components → SIMULATE the mission
        → REJECT infeasible → OPTIMIZE survivors → RANK → EXPLAIN the winner
```

You provide only the mission requirements (weight, payload, speed, altitude, endurance
target). HEPSim generates hundreds of candidate propulsion configurations, sizes each
from a library of real, buildable components, flies every one through the full mission
with a physics-based simulator, rejects the infeasible ones, optimizes the rest, and
recommends the best design with an engineering justification.

## Why it exists

Hybrid-electric propulsion is a fast-growing aerospace field (NASA SUSAN, EPFD, X-57;
HAL CATS/MALE UAV programs), but the tooling to explore it openly does not really exist.
Commercial conceptual-design tools are expensive, closed, and often export-restricted.
HEPSim is free and open, so students, researchers, and small teams can explore
hybrid-electric design spaces without those barriers.

## Key ideas

- **Design-space exploration, not single-point design.** HEPSim optimizes *the process
  of designing the aircraft*, evaluating many architectures rather than assuming one.
- **Two-level optimization.** An outer loop (NSGA-II) picks the aircraft configuration;
  an inner loop (energy-management strategy) decides how to split power between the
  engine and battery second-by-second.
- **Real components only.** The design space snaps to a library of real, discrete
  catalog parts — you cannot procure a 58.3 kW engine, so HEPSim doesn't pretend you can.
- **Manufacturing realism.** Packaging volume, centre-of-gravity, and manufacturing
  complexity are real constraints and objectives, not afterthoughts.

## Architecture

```
hepsim/
  mission/       mission profile & requirements
  library/       real-component database (engines, motors, batteries)
  generate/      enumerates candidate architectures
  physics/       simulation core: aero, propulsion, battery, fuel
  ems/           energy-management strategies (rule-based; DP/RL advanced)
  feasibility/   rejection rules (MTOW, SoC, climb, packaging, CG...)
  optimize/      NSGA-II outer loop
  dashboard/     Streamlit + Plotly UI
  explain/       natural-language justification of the winner
  data/          CSV libraries & assumptions tables
```

## Install

Requires Python 3.10+.

```bash
git clone https://github.com/<your-org>/hepsim.git
cd hepsim
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

Core dependencies: `numpy`, `scipy`, `pymoo`, `pandas`, `streamlit`, `plotly`, `matplotlib`.

## Quick start

```bash
# run a design study from a mission file
python -m hepsim.run --mission examples/uav_1000kg.yaml

# launch the interactive dashboard
streamlit run hepsim/dashboard/app.py
```

## Status

**v0.x — early development.** HEPSim began as a hackathon design-space-exploration tool
(Aerothon, IIT Indore × HAL) and is being refactored into a general open-source platform.
Expect breaking changes until v1.0.

Roadmap: single UAV class + rule-based EMS (v0.1) → multiple architectures + DP benchmark
(v0.2) → RL energy management + parametric CAD (v0.3) → web-hosted, community-driven (v1.0).

## Contributing

Contributions are welcome once v0.1 is published. The physics models and component library
are maintained by the aero/mechanical side; the simulation, optimizer, and interface by the
software side. See `CONTRIBUTING.md` (coming with v0.1) for the module ownership map and the
component-library data schema.

**Ground rule:** every number in the component library and assumptions tables must trace to
a source, or be labelled an engineering estimate with reasoning. No fake precision.

## License

Apache-2.0 (suggested — confirm before first public release). Chosen for permissive
corporate and academic adoption.

## Acknowledgements

Physics grounded in standard aircraft-performance references (Anderson, Raymer, Gudmundsson).
Reference programs: NASA Electrified Aircraft Propulsion (SUSAN, EPFD, X-57); HAL HTSE-1200
turboshaft and CATS/MALE UAV roadmap.
