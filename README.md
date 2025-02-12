# Data_Scrapping

#Importing all the tools and modules im gonna need for this project

import requests
from bs4 import BeautifulSoup
import pandas as pd

# Making a GET request
url = 'https://en.wikipedia.org/wiki/List_of_largest_companies_in_South_Africa'
response = requests.get(url)

# Check if the request was successful
if response.status_code == 200:
    soup = BeautifulSoup(response.content, 'html.parser')

    # Find the table (first table in the content)
    table = soup.find('table', {'class': 'wikitable'})

    # Get the table headers (column names)
    sa_title = table.find_all('th')
    sa_table_titles = [title.text.strip() for title in sa_title]

   #Create an empty DataFrame with the table titles as column headers
    df = pd.DataFrame(columns=sa_table_titles)

    # Get the rows of the table (excluding the header row)
    column_data = table.find_all('tr')[1:]  # Skipping the first row (header row)

    # Loop through each row and collect the data
    for row in column_data[0:]:
        row_data = row.find_all('td')
        individual_row_data = [data.text.strip() for data in row_data]


       # Check if the row has the same number of columns as the header
        if len(individual_row_data) == len(sa_table_titles):
            df.loc[len(df)] = individual_row_data  # Append the row to the DataFrame
        else:
            # If the row is malformed, print a message and skip it
            print(f"Skipping row with {len(individual_row_data)} columns (expected {len(sa_table_titles)}): {individual_row_data}")


    # Remove the first column by index (0)
    #df = df.drop(df.columns[0], axis=1)

    # Save the DataFrame to a CSV file
    #df.to_csv('output.csv', index=False)

    # Print confirmation message
    #print("Data saved to 'output.csv'.")

    #print dataframe
    print(df)
else:
    print(f"Failed to retrieve the page. Status code: {response.status_code}")


