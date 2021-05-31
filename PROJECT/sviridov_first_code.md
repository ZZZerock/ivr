Активити, благодаря которому значения из активити diagrama переносятся в новое 
package com.it.it;

import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

public class activity_peremenie extends AppCompatActivity {
    private Button button;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_peremenie);
        button=(Button)findViewById(R.id.nazadpom);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                openActivity();
            }
        });

        Intent intent=getIntent();
        String predmet=intent.getStringExtra("predmet");
        TextView peremenie=findViewById(R.id.peremenie);
        peremenie.setText(predmet);

        String data=intent.getStringExtra("data");
        TextView peremenie1=findViewById(R.id.peremenie1);
        peremenie1.setText(data);

    }
    public void openActivity(){
        Intent intent=new Intent(this, diagramma.class);
        startActivity(intent);
    }
}
