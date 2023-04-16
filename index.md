
[testlink1 (fourier)]({{ site.baseurl }}/2022/12/01/my-cool-title)

[testlink2 (recoil)]({{ site.baseurl }}/2022/11/30/recoil)

[testlink3 (loremipsum)]({{ site.baseurl }}/2022/12/01/testpage2/hello)

```mermaid
pie
"Dogs" : 386
"Cats" : 85.9
"Rats" : 15
```


<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.0/jquery.min.js"></script>
<link rel="stylesheet" href="https://xeonmc.github.io/cdn/mathquill/0.10.1/mathquill.css" crossorigin="anonymous" referrerpolicy="no-referrer" />
<script src="https://xeonmc.github.io/cdn/mathquill/0.10.1/mathquill.js" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
<script>
var MQ = MathQuill.getInterface(2);
</script>
<p>Type math here: <span id="math-field"></span></p>
<p>LaTeX of what you typed: <span id="latex"></span></p>
<script>
var mathFieldSpan = document.getElementById('math-field');
var latexSpan = document.getElementById('latex');
var MQ = MathQuill.getInterface(2); // for backcompat
var mathField = MQ.MathField(mathFieldSpan, {
  spaceBehavesLikeTab: true, // configurable
  handlers: {
    edit: function() { // useful event handlers
      latexSpan.textContent = mathField.latex(); // simple API
    }
  }
});
</script>


---

AsciiMath test:


<script src="https://cdn.jsdelivr.net/gh/asciimath/asciimathml@master/ASCIIMathML.js">E=mc^2</script>
