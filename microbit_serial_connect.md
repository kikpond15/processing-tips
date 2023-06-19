---
marp: false
# theme: gaia
# theme: uncover

style: |
  section {
    justify-content: normal;
  }
  section.lead {
    justify-content: center;
    text-align: center;
  }

---
<!-- _class: lead -->
# micro:bitとProcessingでシリアル通信
---
<!-- _class: lead -->
# 1つの値を送る
---
## micro:bitコード
![bg 50% スクリーンショット 2021-08-26 153153.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/191122/c8099b9d-127f-d0ec-6524-80f05ad0862b.png)

---
### Processingコード
```java
import processing.serial.*;
Serial microbit;
float x = 0;

void setup() {
  size(640, 480);
  String []portName = Serial.list();
  println(portName);
  microbit = new Serial(this, portName[0], 115200);
}

void draw() {
  background(0);
  if (microbit.available() > 0) {
      int val = int(microbit.readString());
      x = map(val, -1023, 1023, 0, 640);
  }
  ellipse(x, height/2, 30,30);
}
```

---
上記プログラムの`portName[0]`はPCとUSB接続しているマイクロビットのポート番号を指定する．`println(portName)`をしているので，コンソールにポート番号が表示されるはずなので，micro:bitのポート番号が何番か確認し，番号を指定する．



---
<!-- _class: lead -->
# 複数の値を送る

---
### micri:bitコード
![スクリーンショット 2021-08-26 153021.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/191122/ae0fba7a-d281-2330-3696-1bb1d254bdb1.png)

---

### Processingコード
```java
import processing.serial.*;
Serial microbit;
float x = 0;
float y = 0;

void setup() {
  size(640, 480);
  String []portName = Serial.list();
  println(portName);
  microbit = new Serial(this, portName[0], 115200);
  microbit.bufferUntil(10);
}

void draw() {
  background(0);
  ellipse(x, y, 30, 30);
}

void serialEvent(Serial microbit) {
  String str = microbit.readStringUntil('\n');
  if (str != null) {
    str = trim(str);
    float[] sensors = float(split(str, ','));
    if(sensors.length > 1){
    x = map(sensors[0], -1023, 1023, 0,width);
    y = map(sensors[1], -1023, 1023, 0, height);
    }
  }
}

```
