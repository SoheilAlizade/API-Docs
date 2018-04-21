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
    "PhoneNumber": "string",
    "ReciverName": "string",
    "Province": "string",
    "City": "string",
    "Floor": "string",
    "Alley": "string",
    "Street": "string",
    "Plaque": "string",
    "Latitude": "string",
    "Longitude": "string"
 }
  ```
  
<div dir='rtl'>
زمانی که کاربر نحوه تحویل بسته (ReciverCategoryString)  را  ترمینال (port) انتخاب کند. شما باید ابتدا استان و شهر انتخاب شده کاربر را به آدرس زیر بفرستید و خروجی نمایش داده را دریافت کنید. خروجی زیر شامل  latitude و longitude ترمینال می باشد و پس از دریافت باید آن ها را درون destination قرار بدهید. 
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
هنگام ثبت بسته یک گزینه برای بیمه شدن یا نشدن بسته وجود دارد که نام پارامتر آن IsInsurance می باشد. زمانی کاربر گزینه بیشه شدن را تایید که باید یک رنج قیمتی برای این بسته ثبت شود که. برای این کار باید توسط آدرس زیر خروجی مورد نظر را دریافت کرده و سپس مقدار key دریافتی را درون InsuranceValueId قرار دهید.
</div>

**API Address To Get insurance values**

```
GET /api/v{version}/post-pack/insurance-values 
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
 مقدار item را به کاربر نمایش دهید و بر اساس انتخاب آن key در InsuranceValueId قرار بدید.
</div>

<div dir='rtl'>
توضیح خلاصه تمامی فیلد ها
</div>

```
{
  "Content": "string",
  "Description": "string",
  "PackCount": 0,               // تعداد بسته
  "IsPacking": true,            // بسته بندی شود یا خیر
  "PayAtOrigin": true,         // پرداخت در مبدا
  "IsInsurance": true,          // آیا بیمه شود
  "InsuranceValueId": 0,      // اگر بیمه بود کلید رنج قیمت آن را قراردهید  
  "PackTypeId": 0,            // نوع بسته 
  "ReciverCategoryString": "string",  // "port" or "destination"
 
  "WeightId": 0
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
  "StringPostPackKey": "string" 
  "PayAtOrigin": true, // پرداخت در محل
  "IsCashPayment": true // نقدی بودن پرداخت
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
مقدار  x-created-postpack-key را درون پارامتر StringPostPackKey قرار دهید.
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
اگر نحوه پرداخت نقدی بود در مرحله قبلی IsCashPayment را برابرقرار می دهیم true . اگر نه کاربر قصد پرادخت از اعتبار خود را داشت باید از آدرس زیر استفاده کنید:
</div>

```
POST /api/v{version}/payment/credit 
```

Parameters

```
{
  "PostPackKey": "string"
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

<br>
<br>

<div dir='rtl'>
برای شارژ کیف پول از طریق آدرس زیر یک لینک به همراه جزئیات ارسالی برای شما ارسال می شود.
</div>

```
GET /api/v{version}/payment/balance-link 
```

```
{
  "url": "http://google.com",
  "params": [
    "user_key",
    "device" // "ios" or "android"
  ],
  "method": "GET"
}
```
<div dir='rtl'>
مقادیر params و method ثابت می باشند ولی url به صورت Dynamic است.

پس از انجام این کار پارمتر user_key را از طریق لینک زیر دریافت کنید .

این لینک کلید کاربر جاری را به شما ارئه می دهد.
</div>

```
GET /api/v{version}/account/current-user 
```

```
{
  "user_key": "08fbcf40be9fa4da146339e5bd03a52e"
}
```

<div dir='rtl'>
سپس user_key دریافت شده به را به همراه device به لینک ارئه شده بفرستید.
 <br>
درون device سیستم عامل موبایل را قرار دهید.
</div>
<br>
<br>

<div dir='rtl'>
برای به دست آوردن پست ثبت شده از آدرس زیر استفاده کنید و به جای پارامتر key مقدار x-created-postpack-key را قرار بدید.
</div>

```
GET /api/v{version}/post-pack/created/{key} 
```

```
{
  "content": "string",
  "description": "string",
  "pack_count": 0,
  "price": 53316464,
  "is_packing": true,
  "pay_at_origin": true,
  "pack_explain": "سفارش شما به تعداد 0 بسته و میانگین وزن کل بین 100 تا 200 کیلوگرم شامل string  به ارزش   با بیمه کالارسان  و بسته بندی شرکت ثبت گردید.",
  "reciver_category": "port",
  "is_cash_payment": false,
  "is_accepted": false,
  "status": "reciver",
  "is_closed": false,
  "is_insurance": true,
  "insurance_value": null,
  "pack_type": {
    "name": "سبک",
    "description": "توضیح",
    "key": 1
  },
  "origin": {
    "address": " استان string شهر string خیابان string کوچه string پلاک string طبقه string",
    "province": "string",
    "city": "string",
    "street": "string",
    "plaque": "string",
    "latitude": "string",
    "longitude": "string",
    "floor": "string",
    "alley": "string",
    "key": 11
  },
  "destination": {
    "phone_number": "string",
    "reciver_name": "string",
    "address": " استان string شهر string خیابان string کوچه string پلاک string طبقه string",
    "province": "string",
    "city": "string",
    "street": "string",
    "plaque": "string",
    "latitude": "string",
    "longitude": "string",
    "floor": "string",
    "alley": "string",
    "key": 11
  },
  "weight": {
    "min": 100,
    "max": 200,
    "item": "بین 100 تا 200 کیلو گرم",
    "key": 1
  },
  "created_date": "1523619645",
  "key": 11
}
```

<div dir='rtl'>

لغو بسته 

</div>



```
PUT /api/v{version}/post-pack/{key}/cancel 

```
 <div dir='rtl'>
 برای لغو نیاز است یک key به آدرس موردنظر بفرستیم که میتوان آن را همانند مراحل قبل به دست آوریم.

 در این قسمت از هدر x-created-postpack-key برای مقدار دهی key استفاده کنید که هنگام accept  کردن بسته آن را دریافت کردید.

 </div>


	