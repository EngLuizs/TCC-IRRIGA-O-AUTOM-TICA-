//=====================================ADICIONANDO AS FUNÇÕES===============================================
void Aservo();
void Amotor();
void Aled();
void Bluetooth();
void LCD();
void Contas();

//=====================================DEFININDO OS NOME PARA CADA PINO======================================
#define IN1 12 // Bomba Entrada 1 da Ponte H
#define IN2 14 // Bomba Entrada 2 da Ponte H
#define velocidadeA 27 // Velocidade Bomba
#define Shumidity1 33 // Sensor de Umidade 1
#define SLM35 35 // Sensor de Temperatura
#define Sldr 34 // LDR
#define Schuva 15 // Sensor de Chuva
#define LED 18

//=====================================CONFIGURANDO CONEXÃO BLUETOOTH=========================================
BluetoothSerial serialBLE;

//=====================================CONFIGURANDO DISPLAY LCD==============================================
LiquidCrystal_I2C lcd (0x27, 20, 4);

//=====================================CRIANDO O SIMBOLO DE PORCENTAGEM======================================
byte porcentagem[8] = {0B11000, 0B11001, 0B00010, 0B00100, 0B01000, 0B10011, 0B00011, 0B00000 };
byte grau[8] = { 0B00110, 0B01001, 0B01001, 0B00110, 0B00000, 0B00000, 0B00000, 0B00000 };

//=====================================ADICIONANDO AS VARIÁVEIS==============================================
// INT
int humidity1, Vumid1;
int Vtemp = 0, LM35;
int Vldr = 0, ldr;
int Vchuva = 0, Sensor;
int cont;
int SoloSeco = 4095;
int SoloMolhado = 1700;
int PoucaLuz = 300;
int MuitaLuz = 4095;
int SemChuva = 4095;
int ComChuva = 700;
int percBaixo = 0;
int percAlto = 100;
int FV;
int SV;
int pos;
int ser;

// FLOAT
float mV;

// CHAR
char dados;

// BOOL
bool conexao;

void setup() {
  Serial.begin(9600);
  serialBLE.begin("BLE");

  myservo.attach(4);
  myservo.attach(5);

  lcd.init();
  lcd.backlight();
  lcd.createChar(0, porcentagem);
  lcd.createChar(1, grau);

  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(velocidadeA, OUTPUT);
  pinMode(LED, OUTPUT);

  pinMode(Shumidity1, INPUT);
  pinMode(SLM35, INPUT);
  pinMode(Sldr, INPUT);
  pinMode(Schuva, INPUT);

  analogWrite(velocidadeA, 230);

  lcd.setCursor(2, 0);
  lcd.print("Sejam bem vindos!");
  lcd.setCursor(0, 2);
  lcd.print("Sistema de Irrigacao");
  lcd.setCursor(5, 3);
  lcd.print("Automatico");
  delay(5000);
  lcd.clear();
}

void loop() {
  Contas();
  LCD();

  conexao = serialBLE.available();
  if (conexao == true){
    dados = serialBLE.read();
    if (dados == 'G'){
      while (dados != 'H'){
        Contas();
        LCD();
        dados = serialBLE.read();

        if (dados == 'A' && FV == 0) {
          for (pos = 0; pos <= 180; pos += 1){
            myservo.write(pos);
            delay(20);
            FV = 180;
          }
        } else if(dados == 'B' && FV == 180){
          for (pos = 180; pos >= 0; pos -= 1){
            myservo.write(pos);
            delay(20);
            FV = 0;
          }
        }

        if (dados == 'C'){
          digitalWrite(IN1, HIGH);
          digitalWrite(IN2, LOW);
        } else if (dados == 'D'){
          digitalWrite(IN1, LOW);
          digitalWrite(IN2, LOW);
        }

        if (dados == 'E'){
          digitalWrite(LED, HIGH);
        } else if (dados == 'F'){
          digitalWrite(LED, LOW);
        }
      }
    }
  }

  Aled();
  Aservo();
}

void Contas(){
  LM35 = analogRead(SLM35);
  humidity1 = constrain(analogRead(Shumidity1), SoloMolhado, SoloSeco);
  Vumid1 = map(humidity1, SoloMolhado, SoloSeco, percAlto, percBaixo);
  ldr = constrain(analogRead(Sldr), PoucaLuz, MuitaLuz);
  Vldr = map(ldr, PoucaLuz, MuitaLuz, percBaixo, percAlto);
  Sensor = constrain(analogRead(Schuva), SemChuva, ComChuva);
  Vchuva = map(Sensor, ComChuva, SemChuva, percBaixo, percAlto);
  mV = LM35 * (3300.0 / 4096.0);
  Vtemp = mV / 10;
}

void LCD(){
  lcd.clear();
  lcd.setCursor(2, 0);
  lcd.print("Temperatura: ");
  lcd.print(Vtemp);
  lcd.write(1);
  lcd.print("C");

  lcd.setCursor(2, 1);
  lcd.print("Humidity: ");
  lcd.print(Vumid1);
  lcd.write(0);

  if (Vldr >= 80){
    lcd.setCursor(2, 2);
    lcd.print("Luminosidade ALTA");
  } else if (Vldr <= 79 && Vldr >= 30) {
    lcd.setCursor(2, 2);
    lcd.print("Luminosidade BOA");
  } else if (Vldr <= 29) {
    lcd.setCursor(2, 2);
    lcd.print("Luminosidade BAIXA");
  }

  Serial.print(Vchuva);
  if (Vchuva > 75) {
    lcd.setCursor(3, 3);
    lcd.print("ESTA CHOVENDO!");
  }
  delay(1000);
}

void Aservo(){
  if (Vldr <= 49 && FV == 0){
    for (pos = 0; pos <= 180; pos += 1){
      myservo.write(pos);
      delay(20);
      FV = 180;
    }
  } else if (Vldr >= 80 && FV == 180){
    for (pos = 180; pos >= 0; pos -= 1){
      myservo.write(pos);
      delay(20);
      FV = 0;
    }
  }
}

void Aled(){
  if (Vldr <= 20) {
    digitalWrite(LED, HIGH);
  } else {
    digitalWrite(LED, LOW);
  }
}
