
# স্টিকি ফ্ল্যাগ "y", নির্দিষ্ট (পজিশনে) অবস্থানে অনুসন্ধান

`pattern:y` ফ্ল্যাগটির মাধ্যমে আমরা প্রদত্ত স্ট্রিংয়ের কোন একটা  নির্দিষ্ট পজিশনে অনুসন্ধান চালাতে পারি।

`pattern:y` ফ্ল্যাগ এর ব্যবহারিক প্রয়োগ, এবং রেগুলার এক্সপ্রেশনকে আরো ভালোভাবে বুঝতে, চলুন একটি উদাহরণ দেখি।

রেগুলার এক্সপ্রেশনের একটি সাধারণ কাজ হল "(লেক্সিকাল অ্যানালাইসিস) lexical analysis": কোন একটি টেক্সটে প্রোগ্রামিং ভাষাগুলোর এলিমেন্টের স্ট্রাকচার খুঁজা। যেমন, HTML এর আছে ট্যাগ অ্যাট্রিবিউট, জাভাস্ক্রিপ্টে আছে ফাংশন, ভ্যারিয়েবল ইত্যাদি।

লেক্সিকাল অ্যানালাইজার হল একটি বিশেষ টার্ম এবং এর নিজস্ব টুল এবং অ্যালগরিদম আছে, তবে এখানে আমাদের এই ব্যাপারটির বিস্তারিত জানা লাগবে না এবং এর একটি কমন কাজ হল: কোন একটি নির্দিষ্ট অবস্থানে কোন কিছু পড়া।

যেমন আমাদের একটি স্ট্রিং আছে `subject:let varName = "value"`, এখানে আমাদের ভ্যারিয়েবলের নামটি লাগবে, যা শুরু হয় `4` নাম্বার ইনডেক্স থেকে।

আমরা রেগুলার এক্সপ্রেশনে `pattern:\w+` এর মাধ্যমে ভ্যারিয়েবলটি খুঁজতে পারি।  প্রকৃতপক্ষে, ভ্যালিড জাভাস্ক্রিপ্ট ভ্যারিয়েবলের নাম যাচাইকরণের জন্য আমাদের আরো জটিল রেগুলার এক্সপ্রেশন লাগে, তবে এখানে আমরা ব্যাপারটি বুঝতে সাধারণ একটি প্যাটার্ন ব্যবহার করছি।

- `str.match(/\w+/)` এটি এক্সিকিউট হলে আমরা শুধু প্রথম শব্দটি পাব (`var`)। কিন্তু আমরা এটি চাই না।
- আমরা `pattern:g` ফ্ল্যাগটি ব্যবহার করতে পারি। কিন্তু `str.match(/\w+/g)` এটি টেক্সটের সকল শব্দ অনুসন্ধান করে, যেখানে আমাদের শুধু একটি শব্দ চাই যার অবস্থান শুরু `4` হতে। কিন্তু, এটি দ্বারাও আমাদের সমস্যার সমাধান হবে না।

**সুতরাং, আমরা রেগুলার এক্সপ্রেশনের মাধ্যমে কিভাবে কোন একটি নির্দিষ্ট অবস্থানে অনুসন্ধান করতে পারি?**

চলুন এই `regexp.exec(str)` মেথডটি দেখি।

`regexp` এ `pattern:g` এবং `pattern:y` ফ্ল্যাগ ছাড়া মেথডটি প্রথম মিলটি দেখায়, এটি অনেকটা `str.match(regexp)` এর মত কাজ করে।

...কিন্তু যদি `pattern:g` ফ্ল্যাগটি থাকে, তাহলে `str` এ `regexp.lastIndex` প্রপার্টির অবস্থান হতে অনুসন্ধানটি চালায়। এবং এটি যদি কোন মিল খুঁজে পায়, তাহলে মিলের পরবর্তী অবস্থানটি `regexp.lastIndex` এ সেট হয়।

অন্যকথায় বলা যায়, `regexp.lastIndex` স্ট্রিংয়ের শুরু হতে অনুসন্ধানটি চালায়, তারপর কোন একটি অবস্থান মিল পেলে `regexp.exec(str)` এর পরের অবস্থানটি `regexp.lastIndex` এ নতুন ভ্যালু হিসেবে সেট হয় আর না পেলে এটি রিসেট হয়ে যায়। তবে এই ব্যাপারটি ঘটে যদি শুধুমাত্র `pattern:g` ব্যবহার করি।

সুতরাং, `regexp.exec(str)` একটির পর আরেকটি মিল রিটার্ন করতে থাকে।

আসুন উদাহরণের মাধ্যমে আরো বিস্তারিত দেখি:

```js run
let str = 'let varName'; // স্ট্রিংয়ের প্রতিটি শব্দ খুঁজি
let regexp = /\w+/g;

alert(regexp.lastIndex); // 0 (ডিফল্ট lastIndex=0)

let word1 = regexp.exec(str);
alert(word1[0]); // let (১ম শব্দটি)
alert(regexp.lastIndex); // 3 (মিলের পর নতুন অবস্থান)

let word2 = regexp.exec(str);
alert(word2[0]); // varName (২য় শব্দটি)
alert(regexp.lastIndex); // 11 (পরবর্তী মিলের পর নতুন অবস্থান)

let word3 = regexp.exec(str);
alert(word3); // null (আর কোন শব্দ নেই)
alert(regexp.lastIndex); // 0 (অনুসন্ধান শেষে পুনরায় অবস্থান 0)
```

সকল মিলকে লুপের মাধ্যমে খুঁজতে পারি:

```js run
let str = 'let varName';
let regexp = /\w+/g;

let result;

while (result = regexp.exec(str)) {
  alert( `Found ${result[0]} at position ${result.index}` );
  // শূন্যতম অবস্থানে let, তারপর
  // varName ৪র্থ অবস্থানে
}
```

`regexp.exec` এটির ব্যবহার অনেকটা `str.matchAll` এর মত, তবে প্রসেসটিতে কিছুটা কন্ট্রোল থাকে।

চলুন আমাদের টাস্কটিতে ফিরে যায়।

আমরা `lastIndex` এর পজিশন `4` সেট করি, অই অবস্থান হতে অনুসন্ধানটি চালু হবে!

এভাবে:

```js run
let str = 'let varName = "value"';

let regexp = /\w+/g; // "g" ফ্ল্যাগছাড়া lastIndex প্রপার্টি পাব না

*!*
regexp.lastIndex = 4;
*/!*

let word = regexp.exec(str);
alert(word); // varName
```

ইয়েহহহহহহ! সমস্যাটি সমাধান করে ফেলেছি!

আমরা `pattern:\w+` এর মাধ্যমে `regexp.lastIndex = 4` সেটের মাধ্যমে অনুসন্ধানটি সম্পন্ন করেছি।

এবং আমরা সঠিক ফলাফল পেয়েছি।

...কিন্তু থামুন।

দয়া করে মনে রাখুন: `regexp.exec` অনুসন্ধান শুরু করে `lastIndex` এর অবস্থান হতে এবং পরবর্তী অবস্থানে যায়। যদি `lastIndex` এ পজিশনে কোন শব্দ না থাকে কিন্তু এরপরে থাকে তাহলে আমরা মিলটি পাব:

```js run
let str = 'let varName = "value"';

let regexp = /\w+/g;

*!*
// ৩য় অবস্থান হতে অনুসন্ধান শুরু
regexp.lastIndex = 3;
*/!*

let word = regexp.exec(str);
// ৪র্থ অবস্থানে মিল খুঁজে পায়
alert(word[0]); // varName
alert(word.index); // 4
```

কিন্ত আমাদের টাস্কে লেক্সিকাল অ্যানালাইসিসের মতে এটি ভুল। আমাদের একটি নির্দিষ্ট অবস্থানে নির্দিষ্ট একটি মিল খুঁজতে হবে, অই অবস্থানের আগে বা পরে নই। এবং এজন্যই `y` ফ্ল্যাগটি এসেছে।

**`pattern:y` ফ্ল্যাগটি নিশ্চিত করে `regexp.exec` এটি `lastIndex` এর নির্দিষ্ট অবস্থানে অনুসন্ধান চালাবে, "শুরু থেকে" নই।**

চলুন একই অনুসন্ধানটি `pattern:y` ব্যবহারের মাধ্যমে দেখি:

```js run
let str = 'let varName = "value"';

let regexp = /\w+/y;

regexp.lastIndex = 3;
alert( regexp.exec(str) ); // null (৩য় অবস্থানে একটি স্পেস, সুতরাং মিল পাবে না)

regexp.lastIndex = 4;
alert( regexp.exec(str) ); // varName (৪র্থ অবস্থানে শব্দ)
```

এখানে আমরা দেখছি, রেগুলার এক্সপ্রেশনটিতে `pattern:/\w+/y` এটি `3` অবস্থানে মিলবে না(`pattern:g` ফ্ল্যাগের মত না), কিন্তু `4` অবস্থানে মিলবে।

এটি রেহুলার এক্সপ্রেশন ইঞ্জিনের পারফরম্যান্সে গুরুত্বপূর্ন ভূমিকা রাখে `pattern:y`।

মনে করুন, আমাদের একটি বড় ট্যাক্সটে অনুসন্ধান চালাতে হবে, এবং সেখানে আমাদের কাঙ্ক্ষিত প্যাটার্নটি নাই। যদি আমরা `pattern:g` ফ্ল্যাগের মাধ্যমে চালাই, তাহলে এটি শেষ পর্যন্ত অনুসন্ধান চালাবে, এবং কোন মিল পাবে না এবং এটি অবশ্যই `pattern:y` এর তুলনায় অনেক বেশী সময় নেবে। যেখানে আমরা শুধুমাত্র `pattern:y` এর মাধ্যমে একটি নির্দিষ্ট অবস্থানে অনুসন্ধানটি করতে পারতাম।

আমাদের টাস্কের অনুরূপ, লেক্সিকাল অ্যানালাইসিসে নির্দিষ্ট অবস্থানে কি আছে তা অনুসন্ধান করতে হয়। `pattern:y` ফ্ল্যাগের মাধ্যমে আমাদের অনুসন্ধানটির পারফরম্যান্স অনেক ভালো হবে।
