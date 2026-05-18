# Gap Fill + Opening Range Reaction Strategy

## Why this is trending now

Right now, especially in US markets:

Markets are opening with large gaps due to overnight news and macro tension.

Price often either:

fills the gap

or strongly rejects and trends away

Traders are focusing on:

gap behavior

opening range breakout/reversal

early session volatility

## Strategy Idea

This strategy combines:

### 1. Gap Detection

Today’s open vs yesterday’s close

### 2. Opening Range

First X minutes

Define high & low of early session

### 3. Reaction Logic

Trade:

gap fill, mean reversion

or breakout, continuation

## Automate It with PineGen AI

Take this strategy to the next level with PineGen AI automation:

Website: https://www.pinegen.ai/

Twitter: https://x.com/PineGenAI

Telegram Channel: https://t.me/PineGenAI

YouTube Channel: https://www.youtube.com/@pinegenai

## Pine Script Code

```pine
//@version=6
strategy("Gap Fill + Opening Range Strategy",
     overlay=true,
     initial_capital=100000,
     default_qty_type=strategy.percent_of_equity,
     default_qty_value=5)

// ───── INPUTS ─────
rangeMinutes = input.int(30, "Opening Range Minutes")
atrLen       = input.int(14, "ATR Length")
atrMult      = input.float(1.5, "Stop ATR Multiplier")
rr           = input.float(2.0, "Risk Reward")

// ───── SESSION TIME ─────
sessionStart = timestamp(year, month, dayofmonth, 9, 30)
inOpening    = time >= sessionStart and time <= sessionStart + rangeMinutes * 60 * 1000

// ───── GAP DETECTION ─────
prevClose = close[1]
gapUp     = open > prevClose
gapDown   = open < prevClose

// ───── OPENING RANGE ─────
var float rangeHigh = na
var float rangeLow  = na

if inOpening
    rangeHigh := na(rangeHigh) ? high : math.max(rangeHigh, high)
    rangeLow  := na(rangeLow)  ? low  : math.min(rangeLow, low)

// Reset each day
if dayofmonth != dayofmonth[1]
    rangeHigh := na
    rangeLow  := na

// ───── BREAKOUT LOGIC ─────
breakUp   = ta.crossover(close, rangeHigh)
breakDown = ta.crossunder(close, rangeLow)

// ───── ENTRY CONDITIONS ─────
// Gap fill (reversal)
longGapFill  = gapDown and close > rangeLow
shortGapFill = gapUp and close < rangeHigh

// Breakout continuation
longBreak  = breakUp
shortBreak = breakDown

longCondition  = (longGapFill or longBreak)
shortCondition = (shortGapFill or shortBreak)

// ───── ATR RISK ─────
atrVal = ta.atr(atrLen)

if longCondition and strategy.position_size == 0
    strategy.entry("Long", strategy.long)

if shortCondition and strategy.position_size == 0
    strategy.entry("Short", strategy.short)

longStop   = strategy.position_avg_price - atrVal * atrMult
longTarget = strategy.position_avg_price + atrVal * atrMult * rr

shortStop   = strategy.position_avg_price + atrVal * atrMult
shortTarget = strategy.position_avg_price - atrVal * atrMult * rr

strategy.exit("Exit Long", from_entry="Long", stop=longStop, limit=longTarget)
strategy.exit("Exit Short", from_entry="Short", stop=shortStop, limit=shortTarget)

// ───── VISUALS ─────
plot(rangeHigh, title="Opening Range High", color=color.green)
plot(rangeLow, title="Opening Range Low", color=color.red)
```
