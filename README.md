# Crimes Against Children in India (2022): Data Cleaning, Analysis, and Visualization Using Julia

This project analyzes state-wise crime data against children in India for the year 2022, leveraging **Julia** for data cleaning, exploration, and static visualizations. The aim is to uncover key patterns across states, crime categories, and gender, providing insights for awareness and policy considerations.

---

> 📌 **Note:** The raw dataset has verbose column names (e.g., `"Murder (Sec.302 IPC) - Murder (Sec.302 IPC) - I - Col.(3)"`). A dedicated column-cleaning cell runs regex-based renaming in two passes (duplicated) to ensure fully cleaned names under “Restart Kernel & Run All” in JupyterLab.

---

## 📌 Project Objectives

- Identify **top 10 states/UTs** by total crimes against children.
- Determine **safest states/UTs** by crime rate (per 1 lakh children).
- Highlight **most common crime types** (by incident count).
- Analyze **gender split** in POCSO-related victim data.
- Examine key vulnerable categories: **child labour victims**, **minor trafficking/selling**, **rape victims**, etc.
- Practice Julia-based data cleaning, aggregation, and static plotting with `Plots.jl`.

---

## 📁 Dataset

Raw dataset is stored in `CrimesAgainstChildren2022/Data/crimesAgainstChildren2022.csv`

**Source:** [data.gov.in – State/UT-wise Number of IPC and SLL Crimes Against Children (2022)](https://www.data.gov.in/resource/stateut-wise-number-indian-penal-code-ipc-and-special-and-local-laws-sll-crimes-against).
**Scope:** All Indian states and union territories (36 rows) with over 200 columns capturing IPC/SLL references, gender              breakdown, and rate metrics.  
**Key fields:**  
  - Crime type columns (Incidents, Victims, Rates), often with IPC section references.  
  - Gender-disaggregated counts (e.g., POCSO girls_v, boys_v).  
  - Precomputed “total incidents,” “total victims,” and “crime rate” per state for aggregated analysis.

---

## 🧹 Data Cleaning

1. **Loading:** Read CSV into a Julia `DataFrame`.
     ```julia
      df = CSV.read("../data/crimesAgainstChildren2022.csv", DataFrame)
      ```
   - At this point, `names(df)` shows long strings like `"Murder (Sec.302 IPC) - Murder (Sec.302 IPC) - I - Col.(3)"` and               similar patterns.

2. **Column Name Cleaning**  
   - A custom function `make_clean_name(orig::AbstractString)` is defined to:
     - Lowercase the original name.
     - Remove legal references such as `"Sec...IPC"`.
     - Drop suffixes like `"- Col.(NNN)"`.
     - Replace commas, slashes, and whitespace with underscores.
     - Strip remaining punctuation (parentheses, periods, ampersands, colons).
     - Collapse multiple underscores and trim leading/trailing underscores.
     - Return a clean `Symbol`, e.g. `"Murder (Sec.302 IPC) - I"` → `:murder_i`.
   - This ensures consistent, concise column names that are easy to reference in code.
   - **Implementation note:** In the notebook, the cleaning cell is duplicated so that when the user does “Restart Kernel & Run All,” the regex-based renaming logic runs two passes automatically. This handles subtler patterns that might only be fully removed on a second pass, without manual intervention.

3. **Filtering Rows and Columns** 
   - Drop irrelevant rows (e.g., aggregates or totals) and unneeded columns.
     ```julia
      select!(df, Not(:"sl_no"))
      filter!(row -> !(row."state_ut" in ["Total State(S)", "Total UT (S)", "Total All India"]), df)
     ```
---

## 📈 Analysis Workflow

1. **Top 10 States by Total Crime**  
   - Sort descending by `total_crimes_against_children_ipc+sll_i` column, plot static bar chart.   
2. **Most Common Crime Types**  
   - Aggregate incident counts across states for each crime type, identify top 5 or top N, plot bar & pie charts.  
   - Shorten long crime-type labels for readability.  
3. **Gender Split in POCSO Victims**  
   - Sum national totals of POSCO for girls vs. boys victims; plot static pie/bar chart.  
5. **Key Vulnerable Categories**  
   - Extract columns for child labour victims, minor selling/trafficking, rape victims, etc.; rank states by these metrics and         visualize top states.  
6. **Visualization**
   - All plots are saved in `CrimesAgainstChildren2022/Notebooks/Visualization` as PNGs via `Plots.jl` for easy sharing.

---

## 🚀 Getting Started

1. **Download the Notebook**  
   - Go to the GitHub repository page in your browser.  
   - Click “Code” → “Download ZIP” and extract the folder on your computer.

2. **Prepare the Dataset**  
   - Download the raw CSV from data.gov.in.  [data.gov.in – State/UT-wise Number of IPC and SLL Crimes Against Children (2022)](https://www.data.gov.in/resource/stateut-wise-number-indian-penal-code-ipc-and-special-and-local-laws-sll-crimes-against)
   - Place it in the extracted folder under a `data/` subfolder (create it if needed), e.g.:  
     ```
     CrimesAgainstChildren2022/
     ├── Notebooks/
     └── Data/
         └── crimesAgainstChildren2022.csv
     ```
3. **Install Julia Packages**  
   - Open Julia REPL and run:  
     ```julia
     using Pkg
     Pkg.activate("..")
     Pkg.instantiate()
     Pkg.add(["CSV", "DataFrames", "DataFramesMeta", "Statistics", "Plots", "PrettyTables", "CategoricalArrays"])
     ```  
   - This ensures you have the packages the notebook needs.

4. **Run in JupyterLab**  
   - Launch JupyterLab.  
   - Open the analysis file inside Notebooks folder (e.g., `analysis.ipynb`).  
   - Restart the kernel and click “Run All Cells.” The column-cleaning cell is duplicated to auto-run twice.