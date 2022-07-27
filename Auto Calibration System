#include<EEPROM.h>
#define lmf 7
#define lmb 6
#define rmf 10
#define rmb 9
#define lms 5
#define rms 11
#define sw 13
#define light 12

int s[6], maximum[6], minimum[6], mid[6];
int sensor, sum, base[6] = {1, 2, 4, 8, 16, 32};

void setup() {
  Serial.begin(9600);
  pinMode(sw, INPUT_PULLUP);
  pinMode(light, OUTPUT);
  pinMode(lmf, OUTPUT);
  pinMode(lmb, OUTPUT);
  pinMode(rmf, OUTPUT);
  pinMode(rmb, OUTPUT);
  pinMode(8, OUTPUT);
  digitalWrite(8, 1);
  for (int i = 0; i < 6; i++) {
    mid[i] = EEPROM.read(i) * 4;
    maximum[i] = EEPROM.read(i + 6) * 4;
    minimum[i] = EEPROM.read(i + 12) * 4;
    Serial.println(String(maximum[i]) + " " +String(mid[i]) + " " + String(minimum[i]));
  }
}

void loop() {
  int r = button_read();
  if (r == 1) cal();
}

int button_read() {
  int cl = 0;
p:  int t = 0;
  if (digitalRead(sw) == 0) {
    digitalWrite(light, HIGH);
    while (digitalRead(sw) == 0) {
      delay(1); t++;
    }
    digitalWrite(light, LOW);
    if (t > 15) {
      t = 0; cl++;
      while (digitalRead(sw) == HIGH) {
        delay(1); t++;
        if (t > 1000) return cl;
      }
      goto p;
    }
  }
  return cl;
}

void cal() {
  for (int i = 0; i < 6; i++) {
    maximum[i] = 0;
    minimum[i] = 1023;
  }
  motor(100, -100);
  for (int i = 0; i < 5000 ; i++) {
    for (int i = 0; i < 6 ; i++) {
      s[i] = analogRead(i);
      maximum[i] = max(maximum[i], s[i]);
      minimum[i] = min(minimum[i], s[i]);
    }
  }
  motor(0, 0);
  for (int i = 0; i < 6; i++) {
    mid[i] = (maximum[i] + minimum[i]) / 2;
    EEPROM.write(i, mid[i] / 4); delay(10);
    EEPROM.write(i + 6, maximum[i] / 4); delay(10);
    EEPROM.write(i + 12, minimum[i] / 4); delay(10);
  }
}



void reading() {
  sensor = 0; //this is to refresh initial value
  sum = 0;
  for (int i = 0 ; i < 6 ; i++) {
    s[i] = analogRead(i);
    (s[i] > mid[i]) ? s[i] = 1 : s[i] = 0; //conditional statement. this is to convert analog value to digital. if you want to see real analog value, then comment it. but for line follow, you must uncomment it.
    sensor += s[i] * base[i]; //this is to merge all 6 values and imagine them in a single binary number. then i converted it into decimal number to use as final value. better search about base convertion
    sum += s[i]; //finds out total count of sensor
  }
}

void motor(int a, int b) {
  if (a > 0) {
    digitalWrite(lmf, 1);
    digitalWrite(lmb, 0);
  }
  else if (a < 0) {
    a = -a;
    digitalWrite(lmf, 0);
    digitalWrite(lmb, 1);
  }
  else {
    digitalWrite(lmf, 0);
    digitalWrite(lmb, 0);
  }
  
  if (b > 0) {
    digitalWrite(rmf, 1);
    digitalWrite(rmb, 0);
  }
  else if (b < 0) {
    b = -b;
    digitalWrite(rmf, 0);
    digitalWrite(rmb, 1);
  }
  else {
    digitalWrite(rmf, 0);
    digitalWrite(rmb, 0);
  }
  
  analogWrite(lms, a);
  analogWrite(rms, b);
}
