# Core Motion

**Core Motion** คือ framework ที่อ่านข้อมูลการเคลื่อนไหวของเครื่อง เช่น เครื่องถูกเขย่า เอียง หมุน หรือกำลังขึ้นบันได ข้อมูลนี้เป็นพื้นฐานของ app ออกกำลังกาย เกม และการจดจำท่าทาง ใช้ได้ทั้ง Xcode 26 และ 27

## Sensor มีอะไรบ้าง

| Sensor | วัดอะไร | หน่วย |
|---|---|---|
| **Accelerometer** | ความเร่งของเครื่อง รวมแรงโน้มถ่วง | g (1 g ≈ 9.8 m/s²) |
| **Gyroscope** | ความเร็วในการหมุนรอบแกน x, y, z | radian ต่อวินาที |
| **Magnetometer** | สนามแม่เหล็ก ใช้หาทิศ | microtesla |
| **Barometer** | ความกดอากาศ ใช้หาความสูงที่เปลี่ยนไป | kPa, เมตร |

ค่าดิบจาก sensor แต่ละตัวมี noise และปนกันอยู่ เช่น accelerometer วัดได้ทั้งแรงโน้มถ่วงและการขยับมือรวมกัน Core Motion จึงมี **device motion** ที่รวมข้อมูลหลาย sensor แล้วแยกออกมาให้ใช้ง่าย แนะนำให้ใช้ตัวนี้

| ค่าใน `CMDeviceMotion` | ความหมาย | ใช้ทำอะไร |
|---|---|---|
| `userAcceleration` | ความเร่งที่ผู้ใช้ทำ (ตัดแรงโน้มถ่วงออกแล้ว) | ตรวจจับการเขย่า การกระแทก การเหวี่ยง |
| `gravity` | ทิศของแรงโน้มถ่วงเทียบกับเครื่อง | รู้ว่าเครื่องตั้ง นอน หรือคว่ำอยู่ |
| `rotationRate` | ความเร็วการหมุน (ปรับเทียบแล้ว) | ตรวจจับการบิด การหมุนข้อมือ |
| `attitude` | มุมของเครื่อง: `pitch`, `roll`, `yaw` | ตรวจจับการเอียง ใช้เป็นจอยควบคุม |

## อ่านข้อมูลการเคลื่อนไหว

สร้าง `CMMotionManager` **ตัวเดียว** ทั้ง app เช็กว่าเครื่องรองรับ ตั้ง sampling rate แล้วเริ่มรับข้อมูล Core Motion จะเรียก closure ทุกครั้งที่มีค่าใหม่

```swift
import CoreMotion

let motion = CMMotionManager()

func startMotionUpdates(onSample: @escaping (CMDeviceMotion) -> Void) {
    guard motion.isDeviceMotionAvailable else { return }   // Simulator ไม่มี sensor
    motion.deviceMotionUpdateInterval = 1.0 / 50.0         // 50 ครั้งต่อวินาที (50 Hz)
    motion.startDeviceMotionUpdates(to: .main) { data, _ in
        if let data { onSample(data) }
    }
}

func stopMotionUpdates() {
    motion.stopDeviceMotionUpdates()
}
```

ตัวอย่างการใช้ใน SwiftUI: เริ่มเมื่อหน้าจอแสดง และ **หยุดเมื่อออกจากหน้าจอ** เพราะ sensor ที่เปิดค้างไว้กินแบตเตอรี่ตลอดเวลา

```swift
struct MotionView: View {
    @State private var x = 0.0

    var body: some View {
        Text("แรงเหวี่ยงแกน x: \(x, specifier: "%.2f") g")
            .onAppear {
                startMotionUpdates { data in
                    x = data.userAcceleration.x
                }
            }
            .onDisappear { stopMotionUpdates() }
    }
}
```

!!! note "ต้องทดสอบบน iPad จริง"
    Simulator ไม่มี sensor การเคลื่อนไหว `isDeviceMotionAvailable` จะเป็น `false`

## เลือก sampling rate

Sampling rate คือจำนวนครั้งที่อ่านค่าต่อวินาที ยิ่งสูงยิ่งเห็นการเคลื่อนไหวละเอียด แต่กินแบตเตอรี่และ CPU มากขึ้น

| Rate | `deviceMotionUpdateInterval` | เหมาะกับ |
|---|---|---|
| 10 Hz | `1.0 / 10.0` | ท่าช้าๆ เช่น เอียงเครื่อง, ท่าทางของร่างกาย |
| 50 Hz | `1.0 / 50.0` | การจดจำท่าทางทั่วไป เช่น Motion Coach |
| 100 Hz | `1.0 / 100.0` | การเคลื่อนไหวเร็วมาก เช่น เหวี่ยงไม้ ตีลูก |

!!! warning "ใช้ rate เดียวกันตอนอัดและตอนใช้งาน"
    Model ที่ train จากข้อมูล 50 Hz ต้องได้ข้อมูล 50 Hz ตอนใช้งานด้วย ถ้า rate ต่างกัน ท่าเดียวกันจะดูเร็วหรือช้าไปในสายตา model และทายผิด

## ตรวจจับท่าแบบง่ายโดยไม่ใช้ ML

ท่าง่ายๆ ไม่ต้อง train model ก็ได้ ใช้ค่าจาก device motion ตั้งเงื่อนไขเอง เริ่มจากแบบนี้ก่อนเพื่อเข้าใจข้อมูล แล้วค่อยใช้ Create ML เมื่อท่าซับซ้อนเกินจะเขียนเงื่อนไข

```swift
func detectGesture(_ data: CMDeviceMotion) -> String? {
    let a = data.userAcceleration
    let force = (a.x * a.x + a.y * a.y + a.z * a.z).squareRoot()

    if force > 2.0 { return "เขย่า" }                      // แรงเกิน 2 g
    if abs(data.attitude.roll) > .pi / 4 { return "เอียง" }  // เอียงเกิน 45°
    return nil
}
```

ปรับตัวเลข `2.0` และ `.pi / 4` ด้วยการลองจริง: print ค่าออกมาขณะทำท่า แล้วดูว่าท่าที่ต้องการให้ค่าประมาณเท่าไหร่

## เก็บข้อมูลเป็นช่วงให้ model { #window }

Model จดจำท่าทางไม่ได้ดูค่าทีละตัว แต่ดูเป็น **ช่วงเวลา (window)** เช่น 100 ค่าล่าสุด หรือ 2 วินาทีที่ 50 Hz เพราะท่าหนึ่งท่าใช้เวลาต่อเนื่อง เก็บค่าใส่ buffer ไว้ เมื่อครบจำนวนก็ส่งให้ model แล้วเลื่อน window ต่อไป

```swift
struct MotionWindow {
    let size = 100          // 2 วินาทีที่ 50 Hz ต้องตรงกับตอน train
    var samples: [CMDeviceMotion] = []

    // คืน window เมื่อครบ แล้วเก็บครึ่งหลังไว้ทำ window ถัดไป
    mutating func add(_ sample: CMDeviceMotion) -> [CMDeviceMotion]? {
        samples.append(sample)
        guard samples.count == size else { return nil }
        let window = samples
        samples.removeFirst(size / 2)
        return window
    }
}
```

การเก็บครึ่งหลังไว้ (overlap 50%) ทำให้ model ทายบ่อยขึ้น และไม่พลาดท่าที่เริ่มกลาง window ดูวิธีส่ง window ให้ model ที่ [Core ML](core-ml.md)

## Barometer

`CMAltimeter` บอกความสูงที่เปลี่ยนไปจากตอนเริ่มวัด หน่วยเป็นเมตร ใช้รู้ว่าผู้ใช้ขึ้นหรือลงชั้น ไม่ได้บอกความสูงจากระดับน้ำทะเล

```swift
let altimeter = CMAltimeter()

func startAltitudeUpdates(onChange: @escaping (Double) -> Void) {
    guard CMAltimeter.isRelativeAltitudeAvailable() else { return }
    altimeter.startRelativeAltitudeUpdates(to: .main) { data, _ in
        if let data { onChange(data.relativeAltitude.doubleValue) }   // เมตร
    }
}
```

ชั้นหนึ่งสูงประมาณ 3 เมตร ค่าเปลี่ยนเกิน ±3 จึงแปลว่าขึ้นหรือลงหนึ่งชั้น

## Permission

| ใช้อะไร | ต้องขอ permission ไหม |
|---|---|
| `CMMotionManager` (accelerometer, gyroscope, device motion) | ไม่ต้อง |
| `CMAltimeter`, `CMPedometer`, `CMMotionActivityManager` | ต้องใส่ `NSMotionUsageDescription` ใน Info.plist |

```text
NSMotionUsageDescription
    "ใช้ข้อมูลการเคลื่อนไหวเพื่อนับการขึ้นลงชั้น"
```

ข้อความนี้จะแสดงให้ผู้ใช้เห็นตอนขออนุญาต เขียนให้บอกชัดว่าจะเอาข้อมูลไปทำอะไร ถ้าไม่ใส่ app จะ crash เมื่อเรียก API เหล่านี้

!!! tip "Activity ในตัว ไม่ต้อง train"
    ถ้าแค่อยากรู้ว่าผู้ใช้ เดิน วิ่ง ปั่นจักรยาน หรืออยู่ในรถ ใช้ `CMMotionActivityManager` และนับก้าวด้วย `CMPedometer` ได้เลย ไม่ต้อง train model เอง
