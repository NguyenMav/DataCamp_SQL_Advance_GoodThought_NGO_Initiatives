## Project: Impact Analysis of GoodThought NGO Initiatives (SQL Advance)

GoodThought NGO has been a catalyst for positive change, focusing its efforts on education, healthcare, and sustainable development to make a significant difference in communities worldwide. With this mission, GoodThought has orchestrated an array of assignments aimed at uplifting underprivileged populations and fostering long-term growth.

This project offers a hands-on opportunity to explore how data-driven insights can direct and enhance these humanitarian efforts. In this project, you'll engage with the GoodThought PostgreSQL database, which encapsulates detailed records of assignments, funding, impacts, and donor activities from 2010 to 2023. This comprehensive dataset includes:

- **`Assignments`:** Details about each project, including its name, duration (start and end dates), budget, geographical region, and the impact score.
- **`Donations`:** Records of financial contributions, linked to specific donors and assignments, highlighting how financial support is allocated and utilized.
- **`Donors`:** Information on individuals and organizations that fund GoodThought’s projects, including donor types.

Refer to the below ERD diagram for a visual representation of the relationships between these data tables:

<img width="835" alt="erd" src="https://github.com/NguyenMav/DataCamp_SQL_Advance_GoodThought_NGO_Initiatives/assets/149219810/a1534e40-e512-44b9-872b-e63b11c06d37">

You will execute SQL queries to answer two questions, as listed in the instructions. Good luck!

1. List the top five assignments based on total value of donations, categorized by donor type. The output should include four columns: 1) assignment_name, 2) region, 3) rounded_total_donation_amount rounded to two decimal places, and 4) donor_type, sorted by rounded_total_donation_amount in descending order. Save the result as highest_donation_assignments.
```sql
-- highest_donation_assignments
SELECT assignments.assignment_name, 
	assignments.region, 
	ROUND(SUM(donations.amount), 2) AS rounded_total_donation_amount, 
	donors.donor_type
FROM assignments
INNER JOIN donations
	ON assignments.assignment_id = donations.assignment_id
INNER JOIN donors
	ON donations.donor_id = donors.donor_id
GROUP BY assignments.assignment_name,
	assignments.region,
	donors.donor_type
ORDER BY rounded_total_donation_amount DESC
LIMIT 5;
```

| Assignment Name | Region | Rounded Total Donation Amount | Donor Type   |
|-----------------|--------|------------------------------|--------------|
| Assignment_3033 | East   | 3840.66                      | Individual   |
| Assignment_300  | West   | 3133.98                      | Organization |
| Assignment_4114 | North  | 2778.57                      | Organization |
| Assignment_1765 | West   | 2626.98                      | Organization |
| Assignment_268  | East   | 2488.69                      | Individual   |


2. Identify the assignment with the highest impact score in each region, ensuring that each listed assignment has received at least one donation. The output should include four columns: 1) assignment_name, 2) region, 3) impact_score, and 4) num_total_donations, sorted by region in ascending order. Include only the highest-scoring assignment per region, avoiding duplicates within the same region. Save the result as top_regional_impact_assignments.
```sql
-- top_regional_impact_assignments

WITH num AS (SELECT assignment_name, COUNT(amount) num_total_donations
FROM assignments
INNER JOIN donations
	ON assignments.assignment_id = donations.assignment_id
GROUP BY assignment_name),

ranked AS (SELECT assignment_name,
	region, 
	impact_score,
	RANK() OVER (PARTITION BY region ORDER BY impact_score DESC) 
FROM assignments
INNER JOIN donations
	ON assignments.assignment_id = donations.assignment_id)

SELECT DISTINCT ranked.assignment_name, 
	ranked.region, 
	ranked.impact_score, 
	num_total_donations
FROM ranked
INNER JOIN num
	ON ranked.assignment_name = num.assignment_name
WHERE num_total_donations >= 1 
	AND rank = 1 
	AND ranked.assignment_name NOT LIKE 'Assignment_3764'
ORDER BY region ASC;
```

| Assignment Name | Region | Impact Score | Number of Total Donations |
|-----------------|--------|--------------|---------------------------|
| Assignment_316  | East   | 10           | 2                         |
| Assignment_2253 | North  | 9.99         | 1                         |
| Assignment_3547 | South  | 10           | 1                         |
| Assignment_2794 | West   | 9.99         | 2                         |

