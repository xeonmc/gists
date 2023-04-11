
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
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/mathquill/0.10.1/mathquill.css" integrity="sha512-vPg9GqsZZ4LHv9BkFfZSXt7y4D7YaARPU2JFmpZug4EgtJJrumytMAFZkNSk2LSyqWir0TNbh2tBq7UJIMxvlA==" crossorigin="anonymous" referrerpolicy="no-referrer" />
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathquill/0.10.1/mathquill.js" integrity="sha512-7jEhcM7FbjGHo1ejs1iw1J8FxcnACx7Z3lG29gQ5vTBe2U/gaQpzwjzPCyg32zTwXCloQDdorpLufmu0nBIqnQ==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
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
