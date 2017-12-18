---
mathjax: true
title: Adding math to Jekyll themed (GitHub) website
---
In order to use MathJax code in your Jekyll website you can add the following code to `_includes/scripts.html`:
<script src="https://gist.github.com/ArnoutDevos/51796733969a3412bfdcb8d079b71bfd.js"></script>

All the (math) text that is between `$$` tags will be displayed as a formula on a separate line, and the `@@` tag is used for inline math.

Then, in each post where you want to use math you need to add `mathjax: true` to the YAML header. This lets us use inline math such as @@\int \!\! \int f(x,y)\,\mathrm{d}x\mathrm{d}y@@, and math on a separate line: $${n+1\choose k} = {n\choose k} + {n \choose k-1}$$.

Note that contrary to regular latex practice, you want to avoid using a single `$` tag to result in inline math. The reason for this is that you might want to use the dollar sign `$` for prices without the text in between being parsed into math. For example the sentence `Each apple costs $1 and a pear $2.` would be parsed as: Each apple costs @@1 and a pear @@2.