# Code-along: Build your first app with coding agents

Coding agent คือ AI ที่อ่านโค้ดใน project, แก้ไฟล์, build และรันคำสั่งให้เราได้ ในช่วง hackathon ทีมที่ใช้ agent เป็นจะทำงานได้มากกว่าทีมที่พิมพ์โค้ดเองทั้งหมดหลายเท่า แต่ต้องรู้วิธี **วางแผน → มอบงาน → ตรวจผล → ส่งงาน** ให้ถูก

Workshop นี้ทุกคนจะได้ใช้ agent บนเครื่องของตัวเอง build feature แล้วรวมงานของทั้งทีมเป็น app เดียวบน GitHub ไม่ต้องเคยใช้ Git มาก่อน

## สิ่งที่จะได้เรียน

- **Run app บน iPad ของตัวเอง**: สร้าง SwiftUI project แล้วติดตั้งลง iPad ด้วย Apple ID ฟรี
- **ใช้ Git และ GitHub**: clone, commit, branch, push, pull, merge, แก้ conflict และเปิด pull request ทั้งจาก Xcode และ Terminal
- **ใช้ coding agent บนเครื่องตัวเอง**: **Xcode agents** บน Mac M chip หรือ **OpenCode** บน Intel Mac
- **ทำงานครบ loop**: plan → implement → build → review diff → test บน iPad → commit
- **ทำงานคู่ขนานกับเพื่อน**: แต่ละคนทำ feature บน branch ของตัวเอง แล้ว merge รวมกัน
- **ใช้ agent อย่างปลอดภัย**: ไม่ใส่ข้อมูลลับใน prompt, แบ่งงานให้เล็ก และรู้ว่าเมื่อไหร่ควรหยุดแล้วย้อนกลับ

## ทำไมต้องใช้ Git คู่กับ agent

Agent แก้โค้ดได้ทีละหลายไฟล์ในเวลาไม่กี่วินาที ถ้าผลออกมาไม่ดี เราต้องย้อนกลับได้ทันที Git คือสิ่งที่ทำให้ย้อนกลับได้ จึงมีกฎข้อเดียวของวันนี้: **commit ก่อนให้ agent เริ่มงานทุกครั้ง**

## Agent ของแต่ละเครื่อง

ทุกคนมี agent ใช้ แต่สองตัวทำงานต่างกันเล็กน้อย

| | Xcode agents (Mac M chip) | OpenCode (Intel Mac) |
|---|---|---|
| ใช้ที่ไหน | Panel ใน Xcode 27 | Terminal เปิดคู่กับ Xcode 26 |
| Model | Claude, Codex หรือ Gemini ผ่าน account ของทีม | Free model จาก OpenCode Zen เปลี่ยนได้ภายหลัง |
| แก้โค้ด, ใช้ Git | ✓ | ✓ |
| Build project | ✓ ผ่าน Xcode | ✓ ผ่านคำสั่ง `xcodebuild` |
| ดู SwiftUI Preview | ✓ ตรวจ UI ที่ตัวเองสร้างได้ | ✗ เราต้องดูเองใน Xcode |
| ค้น Apple documentation | ✓ | ✗ ใช้ความรู้ของ model และ `AGENTS.md` |
| ค่าใช้จ่าย | ใช้ usage ของ account ทีม | ฟรี |

## ในหมวดนี้

| หน้า | เนื้อหา |
|---|---|
| [Hardware](hardware.md) | อุปกรณ์ของทีม และเครื่องไหนทำอะไรได้ |
| [Git cheat sheet](git.md) | คำสั่ง Git ที่ต้องใช้ ทั้งใน Xcode และ Terminal พร้อมวิธีแก้ conflict |
| [Xcode agents](xcode-agents.md) | ใช้ agent ใน Xcode 27 บน Mac M chip |
| [OpenCode](opencode.md) | ใช้ agent ใน Terminal บน Intel Mac |
| [Prompting & best practices](prompting.md) | Prompt template, สิ่งที่ห้ามทำ และเทคนิคให้ agent ทำงานได้ดี |

## Guided build: Team Card

ทุกคนสร้าง feature เดียวกันพร้อมกัน คือหน้า **Team Card** ที่แสดงรายชื่อสมาชิกในทีม กดแล้วไปหน้ารายละเอียด ทำบน branch ของตัวเองด้วย agent ของตัวเอง ผู้สอนจะสลับระหว่าง Xcode 27 และ OpenCode บนจอใหญ่ให้ตามได้ทั้งสองแบบ

1. Commit แล้วสร้าง branch ใหม่
2. เขียน prompt ตาม [template](prompting.md#prompt-template) แล้วขอ plan
3. อ่าน plan แก้ส่วนที่ไม่ใช่ แล้วให้ agent ลงมือ
4. Build แล้ว run บน iPad
5. Review diff ทุกไฟล์ แล้ว commit และ push

## Team challenge

ฝึก workflow เดียวกับที่จะใช้จริงตลอด hackathon: 3 คน 3 agent ทำงานพร้อมกัน แล้วรวมเป็น app เดียว

1. แต่ละคนเลือก feature ที่ไม่ซ้ำกัน: search, ปรับ dark mode, share button, settings screen หรือ animation
2. สร้าง branch ของตัวเอง เช่น `feature/search` จะได้ไม่ชนกับงานของเพื่อน
3. ใช้ agent ของตัวเอง build feature แล้ว run บน iPad ของตัวเอง
4. 8 นาทีสุดท้าย: merge ทุก branch เข้า `main` แก้ conflict ด้วยกัน แล้ว run app ที่รวมแล้วบน iPad ทุกเครื่อง

## ก่อนพักกลางวัน

แต่ละทีมเขียน idea ของ project เป็นประโยคเดียว แล้วให้ agent ร่าง plan ให้ อ่าน plan ระหว่างพักกลางวัน แล้วใช้เลือกว่าจะเข้า workshop ไหนช่วงบ่าย

## Exit checklist

- [ ] Run app บน iPad ของตัวเองได้
- [ ] Commit และ push งานของตัวเองอย่างน้อย 1 ครั้ง
- [ ] Build feature ด้วย coding agent สำเร็จ
- [ ] ทีม merge ทั้ง 3 branch เข้า `main` และเปิด pull request อย่างน้อย 1 อัน
