#include <Wire.h>

#define AS5600_ADDR 0x36
#define REG_CONF_HIGH 0x07
#define REG_CONF_LOW  0x08

const int PWM_PIN = 7;               // AS5600 OUT 接 D7，或其他任意的pwm digital接口
const float PWM_PERIOD = 1100.0;     // 910Hz → 1.1ms 周期

void setup() {
  Serial.begin(9600);
  Wire.begin();
  pinMode(PWM_PIN, INPUT);
  delay(500);

  Serial.println("配置 AS5600 为 PWM 模式（910Hz）...");

  // 写 CONF 寄存器，配置为 PWM 输出 + 910Hz
  uint8_t confHigh = 0x38;
  uint8_t confLow  = 0xE0;  // 11100000b → PWMF=11, OUTS=10

  Wire.beginTransmission(AS5600_ADDR);
  Wire.write(REG_CONF_HIGH);
  Wire.write(confHigh);
  Wire.write(confLow);
  Wire.endTransmission();

  delay(100);

  // 再读回验证
  Wire.beginTransmission(AS5600_ADDR);
  Wire.write(REG_CONF_HIGH);
  Wire.endTransmission();
  Wire.requestFrom(AS5600_ADDR, 2);
  uint8_t readHigh = Wire.read();
  uint8_t readLow  = Wire.read();

  Serial.print("CONF HIGH: 0x"); Serial.println(readHigh, HEX);
  Serial.print("CONF LOW : 0x"); Serial.println(readLow, HEX);

  if ((readLow & 0b00110000) == 0b00100000) {
    Serial.println("已进入 PWM 模式");
  } else {
    Serial.println("配置失败，请检查 PGO 引脚或接线");
  }

  Serial.println("开始输出原始角度值（无滤波）...");
}

void loop() {
  uint32_t high_time = pulseIn(PWM_PIN, HIGH, 30000);  // 等待 30ms

  if (high_time == 0) {
    Serial.println("未检测到 PWM 信号");
  } else if (high_time > 1200) {
    Serial.print("异常脉冲宽度："); Serial.print(high_time); Serial.println(" us");
  } else {
    float duty = high_time / PWM_PERIOD;
    float angle = duty * 360.0;

    Serial.print("High: "); Serial.print(high_time); Serial.print(" us\t");
    Serial.print("Duty: "); Serial.print(duty * 100, 1); Serial.print("%\t");
    Serial.print("Angle: "); Serial.print(angle, 1); Serial.println("°");
  }

  delay(300);
}
