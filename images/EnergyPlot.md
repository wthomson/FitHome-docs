An X/Y plot of watt reading/current time is presented at the bottom of the ImpactPage when the member's status is either learning or active.  The StreamBuilder in [TBD CODE] gets a new reading when the monitor writes a reading to the DB.
# Plot Software
The X/Y plot is very simple, emphasizing the real time measurements being made over being used for analytics.  Because of this simplicity and favoring animation, we chose to use [Flutter's chart library](https://medium.com/flutter/beautiful-animated-charts-for-flutter-164940780b8c).  We use the [EnergyReading data class to express a data point](https://github.com/BitKnitting/FitHome_app/blob/master/lib/impact/energy_plot/energy_reading.dart).
```
class EnergyReading {
  DateTime dateTime;
  int watts;

  EnergyReading({this.watts, this.dateTime});

}
```
## Building the Line Plot
[Plot code in energy_plot.dart](https://github.com/BitKnitting/FitHome_app/blob/master/lib/impact/energy_plot/energy_plot.dart)

### Series
Key to Flutter's chart library is the concept of a Series.  A Series is a list of plots.  In our case, we have one plot, thus one entry into the list.  The data we provide to the `_seriesEnergyReadings_` is a list of energy readings.  The list of energy readings is contained within the class variable, `List<EnergyReading> energyReadings'.  The energyReadings list starts out empty.  Each time StreamBuilder gets a new energy reading, it is added to the energy list within the code for the energy plot.  If the energyReadings list has 10 entries, the first entry is removed and the new entry is added to the end of this list.  This way, the timeseries of energy readings scrolls from left to right.
### Chart
The chart widget is what defines the hows and whats of the plot's UI.  We're using a TimeSeriesChart.  [The gallery](https://google.github.io/charts/flutter/gallery.html) displays the wide variety of chart types available.  Clicking on one of the images brings up the code to create that particular type of chart.
#### X-Axis Markers
We wanted to include the minute:seconds going by on the X-axis.  However, [this is not possible to do](https://stackoverflow.com/questions/51964513/format-time-labels-in-charts-flutter-time-series-chart-to-include-hhmmss) with Flutter's chart package. 
