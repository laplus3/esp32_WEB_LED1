このプログラムは、Arduinoを使用してWebサーバーを構築し、WiFiを介してリモートでLEDを制御する。

目的
esp32を用いてWiFiネットワークを接続し、無線通信を行う。
具体的に3つのことを行う。Webブラウザからのリクエストに基づいて、
リモートでLEDを制御できるようになる。
特定の時間帯にLEDを点灯する機能を試す。
SPIFFSを使用してArduino内のファイルを扱い、それをWebページの提供に利用する。

プログラムの説明
esp32_WEB_LED1.ino

#include <WiFi.h>
#include <WebServer.h>
#include <SPIFFS.h>
必要なライブラリをプログラムに組み込む。
WiFi関連、Webサーバー、SPIFFS(フラッシュメモリ内のファイルにアクセスする）

const char* ssid = "SSID";
const char* passwd = "password";
WiFiに接続するためのネットワーク名とパスワード

WebServer server(80);
WebサーバーのHTTPに使用されるポート番号は80である。
任意のポート番号を割り当てることができる。例えば、
ポート番号を12とすると、「http://www.laplus.jp:12/」というように
サーバ名の後ろにコロン（:）とポート番号を加えたURLを指定しなければならない。(1) https://ascii.jp/elem/000/000/458/458706/


server.begin();
SPIFFS.begin();
Webサーバーをポート80で開始し、SPIFFS(フラッシュメモリ内のファイルにアクセスるためのもの）を初期化する。

const int led = 12;
LEDを接続するピンを指定

Serial.begin(115200);
Arduinoからコンピュータへのシリアル通信を開始する。
シリアル通信とは、データを送受信するための伝送路を1本、または2本使用して、データを1ビットずつ連続的に送受信する通信方式である。（2）https://www.contec.com/jp/support/basic-knowledge/daq-control/serial-communicatin/

pinMode(led, OUTPUT);
12番ピンをデジタル出力として設定する。
これにより、12番ピンに接続されたLEDを制御できる。

WiFi.begin(ssid, passwd);
while (WiFi.status() != WL_CONNECTED) {
  delay(300);
  Serial.print(".");
}

![image](https://github.com/laplus3/esp32_WEB_LED1/assets/157358254/38875abf-5805-4cbd-95ed-6b16fbeb999f)
