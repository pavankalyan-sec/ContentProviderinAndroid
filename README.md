
# Ex.No:3 Create Your Own Content Providers to get Contacts details.


## AIM:

To create your own content providers to get contacts details using Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min. required Artic Fox)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as “ex.no.3″ and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml.

Step 6: Get contacts details and Display details give in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
Program to print the text create your own content providers to get contacts details.
Developed by: Pavan Kalyan P
Registeration Number : 212225240104

```
# AndroidManifest.xml
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">
    <uses-permission android:name="android.permission.READ_CONTACTS" />
    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Exp4_contact_details">
        <activity
            android:name=".MainActivity"
            android:exported="true"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

# activity_main.xml
```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/textView"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="@string/contact_details"
        android:textSize="28sp"
        android:textStyle="bold"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <ScrollView
        android:id="@+id/scrollView2"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:layout_marginTop="16dp"
        android:layout_marginBottom="16dp"
        app:layout_constraintBottom_toTopOf="@+id/btnLoadContacts"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/textView">

        <TextView
            android:id="@+id/txtContacts"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:padding="8dp"
            android:textSize="16sp" />

    </ScrollView>

    <Button
        android:id="@+id/btnLoadContacts"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="@string/load_contacts"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

# MainActivity.java
```

package com.example.exp_4_contact_details;

import android.Manifest;
import android.content.pm.PackageManager;
import android.database.Cursor;
import android.os.Bundle;
import android.provider.ContactsContract;
import android.util.Log;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

import androidx.activity.result.ActivityResultLauncher;
import androidx.activity.result.contract.ActivityResultContracts;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.content.ContextCompat;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class MainActivity extends AppCompatActivity {

    private TextView txtContacts;

    private final ActivityResultLauncher<String> permissionLauncher =
            registerForActivityResult(
                    new ActivityResultContracts.RequestPermission(),
                    isGranted -> {
                        if (isGranted) {
                            loadContacts();
                        } else {
                            Toast.makeText(
                                    this,
                                    R.string.permission_denied,
                                    Toast.LENGTH_SHORT
                            ).show();
                        }
                    }
            );

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button btnLoadContacts = findViewById(R.id.btnLoadContacts);
        txtContacts = findViewById(R.id.txtContacts);

        btnLoadContacts.setOnClickListener(view -> {
            if (ContextCompat.checkSelfPermission(
                    this,
                    Manifest.permission.READ_CONTACTS
            ) == PackageManager.PERMISSION_GRANTED) {
                loadContacts();
            } else {
                permissionLauncher.launch(Manifest.permission.READ_CONTACTS);
            }
        });
    }

    private void loadContacts() {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        executor.execute(() -> {
            int contactCount = 0;

            try (Cursor cursor = getContentResolver().query(
                    ContactsContract.CommonDataKinds.Phone.CONTENT_URI,
                    new String[]{
                            ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME,
                            ContactsContract.CommonDataKinds.Phone.NUMBER
                    },
                    null,
                    null,
                    ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME + " ASC"
            )) {
                if (cursor != null) {
                    int nameIndex = cursor.getColumnIndex(
                            ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME
                    );
                    int numberIndex = cursor.getColumnIndex(
                            ContactsContract.CommonDataKinds.Phone.NUMBER
                    );

                    while (cursor.moveToNext()) {
                        String name = nameIndex != -1 ? cursor.getString(nameIndex) : "Unknown";
                        String number = numberIndex != -1 ? cursor.getString(numberIndex) : "";

                        // Log contact details to Logcat
                        Log.i(
                                "CONTACT_DETAILS",
                                "Contact Name ::: " + name +
                                        " Phone Number ::: " + number
                        );

                        contactCount++;
                    }
                }
            } catch (Exception e) {
                Log.e("CONTACT_DETAILS", "Error loading contacts", e);
            }

            final int count = contactCount;
            runOnUiThread(() -> {
                if (count == 0) {
                    txtContacts.setText(R.string.no_contacts_found);
                    Toast.makeText(this, R.string.no_contacts_found, Toast.LENGTH_SHORT).show();
                } else {
                    txtContacts.setText(R.string.contacts_logged);
                    Toast.makeText(this, count + " contact(s) printed to Logcat", Toast.LENGTH_SHORT).show();
                }
            });
        });
        executor.shutdown();
    }
}
```

## OUTPUT

<img width="1917" height="1078" alt="Screenshot 2026-11-02 090147" src="https://github.com/user-attachments/assets/6eb5c58c-bd79-4619-bb62-5071d702e9ab" />


<img width="1917" height="1078" alt="Screenshot 2026-11-02 090209" src="https://github.com/user-attachments/assets/ca7511d2-3210-4408-8344-0dabc350232c" />


<img width="1917" height="1077" alt="Screenshot 2026-11-02 090245" src="https://github.com/user-attachments/assets/098ee5eb-bf4d-43f4-a159-da8680e6f17d" />


## RESULT
Thus a Simple Android Application create your own content providers to get contacts details using Android Studio is developed and executed successfully.
