// Line-tracing-with-obstacle-detection
//This arduino program can be utilized for Line tracing robots using 5 pairs of IR sensors for line detection, along with an ultrasonic sensor to detect the object in the front. PID algorithm is used for line tracing.
 #include<newping.h>
 # define Trigger_pin (23)
 # define Echo_pin (25)
 # define Max_dist 200
 int s1, s2, s3, s4, s5, AI = 0,sw1=1,flag=1,dist;
float error=0, p=0,d, kp = 5000, derivative, kd=1000, drive=0, last_error=0, avgv=128;
int lb=2,lpwm=3,ld=4,rb=5,rpwm=6,rd=7;
Newping Sonar(23,25,200);
void linetrace();
void setup() {
  Serial.begin(9600)
  pinMode(44, INPUT);
  //Sensor Pins
  pinMode(22, INPUT);
  pinMode(24, INPUT);
  pinMode(26, INPUT);
  pinMode(28, INPUT);
  pinMode(30, INPUT);
  //Left Motor
  pinMode(8, OUTPUT); //Break
  pinMode(9, OUTPUT); //PWM
  pinMode(10, OUTPUT); //Direction

  //Right Motor
  pinMode(11, OUTPUT); //Break
  pinMode(12, OUTPUT); //PWM
  pinMode(13, OUTPUT); //Direction

  digitalWrite(8, HIGH);
  digitalWrite(11, HIGH);
  Serial.begin(57600);
}

void loop() {
  
  sw1=digitalRead(44);

  s1 = digitalRead(22);
  s2 = digitalRead(24);
  s3 = digitalRead(26);
  s4 = digitalRead(28);
  s5 = digitalRead(30);

   delay(50);
   Serial.print("Ping :");
   int cm= Sonar.ping_cm();
   Serial.print(cm);
  if (sw1==0)
  {

    flag=1;
  }

    if (flag!=0&& cm>120)
    {
 linetrace();
}
else
{
  stop();
}
  

void linetrace() {
 
  
  //3 Sensors
  if(s1==1&&s2==1&&s3==1){error=1.5;AI=1;}
  if(s2==1&&s3==1&&s4==1){error=0;AI=0;}
  if(s3==1&&s4==1&&s5==1){error=-1.5;AI=2;}
  
  
  //2 Sensors
  if(s1==1&&s2==1){error=1.5;AI=1;}
  if(s2==1&&s3==1){error=0.5;AI=1;}
  if(s3==1&&s4==1){error=-0.5;AI=1;}
  if(s4==1&&s5==1){error=-1.5;AI=2;}
 
  //1 Sensor
  if(s1==1){error=1.5; AI=1;}
  if(s2==1){error=0.5; AI=1;}
  if(s3==1){error=0; AI=0;}
  if(s4==1){error=-0.5; AI=1;}
  if(s5==1){error=-1.5; AI=2;}
 

  if(s1==0&&s2==0&&s3==0&&s4==0&&s5==0){
    if(AI==1){
      error=2;
    }
    else if(AI==2){
      error=-2;
    }
    if (S1==1 && s5==1)
   delay(100);
   {
    if (s2==1||s3==1||s4==1)// Junction
    {
      stop();
      flag=0;
    }
  }

  p=error*kp;
  derivative=error-last_error;
  d=kd*derivative;
  drive=p;
  drive=drive/100;
  if(drive>avgv){
    drive=avgv;
  }
  else if(drive<-avgv){
    drive=-avgv;
  }
  

  digitalWrite(8, LOW);
  digitalWrite(11, LOW);
  digitalWrite(10,LOW);
  digitalWrite(13,LOW);
  analogWrite(9,avgv+drive);
  analogWrite(12,avgv-drive);
  Serial.print(avgv+drive);
  Serial.print("\t");
  Serial.println(avgv-drive);
  last_error=error;
}
}
void stop()
{
  digitalWrite(8, LOW);
  digitalWrite(11, LOW);
    
  }
  }
