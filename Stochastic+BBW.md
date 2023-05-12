// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © Carlo_Caldura

//@version=5
indicator("Stochastic", overlay=true)
//Ampiezza bande di Bollinger
length = input.int(20, minval=1)
src = input(close, title="Source")
mult = input.float(2.0, minval=0.001, maxval=50, title="StdDev")
basis = ta.sma(src, length)
dev = mult * ta.stdev(src, length)
upper = basis + dev
lower = basis - dev
bbw = (upper-lower)/basis

// Stochastic
periodK = input.int(14, title="%K Length", minval=1, group='Stochastic')
smoothK = input.int(1, title="%K Smoothing", minval=1, group='Stochastic')
periodD = input.int(3, title="%D Smoothing", minval=1, group='Stochastic')
k = ta.sma(ta.stoch(close, high, low, periodK), smoothK)
d = ta.sma(k, periodD)


k_long  = k>80 and d>80 and d>k and bbw>0.01
k_short = k<20 and d<20 and d<k and bbw>0.01

bgcolor(k_long ?color.new(color.red  , 80):na)
bgcolor(k_short ?color.new(color.green    , 80):na)

// ————— Days of the Week Filter
day_grp = "Trading Days of Week"
use_mon = input(true, title="Monday  "    , inline="1", group=day_grp)
use_tue = input(true, title="Tuesday  "   , inline="1", group=day_grp)
use_wed = input(true, title="Wednesday  " , inline="1", group=day_grp)
use_thu = input(true, title="Thursday"    , inline="1", group=day_grp)
use_fri = input(true, title="Friday  "    , inline="2", group=day_grp)
use_sat = input(false, title="Saturday  "  , inline="2", group=day_grp)
use_sun = input(false, title="Sunday  "    , inline="2", group=day_grp)

monX = use_mon and dayofweek == dayofweek.monday    
tueX = use_tue and dayofweek == dayofweek.tuesday   
wedX = use_wed and dayofweek == dayofweek.wednesday      
thuX = use_thu and dayofweek == dayofweek.thursday      
friX = use_fri and dayofweek == dayofweek.friday    
satX = use_sat and dayofweek == dayofweek.saturday      
sunX = use_sun and dayofweek == dayofweek.sunday    

trading_days = monX or tueX or wedX or thuX or friX or satX or sunX

// Trading Session
session = input.session("0000-0000", title="Trading Session")+":1234567"
t = time(timeframe.period, session)
trading_session_filter = na(t) ? 0 : 1
