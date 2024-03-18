import pandas as pd
from lightweight_charts import Chart
from ib_insync import *

if __name__ == '__main__':
    ib = IB()
    ib.connect('127.0.0.1', 7497, clientId=13)
    contract = Stock('AAPL', 'SMART', 'USD')

    bars = ib.reqHistoricalData(
            contract,
            endDateTime='',
            durationStr='300 S',
            barSizeSetting='5 secs',
            whatToShow='TRADES',
            useRTH=True,
            formatDate=1)
            
    df = util.df(bars)

    chart = Chart(volume_enabled=False)
    chart.set(df)
    chart.show()

    def orderFilled(trade, fill):
        print("order has been filled")
        print(trade)
        print(fill)
        print(dir(fill))
        chart.marker(text=f"order filled at {fill.execution.avgPrice}")

    def onBarUpdate(bars, hasNewBar):
        last_bar = bars[-1]

        last_bar_series = pd.Series({
            'time': last_bar.time,
            'open': last_bar.open_,
            'high': last_bar.high,
            'low': last_bar.low,
            'close': last_bar.close,
            'volume': last_bar.volume
        })

        chart.update(last_bar_series)

        if len(bars) >= 3:
            if bars[-1].close > bars[-1].open_ and \
                bars[-2].close > bars[-2].open_ and \
                bars[-3].close > bars[-3].open_:
                
                print("3 green bars, let's buy!")

                # buy 10 shares and call orderFilled() when it fills
                order = MarketOrder('BUY', 1)
                trade = ib.placeOrder(contract, order)
                trade.fillEvent += orderFilled

    bars = ib.reqRealTimeBars(contract, 5, 'MIDPOINT', False)
    bars.updateEvent += onBarUpdate

    ib.run()