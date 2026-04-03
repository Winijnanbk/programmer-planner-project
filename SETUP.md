# คู่มือ Setup — Bhatara Work Planner

## ภาพรวม
```
index.html  (GitHub Pages)
    ↓ Microsoft login
Azure AD    (เฉพาะ @bhatarapro.com)
    ↓ Graph API
Excel บน OneDrive  (ฐานข้อมูล)
```

---

## ขั้นตอนที่ 1 — สร้าง Azure App Registration (5 นาที)

1. เปิด https://portal.azure.com → ล็อกอินด้วย admin account
2. ค้นหา **"App registrations"** → คลิก **"New registration"**
3. กรอก:
   - Name: `Bhatara Planner`
   - Supported account types: **"Accounts in this organizational directory only"**
   - Redirect URI: `Single-page application (SPA)` → ใส่ URL GitHub Pages เช่น `https://yourusername.github.io/planner`
4. คลิก **Register**
5. **คัดลอก "Application (client) ID"** → เอาไปใส่ใน `CONFIG.CLIENT_ID` ใน index.html

### เพิ่ม API Permission
1. ในหน้า App registration → **API permissions** → **Add a permission**
2. เลือก **Microsoft Graph** → **Delegated permissions**
3. เพิ่ม: `User.Read` และ `Files.ReadWrite`
4. คลิก **Grant admin consent**

---

## ขั้นตอนที่ 2 — เตรียมไฟล์ Excel บน OneDrive (3 นาที)

1. เปิด OneDrive หรือ SharePoint ของทีม
2. อัปโหลดหรือสร้างไฟล์ Excel ชื่อ `planner.xlsx`
3. สร้าง Sheet ชื่อ **Tasks** และใส่ header row:

| A | B | C | D | E | F | G |
|---|---|---|---|---|---|---|
| id | date | personId | name | status | md | note |

4. หา **File ID** ของไฟล์:
   - เปิดไฟล์ใน Excel Online
   - URL จะมีลักษณะ: `...items/`**ABC123XYZ**`/...`
   - ส่วนที่ขีดเส้นใต้คือ File ID
5. **คัดลอก File ID** → ใส่ใน `CONFIG.EXCEL_FILE_ID` ใน index.html

---

## ขั้นตอนที่ 3 — Deploy บน GitHub Pages (5 นาที)

1. สร้าง GitHub repo ชื่อ `planner` (Public หรือ Private ก็ได้)
2. อัปโหลด `index.html` เข้าไป
3. ไปที่ **Settings** → **Pages** → Source: **main branch / root**
4. URL จะเป็น: `https://yourusername.github.io/planner`
5. นำ URL นี้ไปใส่ใน Redirect URI ของ Azure (ขั้นตอนที่ 1)

---

## ขั้นตอนที่ 4 — แก้ไข config ใน index.html

เปิด `index.html` แล้วแก้:
```javascript
const CONFIG = {
  CLIENT_ID: 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx',  // จาก Azure
  TENANT_ID: 'bhatarapro.com',                         // คงไว้
  EXCEL_FILE_ID: 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx',    // จาก OneDrive URL
  SHEET_NAME: 'Tasks',                                  // คงไว้
  REDIRECT_URI: 'https://yourusername.github.io/planner',
};
```

---

## รายชื่อทีม (แก้ได้ใน index.html)

```javascript
const TEAM = [
  { id: 'pu',   name: 'Pu 😎' },
  { id: 'le',   name: 'Le 😑' },
  // เพิ่ม/ลบตามทีมจริง
  // id ควรตรงกับ username ของ Microsoft account (ก่อน @)
];
```

---

## Demo Mode
ถ้ายังไม่ได้ setup → แอปจะรันใน **Demo Mode** อัตโนมัติ
ข้อมูลเก็บใน browser memory (หายเมื่อ refresh) ใช้ทดลอง UI ได้เลย

---

## คำถามที่พบบ่อย

**Q: ถ้า login แล้วขึ้น "AADSTS50020" แก้ยังไง?**  
A: ไปที่ Azure App Registration → Authentication → เปิด "Allow public client flows"

**Q: หลายคนใช้พร้อมกัน ข้อมูลชนกันไหม?**  
A: มีโอกาสถ้าแก้ row เดียวกันพร้อมกัน แต่ถ้าแต่ละคนแก้ column ของตัวเอง (คนละคอลัมน์) จะไม่ชน

**Q: เพิ่มคนในทีมทำยังไง?**  
A: แก้ array `TEAM` ใน index.html แล้ว push ขึ้น GitHub ใหม่
