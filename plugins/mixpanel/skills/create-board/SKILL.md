---
name: create-board
description: Create a new Mixpanel board with multiple reports organized by section.
---

# create-board

**When to use this skill**: When users want to create a new analytics board, set up monitoring dashboards, or organize multiple reports into a cohesive view.

---

## Instructions

### Overview

This skill helps you create a new Mixpanel board with one or more reports. Boards are collections of reports (Insights, Funnels, Flows, Retention) organized into sections for monitoring key metrics.

### Step-by-step workflow

1. **Understand the request**
   - What metrics or questions should this board answer?
   - Who is the intended audience (PM, executive, data team)?
   - What cadence will this be reviewed (daily, weekly, monthly)?

2. **Determine reports needed**
   - For each metric or question, identify the appropriate report type:
     - **Insights**: Trends, totals, averages over time
     - **Funnels**: Conversion flows between steps
     - **Flows**: User pathways and drop-off points
     - **Retention**: Cohort retention curves
   - Aim for 3-8 reports per board (enough to be comprehensive, not overwhelming)

3. **Organize into sections**
   - Group related reports into logical sections:
     - "Acquisition" (signup metrics, channel performance)
     - "Activation" (onboarding, first actions)
     - "Engagement" (DAU/MAU, feature usage)
     - "Retention" (cohort curves, churn signals)
     - "Revenue" (conversion, ARPU)
   - Each section should tell part of the story

4. **Create the board**

   **Two-step process required:**

   **Step 4a: Create reports and collect query_ids**
   - For each report you want on the board, run:
     ```
     Run-Query(
       project_id=X,
       report_type='insights',  # or 'funnels', 'flows', 'retention'
       report={...report definition...},
       skip_results=true    ← Important: skips execution, just saves the query
     )
     ```
   - This returns a `query_id` (e.g., "abc123")
   - Collect all query_ids you need for the dashboard

   **Step 4b: Create the dashboard**
   - Use `Create-Dashboard` with collected query_ids:
     ```
     Create-Dashboard(
       project_id=X,
       title="Dashboard Name",
       rows=[
         {
           "contents": [
             {"type": "text", "html_content": "<h2>Growth Metrics</h2>"}
           ]
         },
         {
           "contents": [
             {"type": "report", "query_id": "abc123", "name": "DAU Trend"},
             {"type": "report", "query_id": "def456", "name": "Conversion Funnel"}
           ]
         },
         {
           "contents": [
             {"type": "text", "html_content": "<h2>Retention</h2>"}
           ]
         },
         {
           "contents": [
             {"type": "report", "query_id": "ghi789", "name": "30-Day Retention"}
           ]
         }
       ]
     )
     ```

   **Dashboard structure:**
   - Max 30 rows per dashboard
   - Each row: 1-4 items (reports or text cards)
   - Use text cards (type: "text") as section headers
   - Reports need query_id + name (description optional)
   - Text cards support HTML: h1, h2, h3, p, ul, li, strong, em

5. **Confirm with the user**
   - Create-Dashboard returns a dashboard URL
   - Share the URL with the user
   - Explain what each section measures and why it matters
   - Suggest review cadence based on the metrics

### Report selection guide

**For monitoring product health:**
- Insights: DAU/MAU ratio, session frequency
- Retention: D1/D7/D30 curves
- Funnels: Core activation flow

**For growth analysis:**
- Insights: New user signups by channel
- Funnels: Signup → activation → conversion
- Flows: Drop-off points in onboarding

**For feature launches:**
- Insights: Feature adoption rate over time
- Funnels: Feature discovery → first use → repeat use
- Retention: Retention of users who used the feature

### Example interaction

**User**: "Create a weekly executive dashboard for me"

**Assistant**:
1. Understands this needs high-level business metrics
2. Determines key sections:
   - Growth (new users, activation rate)
   - Engagement (DAU, sessions per user)
   - Retention (cohort curves)
   - Revenue (conversion rate, upgrades)
3. Creates 5-6 reports using Run-Query with skip_results=true
4. Collects query_ids from each response
5. Calls Create-Dashboard with rows organized by section
6. Returns: "I've created your Executive Weekly Dashboard with 6 reports across 4 sections. It covers growth, engagement, retention, and revenue. View it here: [board_url]. I recommend reviewing this every Monday morning."

### Common mistakes to avoid

- **Too many reports**: Keep boards focused (3-8 reports). More than 10 becomes hard to digest.
- **Mixed audiences**: Don't combine executive summaries with deep data dives. Create separate boards.
- **No context**: Always explain what each section measures and why it matters.
- **Static snapshots**: Boards should update automatically. Avoid date filters locked to specific ranges unless it's for historical analysis.
- **Forgetting skip_results=true**: Always use this when creating reports for dashboards, otherwise you'll execute queries unnecessarily.

### Success criteria

- Board has a clear purpose and audience
- Reports are organized into logical sections
- Each report answers a specific question
- User understands what the board measures and when to check it
