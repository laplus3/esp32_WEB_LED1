#include <WiFi.h>
#include <WebServer.h>
#include <SPIFFS.h>

const char* ssid = "SSID";    // WiFiのSSID
const char* passwd = "password"; // WiFiのパスワード
WebServer server(80);                 // Webサーバーのインスタンス

const int led = 12;                   // LEDのピン番号

void setup() {
  Serial.begin(115200);               // シリアル通信の初期化
  pinMode(led, OUTPUT);                // LEDピンを出力に設定

  // WiFiへの接続
  WiFi.begin(ssid, passwd);
  while (WiFi.status() != WL_CONNECTED) {
    delay(300);
    Serial.print(".");
  }

  Serial.println("");
  Serial.println("WiFi Connected");
  Serial.print("IP Address : ");
  Serial.println(WiFi.localIP());

  // ウェブサーバーのルートパスへのハンドラの設定
  server.on("/", handleLedOnOff);

  // ハンドラが見つからない場合の処理
  server.onNotFound(handleNotFound);

  // ウェブサーバーの開始
  server.begin();

  // SPIFFS（ファイルシステム）の初期化
  SPIFFS.begin();
}

void loop() {
  server.handleClient();          // ウェブサーバーのクライアントハンドリング
  controlLedByTime();             // 特定の時間帯にLEDを点灯する処理
}

void handleLedOnOff() {
  File dataFile;
  String html;

  // SPIFFSからHTMLファイルを読み込み
  dataFile = SPIFFS.open("/index.txt", FILE_READ);
  
  while (dataFile.available()) {
    html += (dataFile.readStringUntil('\n'));
  }
  dataFile.close();    

  // POSTメソッドでのリクエスト処理
  if (server.method() == HTTP_POST) {
    if (server.arg("click").equals("on")) {
      digitalWrite(led, HIGH);   // LEDをON
    } else if (server.arg("click").equals("off")) {
      digitalWrite(led, LOW);    // LEDをOFF
    }
  }

  // クライアントにHTMLを送信
  server.send(200, "text/html", html);
}

void handleNotFound() {
  // 404 Not Foundの応答をクライアントに送信
  server.send(404, "text/plain", "Not Found");
}

void controlLedByTime() {
  // 現在の時刻を取得
  time_t now = time(nullptr);
  struct tm *tm_info = localtime(&now);

  // 特定の時間帯になったらLEDを点灯
  if (tm_info->tm_hour == 13 && tm_info->tm_min == 27 && tm_info->tm_sec == 50) {
    digitalWrite(led, HIGH);  // LEDを点灯
  } else {
    digitalWrite(led, LOW);   // LEDを消灯
  }
}
