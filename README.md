# Rachapol Skills

คลังรวมคำสั่งและทักษะพิเศษ (**Agent Skills**) สำหรับ **AI Coding Agents** (เช่น Claude Code, Antigravity, Cursor, Codex) ตามมาตรฐาน [skills.sh](https://skills.sh) (Open Agent Skills Ecosystem)

ออกแบบมาเพื่อการพัฒนาซอฟต์แวร์จริง เน้นการทำงานที่กระชับ ป้องกันไม่ให้ AI ทำงานหลุดกรอบ หรือคิดโค้ดไปเอง โดยผสานแนวคิดหลักจาก **Matt Pocock** และ **Andrej Karpathy**

---

## Quick Start

คุณสามารถติดตั้งชุดสกิลนี้ลงในโปรเจกต์ใดๆ ได้ทันทีผ่านคำสั่ง `npx skills`:

```bash
npx skills add Rachapol03/Rachapol-skills --all

# สำหรับการติดตั้งแบบ Global
npx skills add Rachapol03/Rachapol-skills -g
```

### ตรวจสอบรายชื่อสกิลทั้งหมดในคลัง
```bash
npx skills add Rachapol03/Rachapol-skills --list
```

---

## End-to-End Workflow

สกิลทั้ง 8 ตัวถูกออกแบบมาให้ทำงานต่อกันตั้งแต่เริ่มมีไอเดียจนถึงปิดงาน:

```mermaid
flowchart TD
    subgraph 1_Ideation ["1. ช่วงคิดและตกผลึกความต้องการ"]
        Start([เริ่มงาน / มีโจทย์ใหม่]) --> ModeCheck{"โหมดการทำงาน"}
        ModeCheck -- "คิดลอยๆ / ระดมสมอง" --> GrillMe["/grill-me<br/>(สัมภาษณ์จบในแชต ไม่สร้างไฟล์)"]
        ModeCheck -- "ทำโปรเจกต์จริงใน Codebase" --> GrillDocs["/grill-with-docs<br/>(สัมภาษณ์ + สร้าง CONTEXT.md & ADR)"]
    end

    subgraph 2_Planning ["2. ช่วงแปลงเป็นพิมพ์เขียว"]
        GrillMe --> SpecTasks["/spec-to-tasks<br/>(สังเคราะห์ Spec + แตก Checklist งาน Tracer-bullet)"]
        GrillDocs --> SpecTasks
    end

    subgraph 3_Execution ["3. ช่วงลงมือพัฒนา"]
        SpecTasks --> Karpathy["karpathy-guidelines<br/>(คุมความกระชับ: Simplicity First & Surgical Changes)"]
        Karpathy --> Coding[ลงมือทำทีละ Task ใน Checklist]
    end

    subgraph 4_Review ["4. ช่วงตรวจทาน"]
        Coding --> Scrutinize["/scrutinize<br/>(ตรวจทานโค้ดจากมุมมองคนนอกแบบตามรอยจริง)"]
        Scrutinize --> Done([งานเสร็จสมบูรณ์ / Shipped!])
    end

    subgraph 5_Emergency ["5. การแก้บั๊กและส่งต่องาน"]
        Coding -. "เจอบั๊กซับซ้อน" .-> Debug["/debug-mantra<br/>(มนตราสืบสวนบั๊ก 4 สเต็ป)"]
        Debug --> PostMortem["/post-mortem<br/>(เขียนสรุป RCA เชิงลึก)"]
        PostMortem -.-> Coding

        Anywhere[Token ใกล้เต็ม / ย้ายเซสชัน] -.-> Handoff["/handoff<br/>(สรุปงานลง Temp OS ส่งต่อให้ Agent ตัวถัดไป)"]
    end
```

---

## Catalog

### 1. `skills/engineering/`

* **[`spec-to-tasks`](./skills/engineering/spec-to-tasks/SKILL.md)** — สะพานเชื่อมระหว่างการวางแผนกับการเขียนโค้ด ทำหน้าที่สังเคราะห์บทสนทนาเป็น **Technical Specification** และแตกเป็น **Tracer-Bullet Tasks Checklist** พร้อมระบุลำดับก่อน-หลัง เพื่อส่งต่อให้ `karpathy-guidelines` ทำงานทีละข้อ
* **[`post-mortem`](./skills/engineering/post-mortem/SKILL.md)** — บันทึกสรุปการแก้บั๊กอย่างเป็นทางการเชิงลึกสำหรับ Engineer (บันทึกสาเหตุในระดับโค้ด Root Cause, กลไกการเกิดบั๊ก, การทดสอบยืนยัน, และวิธีป้องกันในอนาคต) บันทึกลงใน `docs/post-mortems/<date>-<slug>.md` ให้อัตโนมัติ
* **[`karpathy-guidelines`](./skills/engineering/karpathy-guidelines/SKILL.md)** — กฎเหล็กคุมพฤติกรรม AI ในการเขียนโค้ด: Think before coding, Simplicity first, Surgical changes, และ Goal-driven execution
* **[`scrutinize`](./skills/engineering/scrutinize/SKILL.md)** — ตรวจทาน Plan / PR / โค้ดที่เปลี่ยนแปลงจากมุมมองคนนอก โดยตั้งคำถามถึงเจตนาและไล่ตามรอยเส้นทางโค้ดจริงจากต้นน้ำถึงปลายน้ำ ไม่ดูเฉพาะแค่ diff (ถามว่าไม่มีวิธีการคิดหรือ code ที่ง่ายกว่านี้แล้วหรอ)
* **[`debug-mantra`](./skills/engineering/debug-mantra/SKILL.md)** — วินัยการแก้ Bug 4 สเต็ป: 1. Reproduce reliably (ทำตัวทดสอบให้พังแน่นอนก่อน), 2. Know fail path (debugger -> source trace -> instrumentation), 3. Falsify hypothesis (หาทางหักล้างสมมติฐานก่อน), 4. Every run is a breadcrumb (บันทึกร่องรอยการทดลอง)

---

### 2. `skills/productivity/`

* **[`grill-me`](./skills/productivity/grill-me/SKILL.md)** — การสัมภาษณ์เค้นความคิดแบบโสเครตีส (Socratic Grilling) สำหรับการคิดเร็ว ระดมสมอง และขจัดความคลุมเครือ จบในแชตโดยไม่สร้างไฟล์ในเครื่อง
* **[`grill-with-docs`](./skills/productivity/grill-with-docs/SKILL.md)** — การสัมภาษณ์เค้น Requirement เชิงลึกสำหรับโปรเจกต์จริง พร้อมบันทึกข้อตกลงและคำศัพท์เฉพาะลง `CONTEXT.md` และบันทึกการตัดสินใจสถาปัตยกรรมลง `docs/adr/` ทันที
* **[`handoff`](./skills/productivity/handoff/SKILL.md)** — สรุปงานและสถานะที่ค้างอยู่ลงโฟลเดอร์ชั่วคราว (`OS Temp Directory`) เมื่อแชตยาวหรือ Token ใกล้เต็ม พร้อมตัดข้อมูลความลับ (Secrets/Keys) ทิ้งอัตโนมัติ และระบุ Suggested Skills ให้ Agent ตัวถัดไปเปิดต่อได้ทันที

---

## วิธีการสร้างและเพิ่มสกิลใหม่ (Contributing)

1. เลือกโฟลเดอร์ให้เหมาะสม: `skills/engineering/<name>/` หรือ `skills/productivity/<name>/`
2. สร้างไฟล์ `SKILL.md` โดยใส่ YAML Frontmatter:
   ```yaml
   ---
   name: your-skill-name
   description: Action-oriented summary. Front-load leading keywords for discovery.
   disable-model-invocation: true # ใส่เฉพาะกรณีที่เป็น skill ที่ user ต้องเรียกใช้งานเอง
   ---
   ```
3. ทดสอบการตรวจจับในเครื่อง:
   ```bash
   npx skills add ./ --list
   ```
4. ตรวจสอบความถูกต้องและส่ง Commit / Pull Request ขึ้น GitHub!

---

## License & References

- **License:** MIT
- **Architecture Inspiration:**
  - [Matt Pocock's Skills](https://github.com/mattpocock/skills)
  - [Andrej Karpathy's Coding Pitfalls & Guidelines](https://github.com/multica-ai/andrej-karpathy-skills)
  - [9arm-skills](https://github.com/thananon/9arm-skills)
  - [skills.sh](https://skills.sh)
