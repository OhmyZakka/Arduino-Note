在背地裡，瀏覽器將傳遞如下的HTTP訊息和資料給網站伺服器（註：實際的HTTP訊息通常更複雜）：

![POST請求的HTTP協議內容](https://swf.com.tw/images/books/IoT/post_json/http_post_2.png)

透過HTML表單網頁傳遞資料時，我們的程式不用理會背後的HTTP訊息，瀏覽器會搞定一切。但若採用「Arduino程式」取代「瀏覽器」，直接從Arduino程式傳遞資料，那麼，與網站伺服器通訊的HTTP訊息，就必須寫在Arduino程式裡面。

## 從Arduino以POST方式傳送JSON資料

假設要透過Arduino乙太網路程式，以POST方法傳遞JSON格式的溫濕度資料給位於192.168.1.25的網站伺服器，HTTP訊息可以寫成：

![以POST方法傳遞JSON格式的溫濕度資料的HTTP訊息](https://swf.com.tw/images/books/IoT/post_json/http_post_3.png)

對應的Arduino乙太網路程式如下（client是EthernetClient物件）：

![Arduino乙太網路POST程式](https://swf.com.tw/images/books/IoT/post_json/http_post_4.png)

傳遞POST命令的HTTP協議，只有下列資訊是必要的：

- 下達命令的方法敘述（POST）

- 內容類型

- 內容長度

- 資料

  

```c
String jsonStr = "{\"temp\":23.4,\"humid\":56.7}";  // 定義JSON字串

void loop() {
  if (millis() - past > interval) {
    httpSend();   // 每隔5秒發送一次JSON資料
  }
}

void httpSend() {
  client.stop();

  // 連線到指定伺服器的5438埠號
  if (client.connect(server, 5438)) {
　　　Serial.println("connected");
　　　// 開始傳送JSON資料
    client.println("POST /temp HTTP/1.1");
    client.println("Content-Type: application/json");
    client.print("Content-Length: ");
    client.println(jsonStr.length());
    client.println();
　　　client.print(jsonStr);
　　　
    past = millis();
  } else {
    Serial.println("connection failed");
  }
}

app.use(bodyParser.json());       // 解析POST參數的JSON資料

app.post('/temp',function(req,res){   // JSON資料的處理程式
  var json=req.body;   // 取出POST資料本體

  console.log("溫度：" + json.temp);   // 在控制台顯示溫度值
  console.log("濕度：" + json.humid);  // 顯示濕度值
});
```

