---
layout: page
---
<div class="nba">
  <!-- <h4>{{page.title}}</h4> -->
  <!-- <h4>{{page.title}}</h4> -->
  {{page.content}}

<!--   <div class="blog-post spacing">
    {{ page.content }}
  </div> -->
</div>

<!-- inline config -->
<script>
  MathJax = {
    tex: {
      inlineMath: [['$', '$'], ['\\(', '\\)']],
      macros: {
      	RR: "{\\bf R}",
      	bold: ["{\\bf #1}", 1],
        indep: "{\\perp \\!\\!\\! \\perp}",
    	}
    },
    svg: {
    fontCache: 'global'
  	},
  };
</script>

<!-- load MathJax -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>