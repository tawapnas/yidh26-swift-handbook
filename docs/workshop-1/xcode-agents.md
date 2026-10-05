# Xcode agents

!!! mchip "Mac M chip (Xcode 27)"
    Xcode agents ใช้ได้เฉพาะ Mac M chip Intel Mac ใช้ไม่ได้ ให้ใช้ [OpenCode](opencode.md) แทน

Xcode 27 มี coding agent อยู่ใน panel ของ Xcode เลย เลือกได้ทั้ง **Claude**, **Codex** และ **Gemini** ใช้ account ของทีมที่ sign in ไว้แล้วบนเครื่อง ไม่ต้องตั้งค่าเพิ่ม

ข้อดีของการอยู่ใน Xcode คือ agent เห็นสิ่งเดียวกับที่เราเห็น: build เองได้ ดู error เองได้ และดู Preview ของหน้าจอที่มันสร้างได้ จึงแก้ปัญหาเองได้หลายรอบโดยไม่ต้องให้เราคอยบอก

## Agent ทำอะไรได้บ้าง

- **อ่านและแก้ไฟล์ใน project** ทั้งไฟล์ที่เปิดอยู่และไฟล์อื่นที่เกี่ยวข้อง
- **Build และ run tests** แล้วอ่าน error เพื่อแก้ต่อเอง
- **ค้น Apple documentation** ได้ข้อมูล API ที่ถูกต้องและใหม่กว่าความรู้ของ model
- **ดู SwiftUI Preview** เพื่อตรวจว่า UI ที่สร้างหน้าตาถูกต้อง
- **ใช้ agent skills ของ Apple** ที่สอนให้เขียน Swift และ SwiftUI แบบสมัยใหม่

## Workflow: Plan → Build → Review → Commit

1. **Commit ก่อน** ให้ working tree สะอาด ถ้าผลไม่ดีจะได้ย้อนกลับได้ทั้งหมดในครั้งเดียว
2. **เปิด panel ของ agent** แล้วเลือก agent ที่ต้องการ
3. **วาง prompt** ตาม [template](prompting.md#prompt-template) และขอ **plan ก่อน** อย่าให้แก้โค้ดทันที
4. **อ่าน plan** ซึ่งเป็น Markdown ที่แก้ไขได้ ถ้ามีขั้นตอนที่ไม่ต้องการหรือเข้าใจผิด แก้ใน plan ได้เลย แล้วค่อย approve
5. **ให้ agent implement** มันจะ build และเช็ก Preview เอง ถ้า build ไม่ผ่านจะแก้ต่อจนผ่าน
6. **Review diff ทุกไฟล์** ดูว่าแก้ตรงกับที่ขอ ไม่แตะไฟล์ที่ไม่เกี่ยว แล้ว run บน iPad
7. **พอใจแล้ว commit และ push** ถ้าไม่พอใจ ใช้ Discard All Changes แล้วเขียน prompt ใหม่

## Permissions

ก่อนแก้ไฟล์หรือรันคำสั่ง agent จะขออนุญาตก่อน อ่านทุกครั้งว่ามันจะทำอะไรก่อนกด allow โดยเฉพาะคำสั่งที่ลบไฟล์ เปลี่ยน project settings หรือเพิ่ม package ถ้าไม่เข้าใจว่าคำสั่งทำอะไร ให้ถาม agent ก่อน

!!! warning "Usage limit"
    Account ของทีมมี usage limit ที่ต้องใช้ร่วมกันตลอด hackathon ใช้กับงานที่คุ้มค่า เช่น feature ใหม่หรือ bug ที่ติดนาน งานเล็กๆ อย่างแก้ข้อความบรรทัดเดียวทำเองเร็วกว่า

!!! tip "Agent เขียนโค้ด iOS 27 ได้ แต่ต้องระวัง"
    เพื่อนร่วมทีมที่ใช้ Intel Mac build ได้แค่ iOS 26 ถ้า agent ใช้ API ของ iOS 27 โดยไม่ระวัง เพื่อนจะ build ไม่ผ่านหลัง pull ใส่ constraint "iOS 26 deployment target, no iOS 27-only APIs" ใน prompt เสมอ ยกเว้น feature ที่ตั้งใจใช้ iOS 27 และครอบด้วย `#if compiler(>=6.4)` แล้ว ดู[วิธีครอบโค้ด](../getting-started.md#project-xcode-26-27)
