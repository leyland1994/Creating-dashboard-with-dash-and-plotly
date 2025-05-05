#!/usr/bin/env python
# coding: utf-8

import dash
from dash import dcc, html
from dash.dependencies import Input, Output
import pandas as pd
import plotly.express as px

# Load the data using pandas
data = pd.read_csv('https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DV0101EN-SkillsNetwork/Data%20Files/historical_automobile_sales.csv')

# Initialize the Dash app
app = dash.Dash(__name__)

# Set the app layout
year_list = [i for i in range(1980, 2024, 1)]

app.layout = html.Div([
    html.H1(
        "Automobile Sales Statistics Dashboard",
        style={'textAlign': 'center', 'color': '#503D36', 'font-size': 24}
    ),
    html.Div([
        html.Label("Select Statistics:"),
        dcc.Dropdown(
            id='dropdown-statistics',
            options=[
                {'label': 'Yearly Statistics', 'value': 'Yearly Statistics'},
                {'label': 'Recession Period Statistics', 'value': 'Recession Period Statistics'}
            ],
            value='Recession Period Statistics',
            placeholder='Select a report type'
        )
    ]),
    html.Br(),
    html.Div([
        html.Label("Select Year:"),
        dcc.Dropdown(
            id='select-year',
            options=[{'label': i, 'value': i} for i in year_list],
            placeholder='Select a year'
        )
    ]),
    html.Br(),
    html.Div(id='output-container', className='chart-grid', style={'display': 'flex', 'flexDirection': 'column'})
])

# Callback to enable/disable year dropdown
@app.callback(
    Output(component_id='select-year', component_property='disabled'),
    Input(component_id='dropdown-statistics',component_property='value'))

def update_input_container(selected_statistics):
    if selected_statistics == 'Yearly Statistics':
        return False
    else:
        return True

# Callback to update charts
@app.callback(
    Output(component_id='output-container', component_property='children'),
    [Input(component_id='dropdown-statistics', component_property='value'), Input(component_id='select-year', component_property='value')])

def update_output_container(selected_statistics, input_year):
    if selected_statistics == 'Recession Period Statistics':
        recession_data = data[data['Recession'] == 1]

        # Plot 1: Average Automobile Sales over Recession Period
        yearly_rec = recession_data.groupby('Year')['Automobile_Sales'].mean().reset_index()
        R_chart1 = dcc.Graph(
            figure=px.line(yearly_rec, x='Year', y='Automobile_Sales',
                           title="Average Automobile Sales Over Recession Period")
        )

        # Plot 2: Average number of vehicles sold by Vehicle Type
        average_sales = recession_data.groupby('Vehicle_Type')['Automobile_Sales'].mean().reset_index()
        R_chart2 = dcc.Graph(
            figure=px.bar(average_sales, x='Vehicle_Type', y='Automobile_Sales',
                          title="Average Number of Automobile Sales by Vehicle Type")
        )

        # Plot 3: Total Advertising Expenditure by Vehicle Type (Pie Chart)
        exp_rec = recession_data.groupby('Vehicle_Type')['Advertising_Expenditure'].sum().reset_index()
        R_chart3 = dcc.Graph(
            figure=px.pie(exp_rec, values='Advertising_Expenditure', names='Vehicle_Type',
                          title="Advertising Expenditure Share by Vehicle Type During Recession")
        )

        # Plot 4: Effect of Unemployment Rate on Vehicle Type and Sales
        unemp_data = recession_data.groupby('Vehicle_Type')['Automobile_Sales'].mean().reset_index()
        R_chart4 = dcc.Graph(
            figure=px.bar(unemp_data, x='Vehicle_Type', y='Automobile_Sales',
                          title="Effect of Unemployment on Vehicle Type and Sales")
        )

        return [
            html.Div([R_chart1, R_chart2], style={'display': 'flex', 'flexWrap': 'wrap'}),
            html.Div([R_chart3, R_chart4], style={'display': 'flex', 'flexWrap': 'wrap'})
        ]

    elif selected_statistics == 'Yearly Statistics' and input_year:
        yearly_data = data[data['Year'] == input_year]

        # Plot 1: Automobile Sales for all Years (Overall)
        yas = data.groupby('Year')['Automobile_Sales'].mean().reset_index()
        Y_chart1 = dcc.Graph(
            figure=px.line(yas, x='Year', y='Automobile_Sales',
                           title="Overall Average Automobile Sales Per Year")
        )

        # Plot 2: Total Monthly Automobile Sales for all years combined
        mas = data.groupby('Month')['Automobile_Sales'].sum().reset_index()
        Y_chart2 = dcc.Graph(
            figure=px.line(mas, x='Month', y='Automobile_Sales',
                           title="Total Monthly Automobile Sales")
        )

        # Plot 3: Average Vehicles Sold by Vehicle Type in selected year
        avr_vdata = yearly_data.groupby('Vehicle_Type')['Automobile_Sales'].mean().reset_index()
        Y_chart3 = dcc.Graph(
            figure=px.bar(avr_vdata, x='Vehicle_Type', y='Automobile_Sales',
                          title=f"Average Vehicles Sold by Vehicle Type in {input_year}")
        )

        # Plot 4: Total Advertising Expenditure by Vehicle Type in selected year
        exp_data = yearly_data.groupby('Vehicle_Type')['Advertising_Expenditure'].sum().reset_index()
        Y_chart4 = dcc.Graph(
            figure=px.pie(exp_data, values='Advertising_Expenditure', names='Vehicle_Type',
                          title=f"Total Advertising Expenditure by Vehicle Type in {input_year}")
        )

        return [
            html.Div([Y_chart1, Y_chart2], style={'display': 'flex', 'flexWrap': 'wrap'}),
            html.Div([Y_chart3, Y_chart4], style={'display': 'flex', 'flexWrap': 'wrap'})
        ]

    else:
        return html.Div([
            html.H4("Please select a statistic and a valid year.")
        ])

# Run the app
if __name__ == '__main__':
    app.run(port=8090)
