# Cryptocurrency-Algorithmic-Trading
Here, I present a cryptocurrency project that I created, which can be used for algorithmic trading. 

## Introduction
Algorithmic trading involves using automated, pre-programmed conditions to execute trades in financial markets. These algorithms analyse market data, identify trading opportunities, and make decisions based on defined criteria, such as price movements, cumulative returns over a specified time period, volume of trades, etc. By leveraging programming languages, one can automate trading strategies, backtest the performance of a strategy using historical data, and execute trades with speed while eliminating emotion-driven decisions.

## Background
The project was created to first test and optimise trading strategies before proceeding to trade with real money. The goal was to identify the best-performing cryptocurrencies on Binance and execute trades based on predefined buy and sell conditions. The motivation originated from the desire to explore the role of data science and automation in modern-day trading and to obtain some profits.

## Breakdown
In total, there are several files that must be executed one after another. Scripts that need to be executed simultaneously are labeled with the following symbol *.

### 1. Script labelled as _“GitHub_Crypto_to_MySQL” *_

A requirement to execute this script is to create an API Key and a Secret Key via the Binance website (instructions are available online).
This script automates the process of fetching real-time cryptocurrency market data from the Binance API and storing it in a MySQL database. It retrieves ticker and 1-minute candlestick data for multiple altcoins traded against USD and stores them in several schemas in MySQL.

**Key features include:**
* Data Fetching: Retrieves price and historical candlestick data.
* Database Storage: Inserts fetched data into a MySQL database.
* Threading: Utilizes multi-threading to handle data collection for multiple cryptocurrencies in parallel.
This setup enables continuous market data collection for analysis and backtesting.

### 2. Script labelled as _“GitHub_Testing_Conditions_optim” *_

Similar to the script above, the user must have an API Key and a Secret Key. This script is a continuation of the previous one and monitors retrieved crypto symbols, checks for defined trading conditions (cumulative return), and if they are met, creates a separate crypto schema in MySQL that retrieves real-time price movements at a few-second interval.

**Key features include:**
* API & Database Setup: The notebook connects to Binance’s API using BinanceSocketManager for real-time price data and to MySQL for storing the data using SQLAlchemy.
* Price Monitoring: It monitors six groups of crypto symbols, fetching price data every 2 seconds and storing it in MySQL.
* Trading Logic: When conditions of a defined cumulative return are met, the script calculates the trade quantity and executes a function that runs another script (see point 3 below), which performs a “hypothetical trade” and immediately starts monitoring sell conditions (see the description below).

### 3. Script labelled as _“GitHub_Order_Testing_optim”_
   
Unlike script 2), this one is about selling previously bought cryptos. It retrieves live prices and monitors price movements every few seconds to execute sell conditions (determining how much the price needs to decrease from the cumulative max for stop-loss conditions to be met).

**Key features include:**
* Sell Conditions: Tracks the price for stop-loss or profit-taking brackets and evaluates whether to sell based on predefined conditions.

### 4. Script labelled as _“GitHub_Price_Storage” *_
   
This script implements a loop that tracks and stores the prices of different altcoins through a real-time price retrieval system. It uses a dictionary to store data such as the altcoin name, the buy price, and the sell price.

**Key features include:**
* Sequential Altcoin Data Storage: Retrieves and stores the name, buy price, and sell price in a dictionary through a loop.
* Status Control Mechanism: After processing each altcoin, the script updates a file named “status.txt” and resets the “pa” variable to control the loop flow.

### 5. Script labelled as _“GitHub_MySQL_Scanning” *_
   
The main purpose of this script is to avoid buying the same type of crypto. It continuously connects to a MySQL database at regular intervals (every 5 seconds) to retrieve the available schemas in the database.

**Key features include:**
* Database Connection Setup: Establishes a connection to a MySQL database to retrieve schema information.
* Schema Retrieval: Executes a query to fetch all available database schemas and stores them for later access in a script under point 2).

### 6. Script labelled as _“GitHub_Script_Runner1” *_
   
This script functions as an execution manager for the script under point 2) after the Kernel is restarted. It does so via an infinite loop by reading the current status from “status.txt”. If the status indicates that the process is "stopped" (i.e., the Kernel in script 2) was restarted), the script waits for 20 seconds before updating the status of “status.txt” and then executing the specified notebook.

**Key features include:**
* File Operations: Reads the status from “status.txt” to verify whether the script should proceed with the execution of the notebook.
* Conditional Execution: If the status indicates "stopped," it pauses for 20 seconds before updating “status.txt” and changing the variable “runner2” to “pass” for the script below (point 7)) to start monitoring the status of “status.txt” and executing (if conditions are met) the script under point 2).

### 7. Script labelled as _“GitHub_Script_Runner2” *_
   
Identical to the script above, with the only difference being replaced variables. The reason for this is the re-execution of the script under point 2). The number of these types of scripts (“Script_Runner”) is directly related to the number of crypto trades that can occur.

