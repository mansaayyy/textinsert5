# textinsert5

MainActivity.java

package com.example.manasi.textinsert5;

import android.database.Cursor;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {

    DB_Controller db;
    EditText editText,editText2;
    TextView textView;
    Button insert,display;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        db=new DB_Controller(this);
        editText=(EditText) findViewById(R.id.edit);
        editText2=(EditText) findViewById(R.id.edit2);
        textView=(TextView) findViewById(R.id.text);
        insert=(Button) findViewById(R.id.insert);
        display=(Button) findViewById(R.id.display);
    }

    public void insert(View v){

        String text=editText.getText().toString();
        String text2=editText2.getText().toString();
        db.insert_album(text,text2);
        editText.setText("");
        editText2.setText("");
        Toast.makeText(getApplicationContext(),"Here",Toast.LENGTH_SHORT).show();
    }

    public void Display(View v){
        textView.setText("");
        Cursor smsInboxCursor = db.list_album();
        while (smsInboxCursor.moveToNext()) {
            String str = smsInboxCursor.getString(0) + "     " + smsInboxCursor.getString(1) +
                    "\n";
            textView.append(str);
        }
    }

    public void Delete(View v) {
        textView.setText("");
        String text=editText.getText().toString();
        db.deleteEntry(text);
        Toast.makeText(getApplicationContext(),"Deleted!",Toast.LENGTH_SHORT).show();
        Display(v);
    }

    public void Update(View v) {
        String oldText=editText.getText().toString();
        String newText=editText2.getText().toString();
        db.updateEntry(oldText,newText);
        Toast.makeText(getApplicationContext(),"Updated!",Toast.LENGTH_SHORT).show();
        Display(v);
    }
}


activify_main.xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center_vertical"
    tools:context="com.example.manasi.textinsert5.MainActivity">

    <EditText
        android:id="@+id/edit"
        android:layout_width="154dp"
        android:layout_height="wrap_content"
        android:ems="10"
        android:layout_marginLeft="0dp"
        android:layout_marginTop="80dp"
        android:inputType="textPersonName"
        android:hint="Enter some text" />


    <EditText
        android:id="@+id/edit2"
        android:layout_width="274dp"
        android:layout_height="wrap_content"
        android:ems="10"
        android:layout_marginLeft="0dp"
        android:inputType="textPersonName"
        android:hint="Enter text 2" />


    <Button
        android:id="@+id/insert"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="insert"
        android:text="Insert"
        android:layout_marginLeft="140dp"
        android:layout_marginTop="20dp"/>

    <Button
        android:id="@+id/display"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="Display"
        android:text="Display"
        android:layout_marginTop="0dp"
        android:layout_marginLeft="140dp"/>

    <Button
        android:id="@+id/delete"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="Delete"
        android:text="Delete"
        android:layout_marginTop="0dp"
        android:layout_marginLeft="140dp"/>

    <Button
        android:id="@+id/singleDisplay"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="DisplaySingle"
        android:text="Display Single"
        android:layout_marginTop="0dp"
        android:layout_marginLeft="140dp"/>

    <Button
        android:id="@+id/update"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="Update"
        android:text="Update"
        android:layout_marginTop="0dp"
        android:layout_marginLeft="140dp"/>

    <TextView
        android:id="@+id/text"
        android:layout_width="220dp"
        android:layout_height="156dp"
        android:layout_marginTop="20dp"
        android:layout_marginLeft="80dp" />

</LinearLayout>



DB_Controller

package com.example.manasi.textinsert5;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.widget.Toast;

/**
 * Created by Manasi on 15-10-2017.
 */

public class DB_Controller extends SQLiteOpenHelper {
    public DB_Controller(Context context){
        super(context, "Textimage35.db", null,1);
    }


    @Override
    public void onCreate(SQLiteDatabase db){

        db.execSQL("CREATE TABLE ALBUM(MSG TEXT, MSG2 TEXT)");

    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
    {
        db.execSQL("DROP TABLE IF EXISTS ALBUM ");
        onCreate(db);
    }
    public void insert_album(String Song, String Song2)
    {
        SQLiteDatabase db=this.getWritableDatabase();
        ContentValues contentvalues=new ContentValues();
        contentvalues.put("MSG",Song);
        contentvalues.put("MSG2",Song2);
        db.insert("ALBUM",null,contentvalues);
    }

    public Cursor list_album()
    {
        Cursor cursor=this.getReadableDatabase().rawQuery("SELECT * FROM ALBUM",null);
        return cursor;
    }

    public void deleteEntry(String text) {
        SQLiteDatabase db = this.getWritableDatabase();
        String where = "MSG=?";
        int numberOFEntriesDeleted = db.delete("ALBUM", where, new String[]{text});
    }

    public Cursor getSingleEntry(String text)
    {
        SQLiteDatabase db=this.getReadableDatabase();
        Cursor cursor=db.query("ALBUM", null, " MSG=?", new String[]{text}, null, null, null);
        cursor.moveToFirst();
        return cursor;
    }

    public void  updateEntry(String oldText,String newText)
    {
        SQLiteDatabase db = this.getWritableDatabase();
        // Define the updated row content.
        ContentValues updatedValues = new ContentValues();
        // Assign values for each row.
        updatedValues.put("MSG", oldText);
        updatedValues.put("MSG2", newText);

        String where="MSG = ?";
        db.update("ALBUM",updatedValues, where, new String[]{oldText});
    }
}
