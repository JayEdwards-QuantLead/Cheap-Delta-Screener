import requests
import json

# Set up the IEX Cloud API client
IEX_CLOUD_API_TOKEN = "your_iex_cloud_api_token"
IEX_CLOUD_API_URL = "https://cloud.iexapis.com/stable"

def get_stock_price(ticker):
  url = f"{IEX_CLOUD_API_URL}/stock/{ticker}/price"
  query_params = {
      "token": IEX_CLOUD_API_TOKEN
  }
  response = requests.request("GET", url, params=query_params)
  if response.status_code != 200:
    raise Exception(f"Failed to get stock price for {ticker}: {response.text}")
  return float(response.text)

def get_balance_sheet(ticker):
  url = f"{IEX_CLOUD_API_URL}/stock/{ticker}/balance-sheet"
  query_params = {
      "token": IEX_CLOUD_API_TOKEN,
      "period": "annual"
  }
  response = requests.request("GET", url, params=query_params)
  if response.status_code != 200:
    raise Exception(f"Failed to get balance sheet for {ticker}: {response.text}")
  return response.json()

def get_option_chain(ticker):
  url = f"{IEX_CLOUD_API_URL}/stock/{ticker}/options"
  query_params = {
      "token": IEX_CLOUD_API_TOKEN
  }
  response = requests.request("GET", url, params=query_params)
  if response.status_code != 200:
    raise Exception(f"Failed to get option chain for {ticker}: {response.text}")
  return response.json()

def is_leap(expiration_date):
  # Check if the expiration date is more than a year away
  return (datetime.datetime.strptime(expiration_date, "%Y-%m-%d") - datetime.datetime.now()).days > 365

def find_underpriced_leap_options(ticker):
  # Get the balance sheet for the stock
  balance_sheet = get_balance_sheet(ticker)
  # Calculate the liquidation value of the company
  liquidation_value = balance_sheet["totalAssets"]["raw"] - balance_sheet["totalLiabilities"]["raw"]
  # Get the current price of the stock
  stock_price = get_stock_price(ticker)
  # Check if the stock is trading below liquidation value
  if stock_price < liquidation_value:
    # Get the option chain for the stock
    option_chain = get_option_chain(ticker)
    # Filter the option chain to only include LEAP options
    leap_options = [o for o in option_chain if is_leap(o["expirationDate"])]
    # Sort the options by their price to delta ratio (ascending
