# এক্সপ্রেশনকে পার্স

একটি গাণিতিক সমীকরণে দুটি নাম্বার এবং তাদের মাঝে একটি গাণিতিক চিহ্ন আছে, যেমন:

- `1 + 2`
- `1.2 * 3.4`
- `-3 / -6`
- `-2 - 2`

গাণিতিক চিহ্নগুলো হল: `"+"`, `"-"`, `"*"` অথবা `"/"`।

এখানে সমীকরণের শুরুতে,মাঝে এবং শেষে অতিরিক্ত স্পেস থাকতে পারে।

একটি ফাংশন লিখুন যা `parse(expr)` একটি সমীকরণ নিবে এবং তাদের কে একটি অ্যারের ৩ টি উপাদান হিসেবে রিটার্ন করবে:

১. প্রথম সংখ্যাটি।
২. গাণিতিক চিহ্নটি।
৩. দ্বিতীয় সংখ্যাটি।

যেমন:

```js
let [a, op, b] = parse("1.2 * 3.4");

alert(a); // 1.2
alert(op); // *
alert(b); // 3.4
```