
# Using `request.security` in Pine Script v5

## Introduction

The `request.security` function is one of the most powerful features in Pine Script, allowing you to access data from different symbols and timeframes. However, it's also a common source of syntax errors and confusion, especially when used in complex expressions spanning multiple lines.

## Basic Syntax

The basic syntax for `request.security` is:

```pine
request.security(symbol, timeframe, expression, [gaps, lookahead])
```

Where:
- `symbol`: The ticker symbol to fetch data from (e.g., "AAPL", "BTCUSD")
- `timeframe`: The timeframe to use (e.g., "D", "W", "60", "240")
- `expression`: The data to fetch (can be a simple value or complex expression)
- `gaps`: How to handle gaps in the data (optional)
- `lookahead`: Controls whether to use future data (optional)

## Common Errors and Solutions

### 1. End of Line Without Line Continuation

When using `request.security` with a complex expression that spans multiple lines, you may encounter the "end of line without line continuation" error.

```pine
// WRONG - Will cause an error
data = request.security(syminfo.tickerid, timeframe,
[close, high, low, ta.ema(close, 14)])

// CORRECT - Proper indentation for continuation
data = request.security(syminfo.tickerid, timeframe,
    [close, high, low, ta.ema(close, 14)])
```

### 2. Safer Approach: Separate Calls

For complex calculations, consider making separate `request.security` calls instead of trying to pack everything into one:

```pine
// Instead of this:
data = request.security(syminfo.tickerid, timeframe,
    [close, high, low, 
    ta.ema(close, fast_length), 
    ta.rsi(close, rsi_length),
    ta.macd(close, fast_length, slow_length, signal_length)])

// Do this instead:
price_data = request.security(syminfo.tickerid, timeframe, [close, high, low])
ema_data = request.security(syminfo.tickerid, timeframe, ta.ema(close, fast_length))
rsi_data = request.security(syminfo.tickerid, timeframe, ta.rsi(close, rsi_length))
macd_data = request.security(syminfo.tickerid, timeframe, ta.macd(close, fast_length, slow_length, signal_length))
```

### 3. Working with Multiple Values

When you need to fetch multiple values, you can use arrays:

```pine
[tf_close, tf_high, tf_low] = request.security(syminfo.tickerid, timeframe, [close, high, low])
```

However, for complex expressions like MACD that return multiple values:

```pine
// CORRECT: Store the result in a variable first
macd_result = request.security(syminfo.tickerid, timeframe, ta.macd(close, 12, 26, 9))
// Then extract individual components
macd_line = macd_result[0]
signal_line = macd_result[1]
histogram = macd_result[2]
```

## Best Practices

### 1. Minimize the Number of Calls

Each call to `request.security` has performance implications. Try to group related data:

```pine
// Good - Fetching related data in one call
[tf_close, tf_high, tf_low, tf_volume] = request.security(syminfo.tickerid, timeframe, [close, high, low, volume])
```

### 2. Use Timeframe Variables

Store timeframes in variables for better readability:

```pine
tf_daily = "D"
tf_weekly = "W"
tf_monthly = "M"

daily_close = request.security(syminfo.tickerid, tf_daily, close)
weekly_close = request.security(syminfo.tickerid, tf_weekly, close)
monthly_close = request.security(syminfo.tickerid, tf_monthly, close)
```

### 3. Handle Data Assignment Carefully

For complex data structures, assign to intermediate variables:

```pine
// For multiple indicators
rsi_macd_data = request.security(syminfo.tickerid, timeframe, 
    [ta.rsi(close, 14), ta.macd(close, 12, 26, 9)])

// Then extract the values
rsi_value = rsi_macd_data[0]
macd_result = rsi_macd_data[1]

// Further extract MACD components
macd_line = macd_result[0]
signal_line = macd_result[1]
histogram = macd_result[2]
```

### 4. Be Careful with Multi-timeframe Analysis

When comparing data from different timeframes, be aware of alignment issues:

```pine
// Getting data from different timeframes
current_close = close
daily_close = request.security(syminfo.tickerid, "D", close, barmerge.gaps_off)
weekly_close = request.security(syminfo.tickerid, "W", close, barmerge.gaps_off)

// Comparing them safely
is_above_daily = current_close > daily_close
is_above_weekly = current_close > weekly_close
```

## Advanced Techniques

### 1. Using Historical References

You can access historical data from other timeframes:

```pine
// Getting the close price from 3 bars back on the daily timeframe
daily_close_3_bars_ago = request.security(syminfo.tickerid, "D", close[3])
```

### 2. Conditional Data Fetching

Use conditional logic to fetch different types of data:

```pine
use_custom_calculation = input.bool(false, "Use Custom Calculation")

requested_data = request.security(syminfo.tickerid, timeframe,
    use_custom_calculation ? (high + low) / 2 : close)
```

### 3. Fetching Data from Other Symbols

Compare data across different symbols:

```pine
btc_price = request.security("BTCUSD", timeframe, close)
eth_price = request.security("ETHUSD", timeframe, close)
eth_btc_ratio = eth_price / btc_price
```

## Troubleshooting

If you're experiencing issues with `request.security`:

1. **Check Your Syntax**: Make sure all parentheses and brackets are properly closed
2. **Simplify Complex Expressions**: Break down complex calculations into simpler parts
3. **Verify Symbol Existence**: Make sure the requested symbol exists and is accessible
4. **Check Timeframe Validity**: Ensure the timeframe is valid for the specified symbol
5. **Avoid Lookahead**: Be cautious with the `lookahead` parameter to prevent future data leakage

## Conclusion

The `request.security` function is a powerful tool for accessing data across symbols and timeframes in Pine Script. By understanding its syntax and common pitfalls, you can write more robust and error-free indicators and strategies.

Always strive for clarity and simplicity in your code, especially when dealing with complex data requests. When in doubt, break down complex expressions into smaller, more manageable pieces.
