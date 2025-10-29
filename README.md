# Todo-list-CICD-2025-67030115-น้ำทิพย์

---

## Repo code
https://github.com/namthip4480/flask-todo-cicd.git

## แนบรูปผลการทดลองการทดสอบระบบ
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/30e294da-0319-46f2-95d6-16a6465c9b28" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/55318e31-27e8-4588-8efd-c198ca05f994" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a1aedb94-a55f-4bc7-a955-292413a96692" />

---

## คำถามการทดลอง
**จับคู่ Code ส่วนของการทดสอบ กับ Code การทำงาน อย่างน้อย 3 ฟังก์ชัน พร้อมอธิบายการทำงาน**

1. **ฟังก์ชัน: create_todo()**
   - **Code การทำงาน:** `app/models.py`
     ```python
     def create_todo(title, description):
         todo = Todo(title=title, description=description)
         db.session.add(todo)
         db.session.commit()
         return todo
     ```
   - **Code การทดสอบ:** `tests/test_models.py`
     ```python
     def test_create_todo():
         todo = create_todo("Test", "Test description")
         assert todo.id is not None
         assert todo.title == "Test"
     ```
   - **อธิบาย:** ทดสอบว่าฟังก์ชันสามารถสร้าง Todo ใหม่ในฐานข้อมูลและคืนค่า object ที่มี id ถูกต้อง

2. **ฟังก์ชัน: get_todo_by_id()**
   - **Code การทำงาน:** `app/models.py`
     ```python
     def get_todo_by_id(todo_id):
         return Todo.query.get(todo_id)
     ```
   - **Code การทดสอบ:** `tests/test_models.py`
     ```python
     def test_get_todo_by_id():
         todo = create_todo("Test", "Desc")
         result = get_todo_by_id(todo.id)
         assert result.title == "Test"
     ```
   - **อธิบาย:** ทดสอบว่าฟังก์ชันสามารถดึง Todo ตาม id ได้ถูกต้อง

3. **ฟังก์ชัน: delete_todo()**
   - **Code การทำงาน:** `app/models.py`
     ```python
     def delete_todo(todo_id):
         todo = get_todo_by_id(todo_id)
         db.session.delete(todo)
         db.session.commit()
     ```
   - **Code การทดสอบ:** `tests/test_models.py`
     ```python
     def test_delete_todo():
         todo = create_todo("ToDelete", "Desc")
         delete_todo(todo.id)
         assert get_todo_by_id(todo.id) is None
     ```
   - **อธิบาย:** ทดสอบว่าฟังก์ชันสามารถลบ Todo ตาม id ได้สำเร็จและตรวจสอบว่าไม่มีในฐานข้อมูลแล้ว

---

# ส่วนที่ 10: การทดสอบและประเมินผล

## 10.1 Checklist การทดลอง
- [x] สร้าง GitHub repository และ clone ลงเครื่อง
- [x] สร้าง Flask application ที่มี CRUD operations ครบถ้วน
- [x] เขียน tests ครอบคลุม code coverage > 80%
- [x] สร้าง Dockerfile ที่ optimize แล้ว
- [x] สร้าง docker-compose.yml ที่แยก services
- [x] รัน application ด้วย Docker และทดสอบใน local สำเร็จ
- [x] สร้าง GitHub Actions workflow มีทั้ง CI และ CD
- [ ] Deploy ไปยัง Render สำเร็จ
- [ ] Deploy ไปยัง Railway สำเร็จ
- [ ] ทดสอบ API endpoints บน production
- [ ] Health checks ทำงานถูกต้อง
- [ ] Auto-deployment ทำงานเมื่อ push code ใหม่

---

## 10.2 คำถามทบทวน

1.Docker Architecture
   - **เหตุผลที่แยก Database และ Application เป็น container แยก**
     - Isolation: ปัญหาของ container หนึ่งไม่กระทบอีกตัว
     - Scalability: สามารถ scale app หลาย instance โดยไม่กระทบ database
     - Maintainability: อัปเดต/เปลี่ยน database version ง่าย
     - Security: จำกัดสิทธิ์การเข้าถึงระหว่าง container
     - Reusability: Database container ใช้ร่วมกับหลาย app ได้
   - **ประโยชน์ของ Multi-stage build**
     - ลดขนาด image (build tools ไม่อยู่ใน production)
     - เพิ่มความปลอดภัย (ไม่มี source code หรือ build tools ใน runtime)
     - จัดการ dependencies ได้ดี (แยก build กับ runtime)

2. Testing Strategy
   - **ความสำคัญของ Code Coverage**
     - วัดความสมบูรณ์ของการทดสอบ
     - ลดโอกาส bug ใน production
     - ช่วยปรับปรุงคุณภาพโค้ด (refactor จุดซับซ้อน)
     - ช่วยทีม maintain codebase
     > Coverage สูง ≠ test logic ดี ต้องดูคุณภาพ test ด้วย

3. Deployment
   - **เหตุผลที่ Health check endpoint สำคัญ**
     - ตรวจสอบ container/instance ว่ายังทำงานปกติ
     - Load balancer ส่ง traffic ไปเฉพาะ instance ที่ healthy
     - ใช้สำหรับ monitoring uptime
   - **ความแตกต่าง Render vs Railway**
     | Feature | Render | Railway |
     |---------|--------|---------|
     | Focus | Web service / static site / cron jobs / DB | Web app + DB + background services |
     | Ease of use | GUI + Git deploy, auto HTTPS | GUI + Git deploy, microservices focus |
     | Database support | Postgres, MySQL, Redis | Postgres, MySQL, Redis, MongoDB |
     | Scaling | Manual & Auto | Mostly manual (auto on Pro plan) |
     | Pricing | Free tier + Paid scale easily | Free tier limited, Paid flexible |
     - **Render:** เหมาะเว็บ/static app + auto HTTPS + cron jobs
     - **Railway:** เหมาะ microservices + DB + prototypes
