# EDL-Gen Compliance Video — Vercel Deploy

## โครงสร้างโปรเจกต์
```
project/
├── index.html          ← เดิมคือ index.php (แปลงเป็น static HTML แล้ว ไม่มีโค้ด PHP ฝังอยู่)
├── assets/
│   └── edlgen-logo.png ← ต้องนำไฟล์โลโก้เดิมมาวางไว้ตรงนี้ (path เดิม "assets/edlgen-logo.png" คงไว้เหมือนเดิม)
├── vercel.json
└── .gitignore
```

> หมายเหตุ: ตรวจสอบไฟล์ `index.php` ต้นฉบับแล้วพบว่าเป็น HTML/CSS/JavaScript ล้วนๆ
> (ไม่มีแท็ก `<?php ... ?>` หรือ logic ฝั่งเซิร์ฟเวอร์ใดๆ) ดังนั้นการแปลงจึงเป็นแค่การ
> เปลี่ยนนามสกุลไฟล์และย้ายมาไว้ที่ root — **ไม่จำเป็นต้องมี Serverless Function (`/api`)**
> เพราะโปรเจกต์นี้ทำงานฝั่ง client (JavaScript) ทั้งหมดอยู่แล้ว (filter, search, YouTube modal
> player ล้วนรันในเบราว์เซอร์)
>
> ถ้าในอนาคตมีฟีเจอร์ที่ต้องประมวลผลฝั่งเซิร์ฟเวอร์ (เช่น ปุ่ม "ເພີ່ມລິ້ງວິດີໂອ" ที่ตอนนี้
> เป็นแค่ `alert()`) ให้สร้างไฟล์ในโฟลเดอร์ `/api` ตามตัวอย่างด้านล่าง

## ขั้นตอนเตรียมไฟล์

1. คัดลอกไฟล์ `index.html` และโฟลเดอร์ `assets/` (พร้อมรูปโลโก้ `edlgen-logo.png`
   และรูปอื่นๆ ที่ใช้ใน `assets/...`) ไปไว้ในโฟลเดอร์โปรเจกต์เดียวกัน
2. ตรวจสอบให้แน่ใจว่า `index.html` อยู่ที่ **root** ของโปรเจกต์ (ไม่ใช่ในโฟลเดอร์ย่อย)

## คำสั่ง Git สำหรับ Push ขึ้น GitHub

```bash
cd project
git init
git add .
git commit -m "Initial commit: convert index.php to static site for Vercel"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

## Deploy บน Vercel

### วิธีที่ 1: ผ่านหน้าเว็บ (แนะนำ)
1. เข้า https://vercel.com/new
2. เลือก "Import Git Repository" แล้วเลือก repo ที่เพิ่ง push ไป
3. Framework Preset: เลือก **"Other"** (เพราะเป็น static HTML ล้วนๆ ไม่มี build step)
4. Build Command: เว้นว่างไว้ (ไม่ต้อง build)
5. Output Directory: เว้นว่างไว้ (Vercel จะ serve จาก root โดยอัตโนมัติ)
6. กด Deploy — เสร็จแล้ว Vercel จะ auto-deploy ทุกครั้งที่ push ขึ้น branch `main`

### วิธีที่ 2: ผ่าน Vercel CLI
```bash
npm install -g vercel
cd project
vercel        # deploy preview
vercel --prod # deploy production
```

## ถ้าต้องเพิ่ม Serverless Function ในอนาคต (`/api`)

ตัวอย่างโครงสร้าง:
```
project/
├── index.html
├── assets/
├── api/
│   └── videos.js       ← Vercel จะ auto-mount เป็น /api/videos
└── vercel.json
```

ตัวอย่างโค้ด `api/videos.js` (Node.js Serverless Function):
```js
export default function handler(req, res) {
  if (req.method === 'GET') {
    res.status(200).json({ videos: [] });
  } else {
    res.status(405).json({ error: 'Method not allowed' });
  }
}
```

จากนั้นฝั่ง frontend เรียกใช้ด้วย `fetch('/api/videos')` ได้ทันที โดยไม่ต้องตั้งค่า
CORS หรือ base URL เพิ่มเติม เพราะ frontend กับ API อยู่โดเมนเดียวกัน
# hong_lao
