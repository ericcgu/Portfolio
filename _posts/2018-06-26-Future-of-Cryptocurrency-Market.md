---
title: "Future of Cryptocurrency?"
date: 2018-06-26
header:
    image: "images/water.png"
tags: [data-visualization]
---


<iframe width="900" height="800" frameborder="0" scrolling="no" src="//plot.ly/~ericgu/3.embed"></iframe>


Before November 2017, Litecoin (LTC), a fork of Bitcoin (BTC), followed a close relationship with Bitcoin. Within a short period of Litecoin's price action diverging with Bitcoin, the market began heating up, ultimately approaching the all time high in December. 

On December 10, 2017, Litecoin and Bitcoin diverged back to its normal relationship and the download trend ensued.

As you can see as we move toward the second half of 2018, Litecoin is again beginning to diverge with Bitcoin as the price of Bitcoin falls at an accelerated rate.  

Will we see another market turn as the two coins diverge or will the market continue to fall?

This quick visualization was built with Quandl real time data leveraging Bitfinix's API. 


```python
import configparser
import quandl
import plotly.graph_objs as go 
import plotly.offline as pyo 

parser = configparser.ConfigParser()
parser.read("data/API.ini")
SECRET_KEY = parser.get('API','secret_key')


quandl.ApiConfig.api_key = SECRET_KEY
ltc_usd_data = quandl.get('BITFINEX/LTCUSD', start_date='2017-08-01', end_date='2018-07-30')
btc_usd_data = quandl.get('BITFINEX/BTCUSD', start_date='2017-08-01', end_date='2018-07-30')
print(ltc_usd_data.head(5))


line1 = go.Scatter(x = ltc_usd_data.index,
                   y = ltc_usd_data["Mid"],
                   mode = 'lines',
                   name = 'LTC/USD' )
line2 = go.Scatter(x = btc_usd_data.index,
                   y = btc_usd_data["Mid"]/100,
                   mode = 'lines',
                   name = 'BTC/USD OVER 100' )                  
                                  
              
data = [line1, line2]
layout = go.Layout(title = 'Cryptocurrency Market: LTC/USD vs BTC/USD OVER 100', 
                   xaxis = {'title': 'Time (months)'},
                   yaxis = {'title': 'Price'},
                   hovermode = 'closest')

figure = go.Figure(data = data, layout = layout)

pyo.plot(figure, filename = 'crypto/crypto.html')         

```
