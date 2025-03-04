import time
import pandas as pd
import numpy as np
from binance.client import Client
from binance.enums import *

# Binance API Keys
api_key = 'your_api_key'
api_secret = 'your_api_secret'

# Set up Binance client
client = Client(api_key, api_secret)

# Define the symbol (for example: BTCUSDT for Bitcoin to USDT)
symbol = 'BTCUSDT'
quantity = 0.001  # Amount of BTC to buy/sell

# Function to fetch historical data
def get_historical_data(symbol, interval, lookback):
    bars = client.get_historical_klines(symbol, interval, lookback)
    df = pd.DataFrame(bars)
    df = df.iloc[:, :6]
    df.columns = ['timestamp', 'open', 'high', 'low', 'close', 'volume']
    df['timestamp'] = pd.to_datetime(df['timestamp'], unit='ms')
    df.set_index('timestamp', inplace=True)
    df = df.astype(float)
    return df

# Simple moving average strategy (SMA)
def moving_average_strategy(df):
    short_window = 50
    long_window = 200

    # Calculate moving averages
    df['short_sma'] = df['close'].rolling(window=short_window, min_periods=1).mean()
    df['long_sma'] = df['close'].rolling(window=long_window, min_periods=1).mean()

    # Generate buy/sell signals
    if df['short_sma'].iloc[-1] > df['long_sma'].iloc[-1]:
        return 'BUY'
    elif df['short_sma'].iloc[-1] < df['long_sma'].iloc[-1]:
        return 'SELL'
    else:
        return 'HOLD'

# Place buy order
def place_buy_order(symbol, quantity):
    order = client.order_market_buy(
        symbol=symbol,
        quantity=quantity
    )
    print('Buy order executed:', order)

# Place sell order
def place_sell_order(symbol, quantity):
    order = client.order_market_sell(
        symbol=symbol,
        quantity=quantity
    )
    print('Sell order executed:', order)

# Main loop to execute the bot
def main():
    while True:
        df = get_historical_data(symbol, Client.KLINE_INTERVAL_1MINUTE, '1 day ago UTC')
        signal = moving_average_strategy(df)

        if signal == 'BUY':
            place_buy_order(symbol, quantity)
        elif signal == 'SELL':
            place_sell_order(symbol, quantity)

        time.sleep(60)  # Wait for the next minute to fetch new data

if __name__ == '__main__':
    main()<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Simple Bot</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="chat-container">
    <div class="chat-box" id="chat-box">
      <p>Bot: Hello! How can I help you today?</p>
    </div>
    <input type="text" id="user-input" placeholder="Type a message..." />
    <button onclick="sendMessage()">Send</button>
  </div>

  <script src="script.js"></# https-ifat-future-bot.netlify.app-
