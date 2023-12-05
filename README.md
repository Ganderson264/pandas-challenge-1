# pandas-challenge-1
Part 1: Explore the Data
Import the data and use Pandas to learn more about the dataset.

import pandas as pd
​
df = pd.read_csv('Resources/client_dataset.csv')
​
df.head()
first	last	job	phone	email	client_id	order_id	order_date	order_week	order_year	item_id	category	subcategory	unit_price	unit_cost	unit_weight	qty	line_number
0	Donald	Harding	Immunologist	793-904-7725x39308	harding.donald.7185@sullivan.com	58515	8953482	2023-04-28	17	2023	EUD29711-63-6U	decor	wall art	1096.80	762.71	7.50	105	1
1	Tiffany	Myers	Music therapist	201.442.4543x942	myers.t.6537@ferguson-johnson.net	37609	8069089	2023-05-19	20	2023	XDA18116-89-4A	consumables	pens	24.95	15.09	1.49	21	0
2	Shannon	Watson	Immunologist	687.737.9424x8503	swatson8146@payne.net	57113	1902144	2023-01-29	4	2023	ABE59463-05-7E	software	project management	13.52	7.86	1.68	39	6
3	Nathan	Baker	Accounting technician	827-788-8123x012	bakernathan@benson.com	46554	9031802	2023-04-25	17	2023	ZMM00836-65-0C	consumables	pens	36.42	24.85	1.23	29	3
4	Christina	Schwartz	Chiropractor	265-829-3643	christinaschwartz9252@mcconnell.com	92089	1322274	2023-05-28	21	2023	BZX55559-12-3X	consumables	misc	195.10	108.17	46.43	20	1
# View the column names in the data
print(df.columns)
​
Index(['first', 'last', 'job', 'phone', 'email', 'client_id', 'order_id',
       'order_date', 'order_week', 'order_year', 'item_id', 'category',
       'subcategory', 'unit_price', 'unit_cost', 'unit_weight', 'qty',
       'line_number'],
      dtype='object')
# Use the describe function to gather some basic statistics
print(df.describe())
​
          client_id      order_id    order_week    order_year    unit_price  \
count  54639.000000  5.463900e+04  54639.000000  54639.000000  54639.000000   
mean   54837.869416  5.470190e+06     11.359139   2022.993064    136.267207   
std    25487.438231  2.599807e+06      7.023499      0.082997    183.873135   
min    10033.000000  1.000886e+06      1.000000   2022.000000      0.010000   
25%    33593.000000  3.196372e+06      6.000000   2023.000000     20.800000   
50%    53305.000000  5.496966e+06     11.000000   2023.000000     68.310000   
75%    78498.000000  7.733869e+06     17.000000   2023.000000    173.160000   
max    99984.000000  9.998480e+06     52.000000   2023.000000   1396.230000   

          unit_cost   unit_weight           qty   line_number  
count  54639.000000  54639.000000  5.463900e+04  54639.000000  
mean      99.446073      5.004116  5.702646e+02      2.979667  
std      133.164267      5.326599  1.879552e+04      2.436320  
min        0.010000      0.000000  0.000000e+00      0.000000  
25%       14.840000      1.450000  3.200000e+01      1.000000  
50%       49.890000      3.240000  6.800000e+01      3.000000  
75%      125.570000      6.890000  1.700000e+02      5.000000  
max      846.270000     46.430000  3.958244e+06      9.000000  
# Use this space to do any additional research
# and familiarize yourself with the data.
​
​
# What three item categories had the most entries?
print(df['category'].value_counts().head(3))
​
category
consumables    23538
furniture      11915
software        8400
Name: count, dtype: int64
# For the category with the most entries,
# which subcategory had the most entries?
most_entries_category = df['category'].value_counts().idxmax()
print(df[df['category'] == most_entries_category]['subcategory'].value_counts().idxmax())
​
bathroom supplies
# Which five clients had the most entries in the data?
top_clients = df['client_id'].value_counts().head(5)
print(top_clients)
​
client_id
33615    220
66037    211
46820    209
38378    207
24741    207
Name: count, dtype: int64
# Store the client ids of those top 5 clients in a list.
top_client_ids = top_clients.index.tolist()
​
# How many total units (the qty column) did the
# client with the most entries order order?
print(df[df['client_id'] == top_client_ids[0]]['qty'].sum())
​
64313
Part 2: Transform the Data
Do we know that this client spent the more money than client 66037? If not, how would we find out? Transform the data using the steps below to prepare it for analysis.

# Create a column that calculates the 
# subtotal for each line using the unit_price
# and the qty
df['line_subtotal'] = df['unit_price'] * df['qty']
​
# Create a column for shipping price.
# Assume a shipping price of $7 per pound
# for orders over 50 pounds and $10 per
# pound for items 50 pounds or under.
df['shipping_price'] = df.apply(lambda row: 7 * row['unit_weight'] if row['unit_weight'] > 50 else 10 * row['unit_weight'], axis=1)
​
# Create a column for the total price
# using the subtotal and the shipping price
# along with a sales tax of 9.25%
df['line_price'] = df['line_subtotal'] + df['shipping_price']
​
# Create a column for the cost
# of each line using unit cost, qty, and
# shipping price (assume the shipping cost
# is exactly what is charged to the client).
df['line_cost'] = df['unit_cost'] * df['qty'] + df['shipping_price']
​
# Create a column for the profit of
# each line using line cost and line price
df['line_profit'] = df['line_price'] - df['line_cost']
​
Part 3: Confirm your work
You have email receipts showing that the total prices for 3 orders. Confirm that your calculations match the receipts. Remember, each order has multiple lines.

Order ID 2742071 had a total price of $152,811.89

Order ID 2173913 had a total price of $162,388.71

Order ID 6128929 had a total price of $923,441.25

# Check your work using the totals above
order_totals = df.groupby('order_id')['line_price'].sum()
Part 4: Summarize and Analyze
Use the new columns with confirmed values to find the following information.

# How much did each of the top 5 clients by quantity
# spend? Check your work from Part 1 for client ids.
top_clients_total_spend = df[df['client_id'].isin(top_client_ids)].groupby('client_id')['line_price'].sum()
print(top_clients_total_spend)
​
​
client_id
24741    70186250.85
33615     5851641.51
38378     8396216.15
46820     7328450.83
66037     8005725.68
Name: line_price, dtype: float64
# Create a summary DataFrame showing the totals for the
# for the top 5 clients with the following information:
# total units purchased, total shipping price,
# total revenue, and total profit. Sort by total profit.
summary_df = pd.DataFrame({
    'client_id': top_clients_total_spend.index,
    'qty': df[df['client_id'].isin(top_client_ids)].groupby('client_id')['qty'].sum(),
    'shipping_price': df[df['client_id'].isin(top_client_ids)].groupby('client_id')['shipping_price'].sum(),
    'line_price': top_clients_total_spend,
    'line_cost': df[df['client_id'].isin(top_client_ids)].groupby('client_id')['line_cost'].sum(),
    'line_profit': df[df['client_id'].isin(top_client_ids)].groupby('client_id')['line_profit'].sum()
})
​
# Format the data and rename the columns
# to names suitable for presentation.
# Currency should be in millions of dollars.
summary_df['line_price'] = summary_df['line_price'] / 1e6  # Convert to millions
summary_df['line_cost'] = summary_df['line_cost'] / 1e6  # Convert to millions
summary_df['line_profit'] = summary_df['line_profit'] / 1e6  # Convert to millions
summary_df.columns = ['Client ID', 'Units', 'Shipping', 'Total Revenue', 'Total Cost', 'Total Profit']
print(summary_df)
           Client ID   Units  Shipping  Total Revenue  Total Cost  \
client_id                                                           
24741          24741  239862    9365.6      70.186251   40.571817   
33615          33615   64313   12609.4       5.851642    4.358938   
38378          38378   73667   11895.0       8.396216    6.217161   
46820          46820   75768   11094.8       7.328451    5.416838   
66037          66037   43018   10017.3       8.005726    5.619348   

           Total Profit  
client_id                
24741         29.614434  
33615          1.492703  
38378          2.179056  
46820          1.911613  
66037          2.386377  
# Sort the updated data by "Total Profit" form highest to lowest
summary_df = summary_df.sort_values(by='Total Profit', ascending=False)
# Sort the updated data by "Total Profit" form highest to lowest
summary_df = summary_df.sort_values(by='Total Profit', ascending=False)
​
​