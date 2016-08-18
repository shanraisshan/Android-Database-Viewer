# Android-Database-Viewer
Utility class for viewing Android Application Database by pulling it from internal storage.
No root of android device is required.

Description
----
If you don't want to use ***adb, 3rd party library or gradle dependency etc***, and just want the database file in your ***SD Card***, Android-Database-Viewer is the solution.

Android-Database-Viewer is the utility class that pulls your application database into SD Card.

The default save path is set to **sdcard/shanraisshan/your_database_file**

*Note*
----
> Don't forget to change the name of the database according to your application database name.
> 
> You need to update the variable **DATABASE_NAME** in [DatabaseUtil.java](https://github.com/shanraisshan/Android-Database-Viewer/blob/master/DatabaseUtil.java) accordingly.

````java
final static String DATABASE_NAME = "MyDatabase.sqlite";
````

> **DATABASE_NAME** can be any name like
> 
> 1. MyDatabase.sqlite
> 
> 2. MyDatabase.db
> 
> 3. msgstore.db *(WhatsApp database name)*
>
> 4. MyDatabase *(without extension)*


Changes in Android application
----

Declare permission in your AndroidManifest.xml
````java
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
````

Import [DatabaseUtil.java](https://github.com/shanraisshan/Android-Database-Viewer/blob/master/DatabaseUtil.java) class in your project
````java
package com.util;

import android.content.Context;
import android.os.Environment;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.nio.channels.FileChannel;

/**
 * Shayan Rais (http://shanraisshan.com)
 * created on 8/17/2016
 */
public class DatabaseUtil {
    //You need to declare permission
    // <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    //in your Manifest file in order to use this class

    //______________________________________________________________________________________________

    //todo -> rename the database according to your application
    final static String DATABASE_NAME = "MyDatabase.sqlite";
    //example WhatsApp :  /data/data/com.whatsapp/databases/msgstore.db
    final static String FOLDER_EXTERNAL_DIRECTORY = Environment.getExternalStorageDirectory() + "/shanraisshan";

    //______________________________________________________________________________________________
    /**
     * Call this method from any activity in your app (
     * for example ->    DatabaseUtil.copyDatabaseToExtStg(MainActivity.this);
     * this method will copy the database of your application into SDCard folder "shanraisshan/MyDatabase.sqlite" (DATABASE_NAME)
     */
    public static void copyDatabaseToExtStg(Context ctx) {
        //external storage file
        File externalDirectory = new File(FOLDER_EXTERNAL_DIRECTORY);
        if(!externalDirectory.exists())
            externalDirectory.mkdirs();
        File toFile = new File(externalDirectory, DATABASE_NAME);
        //internal storage file
        //https://developer.android.com/reference/android/content/Context.html#getDatabasePath(java.lang.String)
        File fromFile = ctx.getDatabasePath(DATABASE_NAME);
        //example WhatsApp :  /data/data/com.whatsapp/databases/msgstore.db
        if (fromFile.exists())
            copy(fromFile, toFile);
    }


    //______________________________________________________________________________________________ Utility function
    /**
     * @param fromFile source location
     * @param toFile destination location
     * copy file from 1 location to another
     */
    static void copy(File fromFile, File toFile) {
        try {
            FileInputStream is = new FileInputStream(fromFile);
            FileChannel src = is.getChannel();
            FileOutputStream os = new FileOutputStream(toFile);
            FileChannel dst = os.getChannel();
            dst.transferFrom(src, 0, src.size());
            src.close();	is.close();
            dst.close();	os.close();
        } catch (Exception e) {
            //todo in case of exception
        }
    }
}
````

----
Now simply call ***copyDatabaseToExtStg()*** method from any activity in your app
````java
//calling from MainActivity
DatabaseUtil.copyDatabaseToExtStg(MainActivity.this);
````

----

View Database
----
Image shows **MyDatabase.sqlite** present in SD Card.

![alt tag](https://github.com/shanraisshan/Android-Database-Viewer/blob/master/!Guide/ES%20File%20Explorer.jpg)

Open Database in Desktop
----
Transfer the database file into your PC. You can view the database file using [Sqllite Studio](http://sqlitestudio.pl/files/free/stable/win32/)
![alt tag](https://github.com/shanraisshan/Android-Database-Viewer/blob/master/!Guide/Desktop.JPG)
