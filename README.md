# Classic Stock Charts in Python


Create classic technical analysis stock charts in Python with minimum code.
The library is mainly built around [matplotlib](https://github.com/matplotlib/matplotlib). 
Charts can be defined using a declarative interface,
based on a set of drawing primitives like `Candleststicks`, `Volume`, `Peaks`
and technical indicators like `SMA`, `EMA`, `RSI`, `ROC`, `MACD`, etc ...
If you have [ta-lib](https://github.com/mrjbq7/ta-lib) installed you can also
use any of its abstract functions as indicators.


> **Warning**
This is work in progress. For a related project with a mature api you may want to look into
[mplfinance](https://pypi.org/project/mplfinance/).


![Showcase Chart](/output/showcase.svg "Showcase")


## Typical Usage

```python
from mplchart.chart import Chart
from mplchart.helper import get_prices
from mplchart.primitives import Candlesticks, Volume
from mplchart.indicators import ROC, SMA, EMA, RSI, MACD

ticker = 'AAPL'
freq = 'daily'
prices = get_prices(ticker, freq=freq)

max_bars = 250

indicators = [
    Candlesticks(), SMA(50), SMA(200), Volume(),
    RSI(),
    MACD(),
]

chart = Chart(title=ticker, max_bars=max_bars)
chart.plot(prices, indicators)
```

See example notebook [mplchart-usage.ipynb](/examples/mplchart-usage.ipynb) 


## Conventions

Price data is expected to be predented as a pandas DataFrame
with columns `open`, `high`, `low`, `close` `volume`
and a timestamp index named `date`, all in **lower case**!

For testing purposes you can use the `helper` module
which can fetch sample prices in the proper format via
[yfinance](https://github.com/ranaroussi/yfinance).

```python
from mplchart.helper import get_prices

ticker = 'AAPL'
freq = 'daily'
prices = get_prices(ticker, freq=freq)
```

See example notebook [mplchart-prices.ipynb](/examples/mplchart-prices.ipynb) 


## Drawing Primitives

The library contains drawing primitives that can be used as an indicator in the plot api.
All primitives are classes that must be instantiated as objects before being used in the plot api.

```python
from mplchart.primitives import Candlesticks

indicators = [Candlesticks()]
chart = Chart(title=title, max_bars=max_bars)
chart.plot(prices, indicators)
```

The main drawing primitives are :
- `Candlesticks` for candlesticks plots
- `OHLC` for open, high, low, close bar plots
- `Price` for price line plots
- `Volume` for volume bar plots
- `Peaks` to plot peaks and valleys
- `SameAxes` to force plot on the same axes
- `NewAxes` to force plot on a new axes

See example notebook [mplchart-primitives.ipynb](/examples/mplchart-primitives.ipynb) 

## Builtin Indicators

The libary contains some basic technical analysis indicators implemented in pandas/numpy.
Indicators are classes that must be instantiated before being used in the plot api.

Some of the indicators included are:

- `SMA` Simple Moving Average
- `EMA` Exponential Moving Average
- `ROC` Rate of Change
- `RSI` Relative Strength Index
- `MACD` Moving Average Convergence Divergence
- `PPO` Price Percentage Oscillator 
- `SLOPE` Slope (linear regression with time)
- `BBANDS` Bolling Bands

See example notebook [mplchart-builtins.ipynb](/examples/mplchart-builtins.ipynb) 

## Ta-lib Abstract Functions

If you have [ta-lib](https://github.com/mrjbq7/ta-lib) installed you can use its abstract functions as indicators.
The indicators are created by calling `abstract.Function` with the name of the indicator and its parameters.

```python
from talib.abstract import Function

indicators = [
    Candlesticks(),
    Function('SMA', 50),
    Function('SMA', 200),
    Function('RSI'),
    Function('MACD'),
]
```
 
See example notebook [mplchart-abstract.ipynb](/examples/mplchart-talib.ipynb) 


## Custom Indicators

Any callable that takes a prices data frame and returns a series as result can be used as indicator.
A function can be used as an indicator but you can also implement an indicator as a callable dataclass.

```python
from dataclasses import dataclass

from mplchart.library import get_series, calc_ema

@dataclass
class DEMA:
    """ Double Exponential Moving Average """
    period: int = 20

    same_scale = True
    # same_scale is an optional class attribute that indicates
    # the indicator should be plot on the same axes by default

    def __call__(self, prices):
        series = get_series(prices)
        ema1 = calc_ema(series, self.period)
        ema2 = calc_ema(ema1, self.period)
        return 2 * ema1 - ema2

```

See example notebook [mplchart-custom.ipynb](/examples/mplchart-custom.ipynb) 


## Examples

You can find example notebooks and scripts in the [examples](/examples/) folder. 

## Installation

You can install the current version of this package with pip

```console
python3 -mpip install git+ssh://git@github.com/furechan/mplchart.git
```

## Requirements:

- python >= 3.8
- matplotlib
- pandas
- numpy
- yfinance


## Related Projects & Resources
- [stockcharts.com](https://stockcharts.com/) Classic stock charts and technical analysis reference
- [mplfinance](https://pypi.org/project/mplfinance/) Matplotlib utilities for the visualization,
and visual analysis, of financial data
- [matplotlib](https://github.com/matplotlib/matplotlib) Matplotlib: plotting with Python
- [yfinance](https://github.com/ranaroussi/yfinance) Download market data from Yahoo! Finance's API
- [ta-lib](https://github.com/mrjbq7/ta-lib) Python wrapper for TA-Lib
- [pandas](https://github.com/pandas-dev/pandas) Flexible and powerful data analysis / manipulation library
for Python, providing labeled data structures similar to R data.frame objects,
statistical functions, and much more
- [numpy](https://github.com/numpy/numpy) The fundamental package for scientific computing with Python
