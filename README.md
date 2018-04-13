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
زمانی که کاربر نحوه تحویل بسته (reciver_category)  را  ترمینال (port) انتخاب کند. شما باید ابتدا استان و شهر انتخاب شده کاربر را به آدرس زیر بفرستید و خروجی نمایش داده را دریافت کنید. خروجی زیر شامل  latitude و longitude ترمینال می باشد و پس از دریافت باید آن ها را درون destination قرار بدهید. 
  </div>
  
  ```
GET /api/v{version}/ports ?province={province}&countis={countis}
  ```
  
  **Output**
  
  ```
{
  "name": "قرچک",
  "province": "تهران ",
  "countis": "دماوند",
  "latitude": "35.6892",
  "longitude": "51.3890",
  "key": 1
}
  
  ```
  
  <div dir='rtl'>
  پارامتر های floor,alley, street, plaque,  دلبخواه می باشد و در این زمان نیازی به پر کردن آن ها نیست. 

</div>

<br>
<br>

<div dir='rtl'>
بیمه
</div>
<br>
<div dir='rtl'>
هنگام ثبت بسته یک گزینه برای بیمه شدن یا نشدن بسته وجود دارد که نام پارامتر آن is_insurance می باشد. زمانی کاربر گزینه بیشه شدن را تایید که باید یک رنج قیمتی برای این بسته ثبت شود که. برای این کار باید توسط آدرس زیر خروجی مورد نظر را دریافت کرده و سپس مقدار key دریافتی را درون insurance_value_key قرار دهید.
</div>

**API Address To Get insurance values**

```
get /api/v{version}/post-pack/insurance-values 
```

**Output**

```
{
  "items": [
    {
      "max": 200,
      "min": 100,
      "item": "بین 100 تا 200 میلیون تومان",
      "key": 1
    }
  ]
}
```
<div dir='rtl'>
 مقدار item را به کاربر نمایش دهید و بر اساس انتخاب آن key در insurance_value_key قرار بدید.
</div>

<div dir='rtl'>
توضیح خلاصه تمامی فیلد ها
</div>

```
{
  "content": "string",
  "description": "string",
  "pack_count": 0,               // تعداد بسته
  "is_packing": true,            // بسته بندی شود یا خیر
  "pay_at_origin": true,         // پرداخت در مبدا
  "is_insurance": true,          // آیا بیمه شود
  "insurance_value_key": 0,      // اگر بیمه بود کلید رنج قیمت آن را قراردهید  
  "pack_type_key": 0,            // نوع بسته 
  "reciver_category": "string",  // "port" or "destination"
  "package_value": "string",
  "weight_key": 0
}

```

<div dir='rtl'>

پس از این که پارامتر های مورد نظر را POST کردید و کد 201 دریافت کردید. حال لازم است سراغ مرحله تایید نهایی بسته برویم  و از آدرس زیر استفاده کنیم. بعد از ثبت بسته یک header به شما ارئه می شود که نام آن x-created-postpack-key می باید برای تایید بسته به آن نیاز داریم.
</div>

```
PUT /api/v{version}/post-pack/accept 
```

**Parameters**

```
{
  "string_post_pack_key": "string" 
  "pay_at_origin": true, // پرداخت در محل
  "is_cash_payment": true // نقدی بودن پرداخت
}
```
**Response Headers**
```
{
  "cache-control": "no-cache",
  "content-length": "85",
  "content-type": "application/json; charset=utf-8",
  "date": "Fri, 13 Apr 2018 11:40:46 GMT",
  "expires": "-1",
  "pragma": "no-cache",
  "x-created-postpack-key": "c69add97c04f3e0af4f639e5d0fb9ea8"
}
```

<div dir='rtl'>
مقدار  x-created-postpack-key را درون پارامتر string_post_pack_key قرار دهید.
<br>
پس از ارسال پارامتر ها و دریافت 200 حال باید منتظر دریافت یک راننده باشید. در صورتی که یک راننده آن را تایید یا کنسل کند یک notification به شما ارسال می شود که حاوی داده های زیر می باشد
</div>

```

{
    "driver": {
        "dirver_type": "موتور",
        "phone_number": "09198143732",
        "photo_url": "https://i.stack.imgur.com/4PrQA.jpg"
    },
    "has_driver": false, // آیا راننده دارد ؟
    "is_canceled": false // آیا کنسل شده است ؟
}

```

<div dir='rtl'>

dirver_type نوع وسیله نقیله راننده می باشد که به صورت فارسی نمایش داده می شود

</div>
<br>
<br>

<div dir='rtl'>
<b>
پرداخت مبلغ بسته
</b>
<br>
اگر نحوه پرداخت نقدی بود در مرحله قبلی is_cash_payment را برابرقرار می دهیم true . اگر نه کاربر قصد پرادخت از اعتبار خود را داشت باید از آدرس زیر استفاده کنید:
</div>

```
POST /api/v{version}/payment/credit 
```

Parameters

```
{
  "string_post_pack_key": "string"
}
```

<div dir='rtl'>
در  این قسمت مانند مرحله قبل به  x-created-postpack-key نیاز داریم. 
</div>
<div dir='rtl'>
پست ارسال پارامتر اگر 200 دریافت کردید
مبلغ بسته با موفیت از کیف پول کاربر کسر خواهد شد.
 <br>
اگر 409 دریافت کردید به این معنی است که کیف پول کاربر اعتبار کافی برای پرداخت هزینه بسته را ندارد و باید کیف پول خود را شارژ کند.
</div>
