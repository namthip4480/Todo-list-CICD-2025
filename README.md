# Todo-list-CICD-2025-67030115-น้ำทิพย์

## แนบรูปผลการทดลองการทดสอบระบบ
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/30e294da-0319-46f2-95d6-16a6465c9b28" />

---

## คำถามการทดลอง
ให้จับคู่ Code ส่วนของการทดสอบ กับ Code การทำงาน มาอย่างน้อย 3 ฟังก์ชัน พร้อมอธิบายการทำงานของแต่ละกรณี

```plaintext

1. ฟังก์ชัน: create_todo()
   - Code การทำงาน: app/models.py
     ```python
     def create_todo(title, description):
         todo = Todo(title=title, description=description)
         db.session.add(todo)
         db.session.commit()
         return todo
     ```
   - Code การทดสอบ: tests/test_models.py
     ```python
     def test_create_todo():
         todo = create_todo("Test", "Test description")
         assert todo.id is not None
         assert todo.title == "Test"
     ```
   - การอธิบาย: ทดสอบว่าฟังก์ชัน create_todo() สามารถสร้าง Todo ใหม่ในฐานข้อมูลและคืนค่า object ที่มี id ถูกต้อง

2. ฟังก์ชัน: get_todo_by_id()
   - Code การทำงาน: app/models.py
     ```python
     def get_todo_by_id(todo_id):
         return Todo.query.get(todo_id)
     ```
   - Code การทดสอบ: tests/test_models.py
     ```python
     def test_get_todo_by_id():
         todo = create_todo("Test", "Desc")
         result = get_todo_by_id(todo.id)
         assert result.title == "Test"
     ```
   - การอธิบาย: ทดสอบว่าฟังก์ชันสามารถดึง Todo ตาม id ได้ถูกต้อง

3. ฟังก์ชัน: delete_todo()
   - Code การทำงาน: app/models.py
     ```python
     def delete_todo(todo_id):
         todo = get_todo_by_id(todo_id)
         db.session.delete(todo)
         db.session.commit()
     ```
   - Code การทดสอบ: tests/test_models.py
     ```python
     def test_delete_todo():
         todo = create_todo("ToDelete", "Desc")
         delete_todo(todo.id)
         assert get_todo_by_id(todo.id) is None
     ```
   - การอธิบาย: ทดสอบว่าฟังก์ชันสามารถลบ Todo ตาม id ได้สำเร็จและตรวจสอบว่าไม่มีในฐานข้อมูลแล้ว
