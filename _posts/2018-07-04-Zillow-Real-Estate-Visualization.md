---
title: "Zillow Real Estate Visualization"
date: 2018-07-04
classes: wide
header:
    image: "images/water.png"
tags: [data-visualization]
---



An interactive and reactive dashboard using live Real Estate Metrics from Quandl/Zillow API to compare real estate metrics between zipcode pairs for the last 5 years.

[Live Demo Link](https://tinyurl.com/zillowanalytics)

![alt text](https://github.com/ericcgu/WhereShouldIMove/blob/master/Screen%20Shot%202018-07-04%20at%208.24.09%20PM.png?raw=true)


```python
import quandl
import config
import datetime 
from dateutil.relativedelta import relativedelta

class Zillow:
   
    def __init__(self, code, code_segment, metric, time_span):
        self.code = code
        self.code_segment = code_segment
        self.metric = metric
        self.time_span = time_span
        self.end_date = datetime.datetime.today().strftime('%Y-%m-%d')
        self.start_date = (datetime.datetime.now() - relativedelta(years=self.time_span)).strftime('%Y-%m-%d')
        self.data = self.getData() 
    
    def __str__(self):
        data_string = self.data[:25]
        return str(data_string)

    def getData(self):
        quandl.ApiConfig.api_key = config.api_key
        request = 'ZILLOW/{0}{1}_{2}'.format(self.code_segment, self.code, self.metric)
        return quandl.get(request, start_date=self.start_date, end_date=self.end_date)

if __name__ == "__main__":
    pass

```


```python

import dash
import dash_core_components as dcc
import dash_html_components as html
from dash.dependencies import Input, Output, State
import zillow as z


app = dash.Dash()
app.css.append_css({
    'external_url': (
        'https://cdn.rawgit.com/chriddyp/0247653a7c52feb4c48437e1c1837f75'
        '/raw/a68333b876edaf62df2efa7bac0e9b3613258851/dash.css'
    )
})

app.layout = html.Div(children=[
    html.Div([
        html.Div([
             html.Img(src='https://cdn.geekwire.com/wp-content/uploads/2015/02/Zillow-box-logo.png', height='100', width='100')
        ], className="two columns"),
        html.Div([
             html.H1('Zillow Real Estate Research', style={'text-align': 'center'})
        ], className="eight columns"),
    ], className="row"),
    html.H5('Live Metrics Per Square Foot Over Time by U.S. Zip Code', style={'text-align': 'center'}),
    html.Br(), html.Br(),
      html.Div([
        html.Div([
              html.Label('First Zip Code'),
              dcc.Input(id='zipcode1', value='', type='text', style={'text-align': 'center'}),
        ], className="three columns"),
         html.Div([
              html.Label('Second Zip Code'),
              dcc.Input(id='zipcode2', value='', type='text', style={'text-align': 'center'}),     
        ], className="three columns"),
        html.Div([
               html.Label('Metric'),
               dcc.Dropdown(
                    options=[
                        {'label': 'Rental Price (Median) Per Square Foot', 'value': 'MRPFAH'},
                        {'label': 'Sales Price (Median) Per Square Foot ', 'value': 'MSPFAH'},
                        ],
                value='MRPFAH', id='metric'),
      
        ], className="four columns"),
    ], className="row", style={'text-align': 'center'}),
    html.Br(), html.Br(),
    html.Div([
    html.Button(id='submit-button',
                n_clicks=0,
                children='Compare Visualisation',
                style={'fontSize':20, 'background-color': '#0069E0', 'color': 'white'},
                ),
        ], className="row", style={'text-align': 'center'}),            
    html.Div(id='output-graph'),
    html.Br(),html.Br(),
      html.Div([
    dcc.Link(html.A('Made by Eric Gu', href='http://clouddata.netlify.com/'), href='http://clouddata.netlify.com/',
              style={'fontSize':24} )
            ], className="row", style={'text-align': 'center'}),          
])

@app.callback(
    Output(component_id='output-graph', component_property='children'),
    [Input('submit-button', 'n_clicks')],
    [State('zipcode1', 'value'),
              State('zipcode2', 'value'),
               State('metric', 'value')])


def update_graph(n_clicks, zipcode1, zipcode2, metric):
    data_one = z.Zillow(zipcode1, 'Z', metric, 5).data
    data_two = z.Zillow(zipcode2, 'Z', metric, 5).data

    return dcc.Graph(
        id='example-graph',
        figure={
            'data': [
                {'x': data_one.index, 'y': data_one['Value'], 'type': 'line', 'name': zipcode1},
                {'x': data_two.index, 'y': data_two['Value'], 'type': 'line', 'name': zipcode2},
            ],
            'layout': {
                'xaxis' : dict(title = 'Time (Years)'),
                'yaxis' : dict(title = 'Cost ($ / Foot)')

            }
        }
    )  
 


if __name__ == '__main__':
    app.run_server()


```