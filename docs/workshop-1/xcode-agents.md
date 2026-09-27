# Xcode agents

!!! mchip "เฉพาะ Mac M chip (Xcode 27)"
    Intel Mac ใช้ Xcode agents ไม่ได้ ให้ใช้ [OpenCode](opencode.md) แทน

Xcode 27 มี coding agent ในตัว เลือกได้ทั้ง **Claude**, **Codex** และ **Gemini** ใช้ account ของทีมที่ sign in ไว้แล้วบนเครื่อง

## Agent ทำอะไรได้บ้าง

- อ่านและแก้ไฟล์ใน project
- Build และ run tests
- ค้น Apple documentation
- ดู SwiftUI Preview เพื่อตรวจ UI ที่ตัวเองสร้าง
- ใช้ agent skills ของ Apple สำหรับ Swift และ SwiftUI สมัยใหม่

## Workflow: Plan → Build → Review → Commit

1. **Commit ก่อน** ให้ working tree สะอาด
2. เปิด panel ของ agent แล้วเลือก agent ที่ต้องการ
3. วาง prompt ตาม [template](prompting.md#prompt-template) และขอ **plan ก่อน**
4. อ่าน plan (เป็น Markdown แก้ไขได้) แก้ส่วนที่ไม่ใช่ แล้ว approve
5. ให้ agent implement แล้ว build และเช็ก Preview เอง
6. **Review diff ทุกไฟล์** แล้ว run บน iPad
7. พอใจแล้ว commit และ push

## Permissions

Agent จะขออนุญาตก่อนแก้ไฟล์หรือรันคำสั่ง อ่านทุกครั้งก่อนกด allow โดยเฉพาะคำสั่งที่ลบไฟล์หรือเปลี่ยน project settings

!!! warning "Usage limit"
    Account ของทีมมี usage limit ใช้กับงานที่คุ้มค่า เช่น feature ใหม่หรือ bug ที่ติดนาน ไม่ต้องใช้กับการแก้ตัวอักษรบรรทัดเดียว

!!! tip "Agent เขียนโค้ด iOS 27 ได้ แต่ต้องระวัง"
    เพื่อนร่วมทีมที่ใช้ Intel Mac build ได้แค่ iOS 26 ใส่ constraint "iOS 26 deployment target, no iOS 27-only APIs" ใน prompt เสมอ ยกเว้น feature ที่ตั้งใจใช้ iOS 27 และครอบด้วย `#if compiler(>=6.4)` แล้ว
