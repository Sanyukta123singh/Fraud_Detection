PaySim simulates mobile money transactions based on a sample of real transactions extracted from one month of financial logs from a mobile money service implemented in an African country. The original logs were provided by a multinational company, who is the provider of the mobile financial service which is currently running in more than 14 countries all around the world.

step - maps a unit of time in the real world. In this case 1 step is 1 hour of time. Total steps 744 (30 days simulation).

type - CASH-IN, CASH-OUT, DEBIT, PAYMENT and TRANSFER.

amount -
amount of the transaction in local currency.

nameOrig - customer who started the transaction

oldbalanceOrg - initial balance before the transaction

newbalanceOrig - new balance after the transaction

nameDest - customer who is the recipient of the transaction

oldbalanceDest - initial balance recipient before the transaction. Note that there is not information for customers that start with M (Merchants).

newbalanceDest - new balance recipient after the transaction. Note that there is not information for customers that start with M (Merchants).

isFraud - This is the transactions made by the fraudulent agents inside the simulation. In this specific dataset the fraudulent behavior of the agents aims to profit by taking control or customers accounts and try to empty the funds by transferring to another account and then cashing out of the system.

isFlaggedFraud - The business model aims to control massive transfers from one account to another and flags illegal attempts. An illegal attempt in this dataset is an attempt to transfer more than 200.000 in a single transaction.


**Exploratory Data Analysis**

# Fraud_Detection
Fraud detection project : Exploratory data analysis is done in this project using SQL.

#What type of transactions and total amount per type

select 
	type,
	count (*) as amount 
from Fraud
group by 
	type
order by 
	amount desc;
  
  
  #Which types of transactions are fraudulent
  
select 
	count(*) as Transfer 
from 
	fraud 
where 
	type = 'TRANSFER' and isFraud = 1

select 
	count(*) as Cash_Out 
from 
	fraud 
where 
	type = 'CASH_OUT' and isFraud = 1;
  
  
  #What determines whether the feature isFlaggedFraud gets set or not?
  
  select 
		count(*) as Flagged_Fraud 
      	from 
		Fraud 
where 
		isFlaggedFraud = 1
	select 
		type, 
		amount, 
		isFraud,
		isFlaggedFraud 
from 
		Fraud 
where 
	isFlaggedFraud = 1

select 
		type, 
		amount, 
		isFraud,
		isFlaggedFraud 
from 
		Fraud 
where 
		amount>200000
order by 
		amount desc;
    
select 
		min(amount) as minimum 
from 
		Fraud 
where 
	isFlaggedFraud = 1


select 
		max(amount) as maximum 
from 
		Fraud 
where 
	isFlaggedFraud = 0


#Can oldBalanceDest and newBalanceDest determine isFlaggedFraud being set

select 
		count(*) No_of_Transfers
from fraud 
where 
		isFlaggedFraud = 0 and 
		oldBalanceDest = 0 and 
		newBalanceDest = 0 and 
              type = 'TRANSFER
              
              
 select 
	min(oldbalanceOrg) as minimum, 
	max(oldbalanceOrg) as maximum 
from Fraud
where 
	isFlaggedFraud = 1 and type = 'TRANSFER'


select 
	min(oldbalanceOrg) as minimum, 
	max(oldbalanceOrg) as maximum 
from Fraud
where 
	isFlaggedFraud = 0 and type = 'TRANSFER' and
	oldbalanceOrg = newbalanceOrig



#Can isFlaggedFraud be set based on seeing a customer transacting more than once? 


Select 
	*, 
	RANK() over(order by nameDest) as dr 
from 
	Fraud 
where 
	isFlaggedFraud = 1


Select 
	*, 
	RANK() over(order by nameDest) as dr 
from 
	Fraud 
where 
	isFlaggedFraud = 0


#Are expected merchant accounts accordingly labelled?

select 
	* 
from 
	Fraud 
where 
  type = 'CASH_IN' and nameOrig like '%M%'
  
  
  select 
	* 
from 
	Fraud 
where 
	type = 'CASH_OUT' and nameDest like '%M%'


#Are there account labels common to fraudulent TRANSFERs and CASH_OUTs?


select 
	* 
from 
	Fraud 
where 
	isFraud = 1 and 
  nameDest in (select nameOrig from Fraud where isFraud = 1);
  
  
  
#Could destination accounts for fraudulent TRANSFERs originate CASHOUTs that are not detected and are labeled as genuine? 
 
 
 select 
	step,nameDest 
from 
	Fraud 
where 
	 type = 'TRANSFER' and isFraud = 1 and 
	nameDest in (select nameOrig from Fraud where type = 'CASH_OUT' and isFraud = 0)

select 
	step,nameDest 
from 
	Fraud 
where 
	 type = 'TRANSFER' and isFraud = 1 and 
	nameDest in (select nameOrig from Fraud where type = 'CASH_OUT' and isFraud = 0)


select 
	step,nameOrig
from 
	Fraud 
where 
	 type = 'CASH_OUT' and isFraud = 0 and 
	nameOrig in ('C1714931087',
'C1023330867',
'C423543548')
