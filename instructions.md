# Evaluation Dashboard

> **How you'll submit this lab**
>
> This repo is your lab. Fork it, do the work described below in your fork, then open a pull
> request back into this repository. An AI reviewer will check your PR against `rubric.md` and
> leave feedback directly on the PR. See `README.md` for the full workflow.

**Scenario**  
You're an AI consultant presenting evaluation results to stakeholders. You've completed your analysis in Python notebooks and LangSmith, and now you need to communicate your findings effectively. Your stakeholders—executives, managers, and business decision-makers—cannot read Jupyter notebooks. They need a clear, interactive dashboard that shows where models succeed and fail across different categories.

This lab guides you through building a dashboard in Tableau or PowerBI that visualizes evaluation score distributions, demonstrating the communication layer principles from the morning lesson.

**Learning Objectives**
- [ ] Connect evaluation data to Tableau or PowerBI
- [ ] Create score distribution visualizations by category
- [ ] Build interactive filters for drill-down analysis
- [ ] Design a dashboard suitable for non-technical stakeholders
- [ ] Apply the communication layer principles from the lesson
- [ ] Distinguish between what belongs in dashboards vs. analysis notebooks

**Estimated Time:** 120-150 minutes (2-2.5 hours)

**Prerequisites:**
- [ ] Completion of the Data Visualization & Reporting Tools lesson
- [ ] Access to Tableau Desktop (free trial) or PowerBI Desktop (free)
- [ ] Basic understanding of evaluation metrics
- [ ] (Optional) Previous lab experience with LLM evaluation from Day 1 or Day 2

---

## Introduction

This lab simulates a real-world consulting scenario where you must:
1. **Prepare** evaluation data for visualization
2. **Create** visualizations showing score distributions by category
3. **Build** an interactive dashboard for stakeholders
4. **Apply** communication layer principles (show "what" and "so what", not statistical methods)

**Why this matters:**
- Stakeholders need accessible visualizations, not Python notebooks
- BI tools bridge the gap between analysis and decision-making
- Understanding what belongs in dashboards vs. analysis is essential for effective communication
- Interactive dashboards enable stakeholders to explore data themselves

**Success criteria:**
- [ ] Successfully connected evaluation data to your chosen BI tool
- [ ] Created at least 3 visualizations showing score distributions
- [ ] Built an interactive dashboard with filters
- [ ] Dashboard is suitable for non-technical stakeholders
- [ ] Applied communication layer principles (no p-values, confidence intervals, etc.)

---

## Reference Materials

**Documentation:**
- [Tableau Desktop Documentation](https://help.tableau.com/current/guides/get-started-tutorial/en-us/get-started-tutorial-home.htm)
- [PowerBI Desktop Documentation](https://learn.microsoft.com/en-us/power-bi/desktop-get-the-desktop)
- [Tableau Getting Started Guide](https://help.tableau.com/current/guides/get-started-tutorial/en-us/get-started-tutorial-home.htm)
- [PowerBI Getting Started Guide](https://learn.microsoft.com/en-us/power-bi/fundamentals/desktop-getting-started)

**Lesson Materials:**
- Reference the Data Visualization & Reporting Tools lesson for communication layer principles
- Remember: Dashboards show conclusions, not statistical methodology

---

## Part 1: Data Preparation (30-40 minutes)

### Step 1: Choose Your Data Source (5-10 min)

**Objective:** Select evaluation data to visualize in your dashboard.

**You have two options:**

#### Option A: Use Your Own Data from Previous Labs (Recommended if available)

If you completed labs from Day 1 or Day 2, you can use your own evaluation results:

**From Day 1 Labs:**
- **LLM Judges Lab**: Use your `evaluation_results.json` or evaluation output
- **Human-in-the-Loop Lab**: Use your annotated dataset with evaluation scores

**From Day 2 Labs:**
- **Statistical Analysis Lab**: Use your evaluation metrics and category breakdowns
- **LangSmith Lab**: Export evaluation results from LangSmith experiments

**What to extract:**
- Evaluation scores (numerical values, typically 0-1 or 0-100)
- Categories (e.g., reasoning, knowledge, code, instruction_following, tool_calling)
- Model versions or names
- Dates/timestamps (if available)
- Any metadata that would be useful for filtering

**Data structure needed:**
Your data should have columns like:
- `evaluation_id` or `example_id`
- `category` (the evaluation category)
- `score` (the evaluation score)
- `model_version` (optional, if comparing models)
- `date` (optional, for time series analysis)

#### Option B: Generate Sample Evaluation Data

If you don't have data from previous labs, use the Python script below to generate realistic sample data.

**What to do:**

1. **Create a new Python file** (e.g., `generate_evaluation_data.py`)

2. **Copy and run this script:**

```python
import pandas as pd
import numpy as np
from datetime import datetime, timedelta

# Set random seed for reproducibility
np.random.seed(42)

# Define evaluation categories
categories = ['reasoning', 'knowledge', 'code', 'instruction_following', 'tool_calling']

# Define model versions
model_versions = ['gpt-4', 'gpt-4-turbo', 'gpt-3.5-turbo', 'claude-3-opus']

# Generate date range (last 90 days)
start_date = datetime.now() - timedelta(days=90)
dates = [start_date + timedelta(days=i) for i in range(90)]

# Generate evaluation data
data = []
evaluation_id = 1

for date in dates:
    for category in categories:
        # Generate 5-15 evaluations per category per day
        num_evaluations = np.random.randint(5, 16)
        
        for _ in range(num_evaluations):
            model = np.random.choice(model_versions)
            
            # Generate scores with category-specific distributions
            if category == 'reasoning':
                # Reasoning tends to have lower scores, more variance
                score = np.random.beta(2, 3) * 100
            elif category == 'knowledge':
                # Knowledge tends to have higher scores
                score = np.random.beta(4, 2) * 100
            elif category == 'code':
                # Code has medium scores with high variance
                score = np.random.beta(3, 3) * 100
            elif category == 'instruction_following':
                # Instruction following tends to be high
                score = np.random.beta(5, 2) * 100
            else:  # tool_calling
                # Tool calling has medium scores
                score = np.random.beta(3, 3) * 100
            
            # Add some noise
            score = max(0, min(100, score + np.random.normal(0, 5)))
            
            data.append({
                'evaluation_id': evaluation_id,
                'category': category,
                'score': round(score, 2),
                'model_version': model,
                'date': date.strftime('%Y-%m-%d')
            })
            evaluation_id += 1

# Create DataFrame
df = pd.DataFrame(data)

# Save to CSV
df.to_csv('evaluation_data.csv', index=False)

print(f"Generated {len(df)} evaluation records")
print(f"Date range: {df['date'].min()} to {df['date'].max()}")
print(f"\nScore summary by category:")
print(df.groupby('category')['score'].describe())
print(f"\nFirst few rows:")
print(df.head())
```

3. **Run the script:**
```bash
python generate_evaluation_data.py
```

4. **Verify the output:**
   - Check that `evaluation_data.csv` was created
   - Open it in Excel or a text editor to verify the structure
   - You should see columns: evaluation_id, category, score, model_version, date

**Deliverable:** 
- Either your formatted data from previous labs OR
- The generated `evaluation_data.csv` file
- Document which option you chose and why

---

### Step 2: Clean and Format Data (10-15 min)

**Objective:** Ensure your data is ready for import into your BI tool.

**What to do:**

1. **Open your data file** (CSV or Excel)

2. **Verify data structure:**
   - Check that scores are numeric (not text)
   - Check that categories are consistent (no typos or variations)
   - Check that dates are in a consistent format (YYYY-MM-DD recommended)
   - Remove any rows with missing critical data (score, category)

3. **Make any necessary adjustments:**
   - If using data from previous labs, you may need to:
     - Rename columns to match expected names
     - Convert score formats (e.g., 0-1 to 0-100 if needed)
     - Standardize category names
     - Add date column if missing (use evaluation date or creation date)

4. **Save your cleaned data:**
   - Save as CSV (recommended for both Tableau and PowerBI)
   - Or save as Excel (.xlsx) if preferred
   - Name it clearly: `evaluation_data_clean.csv`

**Tips:**
- Both Tableau and PowerBI can handle CSV files easily
- Ensure your CSV uses commas as delimiters
- If you have special characters, ensure proper encoding (UTF-8)

**Deliverable:** Clean, formatted data file ready for import

---

### Step 3: Prepare Data for Import (5-10 min)

**Objective:** Understand what your BI tool needs and prepare accordingly.

**What to do:**

1. **Review your data one more time:**
   - Count total rows (should be at least 100+ for meaningful visualizations)
   - Verify column names are clear and descriptive
   - Check data types (numeric for scores, text for categories)

2. **Note any transformations you might need:**
   - Do you need to create calculated fields? (e.g., score ranges, categories)
   - Do you need to aggregate data? (usually the BI tool will do this)
   - Are there any filters you want to apply at import? (usually better to do in the tool)

3. **Locate your data file:**
   - Know the exact file path
   - Ensure the file is in a location you can easily access

**Deliverable:** Data file ready and file path noted

---

## Part 2: Connect Data & Create Visualizations (60-80 minutes)

### Step 4: Connect Data to Your BI Tool (10-15 min)

**Objective:** Import your evaluation data into Tableau or PowerBI.

#### For Tableau Desktop:

**What to do:**

1. **Open Tableau Desktop**

2. **Connect to your data:**
   - Click "Text file" or "Excel" under "Connect"
   - Navigate to your `evaluation_data_clean.csv` file
   - Click "Open"

3. **Review the data preview:**
   - Tableau should automatically detect your columns
   - Check that data types are correct:
     - `evaluation_id` should be a dimension (blue pill)
     - `category` should be a dimension (blue pill)
     - `score` should be a measure (green pill)
     - `model_version` should be a dimension (blue pill)
     - `date` should be a date dimension (blue pill)

4. **Fix data types if needed:**
   - Click the data type icon (Abc, #, calendar) next to column names to change types
   - Ensure `score` is numeric (should show #)
   - Ensure `date` is a date type (should show calendar icon)

5. **Click "Sheet 1"** to start creating visualizations

#### For PowerBI Desktop:

**What to do:**

1. **Open PowerBI Desktop**

2. **Get Data:**
   - Click "Get Data" on the Home ribbon
   - Select "Text/CSV" or "Excel"
   - Navigate to your `evaluation_data_clean.csv` file
   - Click "Open"

3. **Preview and Load:**
   - Review the data preview in the dialog
   - Check that column names and data types look correct
   - Click "Load" (or "Transform Data" if you need to make changes)

4. **Verify in Fields pane:**
   - In the right panel, check your fields
   - Numeric fields (like `score`) should show a sigma (Σ) icon
   - Text fields (like `category`) should show Abc icon
   - Date fields should show a calendar icon

5. **You're ready to create visualizations!**

**Deliverable:** Data successfully connected to your BI tool

---

### Step 5: Create Score Distribution Histogram (15-20 min)

**Objective:** Create a visualization showing the overall distribution of evaluation scores.

#### For Tableau:

**What to do:**

1. **Create a new worksheet** (or use Sheet 1)

2. **Build the histogram:**
   - Drag `score` to the Columns shelf
   - Right-click on `score` in the Columns shelf
   - Select "Create" → "Bins"
   - Set bin size to 10 (creates bins: 0-10, 10-20, 20-30, etc.)
   - Click OK
   - Drag `Number of Records` (or Count of evaluation_id) to Rows shelf
   - Tableau should automatically create a bar chart

3. **Format the chart:**
   - Right-click the x-axis and format as needed
   - Add a title: "Score Distribution"
   - Adjust colors if desired

4. **Rename the worksheet:** "Score Distribution"

#### For PowerBI:

**What to do:**

1. **Create a new visual:**
   - In the Visualizations pane, click on "Bar chart" or "Column chart"

2. **Add fields:**
   - Drag `score` to the "Axis" field well
   - Drag `evaluation_id` (or any field) to the "Values" field well
   - PowerBI should count the records

3. **Create bins for better visualization:**
   - Right-click on `score` in the Fields pane
   - Select "New group"
   - Create groups: 0-10, 10-20, 20-30, etc. (or use automatic binning)
   - Use the grouped score in your chart instead

4. **Format the chart:**
   - Add a title: "Score Distribution"
   - Adjust colors and formatting as needed

**What this shows:** The overall distribution of scores helps stakeholders understand the general performance level and whether scores are clustered or spread out.

**Deliverable:** Score distribution histogram visualization

---

### Step 6: Create Score Distribution by Category (20-25 min)

**Objective:** Show how scores vary across different evaluation categories.

#### For Tableau:

**What to do:**

1. **Create a new worksheet**

2. **Build a box plot or violin plot:**
   - Drag `category` to the Columns shelf
   - Drag `score` to the Rows shelf
   - In the Show Me panel, select "Box Plot" or create manually:
     - Change the mark type to "Circle" or "Square"
     - Add `score` to Detail
     - Show distribution by category

3. **Alternative: Create a grouped bar chart:**
   - Drag `category` to Columns
   - Drag `score` to Rows (change to Average)
   - Add `category` to Color
   - This shows average score by category

4. **Add distribution details:**
   - Right-click on the chart
   - Add reference lines for mean, median, or quartiles
   - This helps show where categories perform relative to each other

5. **Format and title:** "Score Distribution by Category"

6. **Rename worksheet:** "Scores by Category"

#### For PowerBI:

**What to do:**

1. **Create a new visual:**
   - Select "Box and whisker chart" (if available) or "Clustered column chart"

2. **For box plot (if available):**
   - Drag `category` to "Category" field
   - Drag `score` to "Values" field
   - PowerBI will show distribution statistics

3. **For bar chart (alternative):**
   - Select "Clustered column chart"
   - Drag `category` to "Axis"
   - Drag `score` to "Values" (change aggregation to Average)
   - This shows average score per category

4. **Add additional context:**
   - Consider adding a line chart overlay showing median or quartiles
   - Or create a separate card showing overall average score

5. **Format and title:** "Score Distribution by Category"

**What this shows:** Stakeholders can see which categories the model performs well in and which need improvement. This answers "where does the model fail?"

**Deliverable:** Score distribution by category visualization

---

### Step 7: Create Category Comparison Chart (15-20 min)

**Objective:** Create a visualization that makes it easy to compare performance across categories.

#### For Tableau:

**What to do:**

1. **Create a new worksheet**

2. **Build a comparison chart:**
   - Option A: Heatmap
     - Drag `category` to Rows
     - Drag a calculated field (e.g., "Score Range") to Columns
     - Drag Count to Color
   - Option B: Bar chart with averages
     - Drag `category` to Rows
     - Drag `score` to Columns (change to Average)
     - Sort by average score (descending)
     - Add color gradient based on score

3. **Add context:**
   - Add reference line for overall average
   - Add labels showing exact values
   - Use color to highlight high/low performers

4. **Format and title:** "Category Performance Comparison"

5. **Rename worksheet:** "Category Comparison"

#### For PowerBI:

**What to do:**

1. **Create a new visual:**
   - Select "Clustered bar chart" or "Matrix" visual

2. **Build the comparison:**
   - Drag `category` to "Axis" or "Rows"
   - Drag `score` to "Values" (set to Average)
   - Sort by score (highest to lowest)

3. **Add visual indicators:**
   - Use conditional formatting to color bars based on score
   - Add data labels showing exact values
   - Consider adding a target line or benchmark

4. **Format and title:** "Category Performance Comparison"

**What this shows:** A clear ranking of categories by performance, making it easy for stakeholders to identify priorities for improvement.

**Deliverable:** Category comparison visualization

---

### Step 8: Add Interactive Filters (10-15 min)

**Objective:** Make your visualizations interactive so stakeholders can explore the data.

#### For Tableau:

**What to do:**

1. **Add filters to any worksheet:**
   - Right-click on `model_version` in the Data pane
   - Select "Show Filter"
   - A filter control appears on the right
   - Repeat for `category` and `date` (if you have date data)

2. **Format filters:**
   - Right-click on filter controls
   - Select "Customize"
   - Choose filter style (dropdown, list, etc.)
   - Position filters where they make sense

3. **Test interactivity:**
   - Click different filter options
   - Verify that all charts update accordingly
   - Ensure filters work across multiple worksheets

#### For PowerBI:

**What to do:**

1. **Add slicers:**
   - In the Visualizations pane, select "Slicer"
   - Drag `model_version` to the "Field" well
   - Repeat for `category` and `date` (if available)

2. **Format slicers:**
   - Adjust slicer style (dropdown, list, between, etc.)
   - Position slicers on your report canvas
   - Set slicer to affect all visuals (or specific ones)

3. **Test interactivity:**
   - Select different options in slicers
   - Verify that all visuals update
   - Check that filters work as expected

**What this enables:** Stakeholders can drill down into specific models, categories, or time periods without needing to modify the underlying data or ask you for new visualizations.

**Deliverable:** Interactive filters added to your visualizations

---

## Part 3: Build Interactive Dashboard (30-40 minutes)

### Step 9: Create Dashboard Layout (15-20 min)

**Objective:** Arrange your visualizations into a cohesive dashboard suitable for stakeholders.

#### For Tableau:

**What to do:**

1. **Create a new dashboard:**
   - Click "New Dashboard" at the bottom
   - Or go to Dashboard → New Dashboard

2. **Add your worksheets:**
   - Drag "Score Distribution" worksheet to the dashboard
   - Drag "Scores by Category" worksheet to the dashboard
   - Drag "Category Comparison" worksheet to the dashboard
   - Arrange them in a logical layout

3. **Add filters to dashboard:**
   - Drag filter controls from your worksheets to the dashboard
   - Or create new filters directly on the dashboard
   - Position filters at the top or side for easy access

4. **Set up filter actions:**
   - Go to Dashboard → Actions
   - Add filter actions so clicking on one chart filters others
   - This creates a more interactive experience

5. **Format the dashboard:**
   - Add a title: "Model Evaluation Score Dashboard"
   - Adjust sizing and spacing
   - Use a clean, professional color scheme
   - Remove unnecessary gridlines or clutter

#### For PowerBI:

**What to do:**

1. **Create a new report page:**
   - Click the "+" icon next to existing pages
   - Or go to Home → New page

2. **Add your visuals:**
   - Copy visuals from previous pages, or
   - Recreate them on the new page
   - Arrange in a grid layout

3. **Add slicers:**
   - Add slicers for model_version, category, and date
   - Position at the top or side
   - Format consistently

4. **Format the page:**
   - Add a text box with title: "Model Evaluation Score Dashboard"
   - Adjust visual sizes to fit the page
   - Use consistent colors and fonts
   - Remove unnecessary elements

5. **Set up cross-filtering:**
   - Ensure visuals are set to interact with each other
   - Test that selecting data in one visual filters others

**Deliverable:** Dashboard with all visualizations arranged and formatted

---

### Step 10: Add Titles, Descriptions, and Context (10-15 min)

**Objective:** Make your dashboard self-explanatory for non-technical stakeholders.

**What to do:**

1. **Add a main title:**
   - Clear, descriptive title: "Model Evaluation Score Dashboard"
   - Subtitle if needed: "Performance by Category and Model Version"

2. **Add descriptions where helpful:**
   - Brief explanation of what each visualization shows
   - Note what the scores represent (e.g., "Scores range from 0-100, where 100 is perfect")
   - Explain how to use filters

3. **Add context cards (optional but recommended):**
   - Overall average score
   - Total number of evaluations
   - Date range of data
   - These help stakeholders understand the scope

4. **Remove technical jargon:**
   - No mention of p-values, confidence intervals, or statistical tests
   - Focus on business language: "performance", "accuracy", "quality"
   - Remember: Dashboard answers "what" and "so what", not "how"

5. **Add instructions (if needed):**
   - Brief note: "Use filters above to explore specific models or categories"
   - Keep it simple and clear

**Deliverable:** Dashboard with clear titles, descriptions, and context

---

### Step 11: Test and Refine (5-10 min)

**Objective:** Ensure your dashboard works correctly and looks professional.

**What to do:**

1. **Test all interactions:**
   - Click through all filter options
   - Verify charts update correctly
   - Check that cross-filtering works
   - Test on different screen sizes (if possible)

2. **Review from stakeholder perspective:**
   - Is it clear what each chart shows?
   - Can someone understand it without explanation?
   - Are the key insights visible immediately?
   - Is it visually appealing and professional?

3. **Make final adjustments:**
   - Fix any broken interactions
   - Adjust colors for clarity
   - Ensure text is readable
   - Remove any unnecessary elements

4. **Save your work:**
   - Tableau: Save as `.twbx` (packaged workbook) or `.twb` (workbook)
   - PowerBI: Save as `.pbix` file
   - Name it clearly: `evaluation_score_dashboard.twbx` or `evaluation_score_dashboard.pbix`

**Deliverable:** Final, tested dashboard file

---

## Deliverables

### Submission hygiene

- **Filenames:** Use clear, descriptive names (avoid vague names such as `lab.ipynb`, `final_v2.py`, or `untitled.md`).
- **Scope:** Your **GitHub** repository must contain **only materials for this lab**—no unrelated projects, dumps, or personal files.
- **README:** Include a `README.md` that briefly explains what each main file or folder is for (a short map of your file structure).

**GitHub only:** Submit the URL to a **GitHub repository** that contains everything for this lab (Markdown, code, exports, images, decks). Do **not** submit a standalone Google Doc, Notion page, or cloud-only link as your primary deliverable—put sources or exports (for example `.md`, `.pdf`, `.pptx`, screenshots) **in the repository**.

Submit the following items:

1. **Dashboard File**
   - Tableau: `.twbx` or `.twb` file
   - PowerBI: `.pbix` file
   - Saved as: `evaluation_score_dashboard.[extension]`

2. **Dashboard Screenshot**
   - Screenshot of your final dashboard
   - Saved as: `dashboard_screenshot.png` or `.jpg`
   - Ensure it's clear and shows all key elements

3. **Data Source Documentation** (Brief)
   - Note which data source you used (previous lab or generated)
   - If from previous lab, note which lab and what data
   - Saved as: `data_source.md` (2-3 sentences)

4. **Reflection** (Optional but recommended)
   - How did you apply the communication layer principles?
   - What did you include vs. exclude from the dashboard?
   - What would you do differently next time?
   - Saved as: `reflection.md` (1-2 paragraphs)

**Submission Format:**
- Create a folder: `lab_evaluation_dashboard_[your_name]`
- Include all deliverables above
- Add a `README.md` with:
  - Which tool you used (Tableau or PowerBI)
  - Brief overview of your dashboard
  - Any notes about your data source

---

## Success Criteria

Your lab is complete when:
- [ ] You've successfully connected evaluation data to your BI tool
- [ ] You've created at least 3 visualizations (score distribution, by category, comparison)
- [ ] You've built an interactive dashboard with filters
- [ ] Your dashboard is suitable for non-technical stakeholders (no statistical jargon)
- [ ] You've applied communication layer principles (showing conclusions, not methods)
- [ ] All deliverables are submitted in the required format

---

## Optional Extra: Judge Agreement Rate Dashboard (60-90 minutes)

This optional section extends your dashboard to include judge agreement metrics, helping stakeholders understand evaluation reliability.

### Learning Objectives
- [ ] Create judge agreement rate visualizations
- [ ] Show evaluation reliability metrics
- [ ] Build comparative views of multiple judges

### Part 1: Data Preparation (15-20 min)

#### Step 1: Get Judge Agreement Data

**Option A: Use Data from Previous Labs** (Recommended if available)

If you completed the Human-in-the-Loop Lab or LLM Judges Lab from Day 1, you may have judge agreement data:

- **Human-in-the-Loop Lab**: Use your annotated dataset with multiple judge scores
- **LLM Judges Lab**: If you compared multiple judges, use that data

**What to extract:**
- Judge 1 scores
- Judge 2 scores (or multiple judges)
- Agreement indicators (exact match, within threshold, etc.)
- Categories
- Evaluation IDs

**Option B: Generate Sample Judge Agreement Data**

If you don't have judge agreement data, use this Python script:

```python
import pandas as pd
import numpy as np
from datetime import datetime, timedelta

np.random.seed(42)

categories = ['reasoning', 'knowledge', 'code', 'instruction_following', 'tool_calling']
start_date = datetime.now() - timedelta(days=90)
dates = [start_date + timedelta(days=i) for i in range(90)]

data = []
evaluation_id = 1

for date in dates:
    for category in categories:
        num_evaluations = np.random.randint(3, 10)
        
        for _ in range(num_evaluations):
            # Generate base score
            base_score = np.random.beta(3, 3) * 100
            
            # Judge 1 score (with some variance)
            judge_1_score = max(0, min(100, base_score + np.random.normal(0, 8)))
            
            # Judge 2 score (correlated with judge 1, but with variance)
            # Higher correlation = better agreement
            correlation = 0.75  # Adjust for different agreement levels
            judge_2_score = max(0, min(100, 
                base_score * correlation + (1 - correlation) * np.random.beta(3, 3) * 100 + np.random.normal(0, 8)))
            
            # Calculate agreement (within 10 points = agreement)
            agreement = 1 if abs(judge_1_score - judge_2_score) <= 10 else 0
            
            data.append({
                'evaluation_id': evaluation_id,
                'category': category,
                'judge_1_score': round(judge_1_score, 2),
                'judge_2_score': round(judge_2_score, 2),
                'score_difference': round(abs(judge_1_score - judge_2_score), 2),
                'agreement': agreement,
                'date': date.strftime('%Y-%m-%d')
            })
            evaluation_id += 1

df = pd.DataFrame(data)
df.to_csv('judge_agreement_data.csv', index=False)

print(f"Generated {len(df)} judge agreement records")
print(f"\nAgreement rate: {df['agreement'].mean():.2%}")
print(f"\nAgreement by category:")
print(df.groupby('category')['agreement'].mean())
```

Save this as `judge_agreement_data.csv`.

**Deliverable:** Judge agreement data file ready for import

---

### Part 2: Create Judge Agreement Visualizations (30-40 min)

#### Step 2: Agreement Rate by Category

**Objective:** Show which categories have higher judge agreement.

**For Tableau:**
- Drag `category` to Columns
- Drag `agreement` to Rows (change to Average)
- Format as percentage
- Add color gradient based on agreement rate

**For PowerBI:**
- Create a bar chart
- `category` on Axis
- `agreement` in Values (Average, formatted as percentage)
- Add conditional formatting

**What this shows:** Stakeholders can see which categories have reliable evaluations and which may need more judges or clearer criteria.

#### Step 3: Agreement Distribution

**Objective:** Show the distribution of score differences between judges.

**For Tableau:**
- Create a histogram of `score_difference`
- Show how often judges agree exactly vs. have large disagreements

**For PowerBI:**
- Create a histogram or bar chart
- Use `score_difference` binned into ranges
- Show frequency of each range

**What this shows:** The overall reliability of evaluations—are judges usually close, or do they disagree frequently?

#### Step 4: Judge Comparison Scatter Plot

**Objective:** Visualize the relationship between judge scores.

**For Tableau:**
- Drag `judge_1_score` to Columns
- Drag `judge_2_score` to Rows
- Change mark type to Circle
- Add trend line
- Points close to diagonal line = good agreement

**For PowerBI:**
- Create a scatter chart
- `judge_1_score` on X-axis
- `judge_2_score` on Y-axis
- Add trend line
- Color by category for additional insight

**What this shows:** A visual representation of judge agreement—points along the diagonal indicate agreement, scattered points indicate disagreement.

---

### Part 3: Build Judge Agreement Dashboard (15-20 min)

#### Step 5: Combine Visualizations

**What to do:**

1. **Create a new dashboard/page** for judge agreement

2. **Add all judge agreement visualizations:**
   - Agreement rate by category
   - Agreement distribution
   - Judge comparison scatter plot

3. **Add key metrics:**
   - Overall agreement rate (as a card/KPI)
   - Average score difference
   - Number of evaluations

4. **Add filters:**
   - Category filter
   - Date filter (if available)

5. **Format and title:** "Judge Agreement Analysis Dashboard"

6. **Add context:**
   - Brief explanation: "This dashboard shows how often judges agree on evaluation scores. Higher agreement indicates more reliable evaluations."
   - Note what agreement threshold you're using (e.g., "Agreement = scores within 10 points")

**Deliverable:** Complete judge agreement dashboard

---

### Optional Extra Deliverables

If you complete the optional extra:

1. **Judge Agreement Dashboard File**
   - `.twbx`/`.twb` (Tableau) or `.pbix` (PowerBI)
   - Saved as: `judge_agreement_dashboard.[extension]`

2. **Screenshot**
   - Screenshot of judge agreement dashboard
   - Saved as: `judge_agreement_screenshot.png`

---

## Tips & Troubleshooting

**Common Issues:**

1. **Data won't import**
   - Check file encoding (should be UTF-8)
   - Verify CSV uses commas as delimiters
   - Ensure no special characters in column names
   - Try opening in Excel first to verify format

2. **Visualizations look wrong**
   - Check data types (numeric vs. text)
   - Verify aggregations (Sum vs. Average vs. Count)
   - Ensure filters aren't excluding all data
   - Check for null or missing values

3. **Filters not working**
   - Verify filter is connected to correct field
   - Check that filter scope includes all relevant visuals
   - In PowerBI, ensure cross-filtering is enabled
   - In Tableau, check filter actions are set up correctly

4. **Dashboard is cluttered**
   - Remove unnecessary gridlines and borders
   - Use consistent colors
   - Group related visualizations
   - Add white space for clarity

5. **Scores don't make sense**
   - Verify score range (0-1 vs. 0-100)
   - Check for data quality issues
   - Ensure you're using the right aggregation (Average vs. Sum)

6. **Can't create certain chart types**
   - Some chart types require specific data structures
   - Try alternative visualizations (bar chart instead of box plot)
   - Check tool documentation for requirements
   - Simplify your data structure if needed

**Best Practices:**

- **Keep it simple:** Stakeholders need clarity, not complexity
- **Focus on insights:** What does the data tell stakeholders to do?
- **Use color wisely:** Too many colors can be distracting
- **Test with others:** Show your dashboard to a non-technical person—can they understand it?
- **Remember the lesson:** This is the communication layer, not the analysis layer

---

## Summary

This lab has guided you through creating an evaluation score distribution dashboard using Tableau or PowerBI. You've learned to:

- Prepare evaluation data for visualization
- Create meaningful visualizations that show score distributions
- Build interactive dashboards for stakeholders
- Apply communication layer principles (showing conclusions, not statistical methods)

The key takeaway: BI tools like Tableau and PowerBI are for communication, not analysis. Your dashboard should answer "what" and "so what" for stakeholders, while your Python notebooks and statistical analysis answer "how" and "why."

As an AI consultant, being able to translate technical analysis into accessible visualizations is essential for demonstrating business impact and getting stakeholder buy-in for AI initiatives.
