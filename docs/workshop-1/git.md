# Git cheat sheet

Git เก็บประวัติทุกการเปลี่ยนแปลงของ project ถ้า agent แก้โค้ดพัง เราย้อนกลับได้เสมอ **ถ้า commit ไว้ก่อน**

## Cheat sheet

| Concept | ความหมาย | Xcode | Terminal |
|---|---|---|---|
| Clone | ดาวน์โหลด repo มาที่เครื่อง | Integrate › Clone | `git clone <url>` |
| Status / Diff | ดูว่าแก้อะไรไปบ้าง | Source Control navigator | `git status` · `git diff` |
| Commit | บันทึก snapshot พร้อมข้อความ | Integrate › Commit | `git add .` · `git commit -m "msg"` |
| Branch | แยกสายงาน | Source Control navigator › New Branch | `git switch -c feature/x` |
| Push | ส่ง commit ขึ้น GitHub | Integrate › Push | `git push` |
| Pull | ดึง commit ของเพื่อนลงมา | Integrate › Pull | `git pull` |
| Merge | รวม branch เข้าด้วยกัน | Source Control navigator › Merge | `git merge feature/x` |
| Undo | ทิ้งสิ่งที่ยังไม่ commit | Integrate › Discard All Changes | `git restore .` |
| Revert | ย้อน commit แบบปลอดภัย | — | `git revert <commit>` |
| Pull request | ขอ merge พร้อมให้เพื่อน review | github.com | `gh pr create` (optional) |

## Workflow ของทีม

```bash
git switch main && git pull          # 1. เริ่มจาก main ล่าสุด
git switch -c feature/search         # 2. สร้าง branch ของตัวเอง
# ... ให้ agent ทำงาน แล้ว review ...
git add . && git commit -m "Add search bar"   # 3. commit
git push -u origin feature/search    # 4. push แล้วเปิด pull request บน GitHub
```

## แก้ conflict

Conflict เกิดเมื่อสอง branch แก้บรรทัดเดียวกัน

1. `git pull` หรือ merge แล้ว Xcode จะแสดงไฟล์ที่ conflict
2. เลือกว่าจะเก็บฝั่งไหน หรือรวมทั้งสองฝั่ง
3. Build ให้ผ่านก่อน แล้ว commit

```text
<<<<<<< HEAD
Text("Team Card")        ← ของเรา
=======
Text("Our Team")         ← ของเพื่อน
>>>>>>> feature/title
```

!!! tip "ให้ agent ช่วยแก้ conflict ได้"
    เช่น prompt ว่า "Resolve the merge conflicts in TeamListView.swift, keep both features, then build."

## `.gitignore` สำหรับ Xcode

```gitignore
xcuserdata/
DerivedData/
*.xcuserstate
.DS_Store
Secrets.plist
```

## ปัญหาที่เจอบ่อย

| อาการ | วิธีแก้ |
|---|---|
| Push ไม่ได้ (rejected) | `git pull` ก่อน แล้วค่อย push |
| Commit ผิด branch | ถาม helper อย่าเพิ่ง push |
| Detached HEAD | `git switch main` (ถ้ามีงานค้าง ให้ถาม helper ก่อน) |
| Conflict ใน `.pbxproj` | ให้คนเดียวเพิ่ม/ลบไฟล์ในแต่ละช่วง แล้ว pull ก่อนเพิ่มไฟล์ |
