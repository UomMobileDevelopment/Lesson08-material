# Lesson 08 - 09 - Mastering Graphical user interface building in Android

We will split today's work into tasks.

### Task 1: Modify list_item_forecast.xml in order to split the item in 5 areas for displaying the tomorrow and next from tomorrow days

```
<?xml version="1.0" encoding="utf-8"?>

<!-- Layout for weather forecast list item for future day (not today) -->

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:gravity="center_vertical"
    android:minHeight="?android:attr/listPreferredItemHeight"
    android:orientation="horizontal"
    android:padding="16dp">

    <ImageView
        android:id="@+id/list_item_icon"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/ic_launcher"/>

    <LinearLayout
        android:layout_height="wrap_content"
        android:layout_width="0dp"
        android:layout_weight="1"
        android:orientation="vertical"
        android:paddingLeft="16dp">

        <TextView
            android:id="@+id/list_item_date_textview"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Tomorrow"/>

        <TextView
            android:id="@+id/list_item_forecast_textview"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Clear"/>

    </LinearLayout>

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <TextView
            android:id="@+id/list_item_high_textview"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="81"/>

        <TextView
            android:id="@+id/list_item_low_textview"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="68"/>
    </LinearLayout>

</LinearLayout>

```


### Task 2: Create List item for TODAY's weather. 

```

### Create a new xml: list_item_forecast_today.xml
<?xml version="1.0" encoding="utf-8"?>

<!-- Layout for weather forecast list item for today -->

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:gravity="center_vertical"
    android:minHeight="?android:attr/listPreferredItemHeight"
    android:orientation="horizontal"
    android:padding="16dp">

    <LinearLayout
        android:layout_height="wrap_content"
        android:layout_width="0dp"
        android:layout_weight="1"
        android:orientation="vertical"
        android:gravity="center_horizontal">

        <TextView
            android:id="@+id/list_item_date_textview"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />

        <TextView
            android:id="@+id/list_item_high_textview"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>

        <TextView
            android:id="@+id/list_item_low_textview"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>
    </LinearLayout>

    <LinearLayout
        android:layout_height="wrap_content"
        android:layout_width="0dp"
        android:layout_weight="1"
        android:orientation="vertical"
        android:gravity="center_horizontal">

        <ImageView
            android:id="@+id/list_item_icon"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"/>

        <TextView
            android:id="@+id/list_item_forecast_textview"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
    </LinearLayout>
</LinearLayout>

```

Task 3: Change bindView method of ForecastAdapter

```
 @Override
    public void bindView(View view, Context context, Cursor cursor) {
        // our view is pretty simple here --- just a text view
        // we'll keep the UI functional with a simple (and slow!) binding.

        // Read weather icon ID from cursor
        int weatherId = cursor.getInt(ForecastFragment.COL_WEATHER_ID);
        // Use placeholder image for now
        ImageView iconView = (ImageView) view.findViewById(R.id.list_item_icon);
        iconView.setImageResource(R.drawable.ic_launcher);

        // TODO Read date from cursor

        // TODO Read weather forecast from cursor

        // Read user preference for metric or imperial temperature units
        boolean isMetric = Utility.isMetric(context);

        // Read high temperature from cursor
        double high = cursor.getDouble(ForecastFragment.COL_WEATHER_MAX_TEMP);
        TextView highView = (TextView) view.findViewById(R.id.list_item_high_textview);
        highView.setText(Utility.formatTemperature(high, isMetric));

        // TODO Read low temperature from cursor

    }
```


### Task 4: Add new data in strings.xml and new date formatting methods in Utility.java

```
<!-- Date label when displaying today's weather forecast [CHAR LIMIT=20] -->
    <string name="today">Today</string>

    <!-- Date label when displaying tomorrow's weather forecast [CHAR LIMIT=20] -->
    <string name="tomorrow">Tomorrow</string>

    <!-- Date format for displaying day of week and date (i.e. Mon Jun 1) [CHAR LIMIT=20] -->
    <string name="format_full_friendly_date"><xliff:g id="day">%1$s</xliff:g>, <xliff:g id="date">%2$s</xliff:g></string>
```
if Android studio complains about xliff, add this in the file resouces:
"xmlns:xliff="urn:oasis:names:tc:xliff:document:1.2"


### Utility.java

```
 // Format used for storing dates in the database.  ALso used for converting those strings
    // back into date objects for comparison/processing.
    public static final String DATE_FORMAT = "yyyyMMdd";

    /**
     * Helper method to convert the database representation of the date into something to display
     * to users.  As classy and polished a user experience as "20140102" is, we can do better.
     *
     * @param context Context to use for resource localization
     * @param dateInMillis The date in milliseconds
     * @return a user-friendly representation of the date.
     */
    public static String getFriendlyDayString(Context context, long dateInMillis) {
        // The day string for forecast uses the following logic:
        // For today: "Today, June 8"
        // For tomorrow:  "Tomorrow"
        // For the next 5 days: "Wednesday" (just the day name)
        // For all days after that: "Mon Jun 8"

        Time time = new Time();
        time.setToNow();
        long currentTime = System.currentTimeMillis();
        int julianDay = Time.getJulianDay(dateInMillis, time.gmtoff);
        int currentJulianDay = Time.getJulianDay(currentTime, time.gmtoff);

        // If the date we're building the String for is today's date, the format
        // is "Today, June 24"
        if (julianDay == currentJulianDay) {
            String today = context.getString(R.string.today);
            int formatId = R.string.format_full_friendly_date;
            return String.format(context.getString(
                    formatId,
                    today,
                    getFormattedMonthDay(context, dateInMillis)));
        } else if ( julianDay < currentJulianDay + 7 ) {
            // If the input date is less than a week in the future, just return the day name.
            return getDayName(context, dateInMillis);
        } else {
            // Otherwise, use the form "Mon Jun 3"
            SimpleDateFormat shortenedDateFormat = new SimpleDateFormat("EEE MMM dd");
            return shortenedDateFormat.format(dateInMillis);
        }
    }

    /**
     * Given a day, returns just the name to use for that day.
     * E.g "today", "tomorrow", "wednesday".
     *
     * @param context Context to use for resource localization
     * @param dateInMillis The date in milliseconds
     * @return
     */
    public static String getDayName(Context context, long dateInMillis) {
        // If the date is today, return the localized version of "Today" instead of the actual
        // day name.

        Time t = new Time();
        t.setToNow();
        int julianDay = Time.getJulianDay(dateInMillis, t.gmtoff);
        int currentJulianDay = Time.getJulianDay(System.currentTimeMillis(), t.gmtoff);
        if (julianDay == currentJulianDay) {
            return context.getString(R.string.today);
        } else if ( julianDay == currentJulianDay +1 ) {
            return context.getString(R.string.tomorrow);
        } else {
            Time time = new Time();
            time.setToNow();
            // Otherwise, the format is just the day of the week (e.g "Wednesday".
            SimpleDateFormat dayFormat = new SimpleDateFormat("EEEE");
            return dayFormat.format(dateInMillis);
        }
    }

    /**
     * Converts db date format to the format "Month day", e.g "June 24".
     * @param context Context to use for resource localization
     * @param dateInMillis The db formatted date string, expected to be of the form specified
     *                in Utility.DATE_FORMAT
     * @return The day in the form of a string formatted "December 6"
     */
    public static String getFormattedMonthDay(Context context, long dateInMillis ) {
        Time time = new Time();
        time.setToNow();
        SimpleDateFormat dbDateFormat = new SimpleDateFormat(Utility.DATE_FORMAT);
        SimpleDateFormat monthDayFormat = new SimpleDateFormat("MMMM dd");
        String monthDayString = monthDayFormat.format(dateInMillis);
        return monthDayString;
    }

```

# Task 5: Remove hardcoded values from xml views







