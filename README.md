<div dir='rtl'>
مراحل ثبت مقصد بسته
  </div>
  
  
  ```
   POST  /api/v{version}/post-pack 
  ```
  
<div dir='rtl'>
آدرس بالا دارای یک پارمتر به نام destination  می باشد که دارای پامتر های زیر است.
  </div>
  
  ```
  {
    "phone_number": "string",
    "reciver_name": "string",
    "province": "string",
    "city": "string",
    "floor": "string",
    "alley": "string",
    "street": "string",
    "plaque": "string",
    "latitude": "string",
    "longitude": "string"
  }
  ```
  
<div dir='rtl'>
زمانی که کاربر نحوه تحویل بسته (reciver_category)  را  ترمینال (port) انتخاب کند. شما باید ابتدا استان و شهر انتخاب شده کاربر را به آدرس زیر بفرستید و خروجی نمایش داده را دریافت کنید. خروجی زیر شامل latitude و longitude می باشد و پس از دریافت باید آن ها را درون destination قرار بدهید. 
  </div>
  
  ```
GET /api/v{version}/ports 
  ```
  
  <div dir='rtl'>
  پارامتر های floor,alley, street, plaque,  دلبخواه می باشد و این زمان نیازی به پر کردن آن ها نیست. 

سپس با گرفتن موقعیت مکانی ترمینال مانند نحوه تحویل بسته دیگر یعنی ((destination روی نقشه نمایش داده شود.
</div>

