# Swift Workshop Handbook

ยินดีต้อนรับสู่ Hackathon! Handbook นี้รวมขั้นตอนและ code snippets ของทั้ง 5 workshop ไว้ในที่เดียว เปิดไว้ข้าง Xcode แล้วทำตามได้เลย

## ตารางเวลา

| เวลา | Room A (Mac M chip) | Room B (Intel Mac + iPad) |
|---|---|---|
| 10:00–12:00 | **1. Code-along: Build your first app with coding agents** | ← Main hall · ทุกคนเข้าร่วม |
| 12:00–13:00 | พักกลางวัน | พักกลางวัน |
| 13:00–14:30 | **2. Bring intelligence to your app with Foundation Models and on-prem AI** | **5. Give your app sight with Vision and VisionKit** |
| 14:30–14:40 | พัก 10 นาที / ย้ายห้อง | พัก 10 นาที / ย้ายห้อง |
| 14:40–16:10 | **3. Build sensor-aware intelligence with Core AI and Create ML** | **4. Extend your app across the system with App Intents** |

!!! tip "เลือก workshop ช่วงบ่าย"
    เลือกตาม idea ของทีม ถ้าทีมแยกกันเข้า ให้คนที่เข้า **Room A** ถือ Mac M chip ไปด้วย

## Workshops

<div class="grid cards" markdown>

-   :material-robot-outline:{ .lg .middle } **1. Code-along: Build your first app with coding agents**

    ---

    Git, Xcode agents, OpenCode และการเขียน prompt · บังคับทุกคน

    [:octicons-arrow-right-24: เริ่ม Workshop 1](workshop-1/index.md)

-   :material-brain:{ .lg .middle } **2. Bring intelligence to your app with Foundation Models and on-prem AI**

    ---

    AFM 3, Private Cloud Compute และ AI cluster ของงาน

    [:octicons-arrow-right-24: เริ่ม Workshop 2](workshop-2/index.md)

-   :material-motion-sensor:{ .lg .middle } **3. Build sensor-aware intelligence with Core AI and Create ML**

    ---

    Train model จาก motion, LiDAR และเสียง แล้ว run บน device

    *เร็วๆ นี้*

-   :material-apps:{ .lg .middle } **4. Extend your app across the system with App Intents**

    ---

    Action เดียว ใช้ได้ทั้ง Shortcuts, Spotlight, Siri, widget และ Control Center

    *เร็วๆ นี้*

-   :material-eye-outline:{ .lg .middle } **5. Give your app sight with Vision and VisionKit**

    ---

    Scan เอกสาร อ่านข้อความ และ track มือ/ร่างกายจากกล้อง

    *เร็วๆ นี้*

</div>

## อุปกรณ์ของแต่ละทีม

MacBook Pro M chip ×1 · MacBook Pro Intel ×2 · iPad M chip ×3 → [ดูรายละเอียด](workshop-1/hardware.md)

## กฎ 3 ข้อ

1. **Commit ก่อนให้ agent แก้โค้ดทุกครั้ง** — ถ้าพังจะย้อนกลับได้ทันที
2. **ห้ามใส่ API key หรือข้อมูลส่วนตัว** ใน prompt และใน commit
3. **ข้อมูลสุขภาพใช้ synthetic data เท่านั้น** ห้ามใช้ข้อมูลผู้ป่วยจริง (PDPA)
