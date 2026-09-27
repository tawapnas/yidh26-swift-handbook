# Hardware

## อุปกรณ์ของแต่ละทีม

| อุปกรณ์ | จำนวน | Software | หน้าที่ |
|---|---|---|---|
| MacBook Pro M chip | 1 | macOS Tahoe 26.6+ · Xcode 27 | เครื่องหลัก: iOS 27 SDK, Xcode agents |
| MacBook Pro Intel | 2 | macOS Sequoia 15.6+ · Xcode 26 | Build, test บน iPad, Git, OpenCode |
| iPad M chip | 3 | iPadOS 27 · Developer Mode | Test device คนละเครื่อง |

## เครื่องไหนทำอะไรได้

| ความสามารถ | Intel Mac · Xcode 26 | Mac M chip · Xcode 27 |
|---|:---:|:---:|
| SwiftUI, build, run บน iPad | ✓ | ✓ |
| Git และ GitHub | ✓ | ✓ |
| OpenCode | ✓ | ✓ |
| Xcode agents | ✗ | ✓ |
| API ใหม่ของ iOS 27 | ✗ | ✓ |
| Simulator ที่รองรับ Apple Intelligence | ✗ | ✓ |

!!! mchip "ทำไม Intel Mac ใช้ Xcode 27 ไม่ได้"
    Xcode 27 รองรับเฉพาะ Apple silicon ส่วน Xcode agents ต้องใช้ macOS 26.2 ขึ้นไป Intel Mac จึงใช้ Xcode 26 กับ OpenCode แทน

## iPad แต่ละรุ่นต่างกัน

บาง feature ขึ้นกับรุ่นของ iPad ดูได้จาก Team card

| Feature | รุ่นที่รองรับ |
|---|---|
| AFM 3 Core (on-device model) | iPad M chip ทุกรุ่น |
| AFM 3 Core Advanced | M3 หรือ M4 ที่มี memory 12 GB ขึ้นไป |
| LiDAR | iPad Pro เท่านั้น |

!!! tip "Test บน iPad จริงเสมอ"
    Camera, sensor และ Apple Intelligence ทำงานบน iPad ได้ครบที่สุด
