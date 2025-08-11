# Bank-Loan-Analysis-Using-Python
**This project utilizes Python to analyze loan data, calculate key metrics like total applications, funded amounts, interest rates, and debt-to-income ratios, and create visualizations showing trends, regional patterns, loan terms, purposes, and the impact of factors like employment length and home ownership.**

import pandas as pd 
import numpy as np 
import matplotlib.pyplot as plt 
import seaborn as sns
import warnings 
import plotly.express as px 

warnings.filterwarnings('ignore')  # Suppress warning messages

df = pd.read_csv("financial_loan.csv")
df.head()

**This code above imports libraries needed for data handling and visualization. It then loads a CSV file containing loan data into a DataFrame called df. Finally, it displays the first few rows to give a quick look at the data. Warnings are suppressed to keep the output clean.**

total_loan_application = df["id"].count()
print("Total Loan Applications:", total_loan_application)

df["issue_date"] = pd.to_datetime(df["issue_date"])

latest_issue_date = df["issue_date"].max()
latest_year = latest_issue_date.year
latest_month = latest_issue_date.month

mtd_data = df[(df["issue_date"].dt.year == latest_year) & (df["issue_date"].dt.month == latest_month)]

mtd_loan_applications = mtd_data["id"].count()

print(f"MTD Loan Applications (for {latest_issue_date.strftime('%B %Y')}): {mtd_loan_applications}")



**This code calculates the total number of loan applications by counting all entries in the "id" column. It then converts the "issue_date" column to a datetime format to allow date-based operations. Next, it finds the most recent loan issue date in the dataset and extracts its year and month. Using this, it filters the data to include only loans issued in that latest month and year, calculating the Month-to-Date (MTD) loan applications. Finally, it prints the total and MTD loan application counts, with the MTD labeled by the relevant month and year.**


total_funded_amount = df["loan_amount"].sum()
total_funded_amount_millions = total_funded_amount / 1_000_000
print("Total Funded Amount: ${:.2f}M".format(total_funded_amount_millions))

latest_issue_date = df["issue_date"].max()
latest_year = latest_issue_date.year
latest_month = latest_issue_date.month

mtd_data = df[(df["issue_date"].dt.year == latest_year) & (df["issue_date"].dt.month == latest_month)]

mtd_total_funded_amount = mtd_data["loan_amount"].sum()
mtd_total_funded_amount_millions = mtd_total_funded_amount / 1_000_000

print("MTD Total Funded Amount: ${:.2f}M".format(mtd_total_funded_amount_millions))




****This code calculates the total amount of loans funded by summing the "loan_amount" column and converts the value into millions for easier readability. It then finds the most recent loan issue date to filter the dataset for the latest month and year, calculates the Month-to-Date (MTD) total funded amount for that period, also converted to millions, and prints both the overall and MTD funded amounts formatted with two decimal places.**


latest_issue_date = df["issue_date"].max()
latest_year = latest_issue_date.year
latest_month = latest_issue_date.month

mtd_data = df[(df["issue_date"].dt.year == latest_year) & (df["issue_date"].dt.month == latest_month)]

mtd_total_amount_received = mtd_data["total_payment"].sum()
mtd_total_amount_received_millions = mtd_total_amount_received / 1_000_000

print("MTD Total Funded Amount Received: ${:.2f}M".format(mtd_total_amount_received_millions))

average_dti = df["dti"].mean() * 100
print("Avg DTI: {:.2f}%".format(average_dti))

**This code identifies the most recent loan issue date and filters the dataset for that month and year to calculate the Month-to-Date (MTD) total amount received from borrowers, converting the sum to millions for clarity. It then prints this MTD received amount. Additionally, it calculates the average debt-to-income (DTI) ratio across all loans, converts it to a percentage, and prints it with two decimal places, providing insight into borrowers’ overall financial health.****


good_loans = df[df["loan_status"].isin(["Fully Paid", "Current"])]

total_loan_applications = df["id"].count()

good_loan_applications = good_loans["id"].count()
good_loan_funded_amount = good_loans["loan_amount"].sum()
good_loan_received = good_loans["total_payment"].sum()

good_loan_funded_amount_millions = good_loan_funded_amount / 1_000_000
good_loan_received_millions = good_loan_received / 1_000_000

good_loan_percentage = (good_loan_applications / total_loan_applications) * 100

print("Good Loan Applications: ", good_loan_applications)
print("Good Loan Funded Amount (in Millions): ${:.2f}M".format(good_loan_funded_amount_millions))
print("Good Loan Total Received (in Millions): ${:.2f}M".format(good_loan_received_millions))
print("Percentage of Good Loan Applications: {:.2f}%".format(good_loan_percentage))


**This code filters the dataset to select loans that are considered "good," meaning their status is either "Fully Paid" or "Current." It then calculates the total number of loan applications and, within the filtered good loans, counts the applications, sums the funded loan amounts, and sums the total payments received from borrowers. These sums are converted to millions for easier interpretation. Finally, it computes the percentage of good loan applications relative to the total and prints these key figures to provide insights into the quality and performance of the loan portfolio.****

bad_loans = df[df["loan_status"].isin(["Charged Off"])]

total_loan_applications = df["id"].count()

bad_loan_applications = bad_loans["id"].count()
bad_loan_funded_amount = bad_loans["loan_amount"].sum()
bad_loan_received = bad_loans["total_payment"].sum()

bad_loan_funded_amount_millions = bad_loan_funded_amount / 1_000_000
bad_loan_received_millions = bad_loan_received / 1_000_000

bad_loan_percentage = (bad_loan_applications / total_loan_applications) * 100

print("Bad Loan Applications: ", bad_loan_applications)
print("Bad Loan Funded Amount (in Millions): ${:.2f}M".format(bad_loan_funded_amount_millions))
print("Bad Loan Total Received (in Millions): ${:.2f}M".format(bad_loan_received_millions))
print("Percentage of Bad Loan Applications: {:.2f}%".format(bad_loan_percentage))


**This code filters the dataset to identify "bad" loans where the loan status is "Charged Off," indicating loans that were not repaid as agreed. It calculates the total loan applications, then within the bad loans, counts the number of applications, sums the funded loan amounts, and sums the total payments received. These amounts are converted to millions for easier interpretation. Finally, it computes the percentage of bad loan applications relative to the total and prints these key metrics to highlight the portion and financial impact of defaulted loans.****



import matplotlib.pyplot as plt

monthly_funded = (
    df.sort_values("issue_date")
      .assign(month_name=lambda x: x["issue_date"].dt.strftime("%b %Y"))
      .groupby("month_name", sort=False)["total_payment"]
      .sum()
      .div(1_000_000)
      .reset_index(name="received_amount_millions")
)

plt.figure(figsize=(10,5))
plt.fill_between(monthly_funded["month_name"], monthly_funded["received_amount_millions"], color="lightgreen", alpha=0.5)
plt.plot(monthly_funded["month_name"], monthly_funded["received_amount_millions"], color="green", linewidth=2)

for i, row in monthly_funded.iterrows():
    plt.text(i, row["received_amount_millions"] + 0.1, f"{row['received_amount_millions']:.2f}",
             ha="center", va="bottom", fontsize=9, rotation=0, color="black")

plt.title("Total Received Amount By Month", fontsize=14)
plt.xlabel("Month")
plt.ylabel("Received Amount (Millions)")
plt.xticks(ticks=range(len(monthly_funded)), labels=monthly_funded["month_name"], rotation=45)
plt.grid(True, linestyle="--", alpha=0.6)
plt.tight_layout()
plt.show()




**This code creates a line and area chart that shows the total amount of payments received from borrowers each month, measured in millions. First, it sorts the loan data by issue date, extracts month and year as a formatted string, and groups the data by this monthly label to sum the total payments. The payments are converted into millions for easier visualization. Then, using Matplotlib, it plots the monthly received amounts with a filled area under the line for visual emphasis. Each point on the graph is labeled with its corresponding value. The chart includes axis labels, a title, rotated month labels for readability, and a grid to make trends easier to follow. Finally, it adjusts the layout and displays the chart.****


import matplotlib.pyplot as plt

state_funding = df.groupby("address_state")["loan_amount"].sum().sort_values(ascending=True)
state_funding_thousands = state_funding / 1_000

plt.figure(figsize=(10,8))
bars = plt.barh(state_funding_thousands.index, state_funding_thousands.values, color="lightcoral")

for bar in bars:
    width = bar.get_width()
    plt.text(width + 10, bar.get_y() + bar.get_height() / 2,
             f"{width:,.0f}K", va="center", fontsize=9)

plt.title("Total Funded Amount By State (in Thousands)")
plt.xlabel("Funded Amount")
plt.ylabel("State")
plt.tight_layout()
plt.show()

**This code produces a horizontal bar chart showing the total loan amounts funded in each state. It groups the loan data by the "address_state" column, sums the loan amounts for each state, and sorts the results in ascending order. The loan amounts are converted into thousands for easier viewing. The horizontal bars represent each state’s total funded amount, with labels positioned slightly past the end of each bar to display the precise value in thousands. The chart includes a clear title and axis labels, and the layout is adjusted for neat presentation before displaying the figure.**


term_funding_millions = df.groupby("term")["loan_amount"].sum() / 1_000_000

plt.figure(figsize=(5,5))
plt.pie(
    term_funding_millions,
    labels=term_funding_millions.index,
    autopct=lambda p: f"{p:.1f}%\n${p*sum(term_funding_millions)/100:.1f}M",
    startangle=90,
    wedgeprops={"width": 0.4}
)

plt.gca().add_artist(plt.Circle((0,0), 0.70, color="white"))
plt.title("Total Funded Amount By Term (in Millions)")
plt.show()


**This code creates a donut chart to show how the total funded loan amount is distributed across different loan terms. It groups the loan amounts by the "term" category, sums them, and converts the totals into millions for easier interpretation. The pie chart slices represent each loan term’s share of the total funding, with labels showing both the percentage and the actual funded amount in millions. The donut shape is achieved by adding a white circle in the center. The chart starts at a 90-degree angle for better orientation, and a clear title is added before displaying the figure.****

emp_funding = df.groupby("emp_length")["loan_amount"].sum().sort_values() / 1_000

plt.figure(figsize=(10,6))
bars = plt.barh(emp_funding.index, emp_funding.values, color="purple")

for bar in bars:
    width = bar.get_width()
    plt.text(width + 5, bar.get_y() + bar.get_height() / 2,
             f"{width:,.0f}K", va="center", fontsize=9)

plt.xlabel("Funded Amount (in Thousands)")
plt.title("Total Funded Amount by Employment Length")
plt.grid(axis="x", linestyle="--", alpha=0.5)
plt.tight_layout()
plt.show()



**This code generates a horizontal bar chart showing how loan funding is distributed across different employment length categories. It groups the loan amounts by "emp_length," sums them, sorts the values in ascending order, and converts the amounts into thousands for easier reading. Each bar represents the total funded amount for a specific employment length, with labels positioned just beyond each bar displaying the value in thousands. The chart includes axis labels, a descriptive title, and a subtle grid along the x-axis for better readability before presenting the final figure.**



emp_funding = df.groupby("purpose")["loan_amount"].sum().sort_values() / 1_000

plt.figure(figsize=(10,6))
bars = plt.barh(emp_funding.index, emp_funding.values, color="skyblue")

for bar in bars:
    width = bar.get_width()
    plt.text(width + 5, bar.get_y() + bar.get_height() / 2,
             f"{width:,.0f}K", va="center", fontsize=9)

plt.xlabel("Funded Amount (in Thousands)")
plt.title("Total Funded Amount by Loan Purpose")
plt.grid(axis="x", linestyle="--", alpha=0.5)
plt.tight_layout()
plt.show()




**This code creates a horizontal bar chart showing how loan funding is distributed across different loan purposes. It groups the loan amounts by the "purpose" category, sums and sorts them, then converts the totals into thousands for easier reading. Each bar represents the total funded amount for a specific loan purpose, with value labels positioned just beyond the end of each bar. The chart includes clear axis labels, a descriptive title, and a light grid along the x-axis for readability before displaying the plot.****


home_funding = df.groupby("home_ownership")["loan_amount"].sum().reset_index()
home_funding["loan_amount_millions"] = home_funding["loan_amount"] / 1_000_000

fig = px.treemap(
    home_funding,
    path=["home_ownership"],
    values="loan_amount_millions",
    color="loan_amount_millions",
    color_continuous_scale="Blues",
    title="Total Funded Amount By Home Ownership (in Millions)"
)

fig.show()

**This code creates an interactive treemap visualization to display how loan funding is distributed based on borrowers' home ownership status. It groups loan amounts by the "home_ownership" category, sums them, and converts the amounts into millions for easier interpretation. The treemap uses color intensity to represent the funded amount, with darker shades indicating higher totals. This visualization helps quickly understand the relationship between home ownership types and the total loans funded, providing a clear hierarchical view of the data.****


#####################################################################################################################


BRIEF SUMMARY OF THE PROJECT

**In this project, I used Python to analyze a loan dataset by calculating key metrics such as total loan applications, funded amounts, and repayments, both overall and for the latest month. I examined loan quality by separating good and bad loans based on their status. To understand lending patterns, I created several visualizations including line and area charts for monthly received payments, bar charts for funding by state, employment length, and loan purpose, a donut chart for loan term distribution, and an interactive treemap for home ownership impact. These analyses provide clear insights into loan performance, borrower profiles, and regional disparities to support informed lending decisions.**


















