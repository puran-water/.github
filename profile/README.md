![Puran Water](assets/logo.png)

Wastewater process engineering tools designed for programmatic access by AI agents.

## Vision

To create a plant-state-aware engineering orchestrator that generates (with human-in-the-loop gating) end-to-end wastewater treatment plant designs and mechanistic models from an initial problem statement to a complete, converged set of engineering artifacts.

This system is built on AI-native process tools (MCP servers) that deliver designs with process unit sizing based on heuristics and refined by mechanistic models.  The mechanistic models can also serve as digital twins for the operating asset, which can create a compounding data flywheel, where insights from live digital twins continuously refine and validate the design heuristics of the entire ecosystem.

## The Orchestrator Approach

The end-goal is a "plant-state-aware" orchestrator that automates the core process engineering workflow:

* Problem Parsing: An AI agent ingests an RFP, Basis of Design, or similar "problem statement" document.

* Topology Suggestion: Leveraging an internal repository of SFILES topologies + feed/discharge state variables, a process topology for a given problem statement can be proposed for human-in-the-loop editing and approval.

* Iterative Design: The orchestrator "walks" the approved process topology, calling the appropriate process unit MCP servers as tools. It passes the output "plant state" from one unit to the next, asserting mass balance and iterating recycle streams until the entire system converges.

* Artifact Generation: The final, converged plant state and all intermediate MCP metadata are used to generate a full suite of engineering deliverables. This includes equipment lists, load lists, PFDs and P&IDs (expanded from the BFD SFILES), IO lists, instrument/line/valve schedules, process and instrument datasheets, and control narratives (complete with SCL code).

## The Digital Twin Flywheel

This architecture is designed for a virtuous, positive feedback loop:

* Digital Twinning: Each MCP server produces a mechanistic model of the process unit, which can serve as a basis for a "digital twin". By ingesting live plant data, it can model current-state operations versus mechanistic model predicted outputs and the mechanistic model can be improved considering non-idealities in the mechanistic model to reduce this error.

* Data Flywheel: The improved mechanistic model will inform better, more robust designs and capture / memorialized this tacit knowledge.

* Compounding Value: This creates a powerful data flywheel. The orchestrator's "first guess" for new designs becomes more intelligent and accurate with every plant deployed and every day a digital twin is active. This compounds institutional knowledge, de-risks new designs, and improves the underlying heuristics and process modeling for the entire system.

## Core Architecture

These tools implement the Model Context Protocol (MCP), providing structured JSON interfaces for deterministic engineering calculations. AI agents can compose multi-step workflows by calling tools programmatically rather than requiring human operators to navigate graphical interfaces.

Engineering drawings follow a database-first architecture where machine-readable data models (DEXPI for P&IDs, SFILES for BFDs/PFDs) generate visualizations. This inverts the traditional CAD workflow, enabling version control via git and automated diff operations on the underlying data structures.

## Repositories

### Public (in development)
- **dexpi-sfiles-mcp-server** – ISO 15926-compliant P&ID + SFILES BFD/PFD tooling with consolidated omnitools, pyDEXPI/component coverage, Proteus XML export, and Git-native persistence.
- **ro-design-mcp** – Reverse osmosis design optimizer with hybrid simulator, PHREEQC chemistry, and WaterTAP costing; exposes tools for configuration, simulation, and defaults.
- **ix-design-mcp** – Ion exchange (SAC/WAC) sizing and simulation with Gaines-Thomas heuristics, PHREEQC breakthrough modeling, and WaterTAP cost analysis plus report generation.
- **degasser-design-mcp** – Packed tower air stripper design with PHREEQC speciation, HTU/NTU sizing, staged simulation, and WaterTAP/QSDsan costing.
- **heat-transfer-mcp** – Thermal analysis omnitools for tank/pipe heat loss, HX design, weather-driven sizing, and parameter sweeps with 390+ material database.
- **fluids-mcp** – Pipe flow, valve sizing (IEC 60534), pump/compressor design, parameter sweeps, and property lookups via CoolProp/Thermo/Fluids.
- **water-chemistry-mcp** – PHREEQC-based speciation, chemical addition/mixing, scaling analysis, and batch processing with CI-backed test/quality/integration workflows.
- **knowledge-base-mcp** – Hybrid dense/sparse/rerank retrieval with Docling ingestion, Qdrant + FTS payloads, deterministic upsert tools, and optional graph/link-out features (advanced graph extraction remains partial/optional).

### Private (in early development)
- **plant-state** – Orchestrator coordination layer for end-to-end plant-state-aware workflows.
- **evaporator-design-mcp**, **anaerobic-design-mcp**, **aerobic-design-mcp**, **primary-clarification-mcp** – Advanced process unit models; private and still under active build-out.
- **corrosion-engineering-mcp** – Physics-based corrosion prediction with PHREEQC coupling; private/in development.
- **compliance-agent** – Regulatory monitoring and permit automation; private/in development.
- **tia-portal-mcp** – Siemens TIA Portal read-only SCADA interface; private/in development.

## Technical Patterns

- **Aqueous chemistry**: PHREEQC via PhreeqPython for thermodynamically rigorous water chemistry
- **Biological process modeling**: QSDsan for aerobic (mASM2d) and anaerobic (mADM1) process modeling with validated kinetics and stoichiometry 
- **Process costing**: Integration wjth public costing databases (QSDsan, WaterTAP, EPA) for CAPEX/OPEX analysis and life cycle cost calculations
- **Thermodynamic properties**: CoolProp, Thermo, and Fluids libraries with NIST-validated correlations
- **Framework**: FastMCP for rapid MCP server development
- **Validation**: Physics-based calculations with literature-sourced parameters rather than empirical approximations

## Reporting

Engineering MCP servers generate **Markdown reports** with:
- **Obsidian frontmatter** for metadata and searchability via Obsidian MCP server
- **Mermaid diagrams** for process flowsheets
- **LaTeX equations** for engineering calculations with symbolic notation

Rationale:
1. **LLM-native format** - Markdown is the native "word processor" for AI agents, enabling seamless reading/writing without parsing overhead
2. **Git version control** - Plain text reports are diffable, enabling standard version control workflows for design artifacts (track changes, rollbacks, blame)
3. **Cross-platform readability** - Reports render in GitHub, VS Code, Obsidian, and any text editor without proprietary software
4. **Programmatic organization** - Global Obsidian MCP server provides semantic search and organization across all design artifacts via frontmatter metadata
5. **Client deliverable conversion** - When client-facing deliverables require traditional formats, Pandoc converts Markdown reports to branded PDF, Word, or PowerPoint documents using custom templates. This preserves Markdown as the version-controlled source of truth while enabling professional formatting for external stakeholders.

This replaces traditional .docx/.xlsx reporting, which requires binary diff tools and lacks the compositional properties needed for AI-driven workflows.

## Rationale

Domain experts should spend their time exploring the full design space, solving complex problems, and anticipating edge cases that lead to failures. By orchestrating AI agents through structured APIs, engineers delegate routine calculations and document generation, freeing cognitive resources for higher-value activities.

AI agents handle:
1. Parametric design iterations across multiple operating conditions
2. Generation of calculation reports and technical drawings
3. Compliance documentation and regulatory submissions
4. Integration of results across multiple analysis domains
5. Version control and change tracking for engineering artifacts

The **knowledge-base-mcp** server provides context retrieval for engineering calculations. Agents query baseline knowledge (textbooks, handbooks, standards) to inform tool selection and constraint formulation. Atomic ingestion tools allow upserting user-supplied tacit knowledge (operating experience, site-specific heuristics) and agent-synthesized insights from mechanistic modeling outputs. This creates a feedback loop where calculation results inform the knowledge base, enabling agents to explain designs with provenance-backed citations.

Git-compatible data formats (JSON, structured text) enable standard version control workflows for engineering artifacts traditionally locked in proprietary binary formats.
