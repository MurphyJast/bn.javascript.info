উত্তরটি হল: **1 and 3**।

উভয়ই কমান্ড ফলাফল হিসেবে `elem` এ `text` সংযোগ করে।

এখানে একটি উদাহরণ দেখুন:

```html run height=80
<div id="elem1"></div>
<div id="elem2"></div>
<div id="elem3"></div>
<script>
  let text = '<b>text</b>';

  elem1.append(document.createTextNode(text));
  elem2.innerHTML = text;
  elem3.textContent = text;
</script>
```
