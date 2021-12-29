# Team4-Segrenator-#include<Servo.h>
Servo servo1;		 //micro servomotor
Servo servo2;		 //metal servomotor
// Sensor pins
#define sensorPower 7   //moisture sensor output
#define sensorPin 2     //moisture sensor input
#define me_pin 11       //metal sensor input
#define t_pin 12        //touch sensor input
#define ir_pin A2       // IR sensor input
int d_ir=0;             
int d_me=0;
int d_t=LOW;
void setup() 
{
  pinMode(sensorPower, OUTPUT);    // setting pin mode
  
  // Initially keep the sensor OFF
  digitalWrite(sensorPower, LOW);  //Initializing pin status
  
  servo1.attach(8); //microservo attached to pin 8
  servo2.attach(9); //metalservo attached to pin 9
  
  pinMode(me_pin,INPUT);        // setting pin mode
  pinMode(t_pin,INPUT);         // setting pin mode
  pinMode (ir_pin, INPUT);      // setting pin mode
  
  Serial.println("Smart Segregator ready");
  Serial.begin(9600);
}

void loop() 
{
  servo2.write(90);  //metal servo positioned initially to 90 degree
  delay(500);       //delay of 1/2 second
  servo1.write(0);  //micro servo is closed 

  //get the reading of INDUCTIVE PROXIMITY SENSOR
  d_me=digitalRead(me_pin);
  delay(15);       

  //get the reading of TOUCH SENSOR
  d_t=digitalRead(t_pin);
  delay(15);
  
  //get the reading of IR SENSOR from the below function
  d_ir = readIR();
  Serial.print("Digital Output IR: ");
  Serial.println(d_ir);
  delay(15);

  //get the reading from the function below and print it
  int val = readSensor();
  Serial.print("Digital Output Moisture: ");
  Serial.println(val);
  delay(15);
 //--------------------WASTE SEGREGATION--------------------
 
  //checks for metal waste or wet waste or dry waste
  
  if(d_me!=0)
  {
    Serial.println("Metal waste detected");
    servo2.write(90);
    delay(1000);
    servo1.write(90);
    delay(1500);
  }  
  else if(val==0 && d_me==0 && d_t==HIGH)
  {
    Serial.println("Wet waste detected");
    servo2.write(180);
    delay(1000);
    servo1.write(90);
    delay(1500);
  }  
  else if(val==1 && d_me==0 && d_ir==1)
  {
    Serial.println("Dry waste detected");
    servo2.write(0);
    delay(1000);
    servo1.write(90);
    delay(1500);
  }  
  else
  {
    servo1.write(0);
  }

  delay(1000); 
  Serial.println();
}

//This function check the input of IR sensor
int readIR()
{
  int ir=digitalRead(ir_pin);
  if(ir==1)
  {
    digitalWrite(LED, LOW);
     return 0;
  }
  else 
  {
  return 1;
  digitalWrite(LED, HIGH);
  } 
}

//  This function returns the analog soil moisture measurement
int readSensor() 
{
  digitalWrite(sensorPower, HIGH);  // Turn the sensor ON
  delay(10);              // Allow power to settle
  int val = digitalRead(sensorPin); // Read the analog value form sensor
  digitalWrite(sensorPower, LOW);   // Turn the sensor OFF
  return val;             // Return analog moisture value
}
