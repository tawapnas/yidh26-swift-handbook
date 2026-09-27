# Prompting, guardrails & best practices

## Prompt template

ใช้ได้กับทั้ง Xcode agents และ OpenCode (มีใน repo เป็น GitHub issue template ด้วย)

```text
GOAL:        Add a search bar that filters the team list by name.
CONTEXT:     TeamListView.swift, TeamMember.swift
CONSTRAINTS: SwiftUI only, iOS 26 deployment target, no iOS 27-only APIs,
             no new packages.
DONE WHEN:   It builds, filtering works in the Preview,
             and existing features still work.
PROCESS:     Show me a plan first. Wait for my approval before editing.
```

| ส่วน | ใส่อะไร |
|---|---|
| **GOAL** | ผลลัพธ์ที่ต้องการ 1 อย่าง ชัดเจน |
| **CONTEXT** | ไฟล์หรือหน้าจอที่เกี่ยวข้อง |
| **CONSTRAINTS** | ข้อห้ามและข้อจำกัด |
| **DONE WHEN** | วิธีตรวจว่างานเสร็จจริง |
| **PROCESS** | ขอ plan ก่อนลงมือเสมอ |

## Prompt ดี vs ไม่ดี

| ✗ ไม่ดี | ✓ ดี |
|---|---|
| "ทำ app ให้สวยขึ้น" | "Add dark mode support to TeamCardView using system colors." |
| "แก้ bug" | "Tapping Save crashes with index out of range in TeamStore.swift line 42. Find the cause and fix it." |
| "เพิ่ม feature ทั้งหมดตาม idea" | แบ่งเป็น task เล็กๆ ทีละ feature |

## Guardrails

!!! danger "ห้ามทำ"
    - ใส่ API key, password หรือข้อมูลส่วนตัวใน prompt
    - Commit `Secrets.plist` หรือ key ใดๆ
    - ใช้ข้อมูลผู้ป่วยจริง ใช้ synthetic data เท่านั้น
    - กด allow คำสั่งที่ไม่เข้าใจ

!!! success "ต้องทำทุกครั้ง"
    - Commit ก่อนให้ agent เริ่มงาน
    - อ่าน plan ก่อน approve
    - Review diff ทุกไฟล์ก่อน commit
    - Run บน iPad จริงก่อน push

## Best practices

- **Task เล็ก** — 1 prompt = 1 feature หรือ 1 bug
- **Plan ก่อนเสมอ** — แก้ plan ถูกกว่าแก้โค้ด
- **1 feature = 1 branch** — merge ง่าย review ง่าย
- **ให้ agent build เอง** — ใส่ "It builds" ใน DONE WHEN
- **หยุดเมื่อวนลูป** — ถ้าแก้ไม่ผ่านเกิน 2–3 รอบ ให้ revert แล้วเขียน prompt ใหม่ให้ชัดขึ้น
- **ให้อธิบายโค้ด** — ถามว่า "Explain what this change does" ถ้าไม่เข้าใจ diff
- **Merge บ่อย** — ยิ่งรอนาน conflict ยิ่งเยอะ
- **แยกไฟล์กันทำ** — เมื่อหลาย agent ทำงานพร้อมกัน ให้แต่ละคนแก้คนละไฟล์ให้มากที่สุด

## `AGENTS.md`

ไฟล์ที่บอก agent ถึงกติกาของ project วางไว้ที่ root ของ repo ให้ทุก agent ทำตามกติกาเดียวกัน

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
