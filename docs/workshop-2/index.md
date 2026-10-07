# Bring intelligence to your app with Foundation Models and on-prem AI

Workshop นี้ทำให้ app ของทีมใช้ language model ได้ ทั้ง model ที่ run บน iPad, บน server ของ Apple และบน AI cluster ของงาน ด้วยโค้ดชุดเดียวกัน เหมาะกับ idea ที่ต้องใช้ภาษา เช่น สรุป แปล ตอบคำถาม หรือจัดข้อความให้เป็นข้อมูลที่มีโครงสร้าง

ต่อจาก Workshop 1: ใช้ coding agent และ Git workflow เดิม แต่เพิ่ม AI เข้าไปใน app แทนการใช้ AI ช่วยเขียนโค้ด

## สิ่งที่จะได้เรียน { #outcomes }

1. **เลือก model ให้เหมาะกับงาน**: เข้าใจว่าแต่ละที่ต่างกันอย่างไรในเรื่อง privacy, ความเร็ว, ความเก่ง, ภาษาไทย และการใช้งานนอกสถานที่
2. **ใช้ `LanguageModelSession`**: ถามแล้วได้คำตอบ, แสดงคำตอบทีละส่วน (streaming), ให้ model ตอบเป็น Swift struct (`@Generable`) และให้ model เรียกฟังก์ชันของเรา (tool calling)
3. **ใช้ model บน cluster**: สลับไปใช้ model ภาษาไทยหรือ model การแพทย์ผ่าน package **HackathonAI**
4. **รับมือเมื่อ model ใช้ไม่ได้**: เช็กว่า iPad มี Apple Intelligence ไหม และถ้า cluster ไม่ว่างหรือเชื่อมต่อไม่ได้ ให้ app ถอยกลับมาใช้ on-device model เองโดยไม่ crash

## แนวคิดหลัก { #concepts }

### 3 ที่ run model

- **On-device AFM 3**: Apple Foundation Models รุ่นที่ 3 run บน iPad เลย ฟรี ข้อมูลไม่ออกจากเครื่อง ตอบเร็ว และใช้ได้แม้ไม่มี internet แต่ model มีขนาดเล็ก เหมาะกับงานสั้นๆ ที่ไม่ซับซ้อน
- **Private Cloud Compute (PCC)**: AFM 3 ที่ run บน server ของ Apple เก่งกว่าบน device และ Apple ออกแบบให้ข้อมูลยังเป็นส่วนตัว แต่ต้องต่อ internet
- **AI cluster ของงาน**: Mac cluster ที่ผู้จัด run open-source model เฉพาะทางไว้ให้ เช่น model ภาษาไทยและ model การแพทย์ ใช้ได้เฉพาะใน Wi-Fi ของงาน

### API เดียวกันทุกที่

ทั้ง 3 ที่ใช้ `LanguageModelSession` ของ Foundation Models framework จะสลับ model ก็แค่เปลี่ยน model ที่ส่งให้ session ส่วน `respond(to:)`, streaming, `@Generable` และ tool calling ใช้โค้ดเดิมทั้งหมด

```swift
// On-device AFM 3
let onDevice = LanguageModelSession()

// Model ภาษาไทยบน cluster: ต่างกันแค่ model ที่ส่งเข้าไป
let thai = LanguageModelSession(
    model: OnPremLanguageModel(.thai, teamKey: Secrets.teamKey))

// เรียกใช้แบบเดียวกันทุก model
let a = try await onDevice.respond(to: "สรุปข้อความนี้: \(text)")
let b = try await thai.respond(to: "สรุปข้อความนี้: \(text)")
```

เริ่มจาก on-device ก่อนเสมอ แล้วค่อยส่งงานที่ยากหรือต้องการภาษาไทยที่ดีกว่าไปที่ PCC หรือ cluster ดูวิธีเลือกได้ที่ [เลือก model](models.md)

## MacBook แต่ละเครื่องทำอะไรได้บ้าง { #machines }

Feature บางอย่างต้องใช้ iOS 27 SDK ซึ่งมีเฉพาะใน Xcode 27 บน {{ mac.apple }}

!!! mchip "{{ mac.apple }} · Xcode 27"
    ทำได้ครบทุกส่วน: ใช้ on-device AFM 3, ส่ง model บน cluster เข้า `LanguageModelSession` ได้โดยตรง, ใช้ PCC, [Dynamic Profile](dynamic-profiles.md) และส่งรูปให้ AFM 3 วิเคราะห์

!!! intel "{{ mac.intel }} · Xcode 26"
    ใช้ `LanguageModelSession` กับ on-device AFM 3 ได้ตามปกติ แต่ iOS 26 SDK ยังส่ง model บน cluster เข้า session ไม่ได้ จึงต้องเรียก cluster ผ่าน [`OnPremClient`](hackathon-ai.md) แทน ซึ่งเป็น API แยกที่ใช้งานง่ายแต่ไม่มี `@Generable` และ tool calling

    ถ้าอยากลองแบบเต็ม ให้นั่งคู่กับเพื่อนที่ใช้ {{ mac.apple }} ช่วง on-prem AI

## หัวข้อหลัก { #pages }

อ่านตามลำดับนี้จะเข้าใจง่ายที่สุด

| หน้า | เนื้อหา |
|---|---|
| [เลือก model](models.md) | Model แต่ละตัว run ที่ไหน เหมาะกับงานแบบไหน และวิธีเช็กว่าเครื่องรองรับ |
| [Foundation Models](foundation-models.md) | พื้นฐาน `LanguageModelSession` บน device: ถาม-ตอบ, streaming, `@Generable` |
| [Tool calling](tool-calling.md) | ให้ model เรียกฟังก์ชันของเราเพื่อดึงข้อมูลหรือทำ action ใน app |
| [Dynamic Profile](dynamic-profiles.md) | เปลี่ยน instructions, tools และ model ของ session ตาม state (iOS 27) |
| [HackathonAI](hackathon-ai.md) | Setup API key, ใช้ model บน cluster, fallback และกติกาการใช้ cluster ร่วมกัน |

## ลงมือปฏิบัติ { #hands-on }

เปิด starter repo ของ Workshop 2 แล้วเลือกทำ 1 track ทุก track ทำตามขั้นตอนเดียวกัน

1. **ทำบน on-device model ก่อน** ให้ feature ทำงานได้โดยไม่ต้องพึ่ง network ดู [Foundation Models](foundation-models.md)
2. **สลับไปใช้ model บน cluster** ที่เหมาะกับ track แล้วเทียบผลกับ on-device ดู [HackathonAI](hackathon-ai.md#model-session)
3. **ใส่ fallback** ถ้าเรียก cluster ไม่สำเร็จ ให้กลับไปใช้ on-device ดู [Fallback](hackathon-ai.md#fallback-cluster)
4. **แสดงใน UI ว่า model ไหนเป็นคนตอบ** เช่น ป้าย "On-device" หรือ "Cluster · Thai" จะได้รู้ว่า fallback ทำงานอยู่หรือเปล่า และเปรียบเทียบคุณภาพได้

### Track: Thai assistant · `.thai`

รับข้อความภาษาไทยแล้วสรุปหรือแปลเป็นภาษาอังกฤษ ใช้ model ภาษาไทยบน cluster เพื่อให้ภาษาเป็นธรรมชาติ และ fallback ไป on-device เมื่อเชื่อมต่อ cluster ไม่ได้

### Track: Health note helper · `.medical`

รับบันทึกอาการที่พิมพ์แบบอิสระ แล้วให้ model ตอบเป็น struct ด้วย `@Generable`: สรุปอาการสั้นๆ และคำถามที่ควรถามต่อ ต้องแสดงใน UI ชัดเจนว่า **เป็นข้อมูลเบื้องต้น ไม่ใช่การวินิจฉัย** และใช้ข้อมูลสมมติ (synthetic) เท่านั้น

### Track: Smart notes · `.general`

App จดโน้ตที่เลือก model ให้เอง: โน้ตสั้นส่งไป on-device ซึ่งเร็วและฟรี ส่วนโน้ตยาวหรือซับซ้อนส่งไป model ทั่วไปบน cluster ทั้งหมดผ่านฟังก์ชัน router ตัวเดียว

!!! tip "ตามไม่ทัน"
    ทำขั้น 1 บน on-device ให้เสร็จก่อนเสมอ แค่นี้ก็ demo ได้แล้ว ถ้า cluster มีปัญหาหรือเวลาไม่พอ checkout branch [`checkpoint/workshop-2`] ที่ทำขั้น 1–2 ไว้ให้ แล้วทำ fallback ต่อ
