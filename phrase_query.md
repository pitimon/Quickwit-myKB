## Quickwit’s phrase query allows you to search for documents containing a specific sequence of terms

- อธิบายเกี่ยวกับ phrase query ใน Quickwit 

1. Phrase query คืออะไร?
Phrase query คือการค้นหาเอกสารที่มีลำดับคำเฉพาะเจาะจงปรากฏอยู่ติดกัน โดยไม่มีคำอื่นคั่นกลาง ตัวอย่างเช่น ถ้าเราค้นหา "quick brown fox" เราต้องการเอกสารที่มีคำเหล่านี้เรียงติดกันในลำดับนี้เท่านั้น

2. การกำหนด index configure เพื่อรองรับ phrase query:
ต้องเปิดใช้ position indexing เพื่อให้รองรับ phrase query โดยในการกำหนดค่า schema ของ Quickwit ต้องตั้งค่า `tokenizer` และ `record` ให้เหมาะสม ตัวอย่างเช่น:

```json
{
  "field_name": {
    "type": "text",
    "tokenizer": "default",
    "record": "position"
  }
}
```

3. กรณีที่ไม่สามารถใช้ phrase query ได้:
ไม่ได้เปิดใช้ position indexing หรือใช้ `record: "basic"` แทน `record: "position"` จะไม่สามารถใช้ phrase query ได้ ตัวอย่าง:

```json
{
  "field_name": {
    "type": "text",
    "tokenizer": "default",
    "record": "basic"
  }
}
```

4. การใช้ query ทดแทนเมื่อไม่มี position indexing:
หากไม่ได้ทำ position indexing ไว้ สามารถใช้ boolean query แทนได้ แต่จะไม่ได้ผลลัพธ์ที่แม่นยำเท่า phrase query ตัวอย่างเช่น:

```
field_name:quick AND field_name:brown AND field_name:fox
```

แต่วิธีนี้อาจจะให้ผลลัพธ์ที่มีคำเหล่านี้อยู่ในเอกสาร แต่ไม่จำเป็นต้องอยู่ติดกันหรือเรียงลำดับเดียวกัน

5. ผลกระทบต่อประสิทธิภาพของ Quickwit engine:
การเปิดใช้ position indexing จะทำให้ Quickwit ต้องทำงานมากขึ้นในขั้นตอนการสร้างดัชนี และจะใช้พื้นที่เก็บข้อมูลมากขึ้น เนื่องจากต้องเก็บข้อมูลตำแหน่งของคำแต่ละคำไว้ด้วย อย่างไรก็ตาม มันจะช่วยให้การค้นหาแบบ phrase query ทำได้เร็วและแม่นยำมากขึ้น ดังนั้น ต้องพิจารณาระหว่างประสิทธิภาพในการค้นหากับทรัพยากรที่ต้องใช้เพิ่มขึ้น

โดยสรุป การใช้ phrase query ใน Quickwit ช่วยให้ค้นหาข้อความที่มีลำดับคำเฉพาะได้อย่างแม่นยำ แต่ต้องแลกมาด้วยการใช้ทรัพยากรที่มากขึ้นในการสร้างและเก็บดัชนี ควรพิจารณาความต้องการของแอปพลิเคชันของว่าจำเป็นต้องใช้ความสามารถนี้หรือไม่ ก่อนที่จะตัดสินใจเปิดใช้งาน