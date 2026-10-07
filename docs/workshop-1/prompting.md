# Prompting, guardrails & best practices

Agent ทำงานได้ดีแค่ไหนขึ้นกับ prompt ที่ได้รับ prompt ที่คลุมเครือจะได้โค้ดที่เดาเอาเอง แก้ไฟล์ที่ไม่เกี่ยว หรือทำเกินที่ขอ หน้านี้รวมวิธีเขียน prompt ให้ได้ผลตรงที่ต้องการ และกติกาที่ทำให้ใช้ agent ได้อย่างปลอดภัย

## Prompt template

ใช้ได้กับทั้ง Xcode agents และ OpenCode และมีใน repo ของทีมเป็น GitHub issue template ด้วย

```text
GOAL:        Add a search bar that filters the team list by name.
CONTEXT:     TeamListView.swift, TeamMember.swift
CONSTRAINTS: SwiftUI only, iOS 26 deployment target, no iOS 27-only APIs,
             no new packages.
DONE WHEN:   It builds, filtering works in the Preview,
             and existing features still work.
PROCESS:     Show me a plan first. Wait for my approval before editing.
```

| ส่วน | ใส่อะไร | ทำไม |
|---|---|---|
| **GOAL** | ผลลัพธ์ที่ต้องการ 1 อย่าง ชัดเจน | Agent รู้ว่าต้องทำอะไร และไม่ทำเกิน |
| **CONTEXT** | ไฟล์หรือหน้าจอที่เกี่ยวข้อง | Agent ไม่ต้องเดาว่าต้องแก้ตรงไหน |
| **CONSTRAINTS** | ข้อห้ามและข้อจำกัด เช่น iOS 26, ห้ามเพิ่ม package | ป้องกันโค้ดที่เพื่อนบน {{ mac.intel }} build ไม่ผ่าน |
| **DONE WHEN** | วิธีตรวจว่างานเสร็จจริง | Agent ตรวจงานตัวเองได้ก่อนส่ง |
| **PROCESS** | ขอ plan ก่อนลงมือเสมอ | เราได้แก้ความเข้าใจผิดก่อนที่จะมีโค้ด |

Prompt เขียนเป็นภาษาไทยหรืออังกฤษก็ได้ แต่ชื่อไฟล์ ชื่อ type และศัพท์เทคนิคให้เขียนตามในโค้ด

## Prompt ดี vs ไม่ดี

| ✗ ไม่ดี | ✓ ดี | ต่างกันตรงไหน |
|---|---|---|
| "ทำ app ให้สวยขึ้น" | "Add dark mode support to TeamCardView using system colors." | บอกหน้าจอและวิธีที่ต้องการ แทนคำว่า "สวย" ที่ agent ต้องเดา |
| "แก้ bug" | "Tapping Save crashes with index out of range in TeamStore.swift line 42. Find the cause and fix it." | บอกอาการ, error และตำแหน่ง agent หาสาเหตุได้ทันที |
| "เพิ่ม feature ทั้งหมดตาม idea" | แบ่งเป็น task เล็กๆ ทีละ feature | งานใหญ่ทำให้ agent หลงทางและ review ยาก |

## Guardrails

!!! danger "ห้ามทำ"
    - **ใส่ API key, password หรือข้อมูลส่วนตัวใน prompt** prompt ถูกส่งไปที่ server ของ model
    - **Commit `Secrets.plist` หรือ key ใดๆ** ถ้าขึ้น GitHub แล้ว ลบทีหลังก็ยังอยู่ในประวัติ
    - **ใช้ข้อมูลผู้ป่วยจริง** ใช้ synthetic data เท่านั้น
    - **กด allow คำสั่งที่ไม่เข้าใจ** ถาม agent ก่อนว่าคำสั่งนั้นทำอะไร

!!! success "ต้องทำทุกครั้ง"
    - **Commit ก่อนให้ agent เริ่มงาน** จะได้ย้อนกลับได้ในครั้งเดียว
    - **อ่าน plan ก่อน approve** แก้ plan ง่ายกว่าแก้โค้ด
    - **Review diff ทุกไฟล์ก่อน commit** ดูว่าไม่มีการแก้ไฟล์ที่ไม่เกี่ยว
    - **Run บน iPad จริงก่อน push** build ผ่านไม่ได้แปลว่าทำงานถูก

## Best practices

- **Task เล็ก**: 1 prompt = 1 feature หรือ 1 bug งานเล็กได้ผลแม่นกว่า และ review เสร็จเร็ว
- **Plan ก่อนเสมอ**: ถ้า agent เข้าใจผิด เห็นได้จาก plan ก่อนที่มันจะแก้โค้ดไปหลายไฟล์
- **1 feature = 1 branch**: ถ้า feature ไหนพังก็ทิ้งเฉพาะ branch นั้น และ merge ง่าย review ง่าย
- **ให้ agent build เอง**: ใส่ "It builds" ใน DONE WHEN agent จะแก้ error เองจนผ่านก่อนส่งงาน
- **หยุดเมื่อวนลูป**: ถ้าแก้ไม่ผ่านเกิน 2–3 รอบ agent มักจะยิ่งแก้ยิ่งแย่ ให้ revert แล้วเขียน prompt ใหม่ที่ชัดขึ้น ดีกว่าแก้ต่อ
- **ให้อธิบายโค้ด**: ถ้าไม่เข้าใจ diff ให้ถามว่า "Explain what this change does" อย่า commit โค้ดที่ไม่เข้าใจ
- **Merge บ่อย**: ยิ่งแยก branch ไว้นาน conflict ยิ่งเยอะ merge เมื่อ feature เสร็จแต่ละชิ้น
- **แยกไฟล์กันทำ**: เมื่อหลาย agent ทำงานพร้อมกัน ให้แต่ละคนแก้คนละไฟล์ให้มากที่สุด จะได้ไม่ conflict

## `AGENTS.md`

`AGENTS.md` คือไฟล์ที่อธิบาย project และกติกาให้ agent วางไว้ที่ root ของ repo agent จะอ่านไฟล์นี้ก่อนเริ่มงานทุกครั้ง จึงไม่ต้องพิมพ์กติกาเดิมซ้ำในทุก prompt และทุก agent ในทีมจะทำตามกติกาเดียวกัน

Starter repo มีไฟล์นี้ให้แล้ว แก้เพิ่มได้เมื่อเจอสิ่งที่ agent ทำผิดซ้ำๆ

```markdown
# Team Card app

## Rules
- SwiftUI only. Deployment target: iOS 26.
- Wrap iOS 27-only APIs in #if compiler(>=6.4) and if #available(iOS 27, *).
- Do not add Swift packages without asking.
- Never read or print Secrets.plist.

## Build check
xcodebuild -scheme TeamCard -destination 'generic/platform=iOS Simulator' build

## Style
- One view per file. Keep views small.
- Use system colors so dark mode works.
```
