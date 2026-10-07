# Bring intelligence to your app with Foundation Models and on-prem AI

ในงานนี้ app ของทีมเรียกใช้ language model ได้จาก 3 ที่ แต่ละที่มีจุดเด่นต่างกัน:

- **On-device AFM 3**: Apple Foundation Models รุ่นที่ 3 run บน iPad เลย ฟรี ข้อมูลไม่ออกจากเครื่อง ตอบเร็ว และใช้ได้แม้ไม่มี internet แต่ model มีขนาดเล็ก เหมาะกับงานสั้นๆ ที่ไม่ซับซ้อน
- **Private Cloud Compute (PCC)**: AFM 3 ที่ run บน server ของ Apple เก่งกว่าบน device และ Apple ออกแบบให้ข้อมูลยังเป็นส่วนตัว แต่ต้องต่อ internet
- **AI cluster ของงาน**: Mac cluster ที่ผู้จัด run open-source model เฉพาะทางไว้ให้ เช่น model ภาษาไทยและ model การแพทย์ ใช้ได้เฉพาะใน Wi-Fi ของงาน

ทั้ง 3 ที่ใช้ **API เดียวกัน** คือ `LanguageModelSession` ของ Foundation Models framework จะสลับ model ก็แค่เปลี่ยน model ที่ส่งให้ session ส่วน `respond(to:)`, streaming, `@Generable` และ tool calling ใช้โค้ดเดิมทั้งหมด

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

แนวทางที่แนะนำคือเริ่มจาก on-device ก่อน แล้วค่อยส่งงานที่ยากหรือต้องการภาษาไทยที่ดีกว่าไปที่ PCC หรือ cluster ดูวิธีเลือกได้ที่ [เลือก model](models.md)

## สิ่งที่จะได้เรียน

- **เลือก model ให้เหมาะกับงาน**: เข้าใจว่าแต่ละที่ต่างกันอย่างไรในเรื่อง privacy, ความเร็ว, ความเก่ง, ภาษาไทย และการใช้งานนอกสถานที่
- **ใช้ `LanguageModelSession`**: ถามแล้วได้คำตอบ, แสดงคำตอบทีละส่วน (streaming), ให้ model ตอบเป็น Swift struct (`@Generable`) และให้ model เรียกฟังก์ชันของเรา (tool calling)
- **รับมือกับเครื่องที่ไม่รองรับ**: เช็กว่า iPad มี Apple Intelligence ไหม และทำให้ app ยังใช้ได้แม้ไม่มี
- **ใช้ model บน cluster**: สลับไปใช้ model ภาษาไทยหรือ model การแพทย์ผ่าน package **HackathonAI**
- **ทำ fallback**: ถ้า cluster ไม่ว่างหรือเชื่อมต่อไม่ได้ ให้ app ถอยกลับมาใช้ on-device model เองโดยไม่ crash

## ทำอะไรได้บ้างตามเครื่อง

Feature บางอย่างต้องใช้ iOS 27 SDK ซึ่งมีเฉพาะใน Xcode 27 บน {{ mac.apple }}

!!! mchip "{{ mac.apple }} · Xcode 27"
    ทำได้ครบทุกส่วน: ใช้ on-device AFM 3, ส่ง model บน cluster เข้า `LanguageModelSession` ได้โดยตรง, ใช้ PCC และส่งรูปให้ AFM 3 วิเคราะห์

!!! intel "{{ mac.intel }} · Xcode 26"
    ใช้ `LanguageModelSession` กับ on-device AFM 3 ได้ตามปกติ แต่ iOS 26 SDK ยังส่ง model บน cluster เข้า session ไม่ได้ จึงต้องเรียก cluster ผ่าน `OnPremClient` แทน ซึ่งเป็น API แยกที่ใช้งานง่ายแต่ไม่มี `@Generable` และ tool calling

    ถ้าอยากลองแบบเต็ม ให้นั่งคู่กับเพื่อนที่ใช้ {{ mac.apple }} ช่วง on-prem AI

## ในหมวดนี้

อ่านตามลำดับนี้จะเข้าใจง่ายที่สุด

| หน้า | เนื้อหา |
|---|---|
| [เลือก model](models.md) | Model แต่ละตัว run ที่ไหน เหมาะกับงานแบบไหน และวิธีเช็กว่าเครื่องรองรับ |
| [Foundation Models](foundation-models.md) | พื้นฐาน `LanguageModelSession` บน device: ถาม-ตอบ, streaming, `@Generable` |
| [Tool calling](tool-calling.md) | ให้ model เรียกฟังก์ชันของเราเพื่อดึงข้อมูลหรือทำ action ใน app |
| [Dynamic Profile](dynamic-profiles.md) | เปลี่ยน instructions, tools และ model ของ session ตาม state (iOS 27) |
| [HackathonAI](hackathon-ai.md) | Setup API key, ใช้ model บน cluster, fallback และกติกาการใช้ cluster ร่วมกัน |

## Hands-on

เปิด starter repo ของ Workshop 2 ซึ่งเพิ่ม package HackathonAI ไว้ให้แล้ว เลือกทำ 1 track ตามขั้นตอนนี้

1. **ทำบน on-device model ก่อน** ให้ feature ทำงานได้โดยไม่ต้องพึ่ง network
2. **สลับไปใช้ model บน cluster** ที่เหมาะกับ track แล้วเทียบผลกับ on-device
3. **ใส่ fallback** ถ้าเรียก cluster ไม่สำเร็จ ให้กลับไปใช้ on-device
4. **แสดงใน UI ว่า model ไหนเป็นคนตอบ** เช่น ป้าย "On-device" หรือ "Cluster · Thai" จะได้รู้ว่า fallback ทำงานอยู่หรือเปล่า และเปรียบเทียบคุณภาพได้

### Thai assistant · `.thai`

รับข้อความภาษาไทยแล้วสรุปหรือแปลเป็นภาษาอังกฤษ ใช้ model ภาษาไทยบน cluster เพื่อให้ภาษาเป็นธรรมชาติ และ fallback ไป on-device เมื่อเชื่อมต่อ cluster ไม่ได้

### Health note helper · `.medical`

รับบันทึกอาการที่พิมพ์แบบอิสระ แล้วให้ model ตอบเป็น struct ด้วย `@Generable`: สรุปอาการสั้นๆ และคำถามที่ควรถามต่อ ต้องแสดงใน UI ชัดเจนว่า **เป็นข้อมูลเบื้องต้น ไม่ใช่การวินิจฉัย** และใช้ข้อมูลสมมติ (synthetic) เท่านั้น

### Smart notes · `.general`

App จดโน้ตที่เลือก model ให้เอง: โน้ตสั้นส่งไป on-device ซึ่งเร็วและฟรี ส่วนโน้ตยาวหรือซับซ้อนส่งไป model ทั่วไปบน cluster ทั้งหมดผ่านฟังก์ชัน router ตัวเดียว

!!! mchip "{{ mac.apple }} · Xcode 27"
    เพิ่ม PCC เป็นอีกตัวเลือกใน track ที่ทำ แล้วเทียบผลกับ on-device และ cluster

## Idea prompts

ไอเดียสำหรับต่อยอดเป็น project ของทีม

- **Study buddy ไทย-อังกฤษ**: วาง lecture notes แล้วได้คำถาม quiz พร้อมเฉลย
- **ผู้ช่วยคลินิก**: จัดบันทึกผู้ป่วย (synthetic) ให้เป็นโครงสร้างที่หมอ review ได้เร็ว
- **Draft แล้วขัดเกลา**: ให้ on-device ร่างคำตอบทันที แล้วส่งไป cluster เพื่อปรับให้ดีขึ้น ผู้ใช้ไม่ต้องรอ
- **Journaling app ที่เป็นส่วนตัว**: วิเคราะห์อารมณ์หรือสรุปบันทึก โดยข้อมูลไม่ออกจากเครื่องหรือจากในงาน
- **ใช้ร่วมกับ Workshop 5**: scan เมนูหรือฉลากยาภาษาไทยด้วย Vision แล้วให้ cluster อธิบายเป็นภาษาที่เข้าใจง่าย

## Exit checklist

- [ ] มี feature ที่ใช้ `LanguageModelSession` กับ on-device model
- [ ] สลับไปใช้ model บน cluster ได้ ถ้าใช้ {{ mac.intel }} ให้เรียกผ่าน `OnPremClient`
- [ ] Fallback กลับมา on-device ได้เมื่อ cluster เชื่อมต่อไม่ได้
- [ ] UI แสดงว่า model ไหนเป็นคนตอบ
- [ ] `Secrets.plist` ไม่อยู่ใน commit
