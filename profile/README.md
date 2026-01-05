![Puran Water](assets/logo.png)

# Puran Water

**Auditable, agent-native industrial engineering infrastructure.**

CI pipelines for engineering artifacts. Change one parameter, reconcile everything.

---

## The Problem

When an engineer updates a basis of design parameter, every downstream calculation must be reconciled—equipment sizing, hydraulic profiles, control setpoints, P&IDs.

This currently requires **3-4 disciplines, hours of meetings, and manual tracking**. Changes still get missed.

## The Solution

Machine-readable artifacts + physics-based validators = **closed-loop iteration**.

```
Basis of Design changes (e.g., influent COD)
    ↓
Agent walks process topology
    ↓
Each MCP re-simulates with upstream state
    ↓
Convergence check (mass balance, constraints)
    ↓
ALL downstream artifacts regenerated
    ↓
Git diff shows every impacted file
    ↓
Engineer reviews and approves
```

This is `make` for engineering. Change one parameter, recompute the dependency graph.

---

## Three Pillars

| Pillar | Why It Matters |
|--------|----------------|
| **Open Protocols** | MCP is a vendor-neutral open standard under the [Agentic AI Foundation](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation). Expose a tool once; use it across Claude, ChatGPT, Cursor, Copilot. |
| **Machine-Readable Artifacts** | DEXPI XML (P&IDs), SFILES (flowsheets), JSON (calculations), Markdown (reports). Git-diffable, agent-editable, no parsing overhead. |
| **Open-Source Stack** | PHREEQC, QSDsan, CoolProp, FreeCAD, OR-Tools. Full automation surface area. We bridge to proprietary tools when needed. |

---

## Open Source MCP Servers

Production-ready tools with physics-based validation.

<details>
<summary><strong>Engineering Foundations</strong></summary>

| Server | Purpose | Maturity |
|--------|---------|----------|
| [fluids-mcp](https://github.com/puran-water/fluids-mcp) | Pipe flow, valve sizing (IEC 60534), 120+ fluids | Standards-compliant |
| [heat-transfer-mcp](https://github.com/puran-water/heat-transfer-mcp) | Tank/pipe heat loss, HX sizing, 390+ materials | Weather-driven sizing |
| [water-chemistry-mcp](https://github.com/puran-water/water-chemistry-mcp) | PHREEQC speciation, dosing, scaling | CI-backed validation |
| [corrosion-engineering-mcp](https://github.com/puran-water/corrosion-engineering-mcp) | CO₂/H₂S corrosion (NORSOK M-506), 42 materials | Physics-based |

</details>

<details>
<summary><strong>Process Unit Design</strong></summary>

| Server | Purpose | Maturity |
|--------|---------|----------|
| [ro-design-mcp](https://github.com/puran-water/ro-design-mcp) | RO optimization, 67 membranes, PHREEQC chemistry | WaterTAP costing |
| [ix-design-mcp](https://github.com/puran-water/ix-design-mcp) | Ion exchange sizing, breakthrough modeling | Gaines-Thomas + PHREEQC |
| [degasser-design-mcp](https://github.com/puran-water/degasser-design-mcp) | Packed tower air stripper, HTU/NTU sizing | Perry's Handbook |
| [adm1-mcp](https://github.com/puran-water/adm1-mcp) | Anaerobic digestion, 35+ processes | QSDsan validated |
| [site-fit-mcp-server](https://github.com/puran-water/site-fit-mcp-server) | Site layout, OR-Tools CP-SAT, NFPA 820 | Constraint-based |

</details>

<details>
<summary><strong>Engineering Artifacts</strong></summary>

| Server | Purpose | Maturity |
|--------|---------|----------|
| [dexpi-sfiles-mcp-server](https://github.com/puran-water/dexpi-sfiles-mcp-server) | P&ID (DEXPI XML) + flowsheet (SFILES2) | **768 tests**, ACID, ISO 15926 |
| [freecad-pid-workbench](https://github.com/puran-water/freecad-pid-workbench) | Human-in-the-loop P&ID editing | 272 equipment classes |
| [knowledge-base-mcp](https://github.com/puran-water/knowledge-base-mcp) | RAG with provenance-backed citations | Hybrid dense/sparse |

</details>

<details>
<summary><strong>Proprietary Tool Bridges</strong></summary>

| Server | Purpose | Note |
|--------|---------|------|
| [autocad-mcp](https://github.com/puran-water/autocad-mcp) | AutoCAD LT via AutoLISP | 600+ ISA 5.1 symbols |
| [mathcad-mcp](https://github.com/puran-water/mathcad-mcp) | MathCAD Prime COM automation | Unit-aware |

Strategic direction is open-source FreeCAD. These bridges exist for integration with existing workflows.

</details>

---

## Vision

To create a plant-state-aware engineering orchestrator that generates end-to-end wastewater treatment plant designs and mechanistic models—from problem statement to converged engineering artifacts—with human-in-the-loop gating at each stage.

<details>
<summary><strong>The Orchestrator Approach</strong></summary>

We are building plant-state-aware orchestrators that compose MCP servers into end-to-end engineering workflows.

The orchestrator walks approved process topologies, calling process unit MCPs as tools, passing output state from one unit to the next, asserting mass balance and iterating recycle streams until convergence.

Final artifacts include equipment lists, PFDs/P&IDs, IO lists, instrument schedules, datasheets, and control narratives.

</details>

<details>
<summary><strong>The Digital Twin Flywheel</strong></summary>

Mechanistic models produced by MCP servers can serve as digital twins. By comparing operational data against model predictions, we continuously refine both sizing heuristics and process models.

This creates compounding institutional knowledge: orchestrator designs become more intelligent and accurate with every plant deployed and every day a digital twin is active.

</details>

---

## Beyond Open Source

Additional capabilities in development:
- Advanced process units (biological treatment, evaporation, clarification)
- Workflow orchestration (plant-state tracking, design automation)
- Compliance intelligence (regulatory monitoring, permit workflows)
- Industrial integration (SCADA connectivity)

**Interested in early access?** Contact: opensource@puranwater.com

---

<details>
<summary><strong>Technical Stack</strong></summary>

| Component | Technology |
|-----------|------------|
| Aqueous chemistry | PHREEQC via PhreeqPython |
| Biological modeling | QSDsan (mASM2d, mADM1) |
| Process costing | WaterTAP, QSDsan, EPA databases |
| Thermodynamics | CoolProp, Thermo, Fluids (NIST-validated) |
| Optimization | OR-Tools (CP-SAT solver) |
| Framework | FastMCP |
| Reports | Markdown + Obsidian frontmatter + LaTeX |

</details>

<details>
<summary><strong>Core Principles</strong></summary>

| Principle | Rationale |
|-----------|-----------|
| **Open-Source Stack** | All dependencies freely available. No proprietary licenses required. |
| **Machine-Readable Formats** | P&IDs as DEXPI XML, flows as SFILES, calculations as JSON. No binary blobs. |
| **Git-Native Workflows** | All artifacts text-diffable. Track changes, rollback, PR review on engineering. |
| **Physics-Based Calculations** | Deterministic correlations from literature. Full auditability for safety-critical systems. |

</details>

<details>
<summary><strong>Reporting Architecture</strong></summary>

Engineering MCP servers generate **Markdown reports** with:
- **Obsidian frontmatter** for metadata and searchability
- **Mermaid diagrams** for process flowsheets
- **LaTeX equations** for engineering calculations

Rationale:
1. **LLM-native format** - Seamless read/write without parsing overhead
2. **Git version control** - Diffable design artifacts
3. **Cross-platform** - Renders in GitHub, VS Code, Obsidian
4. **Client conversion** - Pandoc to PDF/Word/PowerPoint when needed

</details>

---

*Puran Water LLC • [puranwater.com](https://www.puranwater.com) • opensource@puranwater.com*
