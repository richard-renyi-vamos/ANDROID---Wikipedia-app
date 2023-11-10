Default (GPT-3.5)
Custom instructions details

User
create an android code that shows a random wikipedia article
ChatGPT
Certainly! To create an Android app that shows a random Wikipedia article, you can use the Wikipedia API to fetch a random article and then display it in your app. Here's a simplified example using Java and Android Studio:

Add Internet Permission:
Make sure to add the internet permission to your AndroidManifest.xml file.

xml
Copy code
<uses-permission android:name="android.permission.INTERNET" />
Create a Layout (activity_main.xml):
Create a layout file with a button to trigger the random article retrieval.

xml
Copy code
<!-- activity_main.xml -->
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/btnRandomArticle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Random Wikipedia Article"
        android:layout_centerInParent="true" />

</RelativeLayout>
Implement MainActivity (MainActivity.java):
In your MainActivity.java, handle the button click and make a request to the Wikipedia API.

java
Copy code
import android.os.AsyncTask;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import org.json.JSONException;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class MainActivity extends AppCompatActivity {

    private Button btnRandomArticle;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        btnRandomArticle = findViewById(R.id.btnRandomArticle);
        btnRandomArticle.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                new FetchRandomArticleTask().execute();
            }
        });
    }

    private class FetchRandomArticleTask extends AsyncTask<Void, Void, String> {

        @Override
        protected String doInBackground(Void... voids) {
            try {
                URL url = new URL("https://en.wikipedia.org/w/api.php?action=query&format=json&list=random&rnnamespace=0&rnlimit=1");
                HttpURLConnection connection = (HttpURLConnection) url.openConnection();
                BufferedReader reader = new BufferedReader(new InputStreamReader(connection.getInputStream()));
                StringBuilder result = new StringBuilder();
                String line;

                while ((line = reader.readLine()) != null) {
                    result.append(line);
                }

                reader.close();
                return result.toString();
            } catch (IOException e) {
                e.printStackTrace();
            }
            return null;
        }

        @Override
        protected void onPostExecute(String s) {
            super.onPostExecute(s);
            if (s != null) {
                try {
                    JSONObject json = new JSONObject(s);
                    JSONObject query = json.getJSONObject("query");
                    JSONObject page = query.getJSONArray("random").getJSONObject(0);

                    String title = page.getString("title");
                    String url = "https://en.wikipedia.org/wiki/" + title.replace(" ", "_");

                    // Display the title or open the URL in a WebView
                    Toast.makeText(MainActivity.this, "Random Article: " + title, Toast.LENGTH_LONG).show();
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            } else {
                Toast.makeText(MainActivity.this, "Failed to fetch random article", Toast.LENGTH_SHORT).show();
            }
        }
    }
}
This is a basic example, and you might want to enhance error handling, add a WebView to display the article content, or provide a more polished user interface. Additionally, keep in mind that making requests to the Wikipedia API may be subject to rate limiting, so consider implementing proper error handling for API requests.
