[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/0xt079KN)

Vertical Spread: 
import matplotlib.pyplot as plt
import numpy as np

def call_payoff(underlying_price, strike_price, premium, position='long'):
    if position == 'long':
        return np.maximum(0, underlying_price - strike_price) - premium
    elif position == 'short':
        return premium - np.maximum(0, underlying_price - strike_price)
    else:
        raise ValueError("Position must be 'long' or 'short'")

def put_payoff(underlying_price, strike_price, premium, position='long'):
    if position == 'long':
        return np.maximum(0, strike_price - underlying_price) - premium
    elif position == 'short':
        return premium - np.maximum(0, strike_price - underlying_price)
    else:
        raise ValueError("Position must be 'long' or 'short'")

def bull_call_spread_payoff(underlying_price, lower_strike, higher_strike, lower_premium, higher_premium):
    long_call_payoff = call_payoff(underlying_price, lower_strike, lower_premium, 'long')
    short_call_payoff = call_payoff(underlying_price, higher_strike, higher_premium, 'short')
    return long_call_payoff + short_call_payoff

def bear_put_spread_payoff(underlying_price, higher_strike, lower_strike, higher_premium, lower_premium):
    long_put_payoff = put_payoff(underlying_price, higher_strike, higher_premium, 'long')
    short_put_payoff = put_payoff(underlying_price, lower_strike, higher_premium, 'short')
    return long_put_payoff + short_put_payoff

def bear_call_spread_payoff(underlying_price, lower_strike, higher_strike, lower_premium, higher_premium):
    short_call_payoff = call_payoff(underlying_price, lower_strike, lower_premium, 'short')
    long_call_payoff = call_payoff(underlying_price, higher_strike, higher_premium, 'long')
    return short_call_payoff + long_call_payoff

def bull_put_spread_payoff(underlying_price, higher_strike, lower_strike, higher_premium, lower_premium):
    short_put_payoff = put_payoff(underlying_price, higher_strike, higher_premium, 'short')
    long_put_payoff = put_payoff(underlying_price, lower_strike, lower_premium, 'long')
    return short_put_payoff + long_put_payoff

def plot_payoff(underlying_prices, payoffs, title):
    plt.figure(figsize=(10, 6))
    plt.plot(underlying_prices, payoffs, label='Strategy Payoff')
    plt.axhline(0, color='black', linestyle='--', linewidth=0.8)
    plt.xlabel('Underlying Price at Expiration')
    plt.ylabel('Payoff')
    plt.title(title)
    plt.legend()
    plt.grid(True)
    plt.show()

def get_vertical_spread_parameters(spread_type):
    if spread_type == 'bull call':
        lower_strike = float(input("Enter the lower strike price (e.g., 45.00): "))
        higher_strike = float(input("Enter the higher strike price (e.g., 50.00): "))
        lower_premium = float(input("Enter the premium paid for the lower strike call (e.g., 3.00): "))
        higher_premium = float(input("Enter the premium received for the higher strike call (e.g., 1.00): "))
        return lower_strike, higher_strike, lower_premium, higher_premium
    elif spread_type == 'bear put':
        higher_strike = float(input("Enter the higher strike price (e.g., 55.00): "))
        lower_strike = float(input("Enter the lower strike price (e.g., 50.00): "))
        higher_premium = float(input("Enter the premium paid for the higher strike put (e.g., 2.00): "))
        lower_premium = float(input("Enter the premium received for the lower strike put (e.g., 0.50): "))
        return higher_strike, lower_strike, higher_premium, lower_premium
    elif spread_type == 'bear call':
        lower_strike = float(input("Enter the lower strike price (e.g., 50.00): "))
        higher_strike = float(input("Enter the higher strike price (e.g., 55.00): "))
        lower_premium = float(input("Enter the premium received for the lower strike call (e.g., 2.00): "))
        higher_premium = float(input("Enter the premium paid for the higher strike call (e.g., 0.50): "))
        return lower_strike, higher_strike, lower_premium, higher_premium
    elif spread_type == 'bull put':
        higher_strike = float(input("Enter the higher strike price (e.g., 50.00): "))
        lower_strike = float(input("Enter the lower strike price (e.g., 45.00): "))
        higher_premium = float(input("Enter the premium received for the higher strike put (e.g., 3.00): "))
        lower_premium = float(input("Enter the premium paid for the lower strike put (e.g., 1.00): "))
        return higher_strike, lower_strike, higher_premium, lower_premium
    else:
        print("Invalid spread type. Please choose 'bull call', 'bear put', 'bear call', or 'bull put'.")
        return None

if __name__ == "__main__":
    print("Vertical Spread:")
    spread_type = input("Do you want to analyze a (bull call), (bear put), (bear call), or (bull put) spread? ").lower()

    parameters = get_vertical_spread_parameters(spread_type)

    if parameters:
        current_price = float(input("Enter the current underlying asset price (e.g., 50.00): "))
        lower_bound = max(0, current_price * 0.7)
        upper_bound = current_price * 1.3
        underlying_prices = np.linspace(lower_bound, upper_bound, 400)

        if spread_type == 'bull call':
            lower_strike, higher_strike, lower_premium, higher_premium = parameters
            print(f"Underlying Price: {current_price:.2f}, Lower Strike Price: {lower_strike:.2f}, Higher Strike Price: {higher_strike:.2f}, Lower Premium: {lower_premium:.2f}, Higher Premium: {higher_premium:.2f}, Spread Type: Bull Call")
            spread_payoffs = [bull_call_spread_payoff(price, lower_strike, higher_strike, lower_premium, higher_premium) for price in underlying_prices]
            plot_payoff(underlying_prices, spread_payoffs, 'Bull Call Spread Payoff at Expiration')
            max_profit = (higher_strike - lower_strike) - (lower_premium - higher_premium)
            max_loss = (lower_premium - higher_premium)
            print(f"\nMaximum Profit: ${max_profit:.2f}")
            print(f"Maximum Loss: ${max_loss:.2f}")
            print(f"Break-even Point: ${lower_strike + (lower_premium - higher_premium):.2f}")

        elif spread_type == 'bear put':
            higher_strike, lower_strike, higher_premium, lower_premium = parameters
            print(f"Underlying Price: {current_price:.2f}, Lower Strike Price: {lower_strike:.2f}, Higher Strike Price: {higher_strike:.2f}, Lower Premium: {lower_premium:.2f}, Higher Premium: {higher_premium:.2f}, Spread Type: Bear Put")
            spread_payoffs = [bear_put_spread_payoff(price, higher_strike, lower_strike, higher_premium, lower_premium) for price in underlying_prices]
            plot_payoff(underlying_prices, spread_payoffs, 'Bear Put Spread Payoff at Expiration')
            max_profit = (higher_strike - lower_strike) - (higher_premium - lower_premium)
            max_loss = (higher_premium - lower_premium)
            print(f"\nMaximum Profit: ${max_profit:.2f}")
            print(f"Maximum Loss: ${max_loss:.2f}")
            print(f"Break-even Point: ${higher_strike - (higher_premium - lower_premium):.2f}")

        elif spread_type == 'bear call':
            lower_strike, higher_strike, lower_premium, higher_premium = parameters
            print(f"Underlying Price: {current_price:.2f}, Lower Strike Price: {lower_strike:.2f}, Higher Strike Price: {higher_strike:.2f}, Lower Premium: {lower_premium:.2f}, Higher Premium: {higher_premium:.2f}, Spread Type: Bear Call")
            spread_payoffs = [bear_call_spread_payoff(price, lower_strike, higher_strike, lower_premium, higher_premium) for price in underlying_prices]
            plot_payoff(underlying_prices, spread_payoffs, 'Bear Call Spread Payoff at Expiration')
            max_profit = (higher_strike - lower_strike) - (higher_premium - lower_premium)
            max_loss = (lower_premium - higher_premium)
            print(f"\nMaximum Profit: ${max_profit:.2f}")
            print(f"Maximum Loss: ${max_loss:.2f}")
            print(f"Break-even Point: ${lower_strike + (lower_premium - higher_premium):.2f}")

        elif spread_type == 'bull put':
            higher_strike, lower_strike, higher_premium, lower_premium = parameters
            print(f"Underlying Price: {current_price:.2f}, Lower Strike Price: {lower_strike:.2f}, Higher Strike Price: {higher_strike:.2f}, Lower Premium: {lower_premium:.2f}, Higher Premium: {higher_premium:.2f}, Spread Type: Bull Put")
            spread_payoffs = [bull_put_spread_payoff(price, higher_strike, lower_strike, higher_premium, lower_premium) for price in underlying_prices]
            plot_payoff(underlying_prices, spread_payoffs, 'Bull Put Spread Payoff at Expiration')
            max_profit = (higher_premium - lower_premium)
            max_loss = (higher_strike - lower_strike) - (higher_premium - lower_premium)
            print(f"\nMaximum Profit: ${max_profit:.2f}")
            print(f"Maximum Loss: ${max_loss:.2f}")
            print(f"Break-even Point: ${higher_strike - (higher_premium - lower_premium):.2f}")
#%%




Straddle: 
import matplotlib.pyplot as plt
import numpy as np

def call_payoff(underlying_price, strike_price, premium, position='long'):
    if position == 'long':
        return np.maximum(0, underlying_price - strike_price) - premium
    elif position == 'short':
        return premium - np.maximum(0, underlying_price - strike_price)
    else:
        raise ValueError("Position must be 'long' or 'short'")

def put_payoff(underlying_price, strike_price, premium, position='long'):
    if position == 'long':
        return np.maximum(0, strike_price - underlying_price) - premium
    elif position == 'short':
        return premium - np.maximum(0, strike_price - underlying_price)
    else:
        raise ValueError("Position must be 'long' or 'short'")

def straddle_payoff(underlying_price, call_strike, call_premium, put_strike, put_premium, position='long'):
    if position == 'long':
        call_payoff_long = call_payoff(underlying_price, call_strike, call_premium, 'long')
        put_payoff_long = put_payoff(underlying_price, put_strike, put_premium, 'long')
        return call_payoff_long + put_payoff_long
    elif position == 'short':
        call_payoff_short = call_payoff(underlying_price, call_strike, call_premium, 'short')
        put_payoff_short = put_payoff(underlying_price, put_strike, put_premium, 'short')
        return call_payoff_short + put_payoff_short
    else:
        raise ValueError("Position must be 'long' or 'short'")

def plot_payoff(underlying_prices, payoffs, title):
    plt.figure(figsize=(10, 6))
    plt.plot(underlying_prices, payoffs, label='Strategy Payoff')
    plt.axhline(0, color='black', linestyle='--', linewidth=0.8)
    plt.xlabel('Underlying Price at Expiration')
    plt.ylabel('Payoff')
    plt.title(title)
    plt.legend()
    plt.grid(True)
    plt.show()

def get_straddle_parameters(position):
    strike_price = float(input("Enter the strike price for both call and put options (e.g., 105.00): "))
    call_premium = float(input("Enter the call option premium (e.g., 7.00): "))
    put_premium = float(input("Enter the put option premium (e.g., 3.00): "))
    return strike_price, call_premium, put_premium

if __name__ == "__main__":
    print("Straddle Option Analysis Tool")
    position_type = input("Analyze a (long) straddle or (short) straddle? ").lower()

    parameters = get_straddle_parameters(position_type)
    if parameters:
        strike_price, call_premium, put_premium = parameters
        current_price = float(input("Enter the current underlying asset price (e.g., 100.00): "))
        lower_bound = max(0, current_price * 0.7)
        upper_bound = current_price * 1.3
        underlying_prices = np.linspace(lower_bound, upper_bound, 400)

        print(f"Underlying Price: {current_price:.2f}, Strike Price: {strike_price:.2f}, Call Premium: {call_premium:.2f}, Put Premium: {put_premium:.2f}, Position: {position_type.title()} Straddle")

        straddle_payoffs = [straddle_payoff(price, strike_price, call_premium, strike_price, put_premium, position_type) for price in underlying_prices]
        plot_payoff(underlying_prices, straddle_payoffs, f"{position_type.title()} Straddle Payoff at Expiration")

        if position_type == 'long':
            max_loss = call_premium + put_premium
            print(f"\nMaximum Loss: ${max_loss:.2f}")
            print(f"Break-even Points: ${strike_price - (call_premium + put_premium):.2f} (Lower), ${strike_price + (call_premium + put_premium):.2f} (Upper)")
        elif position_type == 'short':
            max_profit = call_premium + put_premium
            print(f"\nMaximum Profit: ${max_profit:.2f}")
            print(f"Break-even Points: ${strike_price - (call_premium + put_premium):.2f} (Lower), ${strike_price + (call_premium + put_premium):.2f} (Upper)")











            Covered Call:

            #%%
import matplotlib.pyplot as plt
import numpy as np

def call_payoff(underlying_price, strike_price, premium, position='long'):
    if position == 'long':
        return np.maximum(0, underlying_price - strike_price) - premium
    elif position == 'short':
        return premium - np.maximum(0, underlying_price - strike_price)
    else:
        raise ValueError("Position must be 'long' or 'short'")

def covered_call_payoff(underlying_price, stock_price, call_strike, call_premium):
    stock_payoff = underlying_price - stock_price
    short_call_payoff = call_premium - np.maximum(0, underlying_price - call_strike)
    return stock_payoff + short_call_payoff

def plot_payoff(underlying_prices, payoffs, title):
    plt.figure(figsize=(10, 6))
    plt.plot(underlying_prices, payoffs, label='Strategy Payoff')
    plt.axhline(0, color='black', linestyle='--', linewidth=0.8)
    plt.xlabel('Underlying Price at Expiration')
    plt.ylabel('Payoff')
    plt.title(title)
    plt.legend()
    plt.grid(True)
    plt.show()

def get_covered_call_parameters():
    stock_price = float(input("Enter the initial stock price (e.g., 100.00): "))
    call_strike = float(input("Enter the call option strike price (e.g., 105.00): "))
    call_premium = float(input("Enter the call option premium received (e.g., 5.00): "))
    return stock_price, call_strike, call_premium

if __name__ == "__main__":
    print("Covered Call strategy:")
    stock_price, call_strike, call_premium = get_covered_call_parameters()
    lower_bound = max(0, stock_price * 0.7)
    upper_bound = stock_price * 1.3
    underlying_prices = np.linspace(lower_bound, upper_bound, 400)
    print(f"Initial Stock Price: {stock_price:.2f}, Call Strike Price: {call_strike:.2f}, Call Premium: {call_premium:.2f}") 
    covered_call_payoffs = [covered_call_payoff(price, stock_price, call_strike, call_premium) for price in underlying_prices]
    plot_payoff(underlying_prices, covered_call_payoffs, 'Covered Call Payoff at Expiration')
    break_even = stock_price - call_premium
    print(f"\nBreak-even point for the Covered Call: ${break_even:.2f}")



Normal Call and Put Options:

import matplotlib.pyplot as plt
import numpy as np



def call_payoff(underlying_price, strike_price, premium, position='long'):
    if position == 'long':
        return np.maximum(0, underlying_price - strike_price) - premium
    elif position == 'short':
        return premium - np.maximum(0, underlying_price - strike_price)
    else:
        raise ValueError("Position must be 'long' or 'short'")

def put_payoff(underlying_price, strike_price, premium, position='long'):
    if position == 'long':
        return np.maximum(0, strike_price - underlying_price) - premium
    elif position == 'short':
        return premium - np.maximum(0, strike_price - underlying_price)
    else:
        raise ValueError("Position must be 'long' or 'short'")
    
    

def plot_payoff(underlying_prices, payoffs, title):
    plt.figure(figsize=(10, 6))
    plt.plot(underlying_prices, payoffs, label='Strategy Payoff')
    plt.axhline(0, color='black', linestyle='--', linewidth=0.8)
    plt.xlabel('Underlying Price at Expiration')
    plt.ylabel('Payoff')
    plt.title(title)
    plt.legend()
    plt.grid(True)
    plt.show()

def get_option_parameters():
    strike_price = float(input("Enter the option strike price (e.g., 90.00): "))
    premium = float(input("Enter the option premium paid/received (e.g., 5.00): "))
    return strike_price, premium

if __name__ == "__main__":
    print("Option Analysis Tool")
    option_type = input("Analyze a (long call), (short call), (long put), or (short put) option? ").lower()
    current_price = float(input("Enter the current underlying asset price (e.g., 100.00): "))
    lower_bound = max(0, current_price * 0.7)
    upper_bound = current_price * 1.3
    underlying_prices = np.linspace(lower_bound, upper_bound, 400)

    if option_type in ('long call', 'short call', 'long put', 'short put'):
        strike_price, premium = get_option_parameters()
        position = option_type.split()[0]
        print(f"Underlying Price: {current_price:.2f}, Strike Price: {strike_price:.2f}, Premium: {premium:.2f}, Position: {position.title()} {option_type.split()[-1].title()}")

        if 'call' in option_type:
            payoffs = [call_payoff(price, strike_price, premium, position) for price in underlying_prices]
            title = f"{position.title()} Call Payoff at Expiration"
            break_even = strike_price + premium if position == 'long' else strike_price + premium
        else:
            payoffs = [put_payoff(price, strike_price, premium, position) for price in underlying_prices]
            title = f"{position.title()} Put Payoff at Expiration"
            break_even = strike_price - premium if position == 'long' else strike_price + premium

        plot_payoff(underlying_prices, payoffs, title)
        print(f"Break-even point: ${break_even:.2f}")
    else:
        print("Invalid option type selected.")
