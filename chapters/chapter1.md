# الفصل الأول : express-session 

مع اول بداية استخدام لك في المكتبة و تقوم باستدعائها باستخدام 
`
()app.use
`
 وبما انك تعلم ان `app.use` يتم وضع فيها `middleware` فيجب ان نضع المكتبة بهذا الشكل `app.use(session)` ولكن الغريب في الأمر أنك ترى انها لا تستخدم ك callback وانما تستخدم كدالة عادية بهذ الشكل `app.use(session())` كيف هذا ؟<br>
 الإجابة بسيطة ومنطقية جدا تقوم الدالة session عند استدعائها ووضع الاعدادات التي تريدها بارجاع دالة اخرى و هذه الدالة هية middleware و تقبل ( req , res , next ) كاي دالة middleware تعرفها وستري الموضوع بشكل أوضح في الكود 
[التالي](https://github.com/expressjs/session/blob/master/index.js#L179)
 : 

```js
function session(options) {
    // code to handel setting your options

  return function session(req, res, next) {
   
    }
}
```
هذه كانت احد النقاط التي كانت تحيرني حين استخدم اي مكتبة تتبع نفس النهج .

بعد ان تقوم بوضع الإعدادات الخاصة بك حين استدعاء `express-session` قد تترك بعض الاعدادات بدون ان تختارها علي سبيل المثال انك لم تقم بوضع `store` فتقوم المكتبة بوضع القيمة الافتراضية لها ك `MemoryStore` كما في الكود 
[التالي](https://github.com/expressjs/session/blob/master/index.js#L100)

```js
function session(options) {

  // get the session store
  var store = opts.store || new MemoryStore()
}
```

## شرح الاعتماديات dependencies

قبل التعمق أكثر في المكتبة يجد أن نعلم ماهية الاعتماديات `dependencies` المستخدمة من طرفها .

### مكتبة `cookie` 

و هية مكتبة مكونة من دالتين . <br>
الاولى هية `parse` و تستخدم لتقوم بعمل تحليل `(parse)` لل cookie واستخراجها على هيئة `object` مثال : 


```js
var cookies = cookie.parse('foo=bar; equation=E%3Dmc%5E2');
// { foo: 'bar', equation: 'E=mc^2' }
```
وتستخدم مكتبة express-session الدالة `parse` لتقوم باستخراج `session id` من ال `cookie` عن طريق الكود [التالي](https://github.com/expressjs/session/blob/master/index.js#L529)
: 
```js
function getcookie(req, name, secrets) {
  var header = req.headers.cookie;
  var raw;
  // some code
  // read from cookie header
  if (header) {
    var cookies = cookie.parse(header);
    raw = cookies[name];
    // some code
  }
  ```

الكود السابق يقوم باستخراج `cookie` الموجودة داخل  `header`  على هيئة `object` و يتم اختصاص `cookie` الخاصة ب `session` عن طريق الكود `raw = cookies[name]` ولمن لا يعلم فالقيمة الافتراضية للمتغير `nama` هيه `connect.sid` و بم انك استخدمت المكتبة من قبل فبالطبع لاحظت ان `cookie` الخاصة ب `session` تحفظ تلقائيا باسم `connect.sid` .<br>
تعريف القيمة الافتراضية ك `connect.sid` موجود في الرابط
[التالي](https://github.com/expressjs/session/blob/master/index.js#L97) .

 أما الدالة الثانية فيهة `serialize` و المسؤولة عن تحويل اسم `cookie` و القيمة الخاصة بها إلى الشكل الذي يوضع به في الـ `Set-Cookie` على هيئة نص `(string)` و الدالة تقبل العديد من الخيارات يمكن  تعلم المزيد عن الدالة `serialize` من [هنا](https://github.com/jshttp/cookie#cookieserializename-value-options) .


### ثانيا مكتبة `cookie-signature` 

ملحوظة هامة هذا الجزء يحتاج معرفة بسيطة بال `cryptography` و خوارزميات التشفير مثل `sha256`

وهية مكتبة وظيفتها حفظ ال `cookie` و التاكد في أنه لم يتم التلاعب بها و تشبة في كيفية عملها `jwt` إذا كنت قد استخدمتها من قبل . تستخدم المكتبة للتشفير `HMAC-SHA256` لمزيد من التفاصيل فيوجد شرح على 
[يوتيوب](https://www.youtube.com/watch?v=MKn3cxFNN1I)
 يغطي هذا 


لمزيد  من المعلومات حول كيف تعمل يمكنك النظر 
[هنا ](https://github.com/tj/node-cookie-signature)


