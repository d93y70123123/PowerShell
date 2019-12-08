# 致敬米家  

## 影片中看的到的&看不到的  
* 插座  
* 錢  
* 等等...  
&  
* 閘道器(Gateway)  
* 加密  
* wifi 帳號密碼  


## 為什麼要自己做?  

## 從選擇IOT開始  

1. 選擇ESP8266(NodeMCU NT 100)  

2. 決定架構  
把IOT設定為 AP MODE  
缺點： IPv6 & 入侵 (連線數限制)  

把IOT設定為 Client  

## Arduino 
範例程式  
```C
// 加載、宣告
#define LED_PIN 13

// 開機只跑這麼一次
void setup() {
    pinMode(LED_PIN, OUTPUT);
}

// 無限迴圈 什麼時候當機什麼時候到頭
void loop() {
    digitalWrite(LED_PIN, HIGH);
    delay(1000);
    digitalWrite(LED_PIN, LOW);
    delay(1000);
}
```

#### Arduino 使用 SmartConfig
* 無螢幕 Client IoT 較佳 WiFi 設定方案
* 將手機使用中的 WiFi 資訊丟給 IoT
* 原理是算 UDP 封包長度，因此 APP 是必須 (Cordova + Plugin)

#### 加密  
2.5.0 後開始支援，SSL 證書更新指紋也會更新  
```c
#include <ESP8266WiFi.h>
#include <WiFiClientSecure.h>

const char fingerprint[] PROGMEM = "E7 67 DA 4F F0 7C CB 59 26 71 C8 D6 19 2B FF 89 75 A7 0C D0";

WiFiClientSecure client;

void setup() {
    WiFi.mode(WIFI_STA);
    WiFi.begin(ssid, password);
    client.setFingerprint(fingerprint);
    client.connect(host, port);
}
```
加密內容通常都會變得無法理解，所以可以調配一下順序，先送出去有證書之後再加密。  
在外面買最多只能買兩年期限的證書，所以自己發證書吧~  
免費版的有：Let's encrypt

## 如果懶的話
直接Google >> IoT platfrom

##  Socket server  
用PHP的前提下找的框架  
框架：Workerman  [網址](https://www.workerman.net/)  
基本架構  
```php
<?php
//websocket & text 擇一
$w = new Worker("websocket://0.0.0.0:3001");
$w = new Worker("text://0.0.0.0:3001");

// 連接處理
$w->onConnect = function($connection){};

// 程式啟動處理 心跳推送、
$w->onWorkerStart = function($w){};

// 接收訊號處理
$w->onMessage = function($connection, $data){};
```

**主動推送-Workerman**  
[workerman參考網址](https://wenda.workerman.net/question/508)
```php
<?php
$w->onWorkerStart = function($w)
{
    // 开启一个内部端口，方便内部系统推送数据，Text协议格式 文本+换行符
    $inner_text_worker = new Worker('Text://0.0.0.0:3002');
    $inner_text_worker->onMessage = function($connection, $buffer)
    {
        // $data数组格式，里面有uid，表示向那个uid的页面推送数据
        $data = json_decode($buffer, true);
        $uid = $data['uid'];
        // 通过workerman，向uid的页面推送数据
        $ret = sendMessageByUid($uid, $buffer);
        // 返回推送结果
        $connection->send($ret ? 'ok' : 'fail');
    };
    $inner_text_worker->listen();
};
```

**主動推送-send.php**
```php
<?php
// 建立socket连接到内部推送端口
$client = stream_socket_client('tcp://127.0.0.1:3002', $errno, $errmsg, 1);
// 推送的数据，包含uid字段，表示是给这个uid推送
$data = array('uid'=>'uid1', 'percent'=>'88%');
// 发送数据，注意 3002 端口是Text协议的端口，Text协议需要在数据末尾加上换行符
fwrite($client, json_encode($data)."\n");
// 读取推送结果
echo fread($client, 8192);
```

## 長連結通訊難點
*建議先從Socket開始寫  

* 通訊協定：內容自訂  
* 設計「認人」方法，通訊對象必須要自己記  
* 長時間逾時：斷線心跳、資料庫再連接 

## Socket 架構
IoT <=> Socket&HTTPserver <=> HTTP API

## 除了IOT以外...  
* WEB  
  * 商家出貨管理後台  
  * 使用者 Web 操作平台 (非必要)  
* APP  
  * 使用者綁定 & 操作 APP  

 ## Web 前後端分離  
 與其開發APP、寫網頁、做IOT，不如用Cordova將網頁轉為APP節省步驟。  
 *　前端沒有 PHP ；後端沒有 HTML；php.js + jQuery + phpQuery 程式統一  

## APP Cordova (Android & iOS)  
* 拿 Web 畫面套用  
* SmartConfig (cordova-plugin-smartconfig)  
* 目前使用 WiFi SSID & BSSID (WifiWizard)  

## 觸發情境的方法
* 智能音箱  
* 語音助理  
* 其他： IFTTT, Chat Bot, 密室逃脫 ...  
需要特定步驟才能在 APP 裝置清單內， ex: WF8266  

## 免費廣告爽賺
WiFi AP 廣告 [FakeBeaconESP8266](https://github.com/markszabo/FakeBeaconESP8266/blob/master/FakeBeaconESP8266/FakeBeaconESP8266.ino)
DeAuth 攻擊 [esp8266_deauther](https://github.com/spacehuhn/esp8266_deauther)

#### 嚕阿嚕程式範例
```c
function wsSend(a,b,c,d) {
    var command = {
        'room': a, // 分區域
        'player': b, // 使用者
        'command': c, // 指令
        'value': d, // 變數、數值
        'checksum': 'xxxxxxx'
    };
    ws.send( JSON.stringify(command) );
}
```


## 來源網址
致敬「米家」：https://hoyo.idv.tw/mopcon2019/#/13
