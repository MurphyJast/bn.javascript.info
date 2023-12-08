# এইচটিএমএল কালারের জন্য রেগুলার এক্সপ্রেশন

হেক্সাডেসিমেল এইচটিএমএল কালার `#ABCDEF`: প্রথমে `#` তারপর ৬ টি হেক্সাডেসিমেল ক্যারাক্টার খোঁজার জন্য একটি রেগুলার এক্সপ্রেশন লিখুন।

ব্যবহারযোগ্য একটি উদাহরণ:

```js
let regexp = /...আপনার রেগুলার এক্সপ্রেশনটি লিখুন.../

let str = "color:#121212; background-color:#AA00ef bad-colors:f#fddee #fd2 #12345678";

alert( str.match(regexp) )  // #121212,#AA00ef
```

পুনশ্চ এই টাস্কে আমাদের `#123` বা `rgb(1,2,3)` ইত্যাদি কালার ফরমেটগুলোর প্রয়োজন নেই।