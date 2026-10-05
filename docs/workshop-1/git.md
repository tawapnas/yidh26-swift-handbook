# Git cheat sheet

**Git** คือระบบเก็บประวัติการแก้ไขของ project ทุกครั้งที่ commit Git จะจำสภาพของไฟล์ทั้งหมดไว้ ถ้า agent แก้โค้ดพัง เราย้อนกลับไปจุดที่ commit ไว้ได้เสมอ **ถ้า commit ไว้ก่อน**

**GitHub** คือเว็บที่เก็บ repo ของทีมไว้ online ทุกคนในทีม push งานขึ้นไปและ pull งานของเพื่อนลงมาจากที่เดียวกัน

ทุกคำสั่งทำได้ทั้งจาก Xcode (เมนู **Integrate** และ Source Control navigator) และจาก Terminal เลือกแบบที่ถนัด

## Cheat sheet

| Concept | ความหมาย | Xcode | Terminal |
|---|---|---|---|
| Repository | Folder ของ project พร้อมประวัติทั้งหมด | — | — |
| Clone | ดาวน์โหลด repo จาก GitHub มาที่เครื่องครั้งแรก | Integrate › Clone | `git clone <url>` |
| Status / Diff | ดูว่าแก้อะไรไปบ้างตั้งแต่ commit ล่าสุด | Source Control navigator | `git status` · `git diff` |
| Commit | บันทึก snapshot ของไฟล์ทั้งหมด พร้อมข้อความอธิบาย | Integrate › Commit (++cmd+opt+c++) | `git add .` · `git commit -m "msg"` |
| Branch | แยกสายงานออกมา แก้ได้โดยไม่กระทบ `main` | Source Control navigator › New Branch | `git switch -c feature/x` |
| Push | ส่ง commit ของเราขึ้น GitHub | Integrate › Push | `git push` |
| Pull | ดึง commit ของเพื่อนจาก GitHub ลงมา | Integrate › Pull | `git pull` |
| Merge | รวมงานจาก branch หนึ่งเข้าอีก branch | Source Control navigator › Merge | `git merge feature/x` |
| Undo | ทิ้งการแก้ไขที่ยังไม่ได้ commit ทั้งหมด | Integrate › Discard All Changes | `git restore .` |
| Revert | สร้าง commit ใหม่ที่ย้อนผลของ commit เก่า ประวัติไม่หาย | — | `git revert <commit>` |
| Pull request | ขอ merge บน GitHub ให้เพื่อน review ก่อน | github.com | `gh pr create` (optional) |

## Workflow ของทีม

ทำแบบนี้ทุกครั้งที่เริ่ม feature ใหม่

```bash
git switch main && git pull          # 1. เริ่มจาก main ล่าสุด มีงานของเพื่อนครบ
git switch -c feature/search         # 2. สร้าง branch ของตัวเอง
# ... ให้ agent ทำงาน แล้ว review ...
git add . && git commit -m "Add search bar"   # 3. commit เมื่อ build ผ่านและ test แล้ว
git push -u origin feature/search    # 4. push แล้วเปิด pull request บน GitHub
```

1. **เริ่มจาก `main` ล่าสุด** ถ้าลืม pull จะทำงานบนโค้ดเก่า แล้ว conflict ตอน merge
2. **สร้าง branch** ตั้งชื่อตาม feature เช่น `feature/search` งานของเราจะแยกจากของเพื่อนจนกว่าจะ merge
3. **Commit** เขียนข้อความบอกว่าทำอะไร เช่น "Add search bar" ไม่ใช่ "update"
4. **Push และเปิด pull request** `-u origin` ใช้แค่ครั้งแรกของ branch ใหม่ หลังจากนั้นใช้ `git push` เฉยๆ ได้

## แก้ conflict

Conflict เกิดเมื่อสอง branch แก้ **บรรทัดเดียวกัน** ในไฟล์เดียวกัน Git ตัดสินใจเองไม่ได้ว่าจะเอาของใคร จึงให้เราเลือก ไม่ใช่ error ร้ายแรง แค่ต้องตัดสินใจ

1. `git pull` หรือ merge แล้ว Xcode จะแสดงไฟล์ที่ conflict
2. เปิดไฟล์ จะเห็นทั้งสองฝั่งคั่นด้วยเครื่องหมายแบบนี้

    ```text
    <<<<<<< HEAD
    Text("Team Card")        ← ของเรา
    =======
    Text("Our Team")         ← ของเพื่อน
    >>>>>>> feature/title
    ```

3. เลือกว่าจะเก็บฝั่งไหน หรือรวมทั้งสองฝั่ง แล้วลบเครื่องหมาย `<<<<<<<`, `=======`, `>>>>>>>` ออกให้หมด
4. Build ให้ผ่านก่อน แล้ว commit

!!! tip "ให้ agent ช่วยแก้ conflict ได้"
    เช่น prompt ว่า "Resolve the merge conflicts in TeamListView.swift, keep both features, then build." แล้ว review ผลก่อน commit

## `.gitignore` สำหรับ Xcode

ไฟล์ `.gitignore` บอก Git ว่าไฟล์ไหน **ไม่ต้อง** เก็บ เช่น ไฟล์ตั้งค่าส่วนตัวของ Xcode ที่ทำให้ conflict โดยไม่จำเป็น และไฟล์ที่มี API key

```gitignore
xcuserdata/
DerivedData/
*.xcuserstate
.DS_Store
Secrets.plist
```

## ปัญหาที่เจอบ่อย

| อาการ | สาเหตุ | วิธีแก้ |
|---|---|---|
| Push ไม่ได้ (rejected) | เพื่อน push ขึ้นไปก่อน GitHub มี commit ที่เรายังไม่มี | `git pull` ก่อน แล้วค่อย push |
| Commit ผิด branch | ลืมสร้างหรือสลับ branch ก่อนเริ่มงาน | ถาม TA อย่าเพิ่ง push |
| Detached HEAD | Checkout ไปที่ commit เก่าแทน branch | `git switch main` (ถ้ามีงานค้าง ให้ถาม TA ก่อน) |
| Conflict ใน `.pbxproj` | หลายคนเพิ่มหรือลบไฟล์ใน project พร้อมกัน | ให้คนเดียวเพิ่ม/ลบไฟล์ในแต่ละช่วง และ pull ก่อนเพิ่มไฟล์ |
