<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.3/dist/katex.min.css" integrity="sha384-Juol1FqnotbkyZUT5Z7gUPjQ9gzlwCENvUZTpQBAPxtusdwFLRy382PSDx5UUJ4/" crossorigin="anonymous">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.3/dist/katex.min.js" integrity="sha384-97gW6UIJxnlKemYavrqDHSX3SiygeOwIZhwyOKRfSaf0JWKRVj9hLASHgFTzT+0O" crossorigin="anonymous"></script>
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.3/dist/contrib/auto-render.min.js" integrity="sha384-+VBxd3r6XgURycqtZ117nYw44OOcIax56Z4dCRWbxyPt0Koah1uHoK0o4+/RRE05" crossorigin="anonymous"></script>
<script>
    document.addEventListener("DOMContentLoaded", function() {
        renderMathInElement(document.body, {
          delimiters: [
            {left: "````math", right: "````", display: true},
            {left: "$$", right: "$$", display: true},
            {left: "$", right: "$", display: false}
          ]
        });
    });
</script>

Inline $a^2 + b^2 = c^2$

Fence block

````math
\begin{aligned}
f(t)      &= \int_{-\infty}^{\infty} F(\omega) e^{ 2 \pi i \omega t} d\omega \\
F(\omega) &= \int_{-\infty}^{\infty} f(t)      e^{-2 \pi i \omega t} dt
\end{aligned}
````

Double Dollar

$$
\begin{aligned}
f(t)      &= \int_{-\infty}^{\infty} F(\omega) e^{ 2 \pi i \omega t} d\omega \\
F(\omega) &= \int_{-\infty}^{\infty} f(t)      e^{-2 \pi i \omega t} dt
\end{aligned}
$$
