Tenha um Código JavaScript Front-end Garantido
==============================================
Créditos
<small>Autor original: [Nicholas Perriault](https://nicolas.perriault.net)<br/>[Artigo Original](https://nicolas.perriault.net/code/2013/get-your-frontend-javascript-code-covered/)</small>

**Você, finalmente, está [testando o seu código JavaScript Front-end, hein](https://nicolas.perriault.net/code/2013/testing-frontend-javascript-code-using-mocha-chai-and-sinon/)? Ótimo! Quanto mais testes você escrever, mais confiante você estará em seu código... mas, quanto precisamente? É nessa questão que a [cobertura de código](http://en.wikipedia.org/wiki/Code_coverage)[1] pode ajudar.**

A ideia por trás da cobertura de código é gravar quais partes do seu código (funções, atribuições, condições, entre outras) foram executadas pelo seu conjunto de testes, para obter métricas desses dados e, geralmente, prover ferramentas de navegação e inspeção dessas métricas.

Poucos desenvolvedores front-end testam seus códigos e, menos ainda, são os que configuraram seus ambientes de trabalho para avaliar a cobertura de código. Isso se dá, praticamente, pelo fato de não ter muitas ferramentas, nessa área, voltada para códigos front-end.

Para falar a verdade, consegui achar apenas uma que provê um adaptador para a [Mocha](http://visionmedia.github.io/mocha/) e que funciona de verdade.

<blockquote class="twitter-tweet" lang="en"><p>Drinking game for web devs: &#10;(1) Think of a noun&#10;(2) Google &quot;&lt;noun&gt;.js&quot;&#10;(3) If a library with that name exists - drink</p>&mdash; Shay Friedman (@ironshay) <a href="https://twitter.com/ironshay/statuses/370525864523743232">August 22, 2013</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

[Blanket.js](http://blanketjs.org/) é script de cobertura de código que é fácil de instalar, configurar e usar, que funciona tanto no navegador quanto com Node.js

Seu uso é muito simples. Adicionar suporte ao Blanket ao seu conjunto de testes Mocha é só questão de adicionar uma linha de HTML para o arquivo de testes:

```html
<script src="vendor/blanket.js" data-cover-adapter="vendor/mocha-blanket.js"></script>
```



[1] Não há versão em português do artigo em questão.