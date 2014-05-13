Tenha um Código JavaScript Front-end Garantido
==============================================
Créditos
<small>Autor original: [Nicholas Perriault](https://nicolas.perriault.net)<br/>[Artigo Original](https://nicolas.perriault.net/code/2013/get-your-frontend-javascript-code-covered/)&mdash;[Licença](http://creativecommons.org/licenses/by-sa/3.0/)</small>

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

Arquivos fonte: [blanket.js](https://raw.github.com/alex-seville/blanket/master/dist/qunit/blanket.min.js), [mocha-blanket.js](https://raw.github.com/alex-seville/blanket/master/src/adapters/mocha-blanket.js)

Como exemplo, vamos usar o simples código da "vaca" (`Cow`, no código abaixo)[2]:

```javascript
// cow.js
(function(exports) {
  "use strict";

  function Cow(name) {
    this.name = name || "Anon cow";
  }
  exports.Cow = Cow;

  Cow.prototype = {
    greets: function(target) {
      if (!target)
        throw new Error("missing target");
      return this.name + " greets " + target;
    }
  };
})(this);
```

E o conjuto de testes, usando Mocha e [Chai](http://chaijs.com/)

```javascript
var expect = chai.expect;

describe("Cow", function() {
  describe("constructor", function() {
    it("should have a default name", function() {
      var cow = new Cow();
      expect(cow.name).to.equal("Anon cow");
    });

    it("should set cow's name if provided", function() {
      var cow = new Cow("Kate");
      expect(cow.name).to.equal("Kate");
    });
  });

  describe("#greets", function() {
    it("should greet passed target", function() {
      var greetings = (new Cow("Kate")).greets("Baby");
      expect(greetings).to.equal("Kate greets Baby");
    });
  });
});
```

Vamos criar um arquivo HTML de teste para ele, estrelando Blanket e seu adaptador para Mocha:

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>Test</title>
		<link rel="stylesheet" media="all" href="vendor/mocha.css">
	</head>
	<body>
		<div id="mocha"></div>
		<div id="messages"></div>
		<div id="fixtures"></div>
		<script src="vendor/mocha.js"></script>
		<script src="vendor/chai.js"></script>
		<script src="vendor/blanket.js"
		      data-cover-adapter="vendor/mocha-blanket.js"></script>
		<script>mocha.setup('bdd');</script>
		<script src="cow.js" data-cover></script>
		<script src="cow_test.js"></script>
		<script>mocha.run();</script>
	</body>
</html>
```

**Observações:**
* Note que o atributo `data-cover` que nós adicionamos à tag `script` que carrega o fonte de nossa biblioteca;
* O arquivo HTML de teste *deve* ser visualizado por HTTP para que o adaptador funcione.

Se rodarmos os testes, teremos algo mais ou menos assim:

![Imagem com resultado da execução dos testes](https://nicolas.perriault.net/static/code/2013/blanket-coverage.png "Imagem com resultado da execução dos testes")

Como você pode ver, o relatório da parte debaixo destaca que nós não chegamos a testar o caso onde uma exceção (`Error`) é lançada , no caso do nome alvo não existir. Nós fomos informados disso, nada mais, nada menos. Nós sabemos que está faltando algo aqui. Isso não é legal? Pelo menos eu acho!

Lembre que a cobertura de código [apenas trará números](http://codebetter.com/karlseguin/2008/12/09/code-coverage-use-it-wisely/) e dados crus. Ela não trará provas cabais que toda a *lógica do seu código* está coberta. Se você me perguntar, as melhores observações que você pode obter sobre a lógica do seu código e a implementação do mesmo são aquelas advindas das sessões de [programação em par](http://www.extremeprogramming.org/rules/pair.html) e das [revisões de código](http://alexgaynor.net/2013/sep/26/effective-code-review/) &mdash; mas isso já é outra história.

**Então, cobertura de código é a salvadora da pátria? Não. Ela é útil? Definitivamente. Bons testes**

-----

[1] Não há versão em português do artigo em questão.<br/>
[2] Esse exemplo foi usado em um artigo anterior pelo autor original. [Link para a tradução](https://github.com/erickpatrick/traducoes/blob/master/artigos/javascript/20140513-testando-codigo-javascript-frontend-usando-mocha-chai-sinon.md)