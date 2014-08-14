News360-SDK
===========

```js
<script>

window.nws360Init = function () {
News360.initPageView();
};

(function (d, e) {
var s = d.createElement(e);
s.src = 'http://news360.com/jspromosdk.js'; 
var fs = d.getElementsByTagName(e)[0];
fs.parentNode.insertBefore(s, fs);
})(document, 'script');

</script>

<script>
window.nws360Init = function () {
News360.push({
url: 'http://example.com/',
containerQuery: '.my-news-container'
});
};

(function (d, e) {
var s = d.createElement(e);
s.src = 'http://news360.com/jspromosdk.js'; 
var fs = d.getElementsByTagName(e)[0];
fs.parentNode.insertBefore(s, fs);
})(document, 'script');

</script>


<script>
window.nws360Init = function () {
News360.push({
url: 'http://example.com/',
containerQuery: '.my-news-container'
});
};
</script>


<script>
(function (d, e) {
var s = d.createElement(e);
s.src = 'http://news360.com/jspromosdk.js'; 
var fs = d.getElementsByTagName(e)[0];
fs.parentNode.insertBefore(s, fs);
})(document, 'script');
</script>
```
