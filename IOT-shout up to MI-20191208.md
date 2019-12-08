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

## Arduino 範例程式
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



## 來源網址
致敬「米家」：https://hoyo.idv.tw/mopcon2019/#/13
