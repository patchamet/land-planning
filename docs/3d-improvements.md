# 3D House Zone Improvements

ไฟล์เป้าหมาย: `src/04-house-zone-3d.html`

---

## 1. สีท้องฟ้าเปลี่ยนตามเวลา

**สถานะ:** ✅ Done

**ปัญหา:** `scene.background` เป็นสีฟ้า `#d4e6f1` ตลอดทั้งวัน ไม่สมจริง

**แนวทาง:**
- อัปเดต `scene.background` ใน `updateSunPosition()`
- ใช้ `THREE.Color.lerpColors()` ผสมสีตามค่า `t` (0=เช้า, 0.5=เที่ยง, 1=เย็น)
- สีเป้าหมาย:
  - 06:00 (t=0): ส้มอุ่น `#f0c8a0` → พระอาทิตย์ขึ้น
  - 09:00 (t=0.25): ฟ้าอ่อน `#c8e0f0`
  - 12:00 (t=0.5): ฟ้าสด `#a0d4f0`
  - 15:00 (t=0.75): ฟ้าอ่อน `#c8e0f0`
  - 18:00 (t=1): ส้มแดง `#e0a080` → พระอาทิตย์ตก
- คำนวณ: ช่วง t=0-0.5 lerp จาก warm → blue, ช่วง t=0.5-1 lerp จาก blue → warm

**แก้ที่:** ฟังก์ชัน `updateSunPosition()` — เพิ่มท้ายฟังก์ชันก่อน `}` ปิด

---

## 2. Ground plane รับเงา

**สถานะ:** ✅ Done

**ปัญหา:** มีแค่ zone ground (trapezoid 11-12-14-13) รับเงา พื้นที่รอบนอก (ถนน, ข้างไผ่) ไม่มี plane → เงาต้นไม้/ไผ่หายไป

**แนวทาง:**
- เพิ่ม plane ใหญ่ (60x40m) วางที่ y=-0.02 (ใต้ทุกอย่าง) รับเงา
- สี neutral เทาอ่อน `#c8c8c0` เพื่อไม่บังสีโซนเขียว
- ตั้ง `receiveShadow = true`
- วางตรงกลาง scene ~(16, 0, 5)

**แก้ที่:** เพิ่มหลัง zone boundary outline section (~line 235)

```js
const bigGroundGeo = new THREE.PlaneGeometry(60, 40);
const bigGroundMat = new THREE.MeshStandardMaterial({ color: 0xc8c8c0 });
const bigGround = new THREE.Mesh(bigGroundGeo, bigGroundMat);
bigGround.rotation.x = -Math.PI / 2;
bigGround.position.set(16, -0.02, 5);
bigGround.receiveShadow = true;
scene.add(bigGround);
```

---

## 3. ลำน้ำ animation (subtle flow)

**สถานะ:** ยังไม่ทำ

**ปัญหา:** ลำน้ำเป็น static shape สีฟ้าเรียบ ไม่มีความเคลื่อนไหว

**แนวทาง:**
- เปลี่ยนจาก `ShapeGeometry` เป็น custom geometry ที่มี UV
- ใช้ canvas texture สร้างลาย ripple/wave เบาๆ
- ในลูป `animate()` เลื่อน `texture.offset` ตามทิศทางน้ำไหล (จาก node 8 → node 14)
- ความเร็วช้าๆ ~0.02 units/sec
- Alternative ง่ายกว่า: ใช้ `MeshStandardMaterial` + normal map จาก canvas + animate offset

**แก้ที่:**
1. สร้าง canvas texture ก่อน waterway section
2. แทนที่ `waterMat` ให้ใช้ texture
3. เพิ่ม update ใน `animate()` loop

```js
// สร้าง water texture
const waterCanvas = document.createElement('canvas');
waterCanvas.width = 128;
waterCanvas.height = 128;
const wCtx = waterCanvas.getContext('2d');
// วาด ripple pattern
for (let i = 0; i < 128; i += 8) {
  wCtx.fillStyle = `rgba(74,159,216,${0.3 + Math.sin(i * 0.15) * 0.15})`;
  wCtx.fillRect(0, i, 128, 4);
}
const waterTex = new THREE.CanvasTexture(waterCanvas);
waterTex.wrapS = waterTex.wrapT = THREE.RepeatWrapping;
waterTex.repeat.set(3, 6);

// ใน animate():
waterTex.offset.y += 0.001; // flow direction
```

---

## 4. แสงในบ้านตอนค่ำ

**สถานะ:** ยังไม่ทำ

**ปัญหา:** ตอนเลื่อน slider ไปช่วงเย็น (17:00+) บ้านมืดสนิท ไม่สมจริง

**แนวทาง:**
- เพิ่ม `PointLight` สีเหลืองอุ่น `#ffe4b0` ไว้กลางบ้าน (x=14, y=1.5, z=9)
- intensity=0 ตอนกลางวัน
- ค่อยๆ เปิดตั้งแต่ 17:00 (t=0.917) → เต็มที่ 18:00
- เพิ่ม glow effect: box เล็กๆ สี emissive ตรงหน้าต่าง/ประตู (optional)

**แก้ที่:**
1. สร้าง PointLight หลัง house section
2. อัปเดต intensity ใน `updateSunPosition()`

```js
// สร้างไฟในบ้าน
const houseLight = new THREE.PointLight(0xffe4b0, 0, 8);
houseLight.position.set(houseX + 3, houseBase + 1.5, houseZ + 3);
scene.add(houseLight);

// ใน updateSunPosition():
// เปิดไฟบ้านตอน 17:00+ (hour >= 17)
const lampT = Math.max(0, (hour - 17)); // 0→0, 17→0, 18→1
houseLight.intensity = lampT * 1.5;
```

---

## ลำดับการทำ

| # | งาน | ความยาก | ผลกระทบ |
|---|------|---------|---------|
| 1 | สีท้องฟ้าตามเวลา | ง่าย | สูง — เปลี่ยน mood ทั้ง scene |
| 2 | Ground plane รับเงา | ง่ายมาก | สูง — เงาสมจริงขึ้นเยอะ |
| 3 | ลำน้ำ animation | ปานกลาง | ปานกลาง — เพิ่มชีวิตชีวา |
| 4 | แสงในบ้านตอนค่ำ | ง่าย | สูง — dramatic effect ตอนเย็น |
