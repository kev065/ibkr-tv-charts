import asyncio
import pandas as pd
from lightweight_charts import Chart

from ib_insync import *
import nest_asyncio
nest_asyncio.apply()


def get_data(symbol, timeframe):
    ib = IB()
    ib.connect('127.0.0.1', 7497, clientId=13)

    contract = Stock(symbol, 'SMART', 'USD')
    bars = ib.reqHistoricalData(
            contract,
            endDateTime='',
            durationStr='90 D',
            barSizeSetting=timeframe,
            whatToShow='TRADES',
            useRTH=True,
            formatDate=1)
    ib.disconnect()
    df = util.df(bars)

    return df

class API:
    def __init__(self):
        self.chart = None

    async def on_search(self, symbol):  # Called when the user searches.
        timeframe = self.chart.topbar['timeframe'].value
        data = get_data(symbol, timeframe)

        self.chart.set(data)  # sets data for the Chart or SubChart in question.
        self.chart.topbar['symbol'].set(symbol)

    async def on_timeframe(self):  # Called when the user changes the timeframe.
        timeframe = self.chart.topbar['timeframe'].value
        symbol = self.chart.topbar['symbol'].value
        data = get_data(symbol, timeframe)

        self.chart.set(data)

    
async def main():
    api = API()
    chart = Chart(api=api, topbar=True, searchbox=True)
    
    symbol = 'AAPL'
    timeframe = '15 mins'
    df = get_data(symbol, timeframe)

    chart.topbar.textbox('symbol', symbol)
    chart.topbar.switcher('timeframe', api.on_timeframe, '15 mins', '1 hour', '1 day', default='15 mins')
    
    chart.set(df)

    await chart.show_async(block=True)


if __name__ == '__main__':
    asyncio.run(main())