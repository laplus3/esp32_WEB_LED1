このプログラムは、Arduinoを使用してWebサーバーを構築し、WiFiを介してリモートでLEDを制御する。

目的
esp32を用いてWiFiネットワークを接続し、無線通信を行う。
具体的に3つのことを行う。Webブラウザからのリクエストに元図いて、リモートでLEDを制御できるようになる。
特定の時間帯にLEDを点灯する機能を試す。SPIFFSを使用してArduino内のファイルを扱い、それをWebページの提供に利用する。

プログラムの説明
esp32_WEB_LED1.ino

#include <WiFi.h>
#include <WebServer.h>
#include <SPIFFS.h>
必要なライブラリをプログラムに組み込む。
WiFi関連、Webサーバー、SPIFFS(フラッシュメモリ内のファイルにアクセスする）

