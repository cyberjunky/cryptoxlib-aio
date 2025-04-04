# cryptoxlib-aio 3.8.1

![](https://img.shields.io/badge/python-3.6-blue.svg) ![](https://img.shields.io/badge/python-3.7-blue.svg) ![](https://img.shields.io/badge/python-3.8-blue.svg)

`cryptoxlib-aio` is a compact Python library providing access to REST and WEBSOCKET API of selected crypto exchanges.

`cryptoxlib-aio` is designed as an asynchronous library utilizing modern features of Python and those of supporting asynchronous libraries (mainly [aiohttp](https://aiohttp.readthedocs.io/en/stable/)).

---

### What's new in version 3.8.1

- new price tickers added to `bitvavo`

For the full history of changes see [CHANGELOG](https://github.com/nardew/cryptoxlib-aio/blob/master/CHANGELOG.md).

---

### Main mission
Today there are numerous existing libraries targeting similar audience as `cryptoxlib-aio`. In order to achieve the broadest coverage of exchanges the supported API is often limited, the most apparent example being lack of _websockets_.  

The mission of `cryptoxlib-aio` is to:

- serve as a single entry point for various crypto exchanges
- provide full REST API as well as full implementation of exchange's _**websockets**_. For free and without subscription plans.

Full support of websockets is the corner stone of `cryptoxlib-aio` which attempts to set `cryptoxlib-aio` apart from existing solutions which either do not provide websocket support or do not provide it for free.

You will also find out that our subject of interest are often exchanges not enjoying a seat in the mainstream crypto libraries and this is a way for them to reach developers community.

Disclaimer: By no means we are suggesting that existing libraries are inferior to `cryptoxlib-aio`, they just appeal to different endusers.

### Features
- access to REST API as well as full support of websockets for selected exchanges (given websockets are provided by the exchange)
- automatic connection management (reconnecting after remote termination, ...)
- bundling of channels 
- lean architecture making it straightforward to implement API of a new exchange
- fully asynchronous design aiming for the best performance

### List of supported exchanges

As mentioned earlier, all exchanges listed below include full support for websockets.

| | Name | Docs |
| --- | --- | --- |
| ![aax](https://raw.githubusercontent.com/nardew/cryptoxlib-aio/master/images/aax.png) | AAX | [API](https://www.aax.com/apidoc/index.html#introduction) |
| ![bibox](https://user-images.githubusercontent.com/51840849/77257418-3262b000-6c85-11ea-8fb8-20bdf20b3592.jpg) | Bibox | [API](https://biboxcom.github.io/en/restful_intro.html#t0) |
| ![bibox_europe](https://raw.githubusercontent.com/nardew/cryptoxlib-aio/master/images/bibox_europe.png) | BiboxEurope | [API](https://github.com/BiboxEurope/API_Docs_en) |
| ![binance](https://user-images.githubusercontent.com/1294454/29604020-d5483cdc-87ee-11e7-94c7-d1a8d9169293.jpg) | Binance |[API](https://binance-docs.github.io/apidocs/spot/en/#change-log) | 
| ![bitforex](https://user-images.githubusercontent.com/1294454/44310033-69e9e600-a3d8-11e8-873d-54d74d1bc4e4.jpg) | Bitforex | [API](https://github.com/githubdev2020/API_Doc_en/wiki) |
| ![bitpanda](https://raw.githubusercontent.com/nardew/cryptoxlib-aio/master/images/bitpanda.png) | Bitpanda Pro | [API](https://developers.bitpanda.com/exchange/) |
| ![bitvavo](https://raw.githubusercontent.com/nardew/cryptoxlib-aio/master/images/bitvavo.png) | Bitvavo | [API](https://docs.bitvavo.com/#section/Introduction) |
| ![btse](https://raw.githubusercontent.com/nardew/cryptoxlib-aio/master/images/btse.png) | BTSE | [API](https://www.btse.com/apiexplorer/spot/#btse-spot-api) |
| ![eterbase](https://user-images.githubusercontent.com/1294454/82067900-faeb0f80-96d9-11ea-9f22-0071cfcb9871.jpg) | Eterbase | [API](https://developers.eterbase.exchange) |
| ![hitbtc](https://user-images.githubusercontent.com/1294454/27766555-8eaec20e-5edc-11e7-9c5b-6dc69fc42f5e.jpg) | HitBTC | [API](https://api.hitbtc.com) |
| ![liquid](https://user-images.githubusercontent.com/1294454/45798859-1a872600-bcb4-11e8-8746-69291ce87b04.jpg) | Liquid | [API](https://developers.liquid.com) |

Unlike REST API which is rather uniform across crypto exchanges websockets are often very exchange-specific and hence very time consuming to implement (which is the reason why they are not offered so broadly). Therefore `cryptoxlib-aio` comes at the cost of the number of exchanges it covers. This is in line with our ideology quality over quantity.

The list of supported exchanges will grow without any specific pattern, usually driven by personal needs. If there is a high demand for a new exchange/feature to be added, there is a high chance for it to happen (but not guranteed). For business related inquiries concerning customized changes tailored for the client please reach out via e-mail mentioned in the contacts.

### Installation
```bash
pip install cryptoxlib-aio
```
In case you want to install the latest version from the repo, use
```bash
pip install git+https://github.com/nardew/cryptoxlib-aio.git@master
```

### Examples
##### BITPANDA
```python
bitpanda = CryptoXLib.create_bitpanda_client(api_key)

print("Account balance:")
await bitpanda.get_account_balances()

print("Order book:")
await bitpanda.get_order_book(Pair("BTC", "EUR"))

print("Create limit order:")
await bitpanda.create_limit_order(Pair("BTC", "EUR"), OrderSide.BUY, "10000", "1")

# Create first bundle of subscriptions
bitpanda.compose_subscriptions([
    AccountSubscription(),
    PricesSubscription([Pair("BTC", "EUR")]),
    OrderbookSubscription([Pair("BTC", "EUR")], "50", callbacks = [order_book_update]),
    CandlesticksSubscription([CandlesticksSubscriptionParams(Pair("BTC", "EUR"), TimeUnit.MINUTES, 1)]),
    MarketTickerSubscription([Pair("BTC", "EUR")])
])

# Bundle another subscriptions into a separate websocket
bitpanda.compose_subscriptions([
    OrderbookSubscription([Pair("ETH", "EUR")], "3", callbacks = [order_book_update]),
])

# Execute all websockets asynchronously
await bitpanda.start_websockets()
```

##### BITFOREX
```python
bitforex = CryptoXLib.create_bitforex_client(api_key, sec_key)

print("Order book:")
await bitforex.get_order_book(pair = Pair('ETH', 'BTC'), depth = "1")

print("Create order:")
await bitforex.create_order(Pair("ETH", "BTC"), side = enums.OrderSide.SELL, quantity = "1", price = "1")

# First bundle of subscriptions
bitforex.compose_subscriptions([
    OrderBookSubscription(pair = Pair('ETH', 'BTC'), depth = "0", callbacks = [order_book_update]),
    TradeSubscription(pair = Pair('ETH', 'BTC'), size = "2", callbacks = [trade_update]),
])

# Another bundle of subscriptions
bitforex.compose_subscriptions([
    TickerSubscription(pair = Pair('BTC', 'USDT'), size = "2", interval = enums.CandelstickInterval.I_1MIN, callbacks = [ticker_update]),
    Ticker24hSubscription(pair = Pair('BTC', 'USDT'), callbacks = [ticker24_update])
])

# Execute all websockets asynchronously
await bitforex.start_websockets()
```

Examples for every exchange can be found in the folder `examples`.

### Contact

- to report issues, bugs, docu corrections or to propose new features use preferably Github Issues
- for topics requiring more personal approach feel free to send an e-mail to <img src="http://safemail.justlikeed.net/e/92d5165877de84f44e7731e4a1b60ba1.png" border="0" align="absbottom">

### Support

If you like the library and you feel like you want to support its further development, enhancements and bugfixing, then it will be of great help and most appreciated if you:
- file bugs, proposals, pull requests, ...
- spread the word
- donate an arbitrary tip
  * `BTC`: `3GJPT6H6WeuTWR2KwDSEN5qyJq95LEErzf`
  * `ETH`: `0xC7d8673Ee1B01f6F10e40aA416a1b0A746eaBe68`
