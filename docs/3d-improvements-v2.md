# 3D House Zone Improvements V2

ไฟล์เป้าหมาย: `src/04-house-zone-3d.html`

---

## 1. Bloom Effect (Post-processing)

**สถานะ:** ✅ Done

**ปัญหา:** แสงอาทิตย์และ sun sphere ดูแข็ง ไม่มี glow ที่ละมุน

**แนวทาง:**
- Import `EffectComposer`, `RenderPass`, `UnrealBloomPass` จาก three/addons
- สร้าง composer แทนการ render ตรง
- ค่า bloom ที่เหมาะสม (subtle, ไม่ over-glow):
  - strength: 0.3
  - radius: 0.5
  - threshold: 0.85
- ต้องเปลี่ยน `renderer.render()` เป็น `composer.render()` ใน animate loop
- CSS2DRenderer ยังคง render แยกต่างหาก (ไม่ผ่าน composer)

**แก้ที่:**
1. เพิ่ม import ใน importmap/import section
2. สร้าง composer หลัง renderer
3. เปลี่ยน animate loop

```js
// Import
import { EffectComposer } from 'three/addons/postprocessing/EffectComposer.js';
import { RenderPass } from 'three/addons/postprocessing/RenderPass.js';
import { UnrealBloomPass } from 'three/addons/postprocessing/UnrealBloomPass.js';

// Setup (หลังสร้าง renderer)
const composer = new EffectComposer(renderer);
composer.addPass(new RenderPass(scene, camera));
const bloomPass = new UnrealBloomPass(
  new THREE.Vector2(window.innerWidth, window.innerHeight),
  0.3,   // strength
  0.5,   // radius
  0.85   // threshold
);
composer.addPass(bloomPass);

// Animate: เปลี่ยนจาก renderer.render() เป็น composer.render()
// Resize: เพิ่ม composer.setSize()
```

**Performance:**
- ปานกลาง — เพิ่ม 1 full-screen pass ต่อเฟรม
- มือถือรุ่นเก่าอาจ drop 5-10 fps
- ใช้ strength ต่ำ (0.3) เพื่อลดภาระ

---

## 2. Double Tap Reset Camera (Mobile UX)

**สถานะ:** ยังไม่ทำ

**ปัญหา:** บนมือถือหมุนกล้องจนหลงทิศ ไม่มีทางกลับมุมเริ่มต้นง่ายๆ

**แนวทาง:**
- ตรวจจับ double tap บน canvas (touchend 2 ครั้งภายใน 300ms)
- Animate กล้องกลับตำแหน่งเริ่มต้น (smooth transition)
- ใช้ GSAP หรือ manual lerp ใน animate loop
- แสดง toast/hint สั้นๆ "แตะ 2 ครั้งเพื่อรีเซ็ตมุมมอง" ตอนโหลดครั้งแรก (optional)

**แก้ที่:** เพิ่มหลัง OrbitControls section

```js
// Double tap to reset camera
const DEFAULT_CAM = { x: 16, y: 25, z: 35 };
const DEFAULT_TARGET = { x: 12, y: 0, z: 10 };
let lastTap = 0;

renderer.domElement.addEventListener('touchend', (e) => {
  const now = Date.now();
  if (now - lastTap < 300) {
    // Double tap detected — animate reset
    e.preventDefault();
    const startPos = camera.position.clone();
    const startTarget = controls.target.clone();
    const endPos = new THREE.Vector3(DEFAULT_CAM.x, DEFAULT_CAM.y, DEFAULT_CAM.z);
    const endTarget = new THREE.Vector3(DEFAULT_TARGET.x, DEFAULT_TARGET.y, DEFAULT_TARGET.z);
    let progress = 0;
    function animateReset() {
      progress += 0.05;
      if (progress >= 1) {
        camera.position.copy(endPos);
        controls.target.copy(endTarget);
        controls.update();
        return;
      }
      const t = progress * progress * (3 - 2 * progress); // smoothstep
      camera.position.lerpVectors(startPos, endPos, t);
      controls.target.lerpVectors(startTarget, endTarget, t);
      controls.update();
      requestAnimationFrame(animateReset);
    }
    animateReset();
  }
  lastTap = now;
});
```

**Performance:** ไม่กระทบ — event listener เท่านั้น, animation ชั่วคราว ~20 frames

---

## ลำดับการทำ

| # | งาน | ความยาก | ผลกระทบ |
|---|------|---------|---------|
| 1 | Bloom Effect | ง่าย | ปานกลาง — แสงละมุนขึ้น |
| 2 | Double Tap Reset | ง่ายมาก | สูง — UX มือถือดีขึ้นมาก |
