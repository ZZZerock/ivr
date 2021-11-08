package com.it.it;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

public class calcResult extends AppCompatActivity {
    private TextView typeTextView;
    private TextView subjectTextView;
    private TextView markTextView;
    private Button returnButton;
    private Double DesiredGrade = new Double(0);
    private TextView num;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_calc_result);

        returnButton =(Button) findViewById(R.id.Return);
        returnButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent=new Intent(calcResult.this,calculator.class);
                startActivity(intent);
            }
        });

        num=findViewById(R.id.kolvoocenok);
        typeTextView =(TextView) findViewById(R.id.typeTextView);
        subjectTextView =(TextView) findViewById(R.id.subjectTextView);
        markTextView =(TextView) findViewById(R.id.markTextView);

        typeTextView.setText(userInfo.calcDesiredGrade[1]);
        subjectTextView.setText(userInfo.calcDesiredGrade[2]);
        markTextView.setText(userInfo.calcDesiredGrade[0]);

        if(Integer.valueOf(userInfo.calcDesiredGrade[0]) == 5){
            DesiredGrade=4.5;
        }
        else if(Integer.valueOf(userInfo.calcDesiredGrade[0]) == 4){
            DesiredGrade=3.5;
        }
        else {
            DesiredGrade=2.5;
        }

        Integer[] result = userInfo.getWishMarks(userInfo.calcDesiredGrade[2], DesiredGrade, userInfo.calcDesiredGrade[1]);
        if(result.length == 0) {
            String ErrText = "Сожалеем, это невозможно.";
            Toast toast = Toast.makeText(getApplicationContext(), ErrText, Toast.LENGTH_LONG);
            toast.show();
        } else {
            num.setText("5:"+result[0]+" 4:"+result[1]+" 3:"+result[0]);
        }

    }





}






package com.it.it;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Spinner;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import java.util.ArrayList;

public class calculator extends AppCompatActivity {
    private Button returnButton;
    Spinner subjectSpinner;
    ArrayAdapter subjectAdapter;
    EditText desiredGradeEditText;
    Spinner typeChoiceSpinner;
    ArrayAdapter typeAdapter;
    Button resultButton;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_calculator);
        setTitle("Калькулятор");

        subjectSpinner = findViewById(R.id.subjectSpinner);

        desiredGradeEditText = (EditText) findViewById(R.id.desiredGrade);

        userInfo.getUserMarks(); // Запрашиваем оценки за последнюю неделю

        ArrayList<String> subjectList = userInfo.getSubjects();
        subjectAdapter = new ArrayAdapter(this, android.R.layout.simple_spinner_item,subjectList);
        subjectAdapter.setDropDownViewResource(android.R.layout.simple_dropdown_item_1line);
        subjectSpinner.setAdapter(subjectAdapter);

        typeChoiceSpinner = findViewById(R.id.typeChoice);
        ArrayList<String> typeList = userInfo.getMarkTypes();

        typeAdapter = new ArrayAdapter(this, android.R.layout.simple_spinner_item,typeList);
        typeAdapter.setDropDownViewResource(android.R.layout.simple_dropdown_item_1line);
        typeChoiceSpinner.setAdapter(typeAdapter);

        resultButton = findViewById(R.id.calcResult);
        resultButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if(desiredGradeEditText.getText().toString().equals("") || desiredGradeEditText.getText().toString().equals(null)) {
                    String ErrText = "Необходимо указать желаемую оценку!";
                    Toast toast = Toast.makeText(getApplicationContext(), ErrText, Toast.LENGTH_LONG);
                    toast.show();
                } else {
                    userInfo.calcDesiredGrade[0] = desiredGradeEditText.getText().toString();
                    userInfo.calcDesiredGrade[1] = typeChoiceSpinner.getSelectedItem().toString();
                    userInfo.calcDesiredGrade[2] = subjectSpinner.getSelectedItem().toString();
                    Intent intent=new Intent(calculator.this, calcResult.class);
                    startActivity(intent);
                }
            }
        });

        returnButton =(Button) findViewById(R.id.Return);
        returnButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openActivitymenu();
            }
        });
    }
    public void openActivitymenu(){
        Intent intent=new Intent(this, mainMenu.class);
        startActivity(intent);
    }

}




package com.it.it;

import android.graphics.Color;
import android.os.Bundle;

import androidx.appcompat.app.AppCompatActivity;

import com.github.mikephil.charting.charts.LineChart;
import com.github.mikephil.charting.components.XAxis;
import com.github.mikephil.charting.data.Entry;
import com.github.mikephil.charting.data.LineData;
import com.github.mikephil.charting.data.LineDataSet;

import java.util.ArrayList;

public class chart extends AppCompatActivity {
    private LineChart marksChart;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_chart);

        marksChart = findViewById(R.id. Linechart);
        ArrayList<Entry> entries = new ArrayList<>();

        ArrayList<Marks> marks = userInfo.marks.get(userInfo.subject);
        for (int i = 0; i < marks.size(); i++) {
            entries.add(new Entry(i,Integer.valueOf(marks.get(i).getMark())));
        }

        ArrayList<String> labels = new ArrayList<String>();
        for (int i = 0; i < marks.size(); i++) {
            labels.add(marks.get(i).getDate().toString());
        }

        LineDataSet dataset = new LineDataSet(entries, userInfo.subject); // Создаем линию
        LineData data = new LineData(dataset);
        dataset.setColor(Color.GREEN); // Настраиваем внешний вид линии
        dataset.setMode(LineDataSet.Mode.CUBIC_BEZIER);
        dataset.setValueTextSize(10f);

        XAxis xAxis = marksChart.getXAxis(); // Настраиваем внешний вид оси x
        xAxis.setDrawLabels(false);
        xAxis.setDrawAxisLine(false);
        xAxis.setDrawGridLines(false);

        marksChart.animateY(500); // Включаем анимированное построение
        marksChart.setData(data);
        marksChart.invalidate();

    }
}








package com.it.it;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.Spinner;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import java.util.ArrayList;
import java.util.HashMap;

public class chartDataSelection extends AppCompatActivity {
    private Button returnButton;
    Spinner firstDateSpinner;
    ArrayAdapter firstDateAdapter;
    ArrayList monthList;
    Spinner secondDateSpinner;
    ArrayAdapter secondDateAdapter;
    HashMap<String,Integer> monthNum = new HashMap<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_chart_data_selection);
        setTitle("Диаграмма");

        monthNum.put("Сентябрь",1);
        monthNum.put("Октябрь",2);
        monthNum.put("Ноябрь",3);
        monthNum.put("Декабрь",4);
        monthNum.put("Январь",5);
        monthNum.put("Февраль",6);
        monthNum.put("Март",7);
        monthNum.put("Апрель",8);
        monthNum.put("Май",9);

        //дата начала
        firstDateSpinner =findViewById(R.id.firstDateSpinner);
        monthList=new ArrayList();
        monthList.add("Сентябрь");
        monthList.add("Октябрь");
        monthList.add("Ноябрь");
        monthList.add("Декабрь");
        monthList.add("Январь");
        monthList.add("Февраль");
        monthList.add("Март");
        monthList.add("Апрель");
        monthList.add("Май");
        firstDateAdapter =new ArrayAdapter(this, android.R.layout.simple_spinner_item,monthList);
        firstDateAdapter.setDropDownViewResource(android.R.layout.simple_dropdown_item_1line);
        firstDateSpinner.setAdapter(firstDateAdapter);
        // дата конца
        secondDateSpinner=findViewById(R.id.secondDateSpinner);

        secondDateAdapter =new ArrayAdapter(this, android.R.layout.simple_spinner_item,monthList);
        secondDateAdapter.setDropDownViewResource(android.R.layout.simple_dropdown_item_1line);
        secondDateSpinner.setAdapter(secondDateAdapter);

        returnButton =(Button) findViewById(R.id.Return);
        returnButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openActivityMenu();
            }
        });
    }
    public void openActivityMenu(){
        Intent intent=new Intent(this, mainMenu.class);
        startActivity(intent);
    }

    public void choiceChartDate(View view) {
        if(monthNum.get(firstDateSpinner.getSelectedItem().toString()) > monthNum.get(secondDateSpinner.getSelectedItem().toString())) {
            String result = "0";
            String errMsg = "Конечная дата не может быть меньше начальной";
            userInfo.marksArgs[0] = result;
            userInfo.marksArgs[1] = errMsg;
            String ErrText = "Ошибка! Код ошибки " + userInfo.marksArgs[0] + " Ошибка: " + userInfo.marksArgs[1];
            Toast toast = Toast.makeText(getApplicationContext(), ErrText, Toast.LENGTH_LONG);
            toast.show();
        } else {
            userInfo.getUserMarks(firstDateSpinner.getSelectedItem().toString(),secondDateSpinner.getSelectedItem().toString()); // Запрашиваем оценки и предметы
            //if(Integer.valueOf(userInfo.marksArgs[0])==200) {
                Intent intent=new Intent(chartDataSelection.this, chartSubjectSelection.class);
                startActivity(intent);
            //} else {
                //String ErrText = "Ошибка! Код ответа " + userInfo.marksArgs[0] + " Ошибка: " + userInfo.marksArgs[1];
                //Toast toast = Toast.makeText(getApplicationContext(), ErrText, Toast.LENGTH_LONG);
                //toast.show();
            //}
        }
    }
}













package com.it.it;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.Spinner;

import androidx.appcompat.app.AppCompatActivity;

import java.util.ArrayList;

public class chartSubjectSelection extends AppCompatActivity {
    private Button returnButton;
    private Button clickButton;
    Spinner choiceSubjectSpinner;
    ArrayAdapter choiceSubjectAdapter;
    private ArrayList<String> subjects = new ArrayList<>(); //Список школьных предметов

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_chart_subject_selection);
        choiceSubjectSpinner =findViewById(R.id.choiceSubjectSpinner);

        subjects = userInfo.getSubjects(); // Формируем список предметов

        choiceSubjectAdapter =new ArrayAdapter(this, android.R.layout.simple_spinner_item,subjects);
        choiceSubjectAdapter.setDropDownViewResource(android.R.layout.simple_dropdown_item_1line);
        choiceSubjectSpinner.setAdapter(choiceSubjectAdapter);
        clickButton =(Button)findViewById(R.id.clickButton) ;
        clickButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openNextActivity();
            }
        });

        returnButton =(Button)findViewById(R.id.Return);
        returnButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openActivity();
            }
        });

    }

    public void openActivity() {
        Intent intent=new Intent(this, chartDataSelection.class);
        startActivity(intent);
    }

    public void openNextActivity() {
        String sub = choiceSubjectSpinner.getSelectedItem().toString();
        Intent intent=new Intent(this, chart.class);
        userInfo.subject = sub;
        startActivity(intent);
    }
}

  
  
  
  
  package com.it.it;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

import java.util.ArrayList;
import java.util.List;

public class diary extends AppCompatActivity {
    TextView para1monday,para2monday,para3monday,para4monday,para5monday,para1tuesday,para2tuesday,para3tuesday,para4tuesday,para5tuesday,para1wednesday,para2wednesday,para3wednesday,para4wednesday,para5wednesday,para1thursday,para2thursday,para3thursday,para4thursday,para5thursday,para1friday,para2friday,para3friday,para4friday,para5friday,para1saturday,para2saturday,para3saturday,para4saturday,para5saturday;
    Button button;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_diary);
        button=findViewById(R.id.backtomenudiary1);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent=new Intent(diary.this, mainMenu.class);
                startActivity(intent);
            }
        });
        ArrayList<String> pon = new ArrayList<>();
        ArrayList<String> vt = new ArrayList<>();
        ArrayList<String> sr = new ArrayList<>();
        ArrayList<String> ct = new ArrayList<>();
        ArrayList<String> pt = new ArrayList<>();
        ArrayList<String> su = new ArrayList<>();

        List<String> keys = new ArrayList<String>(userInfo.shedules.keySet());
        for(int i = 0; i < keys.size(); i++) {
            String key = keys.get(i);
            ArrayList value = userInfo.shedules.get(key);
            for(int j=0;j<value.size();j++){
                schedule schedule = (com.it.it.schedule) value.get(j);
                if("Понедельник".equals(schedule.dayOfWeek)){
                    pon.add(schedule.name);
                }

                if("Вторник".equals(schedule.dayOfWeek)){
                    vt.add(schedule.name);
                }
                if("Среда".equals(schedule.dayOfWeek)){
                    sr.add(schedule.name);
                }
                if("Четверг".equals(schedule.dayOfWeek)){
                    ct.add(schedule.name);
                }
                if("Пятница".equals(schedule.dayOfWeek)){
                    pt.add(schedule.name);
                }
                if("Суббота".equals(schedule.dayOfWeek)){
                    su.add(schedule.name);
                }

            }
        }
        /*para1monday.setText(schedule.name);
        para2monday.setText(schedule.name);
        para3monday.setText(schedule.name);
        para4monday.setText(schedule.name);
        para5monday.setText(schedule.name);*/
        para1monday=findViewById(R.id.para1monday);
        para2monday=findViewById(R.id.para2monday);
        para3monday=findViewById(R.id.para3monday);
        para4monday=findViewById(R.id.para4monday);
        para5monday=findViewById(R.id.para5monday);
        para1tuesday=findViewById(R.id.para1tuesday);
        para2tuesday=findViewById(R.id.para2tuesday);
        para3tuesday=findViewById(R.id.para3tuesday);
        para4tuesday=findViewById(R.id.para4tuesday);
        para5tuesday=findViewById(R.id.para5tuesday);
        para1wednesday=findViewById(R.id.para1wednesday);
        para2wednesday=findViewById(R.id.para2wednesday);
        para3wednesday=findViewById(R.id.para3wednesday);
        para4wednesday=findViewById(R.id.para4wednesday);
        para5wednesday=findViewById(R.id.para5wednesday);
        para1thursday=findViewById(R.id.para1thursday);
        para2thursday=findViewById(R.id.para2thursday);
        para3thursday=findViewById(R.id.para3thursday);
        para4thursday=findViewById(R.id.para4thursday);
        para5thursday=findViewById(R.id.para5thursday);
        para1friday=findViewById(R.id.para1friday);
        para2friday=findViewById(R.id.para2friday);
        para3friday=findViewById(R.id.para3friday);
        para4friday=findViewById(R.id.para4friday);
        para5friday=findViewById(R.id.para5friday);
        para1saturday=findViewById(R.id.para1saturday);
        para2saturday=findViewById(R.id.para2saturday);
        para3saturday=findViewById(R.id.para3saturday);
        para4saturday=findViewById(R.id.para4saturday);
        para5saturday=findViewById(R.id.para5saturday);

    }
}
  
  
  
  
  package com.it.it;

import android.os.AsyncTask;

import org.json.JSONException;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.URL;

public class getAuth extends AsyncTask<Void, Void, Void> {

    @Override
    protected Void doInBackground(Void... voids) {
        String url_str = "https://api.eljur.ru/api/auth?login=" + userInfo.args[0] + "&password=" + userInfo.args[1] + "&devkey=" + userInfo.args[5] +"&vendor=hselyceum&out_format=json";
        URL url = null;
        userInfo.args[2] = "Waiting";
        int result = 0;

        try {
            url = new URL(url_str); // Создаем адрес для подключения
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }

        HttpURLConnection connection = null;
        try {
            //Открываем соединение
            connection = (HttpURLConnection) url.openConnection();
            connection.setRequestProperty("User-Agent", "it");
            connection.setConnectTimeout(100000);
            connection.setReadTimeout(100000);
            connection.setInstanceFollowRedirects(true);

            connection.setRequestProperty("Accept-Language", "en-US,en;q=0.5");
            connection.setRequestProperty("Accept", "text/html");

            String respErr = connection.getResponseMessage();
            result = connection.getResponseCode();

            if(result==200)// Если авторизация успешна, то нужно получить оценки
            {
                JSONObject obj = new JSONObject(GetString(connection));
                userInfo.args[4] = obj.getJSONObject("response").getJSONObject("result").getString("token");// Получаем токен
            }

            //Ставим результат для продолжения основного потока
            userInfo.args[2] = String.valueOf(result);
            userInfo.args[3] = respErr;
        } catch (IOException | JSONException e) {
            e.printStackTrace();
        }

        return null;
    }

    private String GetString(HttpURLConnection connection) throws IOException {
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream()));
        String inputLine;
        StringBuffer response = new StringBuffer();
        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }
        String resp = new String(response);
        return resp;
    }
}

  
  package com.it.it;

import android.os.AsyncTask;

import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.URL;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.util.ArrayList;
import java.util.Iterator;

public class getMarks extends AsyncTask<Void, Void, Void> {

    @Override
    protected Void doInBackground(Void... voids) {
        String url_str = null;
        if(userInfo.dateInterval[1] == null || userInfo.dateInterval[0] == null) {
            url_str = "https://api.eljur.ru/api/getmarks?with_typesets=true&auth_token=" + userInfo.args[4] + "&devkey=" + userInfo.args[5] + "&vendor=hselyceum&out_format=json";
        } else {
            url_str = "https://api.eljur.ru/api/getmarks?with_typesets=true&days=" + userInfo.dateInterval[0] + "-" + userInfo.dateInterval[1] + "&auth_token=" + userInfo.args[4] + "&devkey=" + userInfo.args[5] + "&vendor=hselyceum&out_format=json";
        }
        URL url = null;
        int result = 0;

        try {
            url = new URL(url_str); // Создаем адрес для подключения
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }

        HttpURLConnection connection = null;
        try {
            //Открываем соединение
            connection = (HttpURLConnection) url.openConnection();
            connection.setRequestProperty("User-Agent", "it");
            connection.setConnectTimeout(100000);
            connection.setReadTimeout(100000);
            connection.setInstanceFollowRedirects(true);

            connection.setRequestProperty("Accept-Language", "en-US,en;q=0.5");
            connection.setRequestProperty("Accept", "text/html");

            String respErr = connection.getResponseMessage();
            result = connection.getResponseCode();

            if(result==200) {// Если авторизация успешна, то нужно получить предметы и оценки
                JSONObject obj = null;
                obj = new JSONObject(GetString(connection));

                JSONObject students = obj.getJSONObject("response").getJSONObject("result").getJSONObject("students");// Берем список студентов
                Iterator iterator = students.keys(); // Создаем итератор
                if(iterator.hasNext()){ // Если пришел хоть один студент
                    String studentKey = (String) iterator.next(); // Идентификатор студента
                    JSONObject name = obj.getJSONObject("response").getJSONObject("result").getJSONObject("students").getJSONObject(studentKey);
                    userInfo.userName = name.getString("title");
                    JSONArray lessons = (JSONArray) obj.getJSONObject("response").getJSONObject("result").getJSONObject("students").getJSONObject(studentKey).get("lessons");// Получаем список предметов
                    for (int i = 0; i < lessons.length()-1; i++) {// Проходим по всем предметам и достаем названия и оценки
                        JSONObject lesson = (JSONObject)lessons.get(i);
                        String subject = lesson.getString("name");
                        ArrayList<Marks> marksMass = new ArrayList<>();
                        JSONArray marksInfo = (JSONArray) lesson.get("marks");
                        for (int j = 0; j < marksInfo.length(); j++) {
                            JSONObject mark = (JSONObject) marksInfo.get(j);
                            Boolean b = (Boolean) mark.get("count"); // Проверяем, есть ли оценка
                            if((Boolean) mark.get("count")) {
                                String dateString = (String) mark.get("date"); // Переводим строковую дату в обычную
                                DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
                                LocalDate date = LocalDate.parse(dateString, formatter);

                                String type = null;
                                Double weight = Double.valueOf(0);
                                JSONObject markType = null;
                                try{
                                    markType = (JSONObject) mark.getJSONObject("mtype_set");
                                    type = markType.getString("name");
                                    weight = markType.getDouble("weight");
                                }
                                catch (JSONException e){
                                }
                                String markString = (String) mark.get("value");
                                String markValue[] = markString.split("");

                                Marks m = new Marks(Integer.parseInt(markValue[0]),date,type,weight);
                                marksMass.add(m);
                            }
                        }
                        userInfo.marks.put(subject,marksMass);
                    }
                }
            }

            userInfo.marksArgs[0] = String.valueOf(result);
            userInfo.marksArgs[1] = respErr;
        } catch (IOException | JSONException e) {
            e.printStackTrace();
        }
        return null;
    }

    private String GetString(HttpURLConnection connection) throws IOException {
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream()));
        String inputLine;
        StringBuffer response = new StringBuffer();
        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }
        return new String(response);
    }
}
                                                                                       
                                                                                       
                                                                                       
                                                                                       
                                                                                       package com.it.it;

import android.os.AsyncTask;

import org.json.JSONException;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import java.util.Iterator;

public class getSchedule extends AsyncTask<Void, Void, Void> {

    @Override
    protected Void doInBackground(Void... voids) {
        String url_str = null;
        if(userInfo.dateInterval[1] == null || userInfo.dateInterval[0] == null) {
            url_str = "https://api.eljur.ru/api/getschedule?auth_token=" + userInfo.args[4] + "&devkey=" + userInfo.args[5] + "&vendor=hselyceum&out_format=json";
        } else {
            url_str = "https://api.eljur.ru/api/getschedule?days=" + userInfo.dateInterval[0] + "-" + userInfo.dateInterval[1] + "&auth_token=" + userInfo.args[4] + "&devkey=" + userInfo.args[5] + "&vendor=hselyceum&out_format=json";
        }
        URL url = null;
        int result = 0;

        try {
            url = new URL(url_str); // Создаем адрес для подключения
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }

        HttpURLConnection connection = null;
        try {
            //Открываем соединение
            connection = (HttpURLConnection) url.openConnection();
            connection.setRequestProperty("User-Agent", "it");
            connection.setConnectTimeout(100000);
            connection.setReadTimeout(100000);
            connection.setInstanceFollowRedirects(true);

            connection.setRequestProperty("Accept-Language", "en-US,en;q=0.5");
            connection.setRequestProperty("Accept", "text/html");

            String respErr = connection.getResponseMessage();
            result = connection.getResponseCode();

            if(result == 200) {
                JSONObject obj = null;
                obj = new JSONObject(GetString(connection));

                JSONObject students = obj.getJSONObject("response").getJSONObject("result").getJSONObject("students");// Берем список студентов
                Iterator iterator = students.keys(); // Создаем итератор
                if(iterator.hasNext()){ // Если пришел хоть один студент
                    String studentKey = (String) iterator.next(); // Идентификатор студента
                    JSONObject days = students.getJSONObject(studentKey).getJSONObject("days");
                    Iterator daysIter = days.keys();
                    while (daysIter.hasNext()) {
                        String data = (String) daysIter.next();
                        String dayOfWeek = days.getJSONObject(data).getString("title");

                        ArrayList<schedule> schedules = new ArrayList<>();

                        JSONObject items = days.getJSONObject(data).getJSONObject("items");
                        Iterator itemsIter = items.keys();
                        while (itemsIter.hasNext()) {
                            String itemNum = (String) itemsIter.next();
                            JSONObject item = items.getJSONObject(itemNum);
                            schedules.add(new schedule(dayOfWeek,item.getString("name"), item.getInt("num"), item.getString("room"),item.getString("teacher")));
                        }
                        userInfo.shedules.put(data,schedules);
                    }
                }
            }
            userInfo.sheduleArgs[0] = String.valueOf(result);
            userInfo.sheduleArgs[1] = respErr;
        } catch (IOException | JSONException e) {
            e.printStackTrace();
        }
        return null;
    }

    private String GetString(HttpURLConnection connection) throws IOException {
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream()));
        String inputLine;
        StringBuffer response = new StringBuffer();
        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }
        return new String(response);
    }
}

  
  
  package com.it.it;

import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ImageButton;

import androidx.appcompat.app.AppCompatActivity;

public class help extends AppCompatActivity {
    private Button buttonback;
    private Button buttonquestions;
    private ImageButton vk;
    private ImageButton insta;
    private Button buttonsend;
    private EditText emailtext;
    private String Subject="Question";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_help);
        setTitle("Помощь");
        buttonsend=(Button) findViewById(R.id.send);
        emailtext=(EditText) findViewById(R.id.emailtext);
        buttonsend.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent=new Intent(Intent.ACTION_VIEW,Uri.parse("mailto:"+ "sviridof12345@yandex.ru"));
                intent.putExtra(Intent.EXTRA_SUBJECT, Subject);
                intent.putExtra(Intent.EXTRA_TEXT,emailtext.getText().toString());
                startActivity(intent);
                emailtext.setText("");

            }
        });
        insta=(ImageButton) findViewById(R.id.insta);
        insta.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openinsta();
            }
        });
        vk=(ImageButton) findViewById(R.id.vk);
        vk.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openvk();
            }
        });
        buttonquestions=(Button) findViewById(R.id.questions);
        buttonquestions.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openActivityquestions();
            }
        });
        buttonback=(Button) findViewById(R.id.backtomenuhelp);
        buttonback.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openActivitymenu();
            }
        });
    }
    public void openActivitymenu(){
        Intent intent=new Intent(this, mainMenu.class);
        startActivity(intent);
    }
    public void  openActivityquestions(){
        Intent intent1=new Intent(this, questions.class);
        startActivity(intent1);
    }
    public void openvk(){
        Intent browserIntent=new Intent(Intent.ACTION_VIEW, Uri.parse("https://vk.com/genjimainlol"));
        startActivity(browserIntent);

    }
    public void openinsta(){
        Intent browserIntent1=new Intent(Intent.ACTION_VIEW, Uri.parse("https://www.instagram.com/justilushka/"));
        startActivity(browserIntent1);
    }
}
  
  
  package com.it.it;

import android.content.Intent;
import android.content.SharedPreferences;
import android.net.Uri;
import android.os.Bundle;
import android.preference.PreferenceManager;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;


public class MainActivity extends AppCompatActivity {
    private Button loginButton;
    private EditText nameEditText;
    private EditText passwordEditText;
    private TextView infoTextView;
    private Button button;

    Boolean t;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        setTitle("Вход");
        infoTextView = (TextView) findViewById(R.id.info);

        SharedPreferences myPreferences = PreferenceManager.getDefaultSharedPreferences(getApplicationContext());

        if (myPreferences.getBoolean("darkTheme", false)){
            setTheme(R.style.DarkTheme);
        }else{
            setTheme(R.style.Theme_It);
        }
        userInfo.getUserMarks();
        infoTextView.setVisibility(View.INVISIBLE);
        nameEditText = (EditText) findViewById(R.id.reallogin);
        passwordEditText = (EditText) findViewById(R.id.realparol);
        loginButton = (Button) findViewById(R.id.loginbtn);

        button = findViewById(R.id.link);
        button.setVisibility(View.INVISIBLE);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent browserIntent = new Intent(Intent.ACTION_VIEW, Uri.parse("https://hselyceum.eljur.ru/journal-help-action"));
                startActivity(browserIntent);
            }
        });
        loginButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                nameEditText.toString();
                passwordEditText.toString();
                userInfo.args[5] = "df0d4c0acdb25bed090184abe6a7bc7c";
                //userInfo.args[5] = "dc7c3c00e5d4e49e4a3135b2cf1073e7";
                userInfo.args[0] = nameEditText.getText().toString();
                userInfo.args[1] = passwordEditText.getText().toString();
                userInfo.getUserAuth();

                if(Integer.valueOf(userInfo.args[2]) == 200){
                    Intent intent = new Intent(MainActivity.this, mainMenu.class);
                    startActivity(intent);
                }
                else {
                    String ErrText = "Ошибка подключения. Код ответа " + userInfo.args[2] + " Ошибка: " + userInfo.args[3];
                    Toast toast = Toast.makeText(getApplicationContext(), ErrText, Toast.LENGTH_LONG);
                    toast.show();
                }

            }
        });
    }
}

  
  package com.it.it;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;

import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;

public class mainMenu extends AppCompatActivity {
    private Button calculatorButton;
    private Button scheduleButton;
    private Button chartButton;
    private Button settingsButton;
    private Button helpButton;
    private Button navigationAssistanceButton;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_mainmenu);
        setTitle("Меню");
        navigationAssistanceButton = (Button) findViewById(R.id.navigationAssistance);
        scheduleButton = (Button) findViewById(R.id.schedule);
        settingsButton = (Button) findViewById(R.id.settings);
        helpButton = (Button) findViewById(R.id.help);
        chartButton = (Button) findViewById(R.id.chart);
        calculatorButton= (Button) findViewById(R.id.calculator);
        navigationAssistanceButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                AlertDialog.Builder a_builder=new AlertDialog.Builder(mainMenu.this);
                a_builder.setMessage("ДНЕВНИК предзназначен для просмотра своих оценок и среднего балла; в ДИАГРАММЕ вы сможете увидеть изменение своих оценок; КАЛЬКУЛЯТОР- основная функция приложения, где вы сможете узнать сколько и каких оценок вам не хватает до получения определенного балла; НАСТРОЙКИ нужны для выхода из аккаунта и смены фона приложения; в ПОМОЩИ вы всегда найдете нужную вам информацию")
                        .setCancelable(true);
                AlertDialog alert=a_builder.create();
                alert.show();
            }

        });
        settingsButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openActivitySettings();
            }
        });
        helpButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openActivityHelp();
            }
        });
        chartButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openActivityChart();
            }
        });
        scheduleButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openActivitySchedule();
            }
        });
        calculatorButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openActivityCalculator();
            }
        });
    }
    public void openActivityCalculator(){
        Intent intent=new Intent(this, calculator.class);
        startActivity(intent);
    }
    public void openActivitySchedule(){
        Intent intent2=new Intent(this, schedulers.class);
        userInfo.getUserSchedule();
        startActivity(intent2);
    }
    public void openActivityChart(){
        Intent intent3 = new Intent(this, chartDataSelection.class);
        startActivity(intent3);
    }
    public void openActivitySettings() {
        Intent intent4 = new Intent(this, settings.class);
        startActivity(intent4);
    }
    public void openActivityHelp() {
        Intent intent5 = new Intent(this, help.class);
        startActivity(intent5);
    }
}



  package com.it.it;

import java.time.LocalDate;

public class Marks {
    Integer mark;
    LocalDate date;
    Double weight;
    String type;
    // Констатирующая
    // Творческая
    // Формирующая

    public Marks() { //Конструктор без параметров
        mark = 0;
        type = null;
        date = null;
        weight = Double.valueOf(0);
    }

    public Marks(Integer mark, LocalDate date, String type, Double weight) { // Конструктор с параметрами
        this.mark = mark;
        this.date = date;
        this.type = type;
        this.weight = weight;
    }

    public Integer getMark() { // Геттер оценки
        return mark;
    }

    public LocalDate getDate() {
        return date;
    }

    public String getType() {
        return type;
    }

    public Double getWeight() {
        return weight;
    }

    public void setMark(Integer mark) { // Сеттер оценки
        this.mark = mark;
    }

    public void setDate(LocalDate date) {
        this.date = date;
    }
}

  
  
  
  package com.it.it;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;

import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import java.util.ArrayList;

public class questions extends AppCompatActivity {
    private Button button;
    private RecyclerView recyclerView;
    private RecyclerView.Adapter adapter;
    private RecyclerView.LayoutManager layoutManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_questions);
        ArrayList<RecyclerViewItem> recyclerViewItems=new ArrayList<>();
        recyclerViewItems.add(new RecyclerViewItem(R.drawable.question,"Как можно выйти из аккаунта?","В настройках есть возможность одним нажатием выйти и вы вернетесь на вкладку ввода пароля и логина"));
        recyclerViewItems.add(new RecyclerViewItem(R.drawable.question,"Связь с разработчиком","Вы можете написать сообщения на оставленные соцсети, а также оставить комментарий в разделе Помощь"));

        recyclerView=findViewById(R.id.recycler);
        recyclerView.setHasFixedSize(true);
        adapter=new RecyclerViewAdapter(recyclerViewItems);
        layoutManager=new LinearLayoutManager(this);

        recyclerView.setAdapter(adapter);
        recyclerView.setLayoutManager(layoutManager);


        button=(Button)findViewById(R.id.Return);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openaPreviousActivity();
            }
        });
    }
    public void openaPreviousActivity(){
        Intent intent=new Intent(this, help.class);
        startActivity(intent);
    }
}
  
  
  
  
  package com.it.it;

import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.TextView;

import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;

import java.util.ArrayList;

public class RecyclerViewAdapter extends RecyclerView.Adapter<RecyclerViewAdapter.RecyclerViewViewHolder>  {
    private ArrayList<RecyclerViewItem> arrayList;
    public static class RecyclerViewViewHolder extends RecyclerView.ViewHolder{
        public ImageView imageView;
        public TextView textView1;
        public TextView textView2;

        public RecyclerViewViewHolder(@NonNull View itemView) {
            super(itemView);
            imageView=itemView.findViewById(R.id.imgwopros);
            textView1=itemView.findViewById(R.id.textvop1);
            textView2=itemView.findViewById(R.id.textvop2);
        }
    }
    public RecyclerViewAdapter(ArrayList<RecyclerViewItem> arrayList){
        this.arrayList=arrayList;
    }

    @NonNull
    @Override
    public RecyclerViewViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view= LayoutInflater.from(parent.getContext()).inflate(R.layout.recycler_view_item, parent, false);
        RecyclerViewViewHolder recyclerViewViewHolder=new RecyclerViewViewHolder(view);
        return recyclerViewViewHolder;
    }

    @Override
    public void onBindViewHolder(@NonNull RecyclerViewViewHolder holder, int position) {
        RecyclerViewItem recyclerViewItem=arrayList.get(position);
        holder.imageView.setImageResource(recyclerViewItem.getImageResors());
        holder.textView1.setText(recyclerViewItem.getText1());
        holder.textView2.setText(recyclerViewItem.getText2());
    }

    @Override
    public int getItemCount() {
        return arrayList.size();
    }
}

  
  
  
  package com.it.it;

public class RecyclerViewItem {
    private String text1;
    private String text2;
    private int imageResors;
    public RecyclerViewItem(int imageResors, String text1, String text2){
        this.imageResors=imageResors;
        this.text1=text1;
        this.text2=text2;
    }

    public int getImageResors() {
        return imageResors;
    }

    public String getText1(){
        return  text1;
    }
    public String getText2() {
        return text2;
    }
}

  
  package com.it.it;

public class schedule {
    String dayOfWeek;
    String name;
    Integer num;
    String room;
    String teacher;

    public schedule() { //Конструктор без параметров
        this.dayOfWeek = null;
        this.name = null;
        this.num = 0;
        this.room = null;
        this.teacher = null;
    }

    public schedule(String dayOfWeek, String name, Integer num, String room, String teacher){
        this.dayOfWeek = dayOfWeek;
        this.name = name;
        this.num = num;
        this.room = room;
        this.teacher = teacher;
    }
}

  
  
  package com.it.it;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.Spinner;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

import java.util.ArrayList;

public class schedulers extends AppCompatActivity {
    private Button returnButton;
    private Button showListButton;
    private TextView selectedSubjectTextView;
    private TextView selectedTypeTextView;
    private TextView editText;
    private Spinner subjectChoiceSpinner;
    private Spinner typeChoiceSpinner;
    private ArrayAdapter arrayAdapter;
    private ArrayAdapter arrayAdapter1;
    private Button button1;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_schedulers);

        returnButton = findViewById(R.id.Return);
        showListButton = (Button) findViewById(R.id.showList);

        userInfo.getUserMarks(); // Запрашиваем оценки за последнюю неделю

        subjectChoiceSpinner=findViewById(R.id.subjectChoice);
        ArrayList<String> subjectList = userInfo.getSubjects();
        arrayAdapter=new ArrayAdapter(this, android.R.layout.simple_spinner_item, subjectList);
        arrayAdapter.setDropDownViewResource(android.R.layout.simple_dropdown_item_1line);
        subjectChoiceSpinner.setAdapter(arrayAdapter);

        typeChoiceSpinner=findViewById(R.id.typeChoice);
        ArrayList<String> typeList = userInfo.getMarkTypes();
        typeList.add("По всем типам");
        arrayAdapter1=new ArrayAdapter(this, android.R.layout.simple_spinner_item,typeList);
        arrayAdapter1.setDropDownViewResource(android.R.layout.simple_dropdown_item_1line);
        typeChoiceSpinner.setAdapter(arrayAdapter1);

        selectedSubjectTextView=(TextView) findViewById(R.id.selectedSubject);
        selectedTypeTextView=(TextView) findViewById(R.id.selectedType);
        editText=(TextView) findViewById(R.id.marksList);
        button1=findViewById(R.id.button1);
        button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent=new Intent(schedulers.this, diary.class);
                startActivity(intent);
            }
        });

        returnButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent=new Intent(schedulers.this, mainMenu.class);
                startActivity(intent);
            }
        });

        showListButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                selectedSubjectTextView.setText(subjectChoiceSpinner.getSelectedItem().toString());
                selectedTypeTextView.setText(typeChoiceSpinner.getSelectedItem().toString());
                ArrayList<Integer> marksList = new ArrayList<>();
                String showText = "";
                if(typeChoiceSpinner.getSelectedItem().toString() == null || typeChoiceSpinner.getSelectedItem().toString() == "" || typeChoiceSpinner.getSelectedItem().toString() == "По всем типам") {
                    marksList = userInfo.getMarks(subjectChoiceSpinner.getSelectedItem().toString());
                } else {
                    marksList = userInfo.getMarks(subjectChoiceSpinner.getSelectedItem().toString(),typeChoiceSpinner.getSelectedItem().toString());
                }
                for(Integer mark : marksList) {
                    showText = showText + " " + mark;
                }
                editText.setText(showText);
            }
        });
    }
}
  
  
  package com.it.it;

import android.content.Intent;
import android.content.SharedPreferences;
import android.os.Bundle;
import android.preference.PreferenceManager;
import android.view.View;
import android.widget.Button;
import android.widget.CompoundButton;
import android.widget.Switch;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;
import androidx.appcompat.app.AppCompatDelegate;

public class settings extends AppCompatActivity {
    private Button returnButton;
    private Switch themeSwitch;
    private Button exitButton;
    private TextView username;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_settings);

        SharedPreferences myPreferences = PreferenceManager.getDefaultSharedPreferences(getApplicationContext());
        SharedPreferences.Editor myEditor = myPreferences.edit();

        if (AppCompatDelegate.getDefaultNightMode()==AppCompatDelegate.MODE_NIGHT_YES || myPreferences.getBoolean("darkTheme", false)){
            setTheme(R.style.DarkTheme);
        }else{
            setTheme(R.style.Theme_It);
        }

        themeSwitch=findViewById(R.id.mode);
        if(AppCompatDelegate.getDefaultNightMode()==AppCompatDelegate.MODE_NIGHT_YES){
            themeSwitch.setChecked(true);
        }
        themeSwitch.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
                if(isChecked){
                    AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_YES);
                    myEditor.putBoolean("darkTheme", true);
                    reset();
                }else{
                    AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_NO);
                    myEditor.putBoolean("darkTheme", false);
                    reset();
                }
            }
        });


        username=findViewById(R.id.username);
        username.setText(userInfo.userName);
        exitButton=(Button)findViewById(R.id.exit);
        exitButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openActivityvhod();
            }
        });
        returnButton=(Button) findViewById(R.id.Return);
        setTitle("Настройки");
        returnButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openActivityMenu();
            }
        });

    }

    private void reset() {
        Intent intent5=new Intent(getApplicationContext(), settings.class);
        startActivity(intent5);
        finish();
    }

    public void openActivityMenu(){
        Intent intent=new Intent(this, mainMenu.class);
        startActivity(intent);
    }
    private void openActivityvhod(){
        Intent intent1=new Intent(this, MainActivity.class);
        startActivity(intent1);
    }
}
  
  
  
  package com.it.it;

import android.icu.util.Calendar;

import java.util.ArrayList;
import java.util.Collections;
import java.util.HashMap;
import java.util.HashSet;
import java.util.concurrent.TimeUnit;

public class userInfo {
    public static String userName = null;
    public static String[] args = new String[6];
    // 0. Логин
    // 1. Пароль
    // 2. Код ответа
    // 3. Текст ошибки/ответа???
    // 4. Токен
    // 5. devkey
    public static String[] marksArgs = new String[3];
    // 0. Код ответа
    // 1. Текст ошибки
    public static String[] sheduleArgs = new String[3];
    // 0. Код ответа
    // 1. Текст ошибки
    public static HashMap<String, ArrayList<Marks>> marks = new HashMap<>();
    public static HashMap<String, ArrayList<schedule>> shedules = new HashMap<>();
    public static String[] dateInterval = new String[2];
    public static String subject = new String();
    public static String[] calcDesiredGrade = new String[3];
    // 0. Желаема оценка
    // 1. Тип
    // 2. Предмет
    private static HashMap<String,String> monthMap = new HashMap<>();

    public static void getUserAuth() {
        setMonthMap();
        getAuth GetAuth = new getAuth();
        GetAuth.execute();
        int countsecond = 0;
        do
        {
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            countsecond++;
        }while(userInfo.args[2]=="Waiting" && userInfo.args[3] == null && countsecond<100); // Ждем, пока не получим какой-нибудь ответ
    }

    public static void getUserMarks() {
        marksArgs[0] = "Waiting";
        marksArgs[1] = null;
        dateInterval[0] = null;
        dateInterval[1] = null;
        getMarks GetMarks = new getMarks();
        GetMarks.execute();
        int countsecond = 0;
        do
        {
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            countsecond++;
        }while(userInfo.marksArgs[0]=="Waiting" && userInfo.marksArgs[1] == null && countsecond<100);// Ждем, пока не получим какой-нибудь ответ
    }

    public static void getUserMarks(String beginMonth, String endMonth) {
        marksArgs[0] = "Waiting";
        marksArgs[1] = null;
        String dateFirst = null;
        String dateNext = null;
        if(beginMonth.equals(endMonth)) {
            dateFirst = monthMap.get(beginMonth);
            String str = beginMonth + "+1";
            dateNext = monthMap.get(str);
        } else {
            dateFirst = monthMap.get(beginMonth);
            dateNext = monthMap.get(endMonth);
        }
        dateInterval[0] = dateFirst;
        dateInterval[1] = dateNext;
        getMarks GetMarks = new getMarks();
        GetMarks.execute();
        int countsecond = 0;
        do
        {
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            countsecond++;
        }while(userInfo.marksArgs[0]=="Waiting" && userInfo.marksArgs[1] == null && countsecond<100);// Ждем, пока не получим какой-нибудь ответ
    }

    public static void getUserSchedule() {
        sheduleArgs[0] = "Waiting";
        sheduleArgs[1] = null;
        dateInterval[0] = null;
        dateInterval[1] = null;
        userInfo.shedules = new HashMap<>();
        getSchedule GetSchedule = new getSchedule();
        GetSchedule.execute();
        int countsecond = 0;
        do
        {
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            countsecond++;
        }while(userInfo.sheduleArgs[0]=="Waiting" && userInfo.sheduleArgs[1] == null && countsecond<100);// Ждем, пока не получим какой-нибудь ответ
    }

    public static Integer[] getWishMarks(String nameSubject, Double wishMark, String wishType) {

        ArrayList<Marks> marks = userInfo.marks.get(nameSubject);

        ArrayList<Integer> constType = new ArrayList<>();
        ArrayList<Integer> formType = new ArrayList<>();
        ArrayList<Integer> creativeType = new ArrayList<>();

        Double weightConst = Double.valueOf(0); // Веса оценок
        Double weightForm = Double.valueOf(0);
        Double weightCreative = Double.valueOf(0);

        for (int i = 0; i < marks.size(); i++) {
            if (marks.get(i).getType().equals("Констатирующая")) {
                constType.add(marks.get(i).getMark());
                weightConst = marks.get(i).getWeight();
            } else if (marks.get(i).getType().equals("Творческая")) {
                creativeType.add(marks.get(i).getMark());
                weightCreative = marks.get(i).getWeight();
            } else {
                formType.add(marks.get(i).getMark());
                weightForm = marks.get(i).getWeight();
            }
        } // Заполняем списки оценок по типам

        Integer sumConst = getArraySum(constType); // Получаем суммы оценок по предметам
        Integer sumForm = getArraySum(formType);
        Integer sumCreative = getArraySum(creativeType);

        Double averagekr = Double.valueOf(0);
        Double averagetv = Double.valueOf(0);
        Double averagefr = Double.valueOf(0);
        if(constType.size() != 0) {
            averagekr = Double.valueOf(sumConst / constType.size());
        }
        if(creativeType.size() != 0) {
            averagetv = Double.valueOf(sumCreative / creativeType.size());
        }
        if(formType.size() != 0) {
            averagefr = Double.valueOf(sumForm / formType.size());
        }

        ArrayList<Integer[]> massResult = new ArrayList<Integer[]>();
        if(wishType.toString().equals("Формирующая"))
            for (int x=0;x<10;x++){
                for (int y=0;y<8;y++){
                    for(int z=0;z<5;z++){
                        if (x+y+z+formType.size() != 0) {
                            if(wishMark<=averagekr*weightConst+averagetv*weightCreative+weightForm*((5*x+4*y+3*z+sumForm)/(x+y+z+formType.size()))){//вместо фрмаркстр все оценки(например 5 4 3 4), в ласт скобке количесвто оценок фрмаркстр
                                massResult.add(new Integer[]{x,y,z});
                            }
                        }
                    }
                }
            }
        else if(wishType.toString().equals("Творческая")) {
            for (int x = 0; x < 10; x++) {
                for (int y = 0; y < 8; y++) {
                    for (int z = 0; z < 5; z++) {
                        if(x + y + z + creativeType.size() != 0){
                            if (wishMark <= averagekr * weightConst + averagefr * weightForm + weightCreative * ((5 * x + 4 * y + 3 * z + sumCreative) / (x + y + z + creativeType.size()))) {//вместо твмаркстр все оценки(например 5 4 3 4), в ласт скобке количесвто оценок фрмаркстр
                                massResult.add(new Integer[]{x, y, z});
                            }
                        }
                    }
                }
            }
        }
        else if(wishType.toString().equals("Констатирующая")) {
            for (int x = 0; x < 10; x++) {
                for (int y = 0; y < 8; y++) {
                    for (int z = 0; z < 5; z++) {
                        if (x + y + z + constType.size() != 0) {
                            if (wishMark <= averagetv * weightCreative + averagefr * weightForm + weightConst * ((5 * x + 4 * y + 3 * z + sumConst) / (x + y + z + constType.size()))) {//вместо крмаркстр все оценки(например 5 4 3 4), в ласт скобке количесвто оценок фрмаркстр
                                massResult.add(new Integer[]{x, y, z});
                            }
                        }
                    }
                }
            }
        }
        ArrayList<Integer> sumWeight = new ArrayList<>();
        for(int i = 0; i < massResult.size(); i++) {
            Integer[] intXYZ = massResult.get(i);
            sumWeight.add(intXYZ[0] + intXYZ[1] + intXYZ[2]);
        }

        Integer[] resXYZ = new Integer[3];
        if(sumWeight.size() != 0) {
            int minIndex = sumWeight.indexOf(Collections.min(sumWeight));
            resXYZ = massResult.get(minIndex);
        }
        return resXYZ;
    }

    public static ArrayList<String> getSubjects() {
        ArrayList<String> subjectsList = new ArrayList<>();
        for (String key: marks.keySet()) {
            subjectsList.add(key);
        }
        return subjectsList;
    } // Получаем список всех предметов

    public static ArrayList<String> getMarkTypes() {
        HashSet<String> typesSet = new HashSet<>();
        for(String key: marks.keySet()) {
            ArrayList<Marks> marks = userInfo.marks.get(key);
            for (int i = 0; i < marks.size(); i++) {
                typesSet.add(marks.get(i).getType());
            }
        }
        ArrayList<String> markTypes = new ArrayList<String>(typesSet);
        return markTypes;
    }   // Получаем список типов оценок для всех предметов

    public static ArrayList<String> getMarkTypes(String subject) {
        ArrayList<Marks> marks = userInfo.marks.get(subject);
        HashSet<String> typesSet = new HashSet<>();
        for (int i = 0; i < marks.size(); i++) {
            typesSet.add(marks.get(i).getType());
        }
        ArrayList<String> markTypes = new ArrayList<String>(typesSet);
        return markTypes;
    }  // Получаем список типов оценок для конкретного предмета

    public static ArrayList<Integer> getMarks(String subject) {
        ArrayList<Integer> marksList = new ArrayList<>();
        ArrayList<Marks> marks = userInfo.marks.get(subject);
        for (int i = 0; i < marks.size(); i++) {
            marksList.add(marks.get(i).getMark());
        }
        return marksList;
    } // Поиск оценок по предмету

    public static ArrayList<Integer> getMarks(String subject, String type) {
        ArrayList<Integer> marksList = new ArrayList<>();
        ArrayList<Marks> marks = userInfo.marks.get(subject);
        for (int i = 0; i < marks.size(); i++) {
            if(marks.get(i).getType().equals(type)) {
                marksList.add(marks.get(i).getMark());
            }
        }
        return marksList;
    } // Поиск оценок по предмету определенного типа

    private static Integer getArraySum(ArrayList<Integer> list) {
        Integer resultSum = 0;
        for (int i = 0; i < list.size(); i++) {
            resultSum = resultSum + list.get(i);
        }
        return resultSum;
    } // Получаем сумму оценок

    private static void setMonthMap() {
        Calendar calendar = Calendar.getInstance();
        Integer Year = calendar.get(Calendar.YEAR);
        Integer dayOfYear = Calendar.DAY_OF_YEAR;
        if(dayOfYear<=180) {
            monthMap.put("Сентябрь",Year + "0901");
            monthMap.put("Октябрь",Year + "1001");
            monthMap.put("Ноябрь",Year + "1101");
            monthMap.put("Декабрь",Year + "1201");
            monthMap.put("Январь",(Year+1) + "0101");
            monthMap.put("Февраль",(Year+1) + "0201");
            monthMap.put("Март",(Year+1) + "0301");
            monthMap.put("Апрель",(Year+1) + "0401");
            monthMap.put("Май",(Year+1) + "0501");

            monthMap.put("Сентябрь+1",Year + "1001");
            monthMap.put("Октябрь+1",Year + "1101");
            monthMap.put("Ноябрь+1",Year + "1201");
            monthMap.put("Декабрь+1",(Year+1) + "0101");
            monthMap.put("Январь+1",(Year+1) + "0201");
            monthMap.put("Февраль+1",(Year+1) + "0301");
            monthMap.put("Март+1",(Year+1) + "0401");
            monthMap.put("Апрель+1",(Year+1) + "0501");
            monthMap.put("Май+1",(Year+1) + "0601");
        } else {
            monthMap.put("Сентябрь",(Year-1) + "0901");
            monthMap.put("Октябрь",(Year-1) + "1001");
            monthMap.put("Ноябрь",(Year-1) + "1101");
            monthMap.put("Декабрь",(Year-1) + "1201");
            monthMap.put("Январь",Year + "0101");
            monthMap.put("Февраль",Year + "0201");
            monthMap.put("Март",Year + "0301");
            monthMap.put("Апрель",Year + "0401");
            monthMap.put("Май",Year + "0501");

            monthMap.put("Сентябрь+1",(Year-1) + "1001");
            monthMap.put("Октябрь+1",(Year-1) + "1101");
            monthMap.put("Ноябрь+1",(Year-1) + "1201");
            monthMap.put("Декабрь+1",Year + "0101");
            monthMap.put("Январь+1",Year + "0201");
            monthMap.put("Февраль+1",Year + "0301");
            monthMap.put("Март+1",Year + "0401");
            monthMap.put("Апрель+1",Year + "0501");
            monthMap.put("Май+1",Year + "0601");
        }
    } // Создаем карту соотнесения месца и даты

}

  
  
  
  <?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".calcResult">
    <Button
        android:id="@+id/Return"
        android:layout_width="80dp"
        android:layout_height="40dp"
        android:text="Назад"
        android:textSize="10dp"
        android:background="@drawable/square"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:text="Выбранный тип:"
        android:textSize="20dp"
        android:textColor="@color/purple_500"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:id="@+id/typeTextView"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:text="Выбранный предмет:"
        android:textSize="20dp"
        android:textColor="@color/purple_500"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:id="@+id/subjectTextView"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:text="Выбранная желаемая оценка:"
        android:textSize="20dp"
        android:textColor="@color/purple_500"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:id="@+id/markTextView"/>
    <TextView
        android:layout_width="120dp"
        android:layout_height="50dp"
        android:text="Вам нужно:"
        android:layout_gravity="center"
        android:textSize="20dp"
        android:textColor="@color/purple_500"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:id="@+id/kolvoocenok"/>

</LinearLayout>
  
  
  
  <?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".calculator">

    <Button
        android:id="@+id/Return"
        android:layout_width="80dp"
        android:layout_height="40dp"
        android:text="Назад"
        android:textSize="10dp"
        android:background="@drawable/square"/>
    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <Spinner
            android:id="@+id/subjectSpinner"
            android:layout_width="match_parent"
            android:layout_height="25dp"
            android:gravity="end">
        </Spinner>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:textColor="@color/purple_500"
            android:textSize="15sp"
            android:text="Выбери предмет"
            android:layout_marginLeft="180dp"
            android:gravity="center">
        </TextView>
    </RelativeLayout>
    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <Spinner
            android:id="@+id/typeChoice"
            android:layout_width="match_parent"
            android:layout_height="25dp"
            android:gravity="end"></Spinner>

        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:layout_marginLeft="180dp"
            android:gravity="center"
            android:text="Выбери тип оценки"
            android:textColor="@color/purple_500"
            android:textSize="15sp"></TextView>
    </RelativeLayout>

    <TextView
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:textColor="@color/purple_500"
        android:textSize="19sp"
        android:text="Укажите оценку которую хотите получить"/>

    <EditText
        android:id="@+id/desiredGrade"
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:inputType="number" />
    <Button
        android:id="@+id/calcResult"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Далее"
        android:background="@drawable/square"/>


</LinearLayout>
  
  
  
  
  <?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".chart">
    <com.github.mikephil.charting.charts.LineChart
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/Linechart">

    </com.github.mikephil.charting.charts.LineChart>

</androidx.constraintlayout.widget.ConstraintLayout>
  
  
  
  
  <?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".chartDataSelection">

    <Button
        android:id="@+id/Return"
        android:layout_width="80dp"
        android:layout_height="40dp"
        android:text="Назад"
        android:textSize="10dp"
        android:background="@drawable/square"/>

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <Spinner
            android:id="@+id/firstDateSpinner"
            android:layout_width="match_parent"
            android:layout_height="25dp"
            android:gravity="end">
        </Spinner>

        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:layout_marginLeft="100dp"
            android:gravity="center"
            android:textColor="@color/black"
            android:text="Выбери месяц начала отсчета"></TextView>
    </RelativeLayout>
    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <Spinner
            android:id="@+id/secondDateSpinner"
            android:layout_width="match_parent"
            android:layout_height="25dp"
            android:gravity="end">
        </Spinner>

        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:layout_marginLeft="100dp"
            android:gravity="center"
            android:textColor="@color/black"
            android:text="Выбери месяц конца отсчета"></TextView>
    </RelativeLayout>
    <Button
        android:id="@+id/grafic"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="choiceChartDate"
        android:text="Нажми"
        android:background="@drawable/square"/>


</LinearLayout>
  
  
  
  <?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".chartSubjectSelection">

    <Button
        android:id="@+id/Return"
        android:layout_width="80dp"
        android:layout_height="40dp"
        android:text="Назад"
        android:textSize="10dp"
        android:background="@drawable/square"/>

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <Spinner
            android:id="@+id/choiceSubjectSpinner"
            android:layout_width="match_parent"
            android:layout_height="25dp"
            android:gravity="end">
        </Spinner>

        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:layout_marginLeft="100dp"
            android:gravity="center"
            android:text="Выбери Предмет"></TextView>
    </RelativeLayout>


    <Button
        android:id="@+id/clickButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="нажми"
        android:background="@drawable/square"/>

</LinearLayout>
  
  
  
  
  
  <?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".diary">

    <Button
        android:id="@+id/backtomenudiary1"
        android:layout_width="80dp"
        android:layout_height="40dp"
        android:text="Назад"
        android:textSize="10dp"/>

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">


        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:textColor="@color/purple_500"
            android:textSize="18dp"

            android:text="Понедельник"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:id="@+id/para1monday"
            android:layout_marginTop="20dp"
            android:textColor="@color/black"
            android:text="Пара 1-"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:id="@+id/para2monday"
            android:layout_marginTop="40dp"
            android:textColor="@color/black"
            android:text="Пара 2-"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:id="@+id/para3monday"
            android:layout_marginTop="60dp"
            android:textColor="@color/black"
            android:text="Пара 3-"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:id="@+id/para4monday"
            android:layout_marginTop="80dp"
            android:textColor="@color/black"
            android:text="Пара 4-"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:id="@+id/para5monday"
            android:layout_marginTop="100dp"
            android:textColor="@color/black"
            android:text="Пара 5-"/>

        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:layout_marginLeft="20dp"
            android:layout_marginTop="120dp"
            android:textSize="18dp"

            android:textColor="@color/purple_500"
            android:text="Вторник"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:id="@+id/para1tuesday"
            android:layout_marginTop="140dp"
            android:textColor="@color/black"
            android:text="Пара 1-"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:id="@+id/para2tuesday"
            android:layout_marginTop="160dp"
            android:textColor="@color/black"
            android:text="Пара 2-"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:id="@+id/para3tuesday"
            android:layout_marginTop="180dp"
            android:textColor="@color/black"
            android:text="Пара 3-"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:layout_marginTop="200dp"
            android:id="@+id/para4tuesday"
            android:textColor="@color/black"
            android:text="Пара 4-"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:id="@+id/para5tuesday"
            android:layout_marginTop="220dp"
            android:textColor="@color/black"
            android:text="Пара 5-"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:gravity="end"
            android:layout_marginRight="20dp"
            android:textSize="18dp"

            android:textColor="@color/purple_500"
            android:text="Четверг"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:id="@+id/para1thursday"
            android:text="Пара 1-"
            android:layout_marginTop="20dp"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:text="Пара 2-"
            android:id="@+id/para2thursday"
            android:layout_marginTop="40dp"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:text="Пара 3-"
            android:id="@+id/para3thursday"
            android:layout_marginTop="60dp"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:text="Пара 4-"
            android:id="@+id/para4thursday"
            android:layout_marginTop="80dp"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:text="Пара 5-"
            android:id="@+id/para5thursday"
            android:layout_marginTop="100dp"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:gravity="end"
            android:layout_marginTop="120dp"
            android:layout_marginRight="20dp"
            android:textSize="18dp"

            android:textColor="@color/purple_500"
            android:text="Пятница"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:id="@+id/para1friday"
            android:text="Пара 1-"
            android:layout_marginTop="140dp"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:text="Пара 2-"
            android:id="@+id/para2friday"
            android:layout_marginTop="160dp"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:text="Пара 3-"
            android:id="@+id/para3friday"
            android:layout_marginTop="180dp"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:text="Пара 4-"
            android:id="@+id/para4friday"
            android:layout_marginTop="200dp"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:text="Пара 5-"
            android:id="@+id/para5friday"
            android:layout_marginTop="220dp"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:gravity="end"
            android:textSize="18dp"
            android:layout_marginTop="240dp"
            android:layout_marginRight="20dp"
            android:textColor="@color/purple_500"
            android:text="Суббота"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:text="Пара 1-"
            android:id="@+id/para1saturday"
            android:layout_marginTop="260dp"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:text="Пара 2-"
            android:id="@+id/para2saturday"
            android:layout_marginTop="280dp"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:text="Пара 3-"
            android:layout_marginTop="300dp"
            android:id="@+id/para3saturday"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:text="Пара 4-"
            android:layout_marginTop="320dp"
            android:textColor="@color/black"
            android:id="@+id/para4saturday"
            android:gravity="end"
            android:layout_marginRight="100dp"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:text="Пара 5-"
            android:layout_marginTop="340dp"
            android:id="@+id/para5saturday"
            android:textColor="@color/black"
            android:gravity="end"
            android:layout_marginRight="100dp"/>

        <TextView
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:layout_marginLeft="20dp"
            android:layout_marginTop="240dp"
            android:textSize="18dp"
            android:textColor="@color/purple_500"
            android:text="Среда"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:layout_marginTop="260dp"
            android:textColor="@color/black"
            android:id="@+id/para1wednesday"
            android:text="Пара 1-"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:layout_marginTop="280dp"
            android:textColor="@color/black"
            android:id="@+id/para2wednesday"
            android:text="Пара 2-"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:layout_marginTop="300dp"
            android:textColor="@color/black"
            android:id="@+id/para3wednesday"
            android:text="Пара 3-"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:layout_marginTop="320dp"
            android:textColor="@color/black"
            android:id="@+id/para4wednesday"
            android:text="Пара 4-"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_marginLeft="20dp"
            android:layout_height="40dp"
            android:layout_marginTop="340dp"
            android:textColor="@color/black"
            android:id="@+id/para5wednesday"
            android:text="Пара 5-"/>
    </RelativeLayout>




</LinearLayout>
  
  
  
  
  
  <?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".help">

    <Button
        android:id="@+id/backtomenuhelp"
        android:layout_width="80dp"
        android:layout_height="40dp"
        android:text="Назад"
        android:textSize="10dp"
        android:background="@drawable/square"/>


    <TextView
        android:layout_width="match_parent"
        android:layout_marginTop="20dp"
        android:layout_height="wrap_content"
        android:textSize="20dp"
        android:textColor="@color/purple_500"
        android:text="Оставьте свой комментарий"/>

    <EditText
        android:layout_width="200dp"
        android:layout_height="wrap_content"
        android:id="@+id/emailtext"
        android:gravity="center" />

    <Button
        android:id="@+id/send"
        android:layout_width="130dp"
        android:layout_height="wrap_content"
        android:gravity="start"
        android:text="Отправить"
        android:background="@drawable/square"/>
    <TextView
        android:layout_marginTop="30dp"
        android:layout_width="match_parent"
        android:textColor="@color/purple_500"
        android:textSize="15dp"
        android:layout_height="wrap_content"
        android:text="Хотите увидеть ответы на часто задаваемые вопросы?"/>


    <Button
        android:id="@+id/questions"
        android:layout_width="wrap_content"
        android:text="Перейти"
        android:layout_height="wrap_content"
        android:background="@drawable/square"/>
    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="120dp">
        <ImageButton
            android:id="@+id/vk"
            android:layout_marginLeft="70dp"
            android:scaleType="centerCrop"
            android:layout_width="100dp"
            android:layout_height="100dp"
            style="@style/Widget.MaterialComponents.Button.TextButton.Icon"
            android:src="@drawable/vk"/>
        <ImageButton
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:id="@+id/insta"
            android:layout_marginLeft="220dp"
            android:src="@drawable/instagram"
            style="@style/Widget.MaterialComponents.Button.TextButton.Icon"
            android:scaleType="centerCrop"/>


    </RelativeLayout>
    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="50dp">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:gravity="start"
            android:layout_marginLeft="20dp"
            android:textSize="20dp"
            android:textColor="@color/purple_500"
            android:text="Почта:"/>

        <TextView
            android:layout_width="240dp"
            android:layout_height="wrap_content"
            android:layout_marginLeft="90dp"
            android:textSize="20dp"
            android:textColor="@color/black"
            android:background="@color/purple_500"
            android:gravity="center"
            android:text="sviridof12345@yandex.ru" />

    </RelativeLayout>

</LinearLayout>
  
  
  
  
  <?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:scrollbarThumbHorizontal="@color/white"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/Eljur"
        android:layout_width="match_parent"
        android:layout_height="150dp"
        android:gravity="center"
        android:text="ЭлЖур"
        android:textColor="@color/purple_700"
        android:textSize="50sp" />

    <TextView
        android:id="@+id/login"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Логин"
        android:textColor="@color/gray"
        android:textSize="17dp" />

    <EditText
        android:id="@+id/reallogin"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="textPersonName"
        android:text="" />

    <TextView
        android:id="@+id/password"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Пароль"
        android:textColor="@color/gray"
        android:textSize="17dp" />

    <EditText
        android:id="@+id/realparol"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="textPassword" />

    <Button
        android:id="@+id/loginbtn"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Войти"
        android:textColor="@color/white"
        android:textColorLink="@color/purple_500"
        android:textSize="17dp" />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/info"
        android:layout_marginTop="15dp"
        android:text="Неверный логин или пароль, попробуйте еще раз"/>
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="15dp"
        android:id="@+id/link"
        android:layout_gravity="center"
        android:text="Забыли пароль?"/>


</LinearLayout>
  
  
  
  
  
  
  <?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".mainMenu"
    android:padding="10dp">


    <Button
        android:layout_width="50dp"
        android:id="@+id/navigationAssistance"
        android:layout_height="50dp"
        android:layout_gravity="end"
        android:layout_marginRight="10dp"
        android:text="\?"
        android:textSize="25dp"
        android:background="@drawable/custom_button" />
    <Button
        android:id="@+id/schedule"
        android:layout_width="match_parent"
        android:layout_height="80dp"
        android:gravity="center"
        android:text="Дневник"
        android:textSize="30dp"
        android:background="@drawable/square"
        android:layout_marginTop="5dp"/>
    <Button
        android:id="@+id/chart"
        android:layout_width="match_parent"
        android:layout_height="80dp"
        android:gravity="center"
        android:text="Диаграмма"
        android:textSize="30dp"
        android:background="@drawable/square"
        android:layout_marginTop="5dp"/>
    <Button
        android:id="@+id/calculator"
        android:layout_width="match_parent"
        android:layout_height="80dp"
        android:gravity="center"
        android:text="Калькулятор"
        android:textSize="30dp"
        android:background="@drawable/square"
        android:layout_marginTop="5dp"/>
    <Button
        android:id="@+id/settings"
        android:layout_width="match_parent"
        android:layout_height="80dp"
        android:gravity="center"
        android:text="Настройки"
        android:textSize="30dp"
        android:background="@drawable/square"
        android:layout_marginTop="5dp"/>
    <Button
        android:id="@+id/help"
        android:layout_width="match_parent"
        android:layout_height="80dp"
        android:gravity="center"
        android:text="Помощь"
        android:textSize="30dp"
        android:background="@drawable/square"
        android:layout_marginTop="5dp"/>

</LinearLayout>
  
  
  
  
  <?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".questions">

    <Button
        android:id="@+id/Return"
        android:layout_width="80dp"
        android:layout_height="40dp"
        android:text="Назад"
        android:textSize="10dp"/>

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recycler"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:padding="4dp"
        android:scrollbars="vertical"/>


</LinearLayout>
  
  
  
  <?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".schedulers">
    <Button
        android:layout_width="80dp"
        android:layout_height="40dp"
        android:background="@drawable/square"
        android:id="@+id/Return"
        android:text="Назад"
        android:textSize="10dp"/>
    <TextView
        android:layout_width="130dp"
        android:layout_height="30dp"
        android:textColor="@color/black"
        android:text="Выберите предмет"
        android:background="@color/purple_500"
        android:layout_gravity="center"/>
    <Spinner
        android:layout_width="150dp"
        android:layout_height="40dp"
        android:layout_gravity="center"
        android:id="@+id/subjectChoice"/>
    <TextView
        android:layout_width="180dp"
        android:layout_height="30dp"
        android:textColor="@color/black"
        android:background="@color/purple_500"
        android:text="Выберите тип оценивания"
        android:layout_gravity="center"/>
    <Spinner
        android:layout_width="150dp"
        android:layout_height="40dp"
        android:layout_gravity="center"
        android:id="@+id/typeChoice"/>
    <Button
        android:layout_width="150dp"
        android:layout_height="50dp"
        android:layout_gravity="center"
        android:layout_marginTop="10dp"
        android:id="@+id/showList"
        android:text="Посмотреть"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="40dp"
        android:textSize="20dp"
        android:textColor="@color/purple_500"
        android:text="Выбранный предмет:" />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="40dp"
        android:id="@+id/selectedSubject"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="40dp"
        android:textColor="@color/purple_500"
        android:textSize="20dp"
        android:text="Выбранный тип оценивания:"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="40dp"
        android:id="@+id/selectedType"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="40dp"
        android:text="Ваши оценки"
        android:textSize="20dp"
        android:textColor="@color/purple_500"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="40dp"
        android:id="@+id/marksList"/>
    <Button
        android:layout_width="170dp"
        android:layout_height="80dp"
        android:id="@+id/button1"
        android:layout_gravity="center"
        android:background="@drawable/square"
        android:text="Посмотреть расписание"/>
</LinearLayout>
  
  
  
  
  <?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:background="?attr/bgc"
    android:layout_height="match_parent"
    tools:context=".settings">
    <Button
        android:id="@+id/Return"
        android:layout_width="80dp"
        android:layout_height="40dp"
        android:text="Назад"
        android:textSize="10dp"/>

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="223dp"
        android:src="@drawable/settings" />
    <Button
        android:id="@+id/buttonswitch"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="switch"/>
    <Switch
        android:id="@+id/mode"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_marginTop="10dp"
        android:id="@+id/exit"
        android:text="Выйти из аккаунта"/>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:id="@+id/username"/>


</LinearLayout>
  
  
  
  
  <?xml version="1.0" encoding="utf-8"?>
<androidx.cardview.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="4dp"
    app:cardCornerRadius="4dp">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background=" @android:color/holo_red_light">

        <ImageView
            android:id="@+id/imgwopros"
            android:layout_width="50dp"
            android:layout_height="50dp"
            android:padding="4dp" />
        <TextView
            android:id="@+id/textvop1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentTop="true"
            android:text="text1"
            android:textColor="@android:color/black"
            android:textSize="25sp"
            android:textStyle="bold"
            android:layout_toEndOf="@id/imgwopros"/>

        <TextView
            android:id="@+id/textvop2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@id/textvop1"
            android:layout_toEndOf="@id/imgwopros"
            android:text="text2"
            android:textColor="@android:color/black"
            android:textSize="15sp"
            android:textStyle="italic" />
    </RelativeLayout>




</androidx.cardview.widget.CardView>

