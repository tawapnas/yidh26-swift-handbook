# HackathonAI

Model บน cluster ของงาน run อยู่บน server จึงต้องเรียกผ่าน network ปกติแล้วต้องเขียนโค้ดส่ง HTTP request, แปลง JSON และจัดการ API key เอง Package **HackathonAI** ทำส่วนนี้ให้ทั้งหมด และทำให้ model บน cluster ใช้กับ `LanguageModelSession` ได้เหมือน on-device model ทุกอย่างที่เรียนในหน้า [Foundation Models](foundation-models.md) จึงใช้กับ cluster ได้เลย

Starter repo ของ Workshop 2 เพิ่ม package นี้ไว้ให้แล้ว

## Setup

### 1. เก็บ API key ไว้นอก Git

แต่ละทีมมี API key ของตัวเองบน Team card ไว้ยืนยันตัวตนกับ cluster ถ้า key หลุดขึ้น GitHub คนอื่นจะใช้ quota ของทีมได้ จึงต้องเก็บไว้ในไฟล์ที่ไม่ถูก commit

1. เพิ่ม `Secrets.plist` ลง `.gitignore` **ก่อน**สร้างไฟล์

    ```text
    // .gitignore
    Secrets.plist
    ```

2. สร้าง `Secrets.plist` แล้วใส่ key จาก Team card

    ```text
    // Secrets.plist (ห้าม commit)
    TeamKey = "team-XX-xxxxxxxx"
    ```

3. เช็กด้วย `git status` ว่าไม่เห็น `Secrets.plist` ในรายการไฟล์ที่จะ commit

### 2. เพิ่ม Info.plist key

Cluster อยู่ใน network ของงาน iPadOS จึงอาจถามผู้ใช้ก่อนว่าอนุญาตให้ app เชื่อมต่อ local network ไหม ต้องใส่ข้อความอธิบายไว้ใน Info.plist ไม่อย่างนั้นการเชื่อมต่อจะไม่สำเร็จ

```text
NSLocalNetworkUsageDescription
    "Connects to the hackathon AI server on the venue network."
```

ครั้งแรกที่ run ให้กด **Allow** เมื่อ iPad ถาม

## เปลี่ยน model ใน session

!!! mchip "Mac M chip (Xcode 27)"
    การส่ง model บน cluster เข้า `LanguageModelSession` ต้องใช้ iOS 27 SDK ครอบโค้ดด้วย `#if compiler(>=6.4)` ตาม[วิธีในหน้าเริ่มต้นใช้งาน](../getting-started.md#project-xcode-26-27) เพื่อให้เพื่อนที่ใช้ Xcode 26 ยัง build project ได้

สร้าง `OnPremLanguageModel` ด้วย constant ของ model ที่ต้องการและ team key แล้วส่งเข้า session ผ่าน parameter `model:` ที่เหลือใช้เหมือนเดิมทุกอย่าง

```swift
import FoundationModels
import HackathonAI

// On-device AFM 3
let onDevice = LanguageModelSession()

// Model ภาษาไทยบน cluster: API เดิม แค่เปลี่ยน model
let thai = LanguageModelSession(
    model: OnPremLanguageModel(.thai, teamKey: Secrets.teamKey),
    instructions: "Reply in Thai, concisely.")
let summary = try await thai.respond(to: "Summarize: \(text)")

// @Generable ใช้ได้เหมือนเดิม
@Generable struct HealthNote {
    @Guide(description: "A short, plain-language summary")
    var summary: String
    var followUps: [String]
}
let medical = LanguageModelSession(
    model: OnPremLanguageModel(.medical, teamKey: Secrets.teamKey))
let note = try await medical.respond(to: notes, generating: HealthNote.self)
```

## Fallback เมื่อ cluster ใช้ไม่ได้

การเรียก cluster ล้มเหลวได้หลายแบบ: ออกนอก Wi-Fi ของงาน, cluster มีคิวยาว หรือทีมเรียกเกิน rate limit ถ้าไม่จัดการ ผู้ใช้จะเห็น error แล้ว feature ใช้ไม่ได้

**Fallback** คือการถอยกลับมาใช้ on-device model เมื่อเรียก cluster ไม่สำเร็จ คำตอบอาจไม่ดีเท่า แต่ app ยังทำงานได้ ส่งชื่อ model กลับไปด้วยเพื่อแสดงใน UI ว่าใครเป็นคนตอบ

```swift
func summarize(_ text: String) async throws -> (String, model: String) {
    do {
        let reply = try await thai.respond(to: "Summarize: \(text)")
        return (reply.content, "Cluster · Thai")
    } catch {
        // Cluster ใช้ไม่ได้: ถอยกลับมาใช้ on-device
        let reply = try await LanguageModelSession()
            .respond(to: "สรุปข้อความนี้: \(text)")
        return (reply.content, "On-device")
    }
}
```

## Intel Mac: `OnPremClient`

!!! intel "Intel Mac (Xcode 26)"
    iOS 26 SDK ยังส่ง model บน cluster เข้า `LanguageModelSession` ไม่ได้ HackathonAI จึงมี `OnPremClient` ให้ใช้แทน เป็น async API ธรรมดาที่ส่ง prompt แล้วได้ข้อความกลับมา รองรับทั้งแบบรอคำตอบเต็มและแบบ streaming แต่ไม่มี `@Generable` และ tool calling

```swift
import HackathonAI

let client = OnPremClient(teamKey: Secrets.teamKey)

// รอคำตอบเต็ม
let reply = try await client.respond(to: prompt, model: .thai)

// Streaming: ได้ข้อความทีละส่วน
for try await chunk in client.streamResponse(to: prompt, model: .general) {
    output += chunk
}
```

Fallback ใช้หลักเดียวกัน: ครอบด้วย `do`/`catch` แล้วถอยกลับมาใช้ `LanguageModelSession()` บน device

## Production habits

!!! warning "แบ่ง cluster กันใช้"
    ทุกทีมใช้ cluster ชุดเดียวกัน และแต่ละทีมมี rate limit ถ้าทีมไหนส่ง request เยอะเกิน จะโดนปฏิเสธชั่วคราว

    - อย่าเรียก model ใน loop หรือทุกครั้งที่ผู้ใช้พิมพ์ตัวอักษร ให้เรียกเมื่อกดปุ่มหรือพิมพ์เสร็จ
    - จำกัดความยาวคำตอบเท่าที่จำเป็น คำตอบยาวใช้เวลาและทรัพยากรมากขึ้น
    - ถ้าโดน rate limit อย่ากดซ้ำทันที รอสักครู่หรือใช้ fallback

!!! danger "ข้อมูลสุขภาพ"
    - ใช้ **synthetic data เท่านั้น** ห้ามใช้ข้อมูลผู้ป่วยจริง เพราะข้อมูลสุขภาพเป็นข้อมูลอ่อนไหวตาม PDPA
    - ผลจาก `.medical` เป็นข้อมูลเบื้องต้น **ไม่ใช่การวินิจฉัยหรือคำแนะนำการรักษา** และต้องบอกผู้ใช้ชัดเจนใน UI

!!! tip "Demo ที่ไม่พัง"
    Judging ต้อง demo ใน Wi-Fi ของงาน แต่ network อาจมีปัญหาได้เสมอ ให้มี on-device fallback ไว้ app จะยังใช้ได้แม้ต่อ cluster ไม่ได้ ลองปิด Wi-Fi แล้ว run ดูก่อน demo
