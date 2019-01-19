此链接库有一些功能上的限制：

- 只能发布QoS 0讯息，但可以订阅QoS 0或QoS 1的主题。
- 最大讯息长度（含标头）默认为128字节，可透过PubSubClient.h里的MQTT_MAX_PACKET_SIZE常数值调整。
- keepalive（保持联机）简隔时间默认为15秒，可透过PubSubClient.h里的MQTT_KEEPALIVE常数值调整。
- 客户端默认采用MQTT 3.1.1标准，如果你的MQTT服务器不支持（Mosquitto有支持），可将PubSubClient.h里的MQTT_VERSION值改成3.1。



由于MQTT协议基于TCP/IP，因此网络层要透过其他链接库实作。

采用W5100以太网络卡的场合，使用官方Ethernet链接库建立TCP/IP联机，因此这里的「网络客户」指的是EthernetClinet类型的对象。

 基于以太网络卡，建立PubSubClient对象的叙述如下：

```c
EthernetClient ethClient;        乙太网服务端类型
PubSubClient client(ethClient);  自订的 MQTT 用户端 物件名称
```



底下是本文使用的函式指令：

1. setServer(MQTT服务器, 埠号)：

   > 指定欲连接的MQTT服务器的IP地址或域名，以及埠号。

1. connect(客户端ID)

   > 联机到MQTT服务器，并传入自定义的唯一标识符。

   每个MQTT客户端都需要一个唯一的标识符（Client ID，以下称「客户端ID」），

   MQTT服务器透过客户端ID来识别用户并且纪录个别用户的状态，像是订阅的主题和通讯质量设定。

    根据MQTT 3.1.1规格书Client Identifier单元的说明，客户端ID的长度为1~23个字符，并且只允许数字和英文字母。 

   但实际的状况视服务器和客户端使用的软件而定，例如，HiveMQ公司的MQTT服务器软件允许客户端ID最大长度为65535字符（参阅该公司的这篇文件说明）， 而MQTTLens软件自动产生的客户端ID长度则是32个字符（参阅下图）。 

   话说回来，写程序的时候还是尽量遵循规格书订定的规范，以减少兼容性的问题。

1. connected()：

   > 检查客户端是否和服务器联机，传回true代表仍处于联机状态；false代表已断线。

1. publish(主题, 内容)：

   > 发布主题和内容，「主题」与「内容」参数值都是字符数组类型。 

   此函数会传回一个布尔值，true代表发布成功，false代表不成功，可能是断线或者讯息内容太长。

1. loop()：

   > 程序应该要定期呼叫loop()函式，以便和服务器保持联机并且处理接收到的讯息。

    loop()函式会传回一个布尔值，true代表仍与服务器相连；false代表与服务器断线。



ubSubClient指令：

![PubSubClient程式流程](https://swf.com.tw/images/books/IoT/MQTT/PubSubClient_flow.png)



```c
#include <SPI.h>
#include <Ethernet.h>
#include <PubSubClient.h>

// 設定MAC（實體）位址
const byte mac[] = {0xDE, 0xED, 0xBA, 0xFE, 0xFE, 0xED};
// 設定用戶端和伺服器的IP位址，請自行修改成你的設備的IP位址。
const IPAddress ip(192, 168, 1, 25);
const IPAddress server(192, 168, 1, 19);

// 設定用戶端ID
const char clientID[] = "yard001";
// 設定主題名稱
const char topic[] = "home/yard/DHT11";
// 儲存訊息的字串變數
String msgStr = "";
// 儲存字元陣列格式的訊息字串（參閱下文說明）
char json[25];

EthernetClient ethClient;        // 建立乙太網路前端物件
PubSubClient client(ethClient);  // 基於乙太網路物件，建立MQTT前端物件

void setup(){
  Serial.begin(9600);

  // 設定MQTT代理人的網址和埠號
  client.setServer(server, 1883);
  Ethernet.begin(mac, ip);

  // 留點時間給乙太網路卡進行初始化
  delay(1500);
}

void loop(){
  // 確認用戶端是否已連上伺服器
  if (!client.connected()) {
    // 若沒有連上，則執行此自訂函式。
    reconnect();
  }
  // 更新用戶端狀態
  client.loop();
 
  // 建立MQTT訊息（JSON格式的字串）
  msgStr = msgStr + "{\"temp\":" + (19 + random(10)) + ",\"humid\":" + 20 + "}";
  // 把String字串轉換成字元陣列格式
  msgStr.toCharArray(json, 25);
  // 發布MQTT主題與訊息
  client.publish(topic, json);
  // 清空MQTT訊息內容
  msgStr = "";
  
  delay(5000);
}

void reconnect() {
  // 若目前沒有和伺服器相連，則反覆執行直到連結成功…
  while (!client.connected()) {
    // 指定用戶端ID並連結MQTT伺服器
    if (client.connect(clientID)) {
      // 若連結成功，在序列埠監控視窗顯示「已連線」。
      Serial.println("connected");
    } else {
      // 若連線不成功，則顯示錯誤訊息
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      // 等候5秒，再重新嘗試連線。
      delay(5000);
    }
  }
}
```