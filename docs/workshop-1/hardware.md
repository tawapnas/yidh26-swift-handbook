# Hardware

แต่ละทีมมีเครื่องสองแบบที่ทำงานได้ไม่เท่ากัน รู้ไว้ก่อนจะได้แบ่งงานถูก: งานที่ต้องใช้ iOS 27 ทำบน Mac M chip ส่วนงานอื่นทั้งหมดทำบน Intel Mac ได้

## อุปกรณ์ของแต่ละทีม

| อุปกรณ์ | จำนวน | Software | หน้าที่ |
|---|---|---|---|
| MacBook Pro M chip | 1 | macOS Tahoe 26.6+ · Xcode 27 | เครื่องหลักของทีม ใช้ API ใหม่ของ iOS 27 และ Xcode agents |
| MacBook Pro Intel | 2 | macOS Sequoia 15.6+ · Xcode 26 | งานที่ไม่ต้องใช้ iOS 27: เขียนโค้ด, build, test บน iPad, Git และ OpenCode |
| iPad M chip | 3 | iPadOS 27 · Developer Mode | Test device คนละเครื่อง รองรับ Apple Intelligence และมีกล้อง |
| AI cluster ของงาน | ใช้ร่วมกันทุกทีม | ผ่าน package HackathonAI | Model ทั่วไป ภาษาไทย และการแพทย์ ไม่มีค่าใช้จ่าย ข้อมูลไม่ออกนอกงาน ดู [Workshop 2](../workshop-2/index.md) |

## เครื่องไหนทำอะไรได้

| ความสามารถ | Intel Mac · Xcode 26 | Mac M chip · Xcode 27 |
|---|:---:|:---:|
| SwiftUI, build, run บน iPad | ✓ | ✓ |
| Git และ GitHub | ✓ | ✓ |
| OpenCode | ✓ | ✓ |
| Xcode agents | ✗ | ✓ |
| API ใหม่ของ iOS 27 | ✗ | ✓ |
| Simulator ที่รองรับ Apple Intelligence | ✗ | ✓ |

!!! intel "Intel Mac (Xcode 26)"
    ใช้ Xcode 27 ไม่ได้ เพราะ Xcode 27 รองรับเฉพาะ Apple silicon ส่วน Xcode agents ต้องใช้ macOS 26.2 ขึ้นไป Intel Mac จึงใช้ Xcode 26 กับ OpenCode แทน

    Xcode 26 ยังมี Foundation Models, App Intents, Vision, VisionKit, Create ML และ Core ML ครบ งานส่วนใหญ่ของ hackathon จึงทำบน Intel Mac ได้

## iPad แต่ละรุ่นต่างกัน

iPad ทุกเครื่องเป็น M chip แต่บาง feature ขึ้นกับรุ่นและ memory ดูรุ่นของ iPad ทีมได้จาก Team card ก่อนเลือก feature ที่จะทำ

| Feature | รุ่นที่รองรับ |
|---|---|
| AFM 3 Core (on-device model) | iPad M chip ทุกรุ่น |
| AFM 3 Core Advanced | M3 หรือ M4 ที่มี memory 12 GB ขึ้นไป |
| LiDAR | iPad Pro เท่านั้น |

!!! tip "Test บน iPad จริงเสมอ"
    Simulator จำลองได้ไม่ครบ กล้อง, sensor และ Apple Intelligence ทำงานได้ครบบน iPad จริงเท่านั้น แต่ละคนใช้ iPad ของตัวเองเป็นเครื่อง test ตลอดงาน
