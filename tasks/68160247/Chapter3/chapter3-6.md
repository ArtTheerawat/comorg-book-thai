# บทที่ 3 — มุมมองระดับสูงของฟังก์ชันและการเชื่อมต่อคอมพิวเตอร์

## 3.6 PCI Express (PCIe)

**PCI Express (PCIe)** เป็นมาตรฐานการเชื่อมต่อแบบ Point-to-Point ที่ใช้กันอย่างแพร่หลายสำหรับอุปกรณ์ต่างๆ ภายในเครื่องคอมพิวเตอร์ เช่น การ์ดกราฟิก, SSD, การ์ดเครือข่าย

---

### 3.6.1 สถาปัตยกรรมทางกายภาพและตรรกะ (PCI Physical and Logical Architecture)

- PCIe ใช้โครงสร้างแบบ **Switch-based Topology** แทน Shared Bus
- อุปกรณ์แต่ละชิ้นเชื่อมต่อกับ **Root Complex** (ทำหน้าที่คล้าย CPU/Memory Controller)
- Root Complex เชื่อมกับอุปกรณ์ต่างๆ ผ่าน **Switch** ได้หลายชั้น
- การสื่อสารแบบ **Full-Duplex** — ส่งและรับข้อมูลพร้อมกันได้

```
Root Complex (CPU/Chipset)
    │
  Switch
  ├─── Endpoint (GPU)
  ├─── Endpoint (SSD)
  └─── Switch
           └─── Endpoint (NIC)
```

---

### 3.6.2 ชั้นกายภาพของ PCIe (PCIe Physical Layer)

- ใช้ **Lane** เป็นหน่วยการส่งข้อมูล แต่ละ Lane ประกอบด้วยสาย Differential 2 คู่ (ส่ง 1 คู่, รับ 1 คู่)
- รองรับการรวม Lane ได้หลายแบบ: **×1, ×2, ×4, ×8, ×16**
- ใช้การเข้ารหัสแบบ **8b/10b** (PCIe 1.x/2.x) หรือ **128b/130b** (PCIe 3.x ขึ้นไป) เพื่อความเสถียร

| เวอร์ชัน | ความเร็วต่อ Lane (ต่อทิศทาง) |
|---|---|
| PCIe 1.x | 250 MB/s |
| PCIe 2.x | 500 MB/s |
| PCIe 3.x | ~1 GB/s |
| PCIe 4.x | ~2 GB/s |
| PCIe 5.x | ~4 GB/s |

---

### 3.6.3 ชั้น Transaction ของ PCIe (PCIe Transaction Layer)

- จัดการการส่งข้อมูลในรูปแบบ **TLP (Transaction Layer Packet)**
- รองรับการดำเนินการหลัก ได้แก่:
  - **Memory Read/Write:** อ่าน/เขียนข้อมูลใน Memory Space
  - **I/O Read/Write:** อ่าน/เขียนข้อมูลใน I/O Space
  - **Configuration Read/Write:** ตั้งค่าอุปกรณ์
  - **Message:** ส่งสัญญาณพิเศษ เช่น Interrupt, Power Management
- รองรับการส่งแบบ **Posted** (ไม่รอ ACK) และ **Non-Posted** (รอ ACK)

---

### 3.6.4 ชั้น Data Link ของ PCIe (PCIe Data Link Layer)

- รับผิดชอบความน่าเชื่อถือของการสื่อสาร
- เพิ่ม **LCRC (Link CRC)** เข้าในแต่ละ TLP เพื่อตรวจสอบข้อผิดพลาด
- ใช้กลไก **ACK/NAK** สำหรับการส่งซ้ำเมื่อเกิดข้อผิดพลาด
- จัดการ **Flow Control** เพื่อป้องกัน Buffer Overflow

---

### สรุปสถาปัตยกรรมชั้น PCIe

```
┌─────────────────────────┐
│   Transaction Layer     │  ← TLP, Memory/IO/Config/Message
├─────────────────────────┤
│    Data Link Layer      │  ← LCRC, ACK/NAK, Flow Control
├─────────────────────────┤
│     Physical Layer      │  ← Lane, Differential Signaling
└─────────────────────────┘
```

---
*บทที่ 3 — Computer Organization and Architecture (Stallings)*
