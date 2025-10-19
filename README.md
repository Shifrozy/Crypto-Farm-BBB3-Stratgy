# 🌾 Crypto Farm BBB3 Strategy

> Advanced Bollinger Band-based trading strategy for TradingView with multiple filters, dual take-profit system, and intelligent entry mechanisms.

[![Pine Script](https://img.shields.io/badge/Pine%20Script-v6-blue)](https://www.tradingview.com/pine-script-reference/v6/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Status](https://img.shields.io/badge/status-Production%20Ready-brightgreen)](https://github.com)

---

## 📋 Table of Contents

- [Features](#-features)
- [Installation](#-installation)
- [Strategy Modes](#-strategy-modes)
- [Filter Systems](#-filter-systems)
- [Entry Management](#-entry-management)
- [Exit Management](#-exit-management)
- [Configuration Guide](#-configuration-guide)
- [Performance](#-performance)
- [Changelog](#-changelog)
- [Contributing](#-contributing)
- [License](#-license)

---

## ✨ Features

### Core Strategy Components
- **Dual Trading Modes**: Wide Tide & Distance Capture
- **Multi-Layer Filter System**: BROC, UPROC, LOROC, Marubozu
- **Dual Take-Profit System**: Independent TP1 & TP2 with time/price exits
- **Advanced Entry Logic**: ScoopTime, Delay Entry, Cooldown
- **Redundancy Probes**: Automated alert system after trade closure
- **Stop Loss Management**: Auto-move to breakeven after TP1
- **Non-Repainting**: 100% bar-stable for reliable backtesting

### Key Highlights
✅ **Production-Ready** - Fully tested and optimized  
✅ **Fast Execution** - Efficient code with minimal CPU usage  
✅ **Highly Configurable** - 40+ customizable parameters  
✅ **Visual Feedback** - Clear on-chart indicators and levels  
✅ **Alert System** - Comprehensive alert conditions for automation  

---

## 🚀 Installation

### Method 1: Direct Import
1. Open [TradingView](https://www.tradingview.com/)
2. Go to Pine Editor (Alt + E)
3. Copy the entire script from `crypto_farm_bbb3.pine`
4. Paste into Pine Editor
5. Click "Add to Chart"

### Method 2: Clone Repository
```bash
git clone https://github.com/yourusername/crypto-farm-bbb3.git
cd crypto-farm-bbb3
```

---

## 🎯 Strategy Modes

### Wide Tide Mode
Enters trades when Bollinger Bands are **wide** (high volatility) and price touches the outer bands.

**Logic:**
- Band width must exceed threshold percentage
- Long: Price closes below lower band
- Short: Price closes above upper band

**Best For:** Volatile markets, mean reversion trading

**Configuration:**
```
Mode: "Wide Tide"
Wide Tide Threshold: 2.5% (default)
```

### Distance Capture Mode
Enters trades at a **custom percentage** distance between the middle and outer bands.

**Logic:**
- Calculates entry level as % of distance from middle to outer band
- Long: Price crosses below entry level
- Short: Price crosses above entry level

**Best For:** Precise entry timing, range-bound markets

**Configuration:**
```
Mode: "Distance Capture"
Distance Capture Entry %: 25% (default)
```

---

## 🔍 Filter Systems

### BROC (Basis Rate of Change)
Measures the **slope angle** of the Bollinger Band middle line (basis).

- **Calculation**: Uses `arctan(slope)` converted to degrees
- **Purpose**: Confirms trend direction
- **Threshold**: Minimum angle required for entry
- **Multiplier**: Scaling factor for sensitivity

```
Use BROC Filter: ✓
BROC Length: 14
BROC Threshold: 0.5°
BROC Scaling Multiplier: 1.0
```

### UPROC (Upper Band Rate of Change)
Measures the **slope angle** of the upper Bollinger Band.

- **Purpose**: Confirms upper band expansion/contraction
- **Bullish**: Upper band expanding upward
- **Bearish**: Upper band contracting downward

```
Use UPROC Filter: ☐
UPROC Length: 14
UPROC Threshold: 0.5°
UPROC Scaling Multiplier: 1.0
```

### LOROC (Lower Band Rate of Change)
Measures the **slope angle** of the lower Bollinger Band.

- **Purpose**: Confirms lower band expansion/contraction
- **Bullish**: Lower band expanding downward
- **Bearish**: Lower band contracting upward

```
Use LOROC Filter: ☐
LOROC Length: 14
LOROC Threshold: 0.5°
LOROC Scaling Multiplier: 1.0
```

### Marubozu Filter
Identifies **strong directional candles** with minimal wicks.

- **Bullish Marubozu**: Large green body, small wicks → strong buying
- **Bearish Marubozu**: Large red body, small wicks → strong selling
- **Visual**: Triangle indicators appear on qualifying candles

```
Use Marubozu Filter: ☐
Marubozu Body % of Range: 70%
Marubozu Max Wick % of Body: 15%
```

---

## ⏱️ Entry Management

### ScoopTime
Triggers entries **X seconds before candle close** on any timeframe.

**How It Works:**
- Calculates timeframe duration in seconds
- Determines bars until close
- Triggers when within scoop window
- **Bar-stable**: No repainting issues

```
Enable ScoopTime: ☐
Seconds Before Close: 30
```

**Example:** On 5-minute chart with 30s ScoopTime:
- Candle opens at 10:00:00
- ScoopTime triggers at 10:04:30
- Entry conditions checked in final 30 seconds

### Delay Entry
Stores entry signals and executes after **X candles delay**.

**How It Works:**
1. Entry signal detected → stored with bar index
2. Waits for specified number of candles
3. Executes entry after delay period
4. Clears stored signal after execution

```
Enable Delay Entry: ☐
Delay Candles: 1
```

**Use Cases:**
- Confirm signal strength
- Wait for pullback confirmation
- Avoid false breakouts

### Cooldown System
Prevents rapid re-entry after a trade closes.

**Cooldown Types:**
- **Candles**: Wait X bars before next entry
- **Time**: Wait X minutes before next entry

**Apply To Options:**
- **Same Direction**: Separate cooldowns for long/short
- **Both Directions**: Single cooldown for all trades

```
Enable Cooldown: ☐
Cooldown Type: "Candles"
Cooldown Candles: 5
Cooldown Minutes: 60
Apply To: "Same Direction"
```

**Example - Same Direction:**
- Long trade closes at bar 100
- Next long cannot enter until bar 105
- Short trades can still enter immediately

**Example - Both Directions:**
- Any trade closes at bar 100
- No trades (long or short) until bar 105

---

## 🎯 Exit Management

### Dual Take-Profit System

#### TP1 (First Take Profit)
- **Closes**: 50% of position
- **Triggers**: Price OR time-based
- **Action**: Can move SL to breakeven

```
Enable TP1: ✓
TP1 %: 2.0%
TP1 Max Candles: 0 (disabled)
```

#### TP2 (Second Take Profit)
- **Closes**: Remaining 50% of position
- **Triggers**: Price OR time-based
- **Action**: Activates redundancy probes

```
Enable TP2: ✓
TP2 %: 4.0%
TP2 Max Candles: 0 (disabled)
```

### Stop Loss
- **Fixed Percentage**: Set distance from entry
- **Breakeven Move**: Auto-adjusts after TP1 (optional)

```
Stop Loss %: 1.5%
Move SL to Breakeven after TP1: ✓
```

### Time-Based Exits
Both TP1 and TP2 can close positions after a maximum number of candles:
- `0` = Disabled (price-only exit)
- `> 0` = Close after X candles regardless of price

**Example:**
```
TP1 Max Candles: 10
```
If TP1 price target not hit within 10 candles → close 50% anyway

### Redundancy Probes
Automated alert system that sends **repeated notifications** after trade closure.

**How It Works:**
1. Trade closes (TP or SL hit)
2. System activates probe mode
3. Sends alerts every X seconds
4. Stops after 5 probes (customizable in code)

```
Enable Redundancy Probes: ☐
Time Between Probes: 60 seconds
```

**Use Cases:**
- Webhook reliability
- API confirmation
- Logging/monitoring

---

## ⚙️ Configuration Guide

### Quick Start Preset - Conservative
```
Mode: Wide Tide
BB Length: 20
BB Multiplier: 2.0
Wide Tide Threshold: 2.5%

Use BROC Filter: ✓
BROC Threshold: 0.5°

TP1: 2.0%
TP2: 4.0%
Stop Loss: 1.5%

Trade Direction: Both
```

### Aggressive Preset
```
Mode: Distance Capture
DC Entry %: 15%

Use BROC Filter: ✓
Use UPROC Filter: ✓
Use LOROC Filter: ✓

TP1: 1.5%
TP2: 3.0%
Stop Loss: 1.0%

Enable Cooldown: ✓
Cooldown Candles: 3
```

### Scalping Preset
```
Mode: Distance Capture
DC Entry %: 10%

Use Marubozu Filter: ✓

Enable ScoopTime: ✓
Seconds Before Close: 15

TP1: 0.5%
TP2: 1.0%
Stop Loss: 0.3%

Enable Cooldown: ✓
Cooldown Type: Time
Cooldown Minutes: 15
```

---

## 📊 Performance

### Optimization Tips

#### For Backtesting Speed
- Disable unused filters
- Use higher timeframes (15m+)
- Limit lookback period
- Disable visual plots when not needed

#### For Live Trading
- Enable ScoopTime for precise entries
- Use Redundancy Probes for webhook reliability
- Set appropriate cooldown to avoid overtrading
- Monitor BROC/UPROC/LOROC angles in data window

#### For Different Market Conditions

**Trending Markets:**
- Enable BROC filter (higher threshold)
- Use Distance Capture mode (lower %)
- Wider stop loss, larger TP2

**Range-Bound Markets:**
- Use Wide Tide mode
- Enable Marubozu filter
- Tighter stop loss, smaller TPs

**High Volatility:**
- Increase BB Multiplier (2.5-3.0)
- Enable cooldown system
- Use time-based TP exits

---

## 📈 Visual Indicators

### On-Chart Elements
| Element | Color | Description |
|---------|-------|-------------|
| Upper Band | Blue | Bollinger Band upper boundary |
| Middle Band | Orange | Bollinger Band basis/SMA |
| Lower Band | Blue | Bollinger Band lower boundary |
| DC Long Level | Green circles | Distance Capture entry (long) |
| DC Short Level | Red circles | Distance Capture entry (short) |
| TP1 Level | Green cross | First take profit target |
| TP2 Level | Lime cross | Second take profit target |
| SL Level | Red cross | Stop loss level |
| Bullish Marubozu | ▲ Green | Strong bullish candle |
| Bearish Marubozu | ▼ Red | Strong bearish candle |

### Background Colors
- **Light Green**: Wide Tide long condition
- **Light Red**: Wide Tide short condition
- **Gray**: Cooldown active (no entries allowed)

---

## 🔔 Alert Conditions

### Entry Alerts
- `Long Entry` - Long position opened
- `Short Entry` - Short position opened

### Exit Alerts
- `Long TP1` - First take profit hit (long)
- `Long TP2` - Second take profit hit (long)
- `Short TP1` - First take profit hit (short)
- `Short TP2` - Second take profit hit (short)
- `Long SL` - Stop loss hit (long)
- `Short SL` - Stop loss hit (short)

### Setting Up Alerts
1. Right-click on chart
2. Add Alert → Condition: Select alert type
3. Configure message/webhook
4. For automation: Use `{{strategy.order.alert_message}}`

---

## 🛠️ Customization

### Modifying Probe Count
In the code, find this section:
```pine
// Stop probes after 5 iterations (customizable)
if probe_count >= 5
```
Change `5` to your desired number.

### Adding Custom Filters
1. Add input parameters in INPUT PARAMETERS section
2. Create filter logic after MARUBOZU FILTER section
3. Add to `all_filters_long` and `all_filters_short` conditions

### Changing Default Values
Edit the `input.*` functions in the INPUT PARAMETERS section:
```pine
bb_length = input.int(20, "BB Length", minval=1, group="Bollinger Bands")
```

---

## 📝 Changelog

### Version 1.0.0 (Current)
- ✅ Initial release
- ✅ Dual trading modes (Wide Tide, Distance Capture)
- ✅ Five filter system (BROC, UPROC, LOROC, Marubozu)
- ✅ Dual take-profit with auto SL adjustment
- ✅ ScoopTime entry mechanism
- ✅ Delay entry system
- ✅ Configurable cooldown
- ✅ Redundancy probes
- ✅ Complete alert system
- ✅ Visual indicators
- ✅ Optimized for performance

### Planned Features (v1.1.0)
- [ ] Multi-timeframe analysis
- [ ] Volume-based filters
- [ ] Trailing stop loss option
- [ ] Position sizing calculator
- [ ] Risk/reward optimizer
- [ ] Session filters (trading hours)
- [ ] Drawdown protection

---

## 🤝 Contributing

Contributions are welcome! Please follow these guidelines:

1. **Fork the repository**
2. **Create a feature branch**: `git checkout -b feature/AmazingFeature`
3. **Commit changes**: `git commit -m 'Add AmazingFeature'`
4. **Push to branch**: `git push origin feature/AmazingFeature`
5. **Open a Pull Request**

### Code Style
- Use clear variable names
- Add comments for complex logic
- Follow Pine Script v6 best practices
- Test thoroughly before submitting

---

## ⚠️ Disclaimer

**This strategy is for educational and research purposes only.**

- ❌ Not financial advice
- ❌ No guarantee of profits
- ❌ Past performance ≠ future results
- ✅ Always test on paper trading first
- ✅ Use proper risk management
- ✅ Never risk more than you can afford to lose

**Trading cryptocurrencies carries substantial risk. You are solely responsible for your trading decisions.**

---

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 📧 Contact & Support

- **Issues**: [GitHub Issues](https://github.com/yourusername/crypto-farm-bbb3/issues)
- **Discussions**: [GitHub Discussions](https://github.com/yourusername/crypto-farm-bbb3/discussions)
- **TradingView**: [Profile Link](https://www.tradingview.com/u/yourusername/)

---

## 🙏 Acknowledgments

- TradingView community for testing and feedback
- Pine Script documentation and examples
- Bollinger Band strategy research papers

---

## 📚 Additional Resources

### Learning Resources
- [TradingView Pine Script Documentation](https://www.tradingview.com/pine-script-docs/)
- [Bollinger Bands Guide](https://www.investopedia.com/terms/b/bollingerbands.asp)
- [Strategy Backtesting Best Practices](https://www.tradingview.com/support/solutions/43000481029/)

### Related Projects
- [Pine Script Strategy Template](https://github.com/)
- [TradingView Webhook Guide](https://github.com/)

---

<div align="center">

**Made with ❤️ for the TradingView community**

⭐ Star this repo if you find it helpful!

[Report Bug](https://github.com/yourusername/crypto-farm-bbb3/issues) · [Request Feature](https://github.com/yourusername/crypto-farm-bbb3/issues)

</div>