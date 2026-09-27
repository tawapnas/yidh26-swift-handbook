# OpenCode

**OpenCode** คือ open-source coding agent ที่ run ใน Terminal ใช้ได้ **ทุกเครื่อง** ทั้ง Intel Mac และ Mac M chip

Default model คือ **free model จาก OpenCode Zen** ไม่มีค่าใช้จ่าย ภายหลังทีมเปลี่ยนไปใช้ model หรือ provider ที่ชอบได้

!!! intel "Intel Mac"
    OpenCode คือ coding agent หลักของ Intel Mac เพราะใช้ Xcode agents ไม่ได้

## เริ่มใช้งาน

เครื่องของงานติดตั้งและเชื่อม OpenCode Zen ไว้ให้แล้ว เปิด Terminal แล้ว:

```bash
cd ~/Projects/team-XX    # เข้า folder ของ project
opencode                 # เริ่ม OpenCode
```

ครั้งแรกใน project ใหม่ ให้พิมพ์ `/init` เพื่อสร้าง `AGENTS.md` (ถ้า repo ยังไม่มี)

??? note "ติดตั้งเองบนเครื่องอื่น"
    ```bash
    curl -fsSL https://opencode.ai/install | bash
    opencode
    /connect     # เลือก OpenCode Zen แล้ววาง API key
    /models      # เลือก free model
    ```

## Plan mode และ Build mode

กด ++tab++ เพื่อสลับ mode

| Mode | ใช้ตอนไหน |
|---|---|
| **Plan** | คิดและวางแผน ยังไม่แก้ไฟล์ |
| **Build** | ลงมือแก้ไฟล์และรันคำสั่ง |

เริ่มที่ Plan เสมอ อ่าน plan ให้เข้าใจ แล้วค่อยสลับไป Build

## คำสั่งที่ใช้บ่อย

| คำสั่ง | ใช้ทำอะไร |
|---|---|
| `/models` | ดูหรือเปลี่ยน model |
| `/connect` | เชื่อม provider อื่น |
| `/init` | สร้าง `AGENTS.md` |
| `/undo` | ย้อนการแก้ครั้งล่าสุด |
| `/help` | ดูคำสั่งทั้งหมด |

## ให้ OpenCode build project

OpenCode มองไม่เห็น Xcode แต่ build ผ่าน Terminal ได้ บอกใน prompt ว่าให้ใช้คำสั่งนี้เช็กว่า compile ผ่าน:

```bash
xcodebuild -scheme TeamCard \
  -destination 'generic/platform=iOS Simulator' build
```

## ทำงานคู่กับ Xcode

OpenCode **มองไม่เห็น SwiftUI Preview** ให้เปิด Xcode ไว้ข้างกัน

1. OpenCode แก้ไฟล์ใน Terminal
2. Xcode อัปเดตไฟล์ให้อัตโนมัติ
3. ดู Preview หรือกด ++cmd+r++ เพื่อ run บน iPad
4. Review diff ใน Source Control navigator แล้ว commit

!!! warning "Privacy ของ free model"
    Free model บางตัวอาจนำ prompt ไปปรับปรุง model ห้ามส่ง API key, ข้อมูลส่วนตัว หรือข้อมูลลับผ่าน OpenCode
