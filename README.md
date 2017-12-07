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

### Task 5: Remove hardcoded values from xml views


### Task 6: Using view types in Forecast Adapter in order to render differnt layout for today's weather


```
public class ForecastAdapter extends CursorAdapter {
    public ForecastAdapter(Context context, Cursor c, int flags) {
        super(context, c, flags);
    }

    private static final int VIEW_TYPE_COUNT = 2;
    private static final int VIEW_TYPE_TODAY = 0;
    private static final int VIEW_TYPE_FUTURE_DAY = 1;

    /*
        Remember that these views are reused as needed.
     */
    @Override
    public View newView(Context context, Cursor cursor, ViewGroup parent) {
        // Choose the layout type
        int viewType = getItemViewType(cursor.getPosition());
        int layoutId = -1;
        switch (viewType) {
            case VIEW_TYPE_TODAY: {
                layoutId = R.layout.list_item_forecast_today;
                break;
            }
            case VIEW_TYPE_FUTURE_DAY: {
                layoutId = R.layout.list_item_forecast;
                break;
            }
        }
        return LayoutInflater.from(context).inflate(layoutId, parent, false);
    }

    /*
        This is where we fill-in the views with the contents of the cursor.
     */
    @Override
    public void bindView(View view, Context context, Cursor cursor) {
        // our view is pretty simple here --- just a text view
        // we'll keep the UI functional with a simple (and slow!) binding.

        // Read weather icon ID from cursor
        int weatherId = cursor.getInt(ForecastFragment.COL_WEATHER_ID);
        // Use placeholder image for now
        ImageView iconView = (ImageView) view.findViewById(R.id.list_item_icon);
        iconView.setImageResource(R.drawable.ic_launcher);

        // Read date from cursor
        long dateInMillis = cursor.getLong(ForecastFragment.COL_WEATHER_DATE);
        // Find TextView and set formatted date on it
        TextView dateView = (TextView) view.findViewById(R.id.list_item_date_textview);
        dateView.setText(Utility.getFriendlyDayString(context, dateInMillis));

        // Read weather forecast from cursor
        String description = cursor.getString(ForecastFragment.COL_WEATHER_DESC);
        // Find TextView and set weather forecast on it
        TextView descriptionView = (TextView) view.findViewById(R.id.list_item_forecast_textview);
        descriptionView.setText(description);

        // Read user preference for metric or imperial temperature units
        boolean isMetric = Utility.isMetric(context);

        // Read high temperature from cursor
        double high = cursor.getDouble(ForecastFragment.COL_WEATHER_MAX_TEMP);
        TextView highView = (TextView) view.findViewById(R.id.list_item_high_textview);
        highView.setText(Utility.formatTemperature(high, isMetric));

        // Read low temperature from cursor
        double low = cursor.getDouble(ForecastFragment.COL_WEATHER_MIN_TEMP);
        TextView lowView = (TextView) view.findViewById(R.id.list_item_low_textview);
        lowView.setText(Utility.formatTemperature(low, isMetric));
    }

    @Override
    public int getItemViewType(int position) {
        return position == 0 ? VIEW_TYPE_TODAY : VIEW_TYPE_FUTURE_DAY;
    }

    @Override
    public int getViewTypeCount() {
        return VIEW_TYPE_COUNT;
    }
}

```

### Task 7: Introduce the View Holder pattern

```
  /**
     * Cache of the children views for a forecast list item.
     */
    public static class ViewHolder {
        public final ImageView iconView;
        public final TextView dateView;
        public final TextView descriptionView;
        public final TextView highTempView;
        public final TextView lowTempView;

        public ViewHolder(View view) {
            iconView = (ImageView) view.findViewById(R.id.list_item_icon);
            dateView = (TextView) view.findViewById(R.id.list_item_date_textview);
            descriptionView = (TextView) view.findViewById(R.id.list_item_forecast_textview);
            highTempView = (TextView) view.findViewById(R.id.list_item_high_textview);
            lowTempView = (TextView) view.findViewById(R.id.list_item_low_textview);
        }
    }
    
    
    ////////////
    
       @Override
    public View newView(Context context, Cursor cursor, ViewGroup parent) {
    //.......

        View view = LayoutInflater.from(context).inflate(layoutId, parent, false);

        ViewHolder viewHolder = new ViewHolder(view);
        view.setTag(viewHolder);

        return view;
    }
    
    /////////////
    
     @Override
    public void bindView(View view, Context context, Cursor cursor) {

        ViewHolder viewHolder = (ViewHolder) view.getTag();

        // Use placeholder image for now
        viewHolder.iconView.setImageResource(R.drawable.ic_launcher);

        // Read date from cursor
        long dateInMillis = cursor.getLong(ForecastFragment.COL_WEATHER_DATE);
        // Find TextView and set formatted date on it
        viewHolder.dateView.setText(Utility.getFriendlyDayString(context, dateInMillis));

        // Read weather forecast from cursor
        String description = cursor.getString(ForecastFragment.COL_WEATHER_DESC);
        // Find TextView and set weather forecast on it
        viewHolder.descriptionView.setText(description);

        // Read user preference for metric or imperial temperature units
        boolean isMetric = Utility.isMetric(context);

        // Read high temperature from cursor
        double high = cursor.getDouble(ForecastFragment.COL_WEATHER_MAX_TEMP);
        viewHolder.highTempView.setText(Utility.formatTemperature(high, isMetric));

        // Read low temperature from cursor
        double low = cursor.getDouble(ForecastFragment.COL_WEATHER_MIN_TEMP);
        viewHolder.lowTempView.setText(Utility.formatTemperature(low, isMetric));
    }
    
```

### Task 8: Implementing the GUI for the Detail Activity

- Edit the fragment_detail.xml:

```
<!-- Master layout. -->
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <!-- Header: Day, Date -->
        <TextView
            android:id="@+id/detail_day_textview"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Tomorrow" />

        <TextView
            android:id="@+id/detail_date_textview"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="June 24"/>

        <!-- Main content: high, low, art, weather state -->
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_marginTop="16dp"
            android:orientation="horizontal">

            <LinearLayout
                android:layout_width="0dp"
                android:layout_weight="1"
                android:layout_height="wrap_content"
                android:orientation="vertical">

                <TextView
                    android:id="@+id/detail_high_textview"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="21°"/>

                <TextView
                    android:id="@+id/detail_low_textview"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="11°" />
            </LinearLayout>

            <LinearLayout
                android:layout_width="0dp"
                android:layout_weight="1"
                android:layout_height="wrap_content"
                android:gravity="center_horizontal"
                android:orientation="vertical">

                <ImageView
                    android:id="@+id/detail_icon"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:src="@drawable/ic_launcher" />

                <TextView
                    android:id="@+id/detail_forecast_textview"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="Clear" />
            </LinearLayout>
        </LinearLayout>

        <!-- Humidity, wind, pressure -->
        <TextView
            android:id="@+id/detail_humidity_textview"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="48%" />

        <TextView
            android:id="@+id/detail_wind_textview"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="6 km/h NW" />

        <TextView
            android:id="@+id/detail_pressure_textview"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="1014" />
    </LinearLayout>
</ScrollView>
```

- We'll need some helper methods for the wind direction in Utility.java

```
public static String getFormattedWind(Context context, float windSpeed, float degrees) {
    int windFormat;
    if (Utility.isMetric(context)) {
        windFormat = R.string.format_wind_kmh;
    } else {
        windFormat = R.string.format_wind_mph;
        windSpeed = .621371192237334f * windSpeed;
    }
 
    // From wind direction in degrees, determine compass direction as a string (e.g NW)
    // You know what's fun, writing really long if/else statements with tons of possible
    // conditions.  Seriously, try it!
    String direction = "Unknown";
    if (degrees >= 337.5 || degrees < 22.5) {
        direction = "N";
    } else if (degrees >= 22.5 && degrees < 67.5) {
        direction = "NE";
    } else if (degrees >= 67.5 && degrees < 112.5) {
        direction = "E";
    } else if (degrees >= 112.5 && degrees < 157.5) {
        direction = "SE";
    } else if (degrees >= 157.5 && degrees < 202.5) {
        direction = "S";
    } else if (degrees >= 202.5 && degrees < 247.5) {
        direction = "SW";
    } else if (degrees >= 247.5 && degrees < 292.5) {
        direction = "W";
    } else if (degrees >= 292.5 || degrees < 22.5) {
        direction = "NW";
    }
    return String.format(context.getString(windFormat), windSpeed, direction);
}

//////////////////////////////////////////

```

- new data in strings.xml

```
<!-- Strings for formatting weather-related data -->
<!-- Temperature format [CHAR LIMIT=5] -->
<string name="format_temperature"><xliff:g id="temp">%1.0f</xliff:g>\u00B0</string>
 
<!-- Windspeed formats -->
<!-- Wind in mph [CHAR LIMIT=25] -->
<string name="format_wind_mph">
    Wind: <xliff:g id="speed">%1$1.0f</xliff:g> mph <xliff:g id="direction">%2$s</xliff:g>
</string>
 
<!-- Wind in kph [CHAR LIMIT=25] -->
<string name="format_wind_kmh">
    Wind: <xliff:g id="speed">%1$1.0f</xliff:g> km/h <xliff:g id="direction">%2$s</xliff:g>
</string>
 
<!-- Pressure format CHAR LIMIT=25] -->
<string name="format_pressure">Pressure: <xliff:g id="pressure">%1.0f</xliff:g> hPa</string>
 
<!-- Humidity format CHAR LIMIT=25]-->
<string name="format_humidity">Humidity: <xliff:g id="humidity">%1.0f</xliff:g> %%</string>
```


- Pull Up DetailFragment from DetailActivity and  create a new Class: DetailFragment.java

```
package com.example.android.sunshine.app;

import android.content.Intent;
import android.database.Cursor;
import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.support.v4.app.LoaderManager;
import android.support.v4.content.CursorLoader;
import android.support.v4.content.Loader;
import android.support.v4.view.MenuItemCompat;
import android.support.v7.widget.ShareActionProvider;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.Menu;
import android.view.MenuInflater;
import android.view.MenuItem;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.TextView;

import com.example.android.sunshine.app.data.WeatherContract;
import com.example.android.sunshine.app.data.WeatherContract.WeatherEntry;

/**
 * A placeholder fragment containing a simple view.
 */
public class DetailFragment extends Fragment implements LoaderManager.LoaderCallbacks<Cursor> {

    private static final String LOG_TAG = DetailFragment.class.getSimpleName();

    private static final String FORECAST_SHARE_HASHTAG = " #SunshineApp";

    private ShareActionProvider mShareActionProvider;
    private String mForecast;

    private static final int DETAIL_LOADER = 0;

    private static final String[] DETAIL_COLUMNS = {
            WeatherEntry.TABLE_NAME + "." + WeatherEntry._ID,
            WeatherEntry.COLUMN_DATE,
            WeatherEntry.COLUMN_SHORT_DESC,
            WeatherEntry.COLUMN_MAX_TEMP,
            WeatherEntry.COLUMN_MIN_TEMP,
            WeatherEntry.COLUMN_HUMIDITY,
            WeatherEntry.COLUMN_PRESSURE,
            WeatherEntry.COLUMN_WIND_SPEED,
            WeatherEntry.COLUMN_DEGREES,
            WeatherEntry.COLUMN_WEATHER_ID,
            // This works because the WeatherProvider returns location data joined with
            // weather data, even though they're stored in two different tables.
            WeatherContract.LocationEntry.COLUMN_LOCATION_SETTING
    };

    // These indices are tied to DETAIL_COLUMNS.  If DETAIL_COLUMNS changes, these
    // must change.
    public static final int COL_WEATHER_ID = 0;
    public static final int COL_WEATHER_DATE = 1;
    public static final int COL_WEATHER_DESC = 2;
    public static final int COL_WEATHER_MAX_TEMP = 3;
    public static final int COL_WEATHER_MIN_TEMP = 4;
    public static final int COL_WEATHER_HUMIDITY = 5;
    public static final int COL_WEATHER_PRESSURE = 6;
    public static final int COL_WEATHER_WIND_SPEED = 7;
    public static final int COL_WEATHER_DEGREES = 8;
    public static final int COL_WEATHER_CONDITION_ID = 9;

    private ImageView mIconView;
    private TextView mFriendlyDateView;
    private TextView mDateView;
    private TextView mDescriptionView;
    private TextView mHighTempView;
    private TextView mLowTempView;
    private TextView mHumidityView;
    private TextView mWindView;
    private TextView mPressureView;

    public DetailFragment() {
        setHasOptionsMenu(true);
    }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        View rootView = inflater.inflate(R.layout.fragment_detail, container, false);
        mIconView = (ImageView) rootView.findViewById(R.id.detail_icon);
        mDateView = (TextView) rootView.findViewById(R.id.detail_date_textview);
        mFriendlyDateView = (TextView) rootView.findViewById(R.id.detail_day_textview);
        mDescriptionView = (TextView) rootView.findViewById(R.id.detail_forecast_textview);
        mHighTempView = (TextView) rootView.findViewById(R.id.detail_high_textview);
        mLowTempView = (TextView) rootView.findViewById(R.id.detail_low_textview);
        mHumidityView = (TextView) rootView.findViewById(R.id.detail_humidity_textview);
        mWindView = (TextView) rootView.findViewById(R.id.detail_wind_textview);
        mPressureView = (TextView) rootView.findViewById(R.id.detail_pressure_textview);
        return rootView;
    }

    @Override
    public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) {
        // Inflate the menu; this adds items to the action bar if it is present.
        inflater.inflate(R.menu.detailfragment, menu);

        // Retrieve the share menu item
        MenuItem menuItem = menu.findItem(R.id.action_share);

        // Get the provider and hold onto it to set/change the share intent.
        mShareActionProvider = (ShareActionProvider) MenuItemCompat.getActionProvider(menuItem);

        // If onLoadFinished happens before this, we can go ahead and set the share intent now.
        if (mForecast != null) {
            mShareActionProvider.setShareIntent(createShareForecastIntent());
        }
    }

    private Intent createShareForecastIntent() {
        Intent shareIntent = new Intent(Intent.ACTION_SEND);
        shareIntent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_WHEN_TASK_RESET);
        shareIntent.setType("text/plain");
        shareIntent.putExtra(Intent.EXTRA_TEXT, mForecast + FORECAST_SHARE_HASHTAG);
        return shareIntent;
    }

    @Override
    public void onActivityCreated(Bundle savedInstanceState) {
        getLoaderManager().initLoader(DETAIL_LOADER, null, this);
        super.onActivityCreated(savedInstanceState);
    }

    @Override
    public Loader<Cursor> onCreateLoader(int id, Bundle args) {
        Log.v(LOG_TAG, "In onCreateLoader");
        Intent intent = getActivity().getIntent();
        if (intent == null) {
            return null;
        }

        // Now create and return a CursorLoader that will take care of
        // creating a Cursor for the data being displayed.
        return new CursorLoader(
                getActivity(),
                intent.getData(),
                DETAIL_COLUMNS,
                null,
                null,
                null
        );
    }

    @Override
    public void onLoadFinished(Loader<Cursor> loader, Cursor data) {
        if (data != null && data.moveToFirst()) {
            // Read weather condition ID from cursor
            int weatherId = data.getInt(COL_WEATHER_CONDITION_ID);
            // Use placeholder Image
            mIconView.setImageResource(R.drawable.ic_launcher);

            // Read date from cursor and update views for day of week and date
            long date = data.getLong(COL_WEATHER_DATE);
            String friendlyDateText = Utility.getDayName(getActivity(), date);
            String dateText = Utility.getFormattedMonthDay(getActivity(), date);
            mFriendlyDateView.setText(friendlyDateText);
            mDateView.setText(dateText);

            // Read description from cursor and update view
            String description = data.getString(COL_WEATHER_DESC);
            mDescriptionView.setText(description);

            // Read high temperature from cursor and update view
            boolean isMetric = Utility.isMetric(getActivity());

            double high = data.getDouble(COL_WEATHER_MAX_TEMP);
            String highString = Utility.formatTemperature(getActivity(), high, isMetric);
            mHighTempView.setText(highString);

            // Read low temperature from cursor and update view
            double low = data.getDouble(COL_WEATHER_MIN_TEMP);
            String lowString = Utility.formatTemperature(getActivity(), low, isMetric);
            mLowTempView.setText(lowString);

            // Read humidity from cursor and update view
            float humidity = data.getFloat(COL_WEATHER_HUMIDITY);
            mHumidityView.setText(getActivity().getString(R.string.format_humidity, humidity));

            // Read wind speed and direction from cursor and update view
            float windSpeedStr = data.getFloat(COL_WEATHER_WIND_SPEED);
            float windDirStr = data.getFloat(COL_WEATHER_DEGREES);
            mWindView.setText(Utility.getFormattedWind(getActivity(), windSpeedStr, windDirStr));

            // Read pressure from cursor and update view
            float pressure = data.getFloat(COL_WEATHER_PRESSURE);
            mPressureView.setText(getActivity().getString(R.string.format_pressure, pressure));

            // We still need this for the share intent
            mForecast = String.format("%s - %s - %s/%s", dateText, description, high, low);

            // If onCreateOptionsMenu has already happened, we need to update the share intent now.
            if (mShareActionProvider != null) {
                mShareActionProvider.setShareIntent(createShareForecastIntent());
            }
        }
    }

    @Override
    public void onLoaderReset(Loader<Cursor> loader) { }
}
```

### Task 9: Add official icons to app

- download icons from [here](https://github.com/udacity/Sunshine-Version-2/tree/assets) and copy them in the app 

- update launcher icon in AdnroidManifest.xml

```
<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        ....
        
 ```

- integrate weather app in forecast list and forecast detail screen

```
/**
 * Helper method to provide the icon resource id according to the weather condition id returned
 * by the OpenWeatherMap call.
 * @param weatherId from OpenWeatherMap API response
 * @return resource id for the corresponding icon. -1 if no relation is found.
 */
public static int getIconResourceForWeatherCondition(int weatherId) {
    // Based on weather code data found at:
    // http://bugs.openweathermap.org/projects/api/wiki/Weather_Condition_Codes
    if (weatherId >= 200 && weatherId <= 232) {
        return R.drawable.ic_storm;
    } else if (weatherId >= 300 && weatherId <= 321) {
        return R.drawable.ic_light_rain;
    } else if (weatherId >= 500 && weatherId <= 504) {
        return R.drawable.ic_rain;
    } else if (weatherId == 511) {
        return R.drawable.ic_snow;
    } else if (weatherId >= 520 && weatherId <= 531) {
        return R.drawable.ic_rain;
    } else if (weatherId >= 600 && weatherId <= 622) {
        return R.drawable.ic_snow;
    } else if (weatherId >= 701 && weatherId <= 761) {
        return R.drawable.ic_fog;
    } else if (weatherId == 761 || weatherId == 781) {
        return R.drawable.ic_storm;
    } else if (weatherId == 800) {
        return R.drawable.ic_clear;
    } else if (weatherId == 801) {
        return R.drawable.ic_light_clouds;
    } else if (weatherId >= 802 && weatherId <= 804) {
        return R.drawable.ic_cloudy;
    }
    return -1;
}

/**
 * Helper method to provide the art resource id according to the weather condition id returned
 * by the OpenWeatherMap call.
 * @param weatherId from OpenWeatherMap API response
 * @return resource id for the corresponding image. -1 if no relation is found.
 */
public static int getArtResourceForWeatherCondition(int weatherId) {
    // Based on weather code data found at:
    // http://bugs.openweathermap.org/projects/api/wiki/Weather_Condition_Codes
    if (weatherId >= 200 && weatherId <= 232) {
        return R.drawable.art_storm;
    } else if (weatherId >= 300 && weatherId <= 321) {
        return R.drawable.art_light_rain;
    } else if (weatherId >= 500 && weatherId <= 504) {
        return R.drawable.art_rain;
    } else if (weatherId == 511) {
        return R.drawable.art_snow;
    } else if (weatherId >= 520 && weatherId <= 531) {
        return R.drawable.art_rain;
    } else if (weatherId >= 600 && weatherId <= 622) {
        return R.drawable.art_rain;
    } else if (weatherId >= 701 && weatherId <= 761) {
        return R.drawable.art_fog;
    } else if (weatherId == 761 || weatherId == 781) {
        return R.drawable.art_storm;
    } else if (weatherId == 800) {
        return R.drawable.art_clear;
    } else if (weatherId == 801) {
        return R.drawable.art_light_clouds;
    } else if (weatherId >= 802 && weatherId <= 804) {
        return R.drawable.art_clouds;
    }
    return -1;
}
```


Hint #1: 
Make sure the Cursor projection in ForecastFragment and DetailFragment includes WeatherContract.WeatherEntry.COLUMN_WEATHER_ID to retrieve the weather condition ID. You can pass this into the provided helper function to find the weather icon drawable to display in the UI.


Hint #2: 
In ForecastAdapter.bindView() you may need to retrieve the item view type using getItemViewType(cursor.getPosition()).
```
@Override
    public void bindView(View view, Context context, Cursor cursor) {

        ViewHolder viewHolder = (ViewHolder) view.getTag();

        int viewType = getItemViewType(cursor.getPosition());
        switch (viewType) {
            case VIEW_TYPE_TODAY: {
                // Get weather icon
                viewHolder.iconView.setImageResource(Utility.getArtResourceForWeatherCondition(
                        cursor.getInt(ForecastFragment.COL_WEATHER_CONDITION_ID)));
                break;
            }
            case VIEW_TYPE_FUTURE_DAY: {
                // Get weather icon
                viewHolder.iconView.setImageResource(Utility.getIconResourceForWeatherCondition(
                        cursor.getInt(ForecastFragment.COL_WEATHER_CONDITION_ID)));
                break;
            }
        }
        ```` 

