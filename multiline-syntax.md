
# Multiline Syntax Guide for Pine Script v5

## Introduction
Pine Script has strict rules about how to format code that spans multiple lines. This guide explains the proper syntax to avoid the common `end of line without line continuation` error.

## The Problem
When writing complex code in Pine Script, you'll often need to split expressions across multiple lines for readability. However, Pine Script requires specific indentation and formatting rules to recognize that a line is continuing from the previous one.

A common error message is:
```
Syntax error at input 'end of line without line continuation'
```

## Basic Rules for Multiline Code

### 1. Proper Indentation
When splitting an expression across multiple lines, each continuation line must be properly indented with at least one more space than its parent line:

```pine
// WRONG - No indentation on continued line
request_security = request.security(syminfo.tickerid, timeframe,
[close, high, low])

// CORRECT - Continuation line is indented
request_security = request.security(syminfo.tickerid, timeframe,
    [close, high, low])
```

### 2. Splitting at Logical Places
Split lines at logical places like after commas, operators, or opening brackets:

```pine
// GOOD - Split after comma
my_function(param1,
    param2,
    param3)

// GOOD - Split after opening bracket
data = [
    value1,
    value2,
    value3]
```

### 3. Avoid Comments at the End of Continued Lines
Comments at the end of a line that continues to the next line can cause this error:

```pine
// WRONG - Comment at end of continued line
plotValue = (open != close[1]) ? close : low // This is a comment
    - (.1 * close)

// CORRECT - Move comment to its own line or to the end of the complete expression
// This is a comment
plotValue = (open != close[1]) ? close : low
    - (.1 * close)
```

### 4. Complete All Parentheses on Multiline Expressions
Make sure all brackets, parentheses, and braces are properly closed:

```pine
// WRONG - Missing closing bracket until next line
data = [value1, value2
    value3]

// CORRECT - All brackets are properly closed
data = [value1, value2,
    value3]
```

## Special Cases

### Function Calls Across Multiple Lines
When using functions with multiple parameters across lines:

```pine
// CORRECT
indicator = request.security(
    syminfo.tickerid, 
    timeframe,
    [close, high, low, 
    ta.ema(close, ema_fast)])
```

### Array Assignments
For arrays, ensure proper indentation and commas:

```pine
// CORRECT
my_array = [
    value1,
    value2,
    value3
]
```

### Handling Complex Request.Security Calls
For complex calls with multiple indicator calculations:

```pine
// PROBLEMATIC APPROACH - Complex multi-line request.security
data = request.security(syminfo.tickerid, timeframe,
    [close, high, low, 
    ta.ema(close, ema_fast), ta.ema(close, ema_slow),
    ta.rsi(close, rsi_length)])

// BETTER APPROACH - Use multiple simple calls
price_data = request.security(syminfo.tickerid, timeframe, [close, high, low])
ema_data = request.security(syminfo.tickerid, timeframe, 
    [ta.ema(close, ema_fast), ta.ema(close, ema_slow)])
rsi_data = request.security(syminfo.tickerid, timeframe, ta.rsi(close, rsi_length))
```

## Tips to Avoid the Error

1. **Use separate variables** for complex expressions instead of trying to do everything in one line
2. **Consistent indentation** is crucial - use at least 4 spaces for continuation lines
3. **Avoid mixing tabs and spaces** for indentation
4. If you get errors, try **breaking down complex expressions** into simpler ones
5. **Check for missing commas** between array or function parameters
6. Consider using a **temporary variable approach**:

```pine
// Instead of this complex multiline call
result = complex_function(
    param1,
    param2,
    nested_function(
        nested_param1,
        nested_param2))

// Use temporary variables
temp = nested_function(nested_param1, nested_param2)
result = complex_function(param1, param2, temp)
```

## Examples of Fixed Code

### Example 1: Function Call
```pine
// PROBLEMATIC
result = calculate(parameter1, parameter2, parameter3,
parameter4)

// FIXED
result = calculate(parameter1, parameter2, parameter3,
    parameter4)
```

### Example 2: Array Definition
```pine
// PROBLEMATIC
my_array = [item1, item2, item3
item4, item5]

// FIXED
my_array = [item1, item2, item3,
    item4, item5]
```

### Example 3: Complex Expression
```pine
// PROBLEMATIC
value = (condition1 and condition2) ? 
expression1 : expression2

// FIXED
value = (condition1 and condition2) ? 
    expression1 : expression2
```

## Conclusion

Understanding Pine Script's multiline syntax rules is essential for writing complex indicators. When in doubt, break down your code into smaller, simpler expressions using intermediate variables.

Remember that proper indentation and line continuation practices not only prevent errors but also make your code more readable and maintainable.
