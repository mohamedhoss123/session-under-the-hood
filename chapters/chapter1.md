# الفصل الأول : express-session 

مع اول بداية استخدام لك في المكتبة و تقوم باستدعائها باستخدام 
`
()app.use
`
 وبما انك تعلم ان `app.use` يتم وضع فيها `middleware` فيجب ان نضع المكتبة بهذا الشكل `app.use(session)` ولكن الغريب في الأمر أنك ترى انها لا تستخدم ك callback وانما تستخدم كدالة عادية بهذ الشكل `app.use(session())` كيف هذا ؟<br>
 الإجابة بسيطة ومنطقية جدا تقوم الدالة session عند استدعائها ووضع الاعدادات التي تريدها بارجاع دالة اخرى و هذه الدالة هية middleware و تقبل ( req , res , next ) كاي دالة middleware تعرفها وستري الموضوع بشكل أوضح في الكود التالي : 

```js
function session(options) {
    // code to handel setting your options

  return function session(req, res, next) {
   
    }
}
```
