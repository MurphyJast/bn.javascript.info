# রিসোর্স লোডিং: onload এবং onerror

আমরা এক্সটার্নাল রিসোর্স লোডিং স্টেটও ট্র্যাক করতে পারি, যেমন scripts, iframes, pictures ইত্যাদি।

এজন্য দুটি ইভেন্ট আছে:

- `onload` -- লোড সাকসেস হলে,
- `onerror` -- যদি কোন এরর সংগঠিত হয়।

## script লোডিং

মনে করুন আমরা একটি থার্ড-পার্টি লাইব্রেরী লোড করব এবং ঐ লাইব্রেরীর কোন একটি ফাংশন কল করব।

আমরা এটি ডায়নামিক্যালি লোড করব, এভাবে:

```js
let script = document.createElement('script');
script.src = "my.js";

document.head.append(script);
```

...এখন আমরা কিভাবে ফাংশনটি ডিক্লেয়ার করব? এক্ষেত্রে, আমাদের অবশ্যই লাইব্রেরীটি লোড হওয়া পর্যন্ত অপেক্ষা করতে হবে, অন্যথায় আমরা এটিকে কল করতে চাইলে এরর হবে।

```smart
আমাদের নিজস্ব script এর জন্য [JavaScript modules](info:modules) ব্যবহার করতে পারি, তবে এখানে আমরা একটি স্বতন্ত্র থার্ড-পার্টি লাইব্রেরী লোড করতে চাচ্ছি।
```

### script.onload

এক্ষেত্রে আমরা `load` ইভেন্ট এর সহায়তা নিতে পারি। কেননা script টি লোড হয়ে এক্সিকিউট হলে `load` ইভেন্টটি ট্রিগার হয়।

উদাহরণস্বরূপ:

```js run untrusted
let script = document.createElement('script');

// এখানে যেকোন script লোড করতে পারি
script.src = "https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.3.0/lodash.js"
document.head.append(script);

*!*
script.onload = function() {
  // স্ক্রিপ্ট টি একটি ভ্যারিয়েবল তৈরি করে "_"
  alert( _.VERSION ); // লোডেশ এর ভার্সন দেখাবে
};
*/!*
```

সুতরাং আমরা `onload` হ্যান্ডেলারে script টির বিভিন্ন ভ্যারিয়েবল, ফাংশন ব্যবহার করতে পারব।

...যদি আমাদের কোন কারণে লোডিং না হয় তাহলে কি হবে? মনে করুন, আমরা কোন একটি ভুল রিসোর্স লোড করতে চাচ্ছি (error 404) অথবা রিমোট সার্ভারটি ডাউন হয়েছে (unavailable)।

### script.onerror

এক্ষেত্রে script কোন কারণে লোড না হলে `error` ইভেন্টটি ট্রিগার হয়।

যেমন, এখানে আমরা একটি script লোড করতে চাচ্ছি যেটি একটি 404 পেজ:

```js run
let script = document.createElement('script');
script.src = "https://example.com/404.js"; // no such script
document.head.append(script);

*!*
script.onerror = function() {
  alert("Error loading " + this.src); // Error loading https://example.com/404.js
};
*/!*
```

দয়া করে মনে রাখুন এখানে আমরা HTTP error এর বিস্তারিত জানতে পারব না। যেকোন কারণে এরর সংগঠিত হতে যেমন  404 বা 500 অথবা অন্য যেকোন কারণে। তবে এটি শুধু `error` ইভেন্টটি ট্রিগার করে।

```warn
`onload`/`onerror` ইভেন্ট শুধুমাত্র লোডিং স্টেটকে ট্র্যাক করতে পারে।

তবে script এর মধ্যে কোন এরর থাকা সত্বেও script লোড হতে পারে। script এর মধ্যে কোন এরর থাকা সত্বেও `load` ইভেন্ট টি ট্রিগার হবে। যদি আমরা script এর error ট্র্যাক করতে চায় তাহলে `window.onerror` গ্লোবাল হ্যান্ডেলারের সহায়তা নিতে পারি।
```

## অন্যান্য রিসোর্স

অন্যান্য যেসব এলিমেন্ট এক্সটার্নাল রিসোর্স লোড করতে পারে তাদের জন্যও `load` এবং `error` কাজ করবে।

যেমন:

```js run
let img = document.createElement('img');
img.src = "https://js.cx/clipart/train.gif"; // (*)

img.onload = function() {
  alert(`Image loaded, size ${img.width}x${img.height}`);
};

img.onerror = function() {
  alert("Error occurred while loading image");
};
```

তবে কিছু ব্যতিক্রম বৈশিষ্ট্য আছে:

- বেশিরভাগ রিসোর্স লোড হওয়া শুরু করে যখন তাদের document এ সংযুক্ত করা হয় তবে `<img>` ব্যতিক্রম। এটি লোড শুরু করবে যখনি src দেখবে `(*)`।
- `<iframe>` এর জন্য, error বা load উভয়ের জন্য লোডিং সম্পন্ন হলে `iframe.onload` ট্রিগার হবে।

## Crossorigin পলিসি

ওয়েবের একটি নিয়ম আছে: এক সাইটের script অন্য সাইটের কন্টেন্ট কে অ্যাক্সেস করতে পারে না। যেমন  `https://facebook.com` এর script ইউজারের মেইলবক্সের `https://gmail.com` কন্টেন্ট অ্যাক্সেস করতে পারবে না।

আর সুনির্দিষ্টভাবে বলতে গেলে একটি অরিজিন (domain/port/protocol triplet) অন্য অরিজিনের কন্টেন্ট অ্যাক্সেস করতে পারবে না। যদিও বা তারা যদি ঐ সাইটের সাবডোমেন হয়।

যদি আমরা অন্য একটি ডোমেনের script ব্যবহার করি, এবং যদি ঐ স্ক্রিপ্টে কোন এরর সংগঠিত হয়, তাহলে এরর এর বিস্তারিত জানতে পারব না।

যেমন, আমাদের একটি স্ক্রিপ্ট আছে `error.js` যা একটি অসংজ্ঞায়িত ফাংশনকে কল করে:
```js
// 📁 error.js
noSuchFunction();
```

এখন চলুন, আমরা একই অরিজিন হতে তাদের লোড করি:

```html run height=0
<script>
window.onerror = function(message, url, line, col, errorObj) {
  alert(`${message}\n${url}, ${line}:${col}`);
};
</script>
<script src="/article/onload-onerror/crossorigin/error.js"></script>
```

এখন আমরা একটি বিস্তারিত এরর মেসেজ দেখব, এমন:

```
Uncaught ReferenceError: noSuchFunction is not defined
https://javascript.info/article/onload-onerror/crossorigin/error.js, 1:1
```

এখন চলুন ভিন্ন ডোমেইন হতে স্ক্রিপ্টটি লোড করি:

```html run height=0
<script>
window.onerror = function(message, url, line, col, errorObj) {
  alert(`${message}\n${url}, ${line}:${col}`);
};
</script>
<script src="https://cors.javascript.info/article/onload-onerror/crossorigin/error.js"></script>
```

এখন এরর মেসেজটি দেখাবে এমন:

```
Script error.
, 0:0
```

মেসেজটি ব্রাউজারভেদে ভিন্ন হতে পারে, তবে এরর মেসেজের বিস্তারিত দেখব না, কেননা ভিন্ন ডোমেনের কন্টেন্ট আমরা অ্যাক্সেস করতে পারব না।

কেন আমাদের বিস্তারিত এরর মেসেজ প্রয়োজন হতে পারে?

অনেক সার্ভিস আছে (এমনকি আমরা নিজেরাও বানাতে পারি) যারা `window.onerror` এর মাধ্যমে এরর  ট্র্যাক করে এবং তা অ্যানালাইসিসের জন্য সংরক্ষন করে। এটি ইউজারদের দ্বারা লাইভে সংগঠিত হওয়া এরর সমূহ ট্র্যাক করতে সহায়তা করে। তবে যদি ভিন্ন অরিজিন হতে এরর সংগঠিত হয় তাহলে তা আমরা ট্র্যাক করতে পারব না।

অন্যান্য রিসোর্সের জন্য cross-origin policy (CORS) প্রয়োগ করা হয়।

**cross-origin অ্যাক্সেসের জন্য, `<script>` ট্যাগে `crossorigin` নামের বিশেষ অ্যাট্রিবিউটযুক্ত করতে হবে, এবং রিমোর্ট সার্ভার হতে একটি বিশেষ হেডার প্রভাইড করবে**

cross-origin অ্যাক্সেসের তিনটি ধাপ আছে:

1. **`crossorigin` অ্যাট্রিবিউটব্যতীত** -- অ্যাক্সেস বাধাপ্রাপ্ত হবে।
2. **`crossorigin="anonymous"`** -- যদি রিমোট সার্ভার `Access-Control-Allow-Origin` এর মান `*` রেস্পন্ড করে তাহলে অ্যাক্সেস হবে।  এক্ষেত্রে ব্রাউজার সার্ভারে কোন ধরণের অথরাইজেশন ইনফরমেশন বা cookies সেন্ড করবে না।
3. **`crossorigin="use-credentials"`** -- access allowed if the server sends back the header `Access-Control-Allow-Origin` with our origin and `Access-Control-Allow-Credentials: true`. Browser sends authorization information and cookies to remote server.

```smart
cross-origin অ্যাক্সেস নিয়ে আরো বিস্তারিত <info:fetch-crossorigin>। এখানে নেটওয়ার্ক রিক্যুয়েস্টে `fetch` মেথড কিভাবে কাজ করে তা বর্ণনা করা হয়েছে, এক্ষেত্রে এটিও অনুরুপ কাজ করে।

যদিও আমরা এখনো "cookies" সম্পর্কে জানিনা, তবে আপনি চাইলে এই অধ্যায়ে বিস্তারিত জানতে পারবেন <info:cookie>।
```

উপরের উদাহরণে আমরা কোন `crossorigin` অ্যাট্রিবিউট সংযুক্ত করিনি। তাই cross-origin অ্যাক্সেস বাধাপ্রাপ্ত হবে। চলুন এটি যোগ করি।

`crossorigin` এর ভ্যালু হিসেবে `"anonymous"` বা `"use-credentials"` সেট করতে পারি। এক্ষেত্রে `"anonymous"` এর জন্য কোন cookies সেন্ড হবে না এবং শুধুমাত্র সার্ভার-সাইড হেডার প্রয়োজন হবে অন্যদিকে `"use-credentials"` এর জন্য cookies সেন্ড হবে ও অরিজিন এবং সার্ভার উভয়েই হেডার প্রয়োজন হবে।

যদি আমাদের cookies মূখ্য না হয়, তাহলে `"anonymous"` সেট করলেই হবে:

এখন, ধরে নিন রিমোট সার্ভার আমাদের সার্ভার `Access-Control-Allow-Origin` হেডারটি প্রদান করে। এখন আমরা রিমোট সার্ভারের এরর এর বিস্তারিত জানতে পারব।

```html run height=0
<script>
window.onerror = function(message, url, line, col, errorObj) {
  alert(`${message}\n${url}, ${line}:${col}`);
};
</script>
<script *!*crossorigin="anonymous"*/!* src="https://cors.javascript.info/article/onload-onerror/crossorigin/error.js"></script>
```

## সারাংশ

এক্সটার্নাল রিসোর্স লোড করে যেমন `<img>`, `<link>`, `<script>` এমন এলিমেন্টসমূহের লোডিং স্টেট ট্র্যাকের জন্য `load` এবং `error` নামের দুটি ইভেন্ট আছে:

- `load` যদি সঠিকভাবে লোড হয় তাহলে ট্রিগার হবে,
- `error` যদি সঠিকভাবে লোড না হয় তাহলে ট্রিগার হবে।

তবে `<iframe>` সর্বদা `load` ট্রিগার করবে যদিও রিসোর্স কোন 404 পেজ হয়।

এছাড়াও `readystatechange` ইভেন্টের সাহায্যেও ট্র্যাক করা যায়, তবে এটি তেমন ব্যবহার করা হয় না, কেননা `load/error` ইভেন্টসমূহ আরো বেশি সহজবোধ্য।
