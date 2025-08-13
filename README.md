Loan Data Analysis

Overview:
Python is used to analyze a loan dataset and generate actionable insights. It calculates KPIs, evaluates loan quality, and visualizes trends to understand borrower profiles and lending patterns.

1. Load & Inspect Data
import pandas as pd
df = pd.read_csv("financial_loan.csv")
df.head()


Load CSV data into a DataFrame and display the first few rows to inspect the dataset.

2. Total & MTD Loan Applications
total_loan_application = df["id"].count()
df["issue_date"] = pd.to_datetime(df["issue_date"])
latest_issue_date = df["issue_date"].max()
mtd_data = df[(df["issue_date"].dt.year == latest_issue_date.year) & (df["issue_date"].dt.month == latest_issue_date.month)]
mtd_loan_applications = mtd_data["id"].count()


Calculates total loan applications and Month-to-Date (MTD) applications for the latest month.

3. Total & MTD Funded Amounts
total_funded_amount = df["loan_amount"].sum() / 1_000_000
mtd_total_funded_amount = mtd_data["loan_amount"].sum() / 1_000_000


Calculates overall and MTD funded amounts in millions for easier readability.

4. Month-to-Date Payments & Avg DTI
mtd_total_amount_received = mtd_data["total_payment"].sum() / 1_000_000
average_dti = df["dti"].mean() * 100


Finds total payments received MTD and average debt-to-income ratio across all loans.

5. Loan Quality Analysis

Good Loans:

good_loans = df[df["loan_status"].isin(["Fully Paid", "Current"])]
good_loan_funded_amount = good_loans["loan_amount"].sum() / 1_000_000
good_loan_received = good_loans["total_payment"].sum() / 1_000_000
good_loan_percentage = (good_loans["id"].count() / df["id"].count()) * 100


Filters fully paid or current loans and calculates funded amounts, received payments, and percentage of total loans.

Bad Loans:

bad_loans = df[df["loan_status"] == "Charged Off"]
bad_loan_funded_amount = bad_loans["loan_amount"].sum() / 1_000_000
bad_loan_received = bad_loans["total_payment"].sum() / 1_000_000
bad_loan_percentage = (bad_loans["id"].count() / df["id"].count()) * 100


Filters defaulted loans and calculates their funded amounts, received payments, and percentage of total loans.

6. Visualizations

Monthly Payments:

monthly_funded = df.sort_values("issue_date").assign(month_name=lambda x: x["issue_date"].dt.strftime("%b %Y")).groupby("month_name")["total_payment"].sum().div(1_000_000).reset_index()


Line and area chart showing monthly payments received in millions.

Funding by State:

state_funding = df.groupby("address_state")["loan_amount"].sum().sort_values() / 1_000


Horizontal bar chart displaying total funded amounts by state (in thousands).

Loan Terms:

term_funding_millions = df.groupby("term")["loan_amount"].sum() / 1_000_000


Donut chart showing distribution of funded amounts by loan term.

Employment Length:

emp_funding = df.groupby("emp_length")["loan_amount"].sum().sort_values() / 1_000


Horizontal bar chart of funded amounts by borrowers’ employment length.

Loan Purpose:

purpose_funding = df.groupby("purpose")["loan_amount"].sum().sort_values() / 1_000


Horizontal bar chart showing funded amounts by loan purpose.

Home Ownership:

home_funding = df.groupby("home_ownership")["loan_amount"].sum().reset_index()


Interactive treemap showing total funded amounts based on borrowers’ home ownership status.

Summary:

This project calculates key loan metrics, evaluates loan quality, and visualizes trends across time, location, employment, loan purpose, and home ownership. It provides insights for lenders to understand funding patterns, borrower profiles, and regional disparities.


#####################################################################################


BRIEF SUMMARY OF THE PROJECT

**In this project, I used Python to analyze a loan dataset by calculating key metrics such as total loan applications, funded amounts, and repayments, both overall and for the latest month. I examined loan quality by separating good and bad loans based on their status. To understand lending patterns, I created several visualizations including line and area charts for monthly received payments, bar charts for funding by state, employment length, and loan purpose, a donut chart for loan term distribution, and an interactive treemap for home ownership impact. These analyses provide clear insights into loan performance, borrower profiles, and regional disparities to support informed lending decisions.**


















