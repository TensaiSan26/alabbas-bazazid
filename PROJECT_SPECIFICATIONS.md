# 21-Week Control Systems Portfolio — Project Specifications

## Constraints
- **Simulation only** (no hardware)
- **30-35 hours/week**
- **Software:** TIA Portal, ETAP, AutoCAD Electrical, MATLAB, CODESYS, Ignition, Python
- **Existing:** Railway Crossing project (Arduino-based)

---

## PROJECT 1: PLC-Based Conveyor Sorting System

### Overview
Simulate a manufacturing conveyor that sorts items by size/weight using PLC control.

### Software
- **TIA Portal** (PLC programming)
- **PLCSIM** (PLC simulation)
- **WinCC** (HMI)

### Skills Covered
| Category | Skills |
|---|---|
| PLC | Ladder Logic, Function Block Diagram, Timers, Counters, Analog scaling |
| HMI | Screen design, alarm management, trending, user management |
| Process | Sorting logic, sensor simulation, actuator control |

### Week-by-Week Tasks

**Week 1 (Foundation)**
- [ ] Set up TIA Portal project with S7-1200 CPU
- [ ] Create tag table (IO mapping)
- [ ] Write ladder logic for conveyor start/stop/jog
- [ ] Add motor overload simulation
- [ ] Create HMI main screen with conveyor visualization

**Week 2 (Core Logic)**
- [ ] Add sensor inputs (proximity, photoelectric, weight)
- [ ] Implement sorting logic (if item > threshold → divert)
- [ ] Add timer functions for tracking
- [ ] Create alarm screens (overload, jam, sensor fault)
- [ ] Add data logging to HMI

**Week 3 (Advanced)**
- [ ] Add recipe system (different products)
- [ ] Implement production counters
- [ ] Create trend displays
- [ ] Add user levels (operator, engineer, admin)
- [ ] Test edge cases and fault recovery

### Deliverables
| File | Description |
|---|---|
| `ConveyorSystem.zap15` | TIA Portal project archive |
| `HMI_Screens.pdf` | All HMI screen captures |
| `IO_List.csv` | Complete IO mapping |
| `Cause_and_Effect.pdf` | Matrix of inputs → outputs |
| `Functional_Design.pdf` | System description |
| `Test_Protocol.pdf` | Testing procedures + results |

### GitHub Structure
```
plc-conveyor-sorting/
├── README.md
├── docs/
│   ├── Functional_Design.pdf
│   ├── Cause_and_Effect.pdf
│   ├── IO_List.pdf
│   └── Test_Protocol.pdf
├── hmi/
│   └── HMI_Screens.pdf
├── plc/
│   └── program_blocks/
│       ├── Main [OB1].pdf
│       ├── ConveyorControl [FC1].pdf
│       ├── SortingLogic [FC2].pdf
│       └── Alarms [FC3].pdf
└── media/
    └── demo_video.mp4
```

---

## PROJECT 2: SCADA — Water Treatment Plant

### Overview
Full SCADA system for a water treatment process including tanks, pumps, valves, and sensors.

### Software
- **Ignition SCADA** (Inductive Automation — free trial)
- **Python** (scripting)
- **SQLite** (database)

### Skills Covered
| Category | Skills |
|---|---|
| SCADA | Tag configuration, HMI design, alarm management, historian |
| Protocols | Modbus TCP (simulated), OPC UA |
| Scripting | Python Jython scripts, transaction groups |
| Database | SQL queries, data logging |

### Week-by-Week Tasks

**Week 1 (Setup)**
- [ ] Install Ignition + create project
- [ ] Design P&ID for water treatment process
- [ ] Create tags (digital/analog, simulated)
- [ ] Build main overview screen

**Week 2 (Process Simulation)**
- [ ] Create tank filling/emptying scripts
- [ ] Add pump control with interlocks
- [ ] Implement PID control loops
- [ ] Add valve positioning and feedback

**Week 3 (Alarms + Data)**
- [ ] Configure alarm pipelines
- [ ] Set up transaction groups to database
- [ ] Create historical trend screens
- [ ] Build report generator

### Deliverables
| File | Description |
|---|---|
| `WaterTreatment_ignition` | Ignition project export |
| `PID_Drawing.pdf` | Process flow diagram |
| `Tag_List.pdf` | Complete tag database |
| `Alarm_Rationale.pdf` | Alarm setpoints and priorities |
| `HMI_Style_Guide.pdf` | Design standards |

---

## PROJECT 3: Electrical Design — Small Industrial Facility

### Overview
Complete electrical design for a small manufacturing facility: load list, SLD, cable schedule, panel layout.

### Software
- **AutoCAD Electrical** (schematics + panel layout)
- **ETAP** (load flow + short circuit + protection)
- **Excel** (schedules and calculations)

### Skills Covered
| Category | Skills |
|---|---|
| Design | Single line diagrams, schematics, panel layouts |
| Power Systems | Load flow, short circuit, voltage drop |
| Standards | IEC 60364, cable sizing, earthing |
| Documentation | Cable schedules, BOQ, load lists |

### Week-by-Week Tasks

**Week 1 (Load Analysis)**
- [ ] Create load list (all electrical consumers)
- [ ] Calculate connected/load/demand loads
- [ ] Size transformer
- [ ] Calculate power factor correction

**Week 2 (Distribution Design)**
- [ ] Draw Single Line Diagram (AutoCAD Electrical)
- [ ] Size main cables (IEC 60364)
- [ ] Select switchgear (MCCB, ACB, contactors)
- [ ] Design earthing system

**Week 3 (ETAP Analysis)**
- [ ] Build network model in ETAP
- [ ] Run load flow analysis
- [ ] Run short circuit analysis
- [ ] Protection coordination study
- [ ] Arc flash analysis (if available)

**Week 4 (Deliverables)**
- [ ] Panel layout drawings (AutoCAD Electrical)
- [ ] Cable schedule
- [ ] Bill of quantities
- [ ] Earthing layout
- [ ] Technical report

### Deliverables
| File | Description |
|---|---|
| `SLD.dwg` | Single Line Diagram |
| `Schematics.dwg` | Control schematics |
| `Panel_Layout.dwg` | Physical panel layout |
| `Cable_Schedule.xlsx` | All cables with sizes |
| `Load_List.xlsx` | Complete load analysis |
| `ETAP_Report.pdf` | Analysis results |
| `BOQ.pdf` | Bill of quantities |

---

## PROJECT 4: Python Modbus TCP Data Logger

### Overview
Python application that reads simulated Modbus TCP data and logs to database with visualization.

### Software
- **Python 3.11**
- **pymodbus** (Modbus client/server)
- **SQLite** (database)
- **Matplotlib** (visualization)

### Skills Covered
| Category | Skills |
|---|---|
| Protocols | Modbus TCP, register mapping, data types |
| Programming | Python, OOP, error handling |
| Database | SQL, CRUD operations |
| Visualization | Real-time trends, dashboards |

### Week-by-Week Tasks

**Week 1 (Modbus Server)**
- [ ] Create Modbus TCP server (simulated PLC)
- [ ] Map registers (coils, holding registers, input registers)
- [ ] Add realistic data (temperature, pressure, flow)
- [ ] Test with Modbus client

**Week 2 (Data Logger)**
- [ ] Create Modbus TCP client (Python)
- [ ] Connect to server and read data
- [ ] Log to SQLite database with timestamp
- [ ] Add error handling (timeouts, disconnections)

**Week 3 (Visualization)**
- [ ] Create real-time trend dashboard
- [ ] Add alarm detection (thresholds)
- [ ] Generate reports (CSV export)
- [ ] Build configuration UI

### Deliverables
| File | Description |
|---|---|
| `modbus_server.py` | Simulated PLC |
| `data_logger.py` | Main application |
| `dashboard.py` | Real-time visualization |
| `config.json` | Configuration file |
| `Modbus_Register_Map.pdf` | Register documentation |
| `Test_Report.pdf` | Testing results |

### GitHub Structure
```
python-modbus-logger/
├── README.md
├── requirements.txt
├── config.json
├── src/
│   ├── modbus_server.py
│   ├── data_logger.py
│   ├── dashboard.py
│   └── models.py
├── docs/
│   ├── Modbus_Register_Map.pdf
│   ├── User_Manual.pdf
│   └── Test_Report.pdf
└── tests/
    └── test_modbus.py
```

---

## PROJECT 5: Building Management System (BMS) Dashboard

### Overview
BMS-style dashboard monitoring HVAC, lighting, fire alarm, and power for a commercial building.

### Software
- **Ignition** or **Python + Dash/Streamlit**
- **SQLite**
- **Python** (scripting)

### Skills Covered
| Category | Skills |
|---|---|
| BMS | System integration, zone control |
| HVAC | Setpoints, scheduling, energy optimization |
| Integration | Multi-system monitoring |
| Visualization | Floor plans, zone colors, status |

### Week-by-Week Tasks

**Week 1 (Building Model)**
- [ ] Define building zones (floors, rooms)
- [ ] Create HVAC model (AHUs, FCUs, VAVs)
- [ ] Model lighting circuits
- [ ] Model fire alarm zones

**Week 2 (BMS Logic)**
- [ ] Implement occupancy-based control
- [ ] Add scheduling (business hours vs. after hours)
- [ ] Create energy consumption tracking
- [ ] Add alarm integration

**Week 3 (Dashboard)**
- [ ] Create floor plan visualization
- [ ] Add zone color-coding
- [ ] Build trend displays
- [ ] Add report generation

---

## PROJECT 6: Solar PV System Design

### Overview
Design a grid-tied solar PV system for a commercial building in Saudi Arabia.

### Software
- **MATLAB** (solar calculations)
- **AutoCAD** (layout)
- **Excel** (energy yield, financials)

### Skills Covered
| Category | Skills |
|---|---|
| Renewable | PV system design, inverter selection |
| Analysis | Energy yield, shading, financial |
| Standards | IEC 61724, grid connection |
| Documentation | SLD, layout, single-line |

### Week-by-Week Tasks

**Week 1 (Sizing)**
- [ ] Determine load profile
- [ ] Calculate PV capacity needed
- [ ] Select PV modules + inverters
- [ ] Size cables and protection

**Week 2 (MATLAB Analysis)**
- [ ] Model solar irradiance (Riyadh/Dubai data)
- [ ] Calculate energy yield
- [ ] Perform shading analysis
- [ ] Financial analysis (IRR, payback)

**Week 3 (Documentation)**
- [ ] Create site layout (AutoCAD)
- [ ] Draw SLD for PV system
- [ ] Create BOQ
- [ ] Technical report

---

## PROJECT 7: Fire Alarm System Design

### Overview
Design a fire alarm system for a 3-story office building per EN 54 / NFPA 72.

### Software
- **AutoCAD Electrical** (layout)
- **Excel** (device schedule)

### Skills Covered
| Category | Skills |
|---|---|
| Life Safety | Device selection, spacing, zoning |
| Standards | EN 54, NFPA 72, local codes |
| Design | Cable types, battery calculations |
| Documentation | Cause and effect, drawings |

### Week-by-Week Tasks

**Week 1 (Detection Layout)**
- [ ] Create floor plans (AutoCAD)
- [ ] Place smoke/heat detectors per standards
- [ ] Add manual call points
- [ ] Add sounders/beacons

**Week 2 (System Design)**
- [ ] Design loop wiring
- [ ] Calculate battery capacity
- [ ] Select cable types (fire-rated)
- [ ] Create cause and effect matrix

**Week 3 (Documentation)**
- [ ] Device schedule
- [ ] Cable schedule
- [ ] Battery calculation report
- [ ] Compliance statement

---

## Weekly Schedule Template (35 hrs)

| Day | Time | Activity |
|---|---|---|
| **Monday** | 5 hrs | Theory + software learning |
| **Tuesday** | 5 hrs | Design + calculations |
| **Wednesday** | 5 hrs | CAD/PLC/SCADA work |
| **Thursday** | 5 hrs | CAD/PLC/SCADA work |
| **Friday** | 5 hrs | Testing + debugging |
| **Saturday** | 5 hrs | Documentation |
| **Sunday** | 5 hrs | Review + prep for next week |

---

## Portfolio Website Structure

```
alabbas-bazazid.github.io/
├── index.html (Home)
├── about.html
├── projects/
│   ├── plc-conveyor.html
│   ├── scada-water.html
│   ├── electrical-facility.html
│   ├── python-modbus.html
│   ├── bms-dashboard.html
│   ├── solar-pv.html
│   └── fire-alarm.html
├── resume.html
└── contact.html
```

---

## Mapping Projects to Skills Gap

| Skill | Project(s) |
|---|---|
| PLC Programming | 1, 2 |
| SCADA/HMI | 1, 2, 5 |
| Modbus | 2, 4 |
| OPC UA | 2 |
| Python | 4, 2 |
| CODESYS | (alternative to TIA Portal in Project 1) |
| Ignition | 2, 5 |
| ETAP | 3 |
| AutoCAD Electrical | 3, 6, 7 |
| VFDs | 1, 2 |
| Power Factor Correction | 3 |
| Cable Sizing | 3 |
| Earthing Design | 3 |
| Single Line Diagrams | 3, 6 |
| Switchgear Selection | 3 |
| Load Lists | 3 |
| Lighting Design | 5 |
| Fire Alarm Systems | 7 |
| BMS | 5 |
| Solar PV | 6 |
| Transformer Sizing | 3 |
| FAT/SAT | All projects |
| Method Statements | All projects |
| P&ID Reading | 2, 6 |
| Arc Flash | 3 |
| QA/QC | All projects |
| IEC 60364 | 3 |
| NFPA 70E | 3 |
| ISO 9001 | All projects |
| Technical Documentation | All projects |
| Site Supervision | (covered in Phase 4) |
| Vendor Document Review | (covered in Phase 4) |
| Revit MEP | (awareness only — no license) |
| Stakeholder Coordination | (covered in Phase 4) |
| Progress Reporting | All projects |
| Cultural Awareness | (covered in Phase 4) |

---

## Critical Path

```
Week 1-4: Foundation → Project 1 starts Week 3
Week 5-6: Project 1 complete
Week 7-9: Project 2 complete
Week 10-13: Project 3 complete
Week 14-16: Project 4 complete
Week 17-18: Project 5 complete
Week 19: Project 6 (partial)
Week 20: Project 7 (partial)
Week 21: Portfolio + Applications
```
