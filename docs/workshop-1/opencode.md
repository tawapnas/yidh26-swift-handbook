# OpenCode

**OpenCode** คือ open-source coding agent ที่ run ใน Terminal ใช้ได้ **ทุกเครื่อง** ทั้ง {{ mac.intel }} และ {{ mac.apple }} ทำงานเหมือน Xcode agents: อ่านและแก้ไฟล์, รันคำสั่ง และใช้ Git ได้ ต่างกันที่ไม่ได้อยู่ใน Xcode จึงมองไม่เห็น Preview และค้น Apple documentation ไม่ได้

Default model คือ **free model จาก OpenCode Zen** ซึ่งเป็นบริการรวม model ของ OpenCode ไม่มีค่าใช้จ่าย ภายหลังทีมเปลี่ยนไปใช้ model หรือ provider ที่ชอบได้

!!! intel "{{ mac.intel }} · Xcode 26"
    OpenCode คือ coding agent หลักของ {{ mac.intel }} เพราะใช้ Xcode agents ไม่ได้

## เริ่มใช้งาน

เครื่องของงานติดตั้ง OpenCode และเชื่อม OpenCode Zen ไว้ให้แล้ว เปิด Terminal แล้วเข้าไปที่ folder ของ project ก่อนเริ่ม OpenCode เพราะ OpenCode จะทำงานกับไฟล์ใน folder ที่เปิดเท่านั้น

```bash
cd ~/Projects/team-XX    # เข้า folder ของ project
opencode                 # เริ่ม OpenCode
```

ครั้งแรกใน project ใหม่ ให้พิมพ์ `/init` OpenCode จะอ่าน project แล้วสร้าง [`AGENTS.md`](prompting.md#agentsmd) ซึ่งเป็นไฟล์อธิบาย project และกติกาให้ agent อ่านทุกครั้ง ถ้า starter repo มีไฟล์นี้อยู่แล้วไม่ต้องทำ

??? note "ติดตั้งเองบนเครื่องอื่น"
    ต้องมี OpenCode account และ API key (ฟรี ไม่ต้องผูกบัตร)

    ```bash
    curl -fsSL https://opencode.ai/install | bash
    opencode
    /connect     # เลือก OpenCode Zen แล้ววาง API key
    /models      # เลือก free model
    ```

## Plan mode และ Build mode

OpenCode มี 2 mode กด ++tab++ เพื่อสลับ

| Mode | ใช้ตอนไหน |
|---|---|
| **Plan** | ให้ agent อ่านโค้ดและวางแผน ยังแก้ไฟล์ไม่ได้ ปลอดภัยที่จะลองถามอะไรก็ได้ |
| **Build** | ให้ agent ลงมือแก้ไฟล์และรันคำสั่งตาม plan |

เริ่มที่ Plan เสมอ อ่าน plan ให้เข้าใจ ถ้ามีส่วนที่ไม่ใช่ให้บอกแก้ใน Plan mode ก่อน แล้วค่อยสลับไป Build ระหว่าง Build mode OpenCode จะขออนุญาตก่อนแก้ไฟล์หรือรันคำสั่ง อ่านก่อนกด allow ทุกครั้ง

## คำสั่งที่ใช้บ่อย

| คำสั่ง | ใช้ทำอะไร |
|---|---|
| `/models` | ดู model ที่ใช้อยู่ หรือเปลี่ยน model |
| `/connect` | เชื่อม provider อื่น เช่น account ของทีมเอง |
| `/init` | สร้าง `AGENTS.md` จาก project |
| `/undo` | ย้อนการแก้ไฟล์ครั้งล่าสุดของ agent |
| `/help` | ดูคำสั่งทั้งหมด |

## ให้ OpenCode build project

OpenCode มองไม่เห็น Xcode จึงไม่รู้ว่าโค้ดที่แก้ compile ผ่านหรือเปล่า เว้นแต่จะ build ผ่าน Terminal บอกใน prompt ให้ใช้คำสั่งนี้เช็ก ถ้ามี error agent จะอ่านแล้วแก้ต่อเอง

```bash
xcodebuild -scheme TeamCard \
  -destination 'generic/platform=iOS Simulator' build
```

เปลี่ยน `TeamCard` เป็นชื่อ scheme ของ project ตัวเอง ใส่คำสั่งนี้ไว้ใน `AGENTS.md` ด้วย agent จะได้ใช้ทุกครั้งโดยไม่ต้องบอก

## ทำงานคู่กับ Xcode

OpenCode **มองไม่เห็น SwiftUI Preview** จึงตรวจไม่ได้ว่าหน้าจอหน้าตาถูกไหม ให้เปิด Xcode ไว้ข้างกันแล้วตรวจเอง

1. OpenCode แก้ไฟล์ใน Terminal
2. Xcode อัปเดตไฟล์ที่เปิดอยู่ให้อัตโนมัติ
3. ดู Preview หรือกด ++cmd+r++ เพื่อ run บน iPad
4. Review diff ใน Source Control navigator แล้ว commit

!!! warning "Privacy ของ free model"
    Free model บางตัวอาจนำ prompt ไปใช้ปรับปรุง model ห้ามส่ง API key, ข้อมูลส่วนตัว หรือข้อมูลลับผ่าน OpenCode ถ้าทีมเปลี่ยนไปใช้ provider อื่น ให้เช็ก data policy ของ provider นั้นด้วย
