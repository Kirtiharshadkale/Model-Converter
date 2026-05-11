# Fabric Model Toolkit

A browser-based toolkit to migrate Azure Analysis Services models to Microsoft Fabric. Upload your TMSL or relationship files and instantly download ready-to-run C# scripts. No server, no install required.

---

## What It Does

The Fabric Model Toolkit is a single HTML file that runs entirely in the browser. It converts Azure Analysis Services (AAS) model exports into executable scripts for Microsoft Fabric Semantic Models, covering three key migration workflows — Calculation Groups, Relationships, and Measures.

---

## Sections

### Section 1 — Calculation Group → C#
Upload a TMSL file exported from Azure Analysis Services to generate a C# script that recreates calculation groups in your Fabric Semantic Model.

- **Input:** `script.tmsl` (exported from AAS via SSMS or Azure Portal)
- **Output:** `.cs` file — a TOM-based C# console app
- **How to run the output:** Execute as a .NET console app targeting your Fabric XMLA endpoint
- **NuGet dependency:** `Microsoft.AnalysisServices.NetCore.retail.amd64 >= 19.x`

---

### Section 2 — Relationships → C# (Tabular Editor Script)
Upload a Relationships.txt file to generate a Tabular Editor script that bulk creates all relationships in your target model.

- **Input:** `Relationships.txt` — pipe-delimited (`|`) file with the following columns:

  ```
  FromTable | FromColumn | ToTable | ToColumn | CrossFilter | IsActive | SecurityFilter
  ```

- **Output:** `CreateRelationships.cs` — a Tabular Editor Advanced Scripting file
- **How to run the output:** Open Tabular Editor → connect to your Fabric Semantic Model → paste into Advanced Scripting → Run
- **Cardinality default:** Many (From) → One (To)

---

### Section 3 — Measures → .csx (Tabular Editor Script)
Upload a TMSL file to generate a Tabular Editor script that recreates all measures with their DAX expressions and format strings.

- **Input:** `script.tmsl` (same file as Section 1, or any TMSL containing measures)
- **Output:** `CreateMeasures.csx` — a Tabular Editor Advanced Scripting file
- **How to run the output:** Open Tabular Editor → connect to your Fabric Semantic Model → paste into Advanced Scripting → Run
- **Handles:** DAX expressions (single-line and multi-line arrays), format strings, descriptions

---

## How to Use

1. Open `tmsl_converter.html` in any modern browser (Chrome, Edge, Firefox)
2. Go to the relevant section for your migration task
3. Click **Browse** or drag and drop your input file onto the upload area
4. Click the **Convert / Generate** button
5. Review the **count summary** shown after conversion
6. Optionally click **👁 Preview** to inspect the first 40 lines of the output
7. Click **⬇ Download** to save the generated script file

---

## Sharing With Your Team

The entire toolkit is a **single self-contained HTML file** — no backend, no dependencies, no installation needed.

**Options to share:**

| Method | Steps |
|---|---|
| **GitHub Pages** | Upload as `index.html` to a repo → enable Pages under Settings → share the URL |
| **Netlify Drop** | Drag the file to [app.netlify.com/drop](https://app.netlify.com/drop) → instant public URL |
| **SharePoint / Teams** | Upload to a shared channel or document library → share the file link |
| **Email / Chat** | Attach the HTML file directly — recipients open it locally in their browser |

---

## Benefits Over Manual Migration

- **Saves time** — Converts entire AAS model exports in seconds instead of hours of manual copy-paste work.
- **Eliminates human error** — Extracts and maps DAX expressions, table names, and relationship configurations programmatically, ensuring accuracy.
- **No environment setup** — Runs entirely in the browser. No SSMS, no scripting knowledge, no dependencies required.
- **Consistent, repeatable output** — Every generated script follows the same structure, making it easy to review, version-control, and re-run across multiple models.
- **Speeds up team collaboration** — A single shareable file means the whole team uses the same conversion process, reducing knowledge silos.

---

## Input File Reference

### TMSL File (Sections 1 & 3)
Exported from Azure Analysis Services using SSMS:
- Right-click your model database → **Script Database as** → **CREATE OR REPLACE To** → **File**
- Supports `createOrReplace`, `create`, `alter` command shapes
- Supports root `model` shape with multiple tables

### Relationships.txt (Section 2)
A plain text file with pipe-delimited columns. Can be generated using the companion Python script `generate_relationships_script.py` with the `--dump-csv` flag, or created manually.

Required columns (header row must be present):

| Column | Description |
|---|---|
| `FromTable` | Name of the fact/source table |
| `FromColumn` | Column name on the From side |
| `ToTable` | Name of the dimension/target table |
| `ToColumn` | Column name on the To side |
| `CrossFilter` | `OneDirection` or `BothDirections` |
| `IsActive` | `True` or `False` |
| `SecurityFilter` | `OneDirection`, `BothDirections`, or `None` |

---

## Output File Reference

| Section | Output File | Run In |
|---|---|---|
| Calculation Group | `<source-name>.cs` | .NET console app via XMLA endpoint |
| Relationships | `CreateRelationships.cs` | Tabular Editor → Advanced Scripting |
| Measures | `CreateMeasures.csx` | Tabular Editor → Advanced Scripting |

---

## Prerequisites for Running the Generated Scripts

**Section 1 — C# console app:**
- .NET 6+ SDK
- NuGet package: `Microsoft.AnalysisServices.NetCore.retail.amd64 >= 19.x`
- Power BI Premium or Fabric workspace with **XMLA Read/Write** enabled
- Valid credentials (user or service principal)

**Sections 2 & 3 — Tabular Editor scripts:**
- [Tabular Editor 2](https://github.com/TabularEditor/TabularEditor) or [Tabular Editor 3](https://tabulareditor.com/)
- Connected to the target Fabric Semantic Model

---

## Privacy

This tool runs **100% in the browser**. No data is sent to any server. Your TMSL files and relationship data never leave your machine.

---

## Companion Files

| File | Purpose |
|---|---|
| `tmsl_converter.html` | The main toolkit UI (this file) |
| `tmsl_to_csharp.py` | Python CLI version of the TMSL → C# converter |
| `generate_relationships_script.py` | Python script to extract relationships from a live AAS model and export `Relationships.txt` |
