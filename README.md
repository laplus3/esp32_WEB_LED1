このプログラムは、esp32を使用してWebサーバーを構築し、WiFiを介してリモートでLEDを制御する。

# 目的
esp32を用いてWiFiネットワークを接続し、無線通信を行う。
具体的に3つのことを行う。Webブラウザからのリクエストに基づいて、
リモートでLEDを制御できるようにする。
特定の時間帯にLEDを点灯する機能を試す。
SPIFFSを使用してArduino内のファイルを扱い、それをWebページの提供に利用する。

# プログラムの説明
esp32_WEB_LED1.ino
```
#include <WiFi.h>
#include <WebServer.h>
#include <SPIFFS.h>
```
必要なライブラリをプログラムに組み込む。
WiFi関連、Webサーバー、SPIFFS(フラッシュメモリ内のファイルにアクセスする）
```
const char* ssid = "SSID";
const char* passwd = "password";
```
WiFiに接続するためのネットワーク名とパスワード
```
WebServer server(80);
```
WebサーバーのHTTPに使用されるポート番号は80である。
任意のポート番号を割り当てることができる。例えば、
ポート番号を12とすると、「http://www.laplus.jp:12/」というように
サーバ名の後ろにコロン（:）とポート番号を加えたURLを指定しなければならない。  
(1)https://ascii.jp/elem/000/000/458/458706/

const int led = 12;
LEDを接続するピンを指定
## setup関数
初期化処理を行う。シリアル通信の開始、LEDのピンモード設定、WiFi接続、Webサーバーの設定、SPIFFSの初期化などが含まれる。
```
Serial.begin(115200);
```
Arduinoからコンピュータへのシリアル通信を開始する。
シリアル通信とは、データを送受信するための伝送路を1本、または2本使用して、データを1ビットずつ連続的に送受信する通信方式である。  (2）https://www.contec.com/jp/support/basic-knowledge/daq-control/serial-communicatin/
```
pinMode(led, OUTPUT);
```
12番ピンをデジタル出力として設定する。
これにより、12番ピンに接続されたLEDを制御できる。
```
WiFi.begin(ssid, passwd);
while (WiFi.status() != WL_CONNECTED) {
  delay(300);
  Serial.print(".");
}
```
接続が確立されるまで、状態がWL_CONNECTEDになるまで待機する。
接続が確立されるとループを抜ける。
```
Serial.println("");
Serial.println("WiFi Connected");
Serial.print("IP Address : ");
Serial.println(WiFi.localIP());
```
WiFi接続が確立されたら、シリアルモニターに「WiFi Connected」と表示し、
ArduinoのIPアドレスを表示する。
```
server.on("/", handleLedOnOff);
```
Webサーバーの"/"（ルート）へのリクエストがあった場合、'handleLedOnOff'関数を呼び出す。これにより、ブラウザからのアクセスに対する応答を処理する。Webサーバーへのルートへのリクエストとは、サイトやアプリケーションのホームページやデフォルトのエントリーポイントにアクセスするための最初の要求である。
```
server.onNotFound(handleNotFound);
```
Webサーバーがリクエストされたページを見つけられなかった場合、'handleNotFound'関数を呼び出す。
```
server.begin();
SPIFFS.begin();
```
Webサーバーをポート80で開始し、SPIFFS(フラッシュメモリ内のファイルにアクセスするためのもの）を初期化する。これにより、後で、SPIFFSを使用してファイル（HTMLファイルなど）を読み込むことができる。
## loop関数
```
void loop() {
  server.handleClient();
  controlLedByTime();  // 追加: 特定の時間帯にLEDを点灯する処理
}
```
メインのループ処理を行う。Webサーバーのクライアント処理と、特定の時間にLEDを制御する処理が繰り返される。
## handleLedOnOff関数
Webサーバーが"/"(ルート)に対するリクエストを受けたときに呼び出される処理を担当している。
```
dataFile = SPIFFS.open("/index.txt", FILE_READ);
while (dataFile.available()) {
  html += (dataFile.readStringUntil('\n'));
}
dataFile.close();
```
"/index.txt"というファイルから改行ごとにデータを読み取り、それを'html'という変数に追加している。読み取ったデータを後でWebサーバーのクライアントに返すために使用する。
```
if (server.method() == HTTP_POST) {
  if (server.arg("click").equals("on")) {
    digitalWrite(led, HIGH);
  } else if (server.arg("click").equals("off")) {
    digitalWrite(led, LOW);
  }
}
```
Webブラウザなどから送信されたフォームデータの中に "click" という名前のパラメータがあり、その値が "on" であればLEDを点灯させ、"off" であればLEDを消灯させるような条件分岐を行っている。
```
server.send(200, "text/html", html);
```
読み込んだHTMLコンテンツをHTTPステータスコード200(成功)とともにクライアント(ブラウザ)に返す。
## controlLedByTime関数
```
// 現在の時刻を取得
time_t now = time(nullptr);  // 現在の時間を取得し、それを time_t 型の変数 now に格納
struct tm *tm_info = localtime(&now);  // 現在の時間を tm 構造体に変換し、tm_info に格納

// 特定の時間帯になったらLEDを点灯
if (tm_info->tm_hour == 13 && tm_info->tm_min == 27 && tm_info->tm_sec == 50) {
  digitalWrite(led, HIGH);  // もし時が 13 時、分が 27 分、秒が 50 秒であれば、LEDを点灯
} else {
  digitalWrite(led, LOW);  // それ以外の場合、LEDを消灯
}
```
# 結果
## Webブラウザ
![image](https://github.com/laplus3/esp32_WEB_LED1/assets/157358254/9eaf60ae-3e2f-4c5f-a185-910ce343a627)
LED ONをクリックするとLEDが点灯する。LED OFFをクリックするとLEDが消灯する。　　
## 回路
![esp32led1 1](https://github.com/laplus3/esp32_WEB_LED1/assets/157358254/e660c003-1135-4956-a87e-441bdbeeeb5a)
