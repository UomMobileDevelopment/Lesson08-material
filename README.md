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


### Task 2: Create List item for TODAY's weather. Create a new xml: list_item_forecast_today.xml

```
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







