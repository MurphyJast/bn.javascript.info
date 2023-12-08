# Scrolling

`scroll` ইভেন্ট পেজে স্ক্রলিংয়ের সময় সংগঠিত হয়। এটির সাহায্যে আমরা অনেক কিছু করতে পারি।

উদাহরণস্বরূপ:
- ইউজারের বিভিন্ন তথ্য দেখাতে বা লুকাতে ব্যবহার করতে পারি।
- আমরা অন ডিমান্ড অর্থাৎ স্ক্রলিংয়ের উপর ভিত্তি করে ডাটা লোড করতে পারি।

নিচের হ্যান্ডেলারটি বর্তমান স্ক্রলের মানটি দেখাবে:

```js autorun
window.addEventListener('scroll', function() {
  document.getElementById('showScroll').innerHTML = window.pageYOffset + 'px';
});
```

```online
এখানে দেখুন:

Current scroll = <b id="showScroll">scroll the window</b>
```

 `window` বা স্ক্রলেবল এলিমেন্ট উভয়ের সাহায্যে আমরা `scroll` ইভেন্ট অ্যাক্সেস করতে পারি।

## Prevent scrolling

কিভাবে আমরা কোন এলিমেন্টের স্ক্রল থামাতে পারি?

`onscroll` লিসেনারে `event.preventDefault()` কল করার মাধ্যমে আমরা স্ক্রলকে prevent করতে পারব না, কেননা `scroll` ইভেন্টটি ট্রিগার হয় কোন এলিমেন্ট স্ক্রল হওয়ার পর।

কিন্ত আমরা ভিন্ন উপায়ে স্ক্রলকে prevent করতে পারি, যেসব ইভেন্ট এর জন্য স্ক্রল সংগঠিত হয় যদি তাদের কে `event.preventDefault()` করা হয় তাহলে স্ক্রল ইভেন্টটি সংগঠিত হবে না, যেমন `keydown`, `key:pageUp` বা `key:pageDown`।

যদি আমরা এইসব ইভেন্টে `event.preventDefault()` করি, তাহলে `scroll` ইভেন্টটি ট্রিগার হয় না।

আমরা বিভিন্নভাবে স্ক্রল ইনিশিয়ালাইজ করতে পারি, তবে সবচেয়ে বেশি সুবিধাজনক হল CSS এর `overflow` প্রপার্টি দ্বারা ডিফাইন করা।

এখানে কিছু টাস্ক দেয়া হল যার মাধ্যমে আপনি `onscroll` এর ব্যবহার আরো ভালো করে বুঝতে পারবেন।