# Analysing-Employee-Salary-in-SQL
In this SQL query, I have worked on employee database using employee salary, income, deduction tables. Have used PIVOT in MSSQL Server
for tranforming rows to columns in order to create desired output report.

SQL queries used:
Query for First output table: 

select 
	emp_id,
	emp_name, 
	trns_type,
	case when trns_type='Allowance' then round(base_salary*(percentage/100),1)
		 when trns_type='Basic' then round(base_salary*(percentage/100),1)
		 when trns_type='Others' then round(base_salary*(percentage/100),1)
		 when trns_type='House' then round(base_salary*(percentage/100),1)
		 when trns_type='Health' then round(base_salary*(percentage/100),1)
		when trns_type='Insurance' then round(base_salary*(percentage/100),1)
	end as amount
from salary
cross join(select income as trns_type,cast(percentage as decimal) as percentage from income
			union
			select deduction as trns_type,cast(percentage as decimal) as percentage from deduction) a;

Query for overall employee pay report: 

select  emp_name as employe
, basic,allowance,others
,(basic + allowance +others) as gross
,insurance, health, house,
(insurance + health +house) as total_deductions
,(basic+allowance+others)-(insurance + health +house) as net_pay
from
(
	select emp_name, trns_type, amount
	from emp_transaction
	) s

pivot
	( 
	sum(amount)
	for trns_type in ([allowance],[Basic],[others],[Insurance],[Health],[House])
	)g;
