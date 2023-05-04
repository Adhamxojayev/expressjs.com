---
layout: page
title: Expressda Xatoliklar bilan ishlash
menu: guide
lang: uz
---

# Xato bilan ishlash

xatoliklar bilan ishlash uchun Expressda o'rta dasturlar bir oz farq qiladi
argumentlar uchta emas torta bo'ladi
`(err, req, res, next)`):

<pre><code class="language-javascript" translate="no">
app.use(function(err, req, res, next){
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
</code></pre>

Xatoliklar bilan ishlaydigan o'rta dasturlar `app.use()` va marshrut qo'ng'iroqlaridan so'ng yoziladi;
Masalan:

<pre><code class="language-javascript" translate="no">
var bodyParser = require('body-parser');
var methodOverride = require('method-override');

app.use(bodyParser());
app.use(methodOverride());
app.use(function(err, req, res, next){
  // logic
});
</code></pre>

O‘rta dastur ichidagi javoblarni o‘zingiz istagingiz bo‘yicha yozishingiz mumkin. Siz HTML xato sahifasi, oddiy xabar, JSON yoki o‘zingiz yoqtirgan boshqa narsalar bilan javob berishingiz mumkin.

Tashkiliy (va yuqori darajadagi tizim) maqsadlar uchun siz oddiy o‘rta dastur bilan bo‘lgani kabi, xatolarni qayta ishlash uchun bir nechta o‘rta dasturlarni belgilashingiz mumkin. Misol uchun, siz XHR orqali qilingan so‘rovlar uchun xato ishlov beruvchini aniqlamoqchi bo‘lsangiz, va ularsiz so‘rovlar uchun siz quyidagicha qilishingiz mumkin:

<pre><code class="language-javascript" translate="no">
var bodyParser = require('body-parser');
var methodOverride = require('method-override');

app.use(bodyParser());
app.use(methodOverride());
app.use(logErrors);
app.use(clientErrorHandler);
app.use(errorHandler);
</code></pre>

Umumiy `logErrors` stderr, loggly yoki shunga o'xshash xizmatlarga 
so'rov va xato ma'lumotlarini yozishi mumkin bo'lgan hollarda:

<pre><code class="language-javascript" translate="no">
function logErrors(err, req, res, next) {
  console.error(err.stack);
  next(err);
}
</code></pre>

Bu erda `clientErrorHandler` quyidagicha ta'riflangan (xato 
aniq keyingisiga uzatilganligiga e'tibor bering):

<pre><code class="language-javascript" translate="no">
function clientErrorHandler(err, req, res, next) {
  if (req.xhr) {
    res.status(500).send({ error: 'Something blew up!' });
  } else {
    next(err);
  }
}
</code></pre>

Quyidagi `errorHandler` "hammasini ushlab qolish" quyidagicha amalga oshirish mumkin:

<pre><code class="language-javascript" translate="no">
function errorHandler(err, req, res, next) {
  res.status(500);
  res.render('error', { error: err });
}
</code></pre>