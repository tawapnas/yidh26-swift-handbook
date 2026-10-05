# Dynamic Profile

!!! mchip "Mac M chip (Xcode 27)"
    Dynamic Profile เป็น API ใหม่ของ iOS 27 ใช้ได้เฉพาะ Xcode 27 ครอบโค้ดด้วย `#if compiler(>=6.4)` และใส่ `@available(iOS 27, *)` ให้ type ที่ใช้ ตาม[วิธีในหน้าเริ่มต้นใช้งาน](../getting-started.md#project-xcode-26-27) เพื่อให้เพื่อนที่ใช้ Xcode 26 ยัง build project ได้

## ปัญหาที่ Dynamic Profile แก้

ใน app จริง model มักต้องทำตัวต่างกันตามสถานการณ์ เช่น ผู้ช่วยติวที่ตอนแรก **อธิบาย** เนื้อหา แล้วพอผู้ใช้พร้อมก็เปลี่ยนเป็น **ถาม quiz** สองโหมดนี้ต้องใช้ instructions, tools และ temperature ต่างกัน

ถ้าใช้แค่ `LanguageModelSession(instructions:)` แบบเดิม ทั้งหมดนี้ต้องกำหนดตอนสร้าง session และเปลี่ยนทีหลังไม่ได้ ทางเลือกคือสร้าง session ใหม่ ซึ่งทำให้ model ลืมบทสนทนาที่ผ่านมา

**Dynamic Profile** ให้เราเขียน "profile" ของ session แบบเดียวกับเขียน SwiftUI view: ใน `body` เลือกว่าจะใช้ instructions, tools, model และ option แบบไหน ตาม state ตอนนั้น เมื่อ state เปลี่ยน session จะเปลี่ยน profile ให้เอง โดยบทสนทนาเดิมยังอยู่

## ส่วนประกอบ

| ส่วน | ทำอะไร | เทียบกับ SwiftUI |
|---|---|---|
| `DynamicProfile` | Protocol ของ profile มี `body` ที่คืน profile ที่ใช้อยู่ตอนนี้ | `View` |
| `Profile { … }` | ใส่ `Instructions` และ tool ที่ profile นี้ใช้ | `VStack { … }` |
| Modifier เช่น `.temperature()` | ตั้ง option ของ profile เช่น model, ความยาวคำตอบ | `.padding()` |
| `@SessionPropertyEntry` | ประกาศ state ของ session พร้อมค่าเริ่มต้น | `@Entry` |
| `@SessionProperty` | อ่านหรือเปลี่ยน state ใน profile หรือ tool | `@Environment` |
| `session.properties` | อ่านหรือเปลี่ยน state จากโค้ดใน app | — |

## 1. Profile แรก

Profile ที่ง่ายที่สุดมี instructions อย่างเดียว แล้วตั้ง option ด้วย modifier สร้าง session ด้วย `LanguageModelSession(profile:)`

```swift
import FoundationModels

struct ExplainProfile: LanguageModelSession.DynamicProfile {
    var body: some DynamicProfile {
        Profile {
            Instructions("อธิบายเนื้อหาเป็นภาษาไทยแบบเข้าใจง่าย ยกตัวอย่างประกอบ")
        }
        .temperature(0.7)
        .maximumResponseTokens(300)
    }
}

let session = LanguageModelSession(profile: ExplainProfile())
let reply = try await session.respond(to: "อธิบาย optional ใน Swift")
```

ถึงตรงนี้ยังทำได้เหมือน session แบบเดิม จุดเด่นของ Dynamic Profile อยู่ที่ตัวอย่างถัดไป

## 2. เปลี่ยน profile ตาม state

ขั้นแรก ประกาศ state ของ session ใน `extension SessionPropertyValues` ด้วย `@SessionPropertyEntry` พร้อมค่าเริ่มต้น

```swift
enum TutorMode: Sendable {
    case explain, quiz
}

extension SessionPropertyValues {
    @SessionPropertyEntry var tutorMode: TutorMode = .explain
}
```

จากนั้นใน profile อ่าน state ด้วย `@SessionProperty` แล้วใช้ `if`/`else` เลือก profile แต่ละโหมดมี instructions, tools และ option ของตัวเอง

```swift
struct TutorProfile: LanguageModelSession.DynamicProfile {
    @SessionProperty(\.tutorMode) var mode

    var body: some DynamicProfile {
        if mode == .explain {
            Profile {
                Instructions("อธิบายเนื้อหาเป็นภาษาไทยแบบเข้าใจง่าย ยกตัวอย่างประกอบ")
            }
            .temperature(0.7)
        } else {
            Profile {
                Instructions("ถามคำถามทีละข้อจากเนื้อหาที่คุยกันมา แล้วรอให้ผู้ใช้ตอบ")
                TodayTool()
            }
            .temperature(0.2)
            .maximumResponseTokens(200)
        }
    }
}
```

สุดท้าย เปลี่ยน state จากโค้ดใน app เช่น เมื่อผู้ใช้กดปุ่ม "เริ่ม quiz" session จะใช้ profile ใหม่ในการตอบครั้งถัดไป และ model ยังจำได้ว่าอธิบายอะไรไปแล้ว จึงถามจากเนื้อหานั้นได้

```swift
let session = LanguageModelSession(profile: TutorProfile())
try await session.respond(to: "อธิบาย optional ใน Swift")

session.properties.tutorMode = .quiz      // เช่น ตอนกดปุ่ม "เริ่ม quiz"
try await session.respond(to: "เริ่มเลย")   // ใช้ profile โหมด quiz
```

!!! note "`body` ต้องได้ profile เดียวเสมอ"
    ทุกทางของ `if`/`else` ต้องคืน `Profile` หนึ่งตัวพอดี ถ้าวาง `Profile` สองตัวติดกันจะ compile ไม่ผ่าน เพราะ session ใช้ได้ทีละ profile

## 3. ให้ tool เปลี่ยน state

Tool อ่านและเปลี่ยน state ของ session ได้ด้วย `@SessionProperty` เหมือนกัน ทำให้ model สลับโหมดได้เองเมื่อเห็นว่าผู้ใช้พร้อม ไม่ต้องรอผู้ใช้กดปุ่ม (ดูพื้นฐานของ tool ที่ [Tool calling](tool-calling.md))

```swift
struct StartQuizTool: Tool {
    let name = "startQuiz"
    let description = "เปลี่ยนเป็นโหมด quiz เมื่อผู้ใช้พร้อมทำแบบทดสอบ"

    @SessionProperty(\.tutorMode) var mode

    @Generable
    struct Arguments {}

    func call(arguments: Arguments) async throws -> String {
        mode = .quiz
        return "เปลี่ยนเป็นโหมด quiz แล้ว"
    }
}
```

ใส่ `StartQuizTool()` ไว้ใน `Profile` ของโหมด explain พอผู้ใช้พิมพ์ว่า "เข้าใจแล้ว ลองถามหน่อย" model จะเรียก tool นี้ แล้วเปลี่ยนไปโหมด quiz เอง

## 4. ใช้คนละ model ในแต่ละโหมด

Modifier `.model()` กำหนดได้ว่า profile นี้ใช้ model ตัวไหน ใช้คู่กับ [HackathonAI](hackathon-ai.md) ได้ เช่น โหมดอธิบายใช้ model ภาษาไทยบน cluster ส่วนโหมด quiz ที่ต้องตอบเร็วใช้ on-device

```swift
struct ThaiTutorProfile: LanguageModelSession.DynamicProfile {
    @SessionProperty(\.tutorMode) var mode
    let thaiModel: any LanguageModel   // เช่น OnPremLanguageModel(.thai, teamKey: …)

    var body: some DynamicProfile {
        if mode == .explain {
            Profile { Instructions("อธิบายเป็นภาษาไทยที่เป็นธรรมชาติ") }
                .model(thaiModel)
        } else {
            Profile { Instructions("ถามคำถามสั้นๆ ทีละข้อ") }
                .model(SystemLanguageModel.default)
        }
    }
}
```

## 5. ดูว่า model กำลังทำอะไร

Modifier กลุ่ม `on…` ให้ run โค้ดของเราเมื่อเกิด event ใน session เช่น แสดงสถานะ "กำลังค้นโน้ต…" ใน UI ระหว่างที่ model เรียก tool หรือ print log ไว้ debug

```swift
Profile {
    Instructions("ตอบสั้นๆ")
    StartQuizTool()
}
.onToolCall { call in
    print("เรียก tool:", call.toolName)
}
.onResponse {
    print("ตอบเสร็จแล้ว")
}
```

## Modifier ที่ใช้ได้

| Modifier | ใช้ทำอะไร |
|---|---|
| `.model(_:)` | เลือก model ของ profile นี้ |
| `.temperature(_:)` | ค่ายิ่งสูงคำตอบยิ่งหลากหลาย ค่าต่ำได้คำตอบคงที่กว่า |
| `.maximumResponseTokens(_:)` | จำกัดความยาวคำตอบ |
| `.samplingMode(_:)` | เลือกวิธีสุ่มคำตอบของ model |
| `.reasoningLevel(_:)` | ให้ model คิดก่อนตอบมากหรือน้อย: `.light`, `.moderate`, `.deep` (สำหรับ model ที่รองรับ) |
| `.toolCallingMode(_:)` | `.allowed` ให้ model เลือกเอง, `.required` ต้องเรียก tool, `.disallowed` ห้ามเรียก |
| `.historyTransform(_:)` | ปรับบทสนทนาก่อนส่งให้ model เช่น ตัดข้อความเก่าออก |
| `.onPrompt`, `.onResponse` | Run โค้ดเมื่อส่ง prompt หรือได้คำตอบ |
| `.onToolCall`, `.onToolOutput` | Run โค้ดเมื่อ model เรียก tool หรือ tool ส่งผลลัพธ์กลับ |
| `.onActivate`, `.onDeactivate` | Run โค้ดเมื่อ profile นี้เริ่มหรือเลิกถูกใช้ |

!!! tip "เมื่อไหร่ควรใช้ Dynamic Profile"
    ถ้า session ทำงานแบบเดียวตลอด `LanguageModelSession(instructions:)` แบบเดิมง่ายกว่า ใช้ Dynamic Profile เมื่อ app มีหลายโหมดในบทสนทนาเดียว, ต้องเปลี่ยน model ตามงาน หรืออยากให้ model สลับโหมดเองผ่าน tool
