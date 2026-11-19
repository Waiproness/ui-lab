# แล็บทดสอบหน้าเว็บ ITDS362

งานนี้เลือกทำ Part 3 ด้วย Robot Framework และ Part 4 ด้วย Cypress ทั้งสองชุดทดสอบกรณี QP-01 บน [QuickPizza](https://quickpizza.grafana.com) และทดสอบแบบฟอร์มตัวอย่างของ Selenium ด้วย

## QP-01 ทดสอบอะไร

QP-01 เริ่มจากลูกค้าขอพิซซ่าแบบมังสวิรัติ กำหนดแคลอรีไม่เกิน 600 ต่อชิ้น และห้ามใช้เครื่องมือ Knife เมื่อเว็บแนะนำพิซซ่าแล้ว การทดสอบจะตรวจชื่อ เครื่องมือ และแคลอรี จากนั้นกดให้คะแนนโดยยังไม่ล็อกอินเพื่อดูข้อความที่เว็บตอบกลับ สุดท้ายจึงเข้าเมนูแอดมิน แล้วค้นว่ารายการมีพิซซ่าชื่อเดิมและเครื่องมือตรงกับหน้าลูกค้า

การรันแต่ละครั้งตั้งชื่อขึ้นต้นด้วย `Lab Pizza` แล้วต่อท้ายวันเวลา ทำให้แยกพิซซ่าของรอบนั้นออกจากรายการของผู้ใช้อื่นได้ ชุดทดสอบไม่กดล้างคะแนนของเว็บที่ทุกคนใช้ร่วมกัน

## วิธีรันทดสอบ

เปิดเทอร์มินัลในโฟลเดอร์ของเครื่องมือที่จะรันก่อน ไลบรารีที่ติดตั้ง, ChromeDriver และ virtual environment ไม่ได้เก็บไว้ใน Git จึงต้องเตรียมในเครื่องที่นำ repo ไปรัน

### Robot Framework

ใน `robot/` มี `first.robot` สำหรับตรวจชื่อหน้าเว็บ, `web_form.robot` สำหรับกรอกและส่งแบบฟอร์ม และ `pizza_scenario.robot` สำหรับ QP-01 ตัวระบุตำแหน่งบนหน้าเว็บและคำสั่งที่ใช้ร่วมกันอยู่ใน `resources/quickpizza.resource` ผลการรันเต็มที่ผ่านสองรอบอยู่ใน `results/run1` และ `results/run2` โดยแต่ละโฟลเดอร์มีรายงานและภาพหน้าจอ

เมื่ออยู่ใน `robot/` ให้สร้าง virtual environment ด้วย `python -m venv .venv` แล้วติดตั้งแพ็กเกจด้วย `.venv\Scripts\python.exe -m pip install -r requirements.txt` ดาวน์โหลด ChromeDriver รุ่นที่ตรงกับ Chrome ในเครื่อง จากนั้นตั้งค่าใน Windows PowerShell โดยเปลี่ยน path ตัวอย่างให้เป็นตำแหน่งไฟล์จริง และเปิดใช้ virtual environment:

```powershell
$env:CHROMEDRIVER = 'C:\path\to\chromedriver.exe'
.\.venv\Scripts\Activate.ps1
```

สั่งรันจาก `robot/` ด้วย `robot -v BROWSER:headlesschrome first.robot web_form.robot pizza_scenario.robot` คำว่า `headlesschrome` หมายถึง Chrome ทำงานโดยไม่เปิดหน้าต่างให้เห็น เมื่อจบแล้ว Robot จะเขียน log และ report ไว้ในโฟลเดอร์ที่รันคำสั่ง

### Cypress

ใน `cypress/` มีไฟล์ทดสอบแบบฟอร์มและ QP-01 ที่ `cypress/e2e` คำสั่งล็อกอินแอดมินอยู่ใน `cypress/support/commands.js` ส่วนภาพและวิดีโอจากรอบที่ผ่านอยู่ใน `cypress/screenshots` และ `cypress/videos`

เมื่ออยู่ใน `cypress/` ให้ติดตั้งแพ็กเกจด้วย `npm ci` แล้วรัน `npm test` คำสั่งนี้ทดสอบทั้งสองไฟล์ และรันผ่านสองรอบบนเครื่อง Windows ที่ใช้ทำงานนี้ Cypress 16.2.1 เคยหยุดตอนเปลี่ยนจากไฟล์ทดสอบหนึ่งไปอีกไฟล์ โดยแสดง `* is not a valid URL` ไม่ว่าจะสลับลำดับไฟล์อย่างไร จึงตั้งให้ `npm test` เปิด Cypress process ใหม่สำหรับแต่ละไฟล์

## ผลและหลักฐาน

[RESULTS.md](RESULTS.md) บันทึกเวอร์ชันเครื่องมือ ผลรันจริง และคำตอบ Part B ภาพการทำ QP-01 ด้วยมืออยู่ใน `manual-evidence/` รอบสุดท้ายผ่านทั้ง 7 ขั้นตอน: [ภาพฝั่งลูกค้า](manual-evidence/qp01-attempt3-full-customer-flow.png) แสดงค่าที่กรอก ผลพิซซ่า และข้อความให้ล็อกอินก่อนให้คะแนน ส่วน [ภาพฝั่งแอดมิน](manual-evidence/qp01-attempt3-admin-list.png) แสดงพิซซ่าชื่อเดียวกันพร้อมเครื่องมือที่ตรงกัน
