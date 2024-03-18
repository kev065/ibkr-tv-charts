import time
from ib_insync import *
from lightweight_charts import Chart


if __name__ == '__main__':
    # connect to Interactive Brokers
    ib = IB()
    ib.connect('127.0.0.1', 7497, clientId=1)

    # request minute bars for a stock
    stock = Stock('AAPL', 'SMART', 'USD')

    bars = ib.reqHistoricalData(
        stock, endDateTime='', durationStr='3000 S',
        barSizeSetting='1 min', whatToShow='MIDPOINT', useRTH=True)

    # convert bars to a pandas dataframe
    df = util.df(bars)

    # show the initial chart with the minute bars
    chart = Chart(volume_enabled=False)
    chart.set(df)
    chart.show()

    # request market data and update the chart with real-time data
    market_data = ib.reqMktData(stock, '233', False, False)

    def onPendingTicker(ticker):
        print("pending ticker event received")
        for tick in ticker:
            ticks = util.df(tick.ticks)
            if ticks is not None:
                last_price  = ticks[ticks['tickType'] == 4]
                if not last_price.empty:
                    print(last_price)
                    chart.update_from_tick(last_price.squeeze())

    ib.pendingTickersEvent += onPendingTicker

    ib.run()