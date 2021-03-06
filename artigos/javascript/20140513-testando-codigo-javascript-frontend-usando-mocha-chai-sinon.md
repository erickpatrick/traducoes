Testando seu código JavaScript Front-end usando Mocha, Chai e Sinon
===================================================================
Créditos<br/>
<small>Autor original: [Nicholas Perriault](https://nicolas.perriault.net)<br/>[Artigo Original](https://nicolas.perriault.net/code/2013/testing-frontend-javascript-code-using-mocha-chai-and-sinon/)&mdash;[Licença](http://creativecommons.org/licenses/by-sa/3.0/)</small>

**Com o crescimento da complexidade das aplicações Web ditas *ricas*, se você quiser manter sua sanidade, precisará de testes unitários para seu código JavaScript front-end.**

Pelos 4 últimos meses, eu trabalhei para [Mozilla](http://mozilla.org/) em alguns grandes projetos onde a estratégia acima era usada. Por mais que eu preferisse usar o [Casper JS](http://casperjs.org/) nessa tarefa, o Firefox não era suportado no momento e precisávamos garantir compatibilidade real com seu motor JavaScript. Então, acabamos usando [Mocha](http://visionmedia.github.io/mocha/), [Chai](http://chaijs.com/) e [Sinon](http://sinonjs.org/), e elas acabaram sendo ótimas ferramentas para nós até agora.

## O *framework* Mocha de testes e a biblioteca Chai de expectativas

[Mocha](http://visionmedia.github.com/mocha/) é um arcabouço (*framework*) de testes enquanto [Chai](http://chaijs.com) é uma bibliteca de expectativas. Em outras palavras, Mocha prepara e descreve os conjuntos de testes e a Chai provê *helpers* conveninentes para realizar todos os tipos de asserções em relação ao seu código JavaScript.

Digamos que temos um objeto vaca, `Cow`, no código em diante, que queiramos criar testes unitários:

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

Nada demais, mas nós queremos testar as unidades dela.

Tanto Mocha quanto Chai podem ser usadas em ambientes Node quanto em navegadores; no segundo caso, você terá de prepará uma página HTML para os testes e usar versões especiais dessas bibliotecas:

- para Mocha: [instruções de instalação](http://visionmedia.github.io/mocha/#browser-support), [mocha.css](https://github.com/visionmedia/mocha/raw/master/mocha.css) e [mocha.js](https://github.com/visionmedia/mocha/raw/master/mocha.js)
- para Chai: [instruções de instalação](http://chaijs.com/guide/installation/), [chai.js](http://chaijs.com/chai.js)

Aconselho salvar esses arquivos em uma subpasta chamada `vendor`. Agora, criemos o arquivo HTML para testar nossa biblioteca:

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>Testes para Cow</title>
  <link rel="stylesheet" media="all" href="vendor/mocha/css">
</head>
<body>
  <div id="mocha"><p><a href=".">Index</a></p></div>
  <div id="messages"></div>
  <div id="fixtures"></div>
  <script src="vendor/mocha.js"></script>
  <script src="vendor/chai.js"></script>
  <script src="cow.js"></script>
  <script>mocha.setup('bdd');</script>
  <script src="cow_test.js"></script>
  <script>mocha.run();</script>
</body>
</html>
```

Atente que usaremos a [API BDD de expectativas da Chai](http://chaijs.com/api/bdd/), por isso a chamada `mocha.setup('bdd');` acima.

Agora, vamos escrever um conjunto simples de teste para o construtor do nosso objeto `Cow` em `cow_test.js`:

```javascript
var expect = chai.expect;

describe("Cow", function() {
  describe("constructor", funtion() {
    it("deveria ter um nome inicial padrão", function() {
      var cow = new Cow();
      expect(cow.name).to.equal("Anon cow");
    });

    it("deveria setar o nome da vaca se passado algum nome", function() {
      var cow = new Cow("Kate");
      expect(cow.name).to.equal("Kate");
    });
  });

  describe("#greets", function() {
    it("deveria lançar algum erro se não for passado algum alvo -target-", function() {
      expect(function() {
        (new Cow())->greets();
        }).to.throw(Error);
      });

    it("deveria saudar o alvo passado", function() {
      var greetings = (new Cow("Kate")).greets("Baby");
      expect(greetings).to.equal("Kate greets Baby");
    });
  });
});
```

Os testes devem passar, logo, se você abrir o arquivo de testes no seu navegador, você deveria ver algo como isso:

![Arquivo de Testes HTML com testes passantes](https://nicolas.perriault.net/static/code/2013/cow-tests-ok.png "Arquivo de Testes HTML com testes passantes")

Se qualquer uma dessas expectativas falhar, você será notificado nos resultados dos testes. Ou seja, se alterarmos a implementação de `greets`, como abaixo...

```javascript
Cow.prototype = {
  greets: function(target) {
    if (!target)
      throw new Error("missing target");
    return this.name + " greets " + target + "!";
  }
};
```

...verá algo assim:

![Arquivo de Testes HTML com testes falhos](https://nicolas.perriault.net/static/code/2013/cow-tests-ko.png "Arquivo de Testes HTML com testes falhos")

## Que tal testar coisas assíncronas?

Agora, imagine que implementemos um método `Cow#lateGreets` que faz com que as saudações venham com um segundo de atraso:

```javascript
Cow.prototype = {
  greets: function(target) {
    if (!target)
      throw new Error("missing target");
    return this.name + " greets " + target + "!";
  },

  lateGreets: function(target, db) {
    setTimeout(function(self) {
      try {
        cb(null, self.greets(target));
      } catch (err) {
        cb(err);
      }
    }, 1000, this);
  }
};
```

Nós também precisamos testar essa daqui, e Mocha nos ajuda com sua função *callback* opcional, `done`, para esse tipo de testes:

```javascript
describe("$lateGreets", function() {
  it("deveria lançar um erro se nenhum alvo for passado", function(done) {
    (new Cow()).lateGreets(null, function(err, greetings) {
      expect(err).to.be.an.instanceof(Error);
      done();
    });
  });

  it("deveria saudar o alvo passado após um segundo", function(done) {
    (new Cow("Kate")).lateGreets("Baby", funcion(err, greetings) {
      expect(greetings).to.equal.("Kate greets Baby");
      done();
    });
  });
});
```

Convenientemente, Mocha ressaltará qualquer operação longa suspeita com *pilulas vermelhas*, no caso daquilo não ser, realmente, esperado:

![Arquivo HTML de Testes mostrando testes passantes e pílulas vermelhas indicadoras](https://nicolas.perriault.net/static/code/2013/cow-async-tests-ok.png "Arquivo HTML de Testes mostrando testes passantes e pílulas vermelhas indicadoras")

## Using Sinon para simular o ambiente
Quando você realiza testes unitários, você não quer depender de coisas externas ao código unitário sob teste. E, enquanto é boa prática evitar que suas funções tenham efeitos colaterais, no desenvolvimento para Web, isso nem sempre é uma tarefa fácil (pense na DOM, Ajax, APIs nativas dos navegadores, etc).

[Sinon](http://sinonjs.org/) é uma excelente biblioteca JavaScript para simular e imitar as dependências externas e manter controle sobre os efeitos colaterais delas.

Como um exemplo, imaginemos que nosso método `Cow#greets` não retornasse uma *string* mas a logasse, diretamente, no console do navegador:

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
        return console.error("missing target");
      console.log(this.name + " greets " + target);
    }
  };
})(this);
```

Como criar um teste unitário para isso? Bem, a Sinan vem ao nosso resgate! Primeiro, adicionemos uma chamada para o [script da Sinon](http://sinonjs.org/releases/sinon-1.7.1.js) em nosso arquivo de testes HTML:

```html
<!-- ... -->
<script src="vendor/mocha.js"></script>
<script src="vendor/chai.js"></script>
<script src="vendor/sinon-1.7.1.js"></script>
```

Nós iremos simular os métodos `log` e `error` do objeto `console` para que possamos checar se eles são chamados e o que é passado para eles:

```javascript
var expect = chai.expect;

describe("Cow", function() {
  var sandbox;

  beforeEach(function() {
    // create um ambiente 'caixa de area' (sandbox)
    sandbox = sinon.sandbox.create();

    // simular alguns métodos do objeto console
    sandbox.stub(window.console, "log");
    sandbox.stub(window.console, "error").
  });

  afterEach(function() {
    // restauro o ambiente para o estado inicial
    sandbox.restore();
  });

  // ...

  describe("#greets", function() {
    it("deveria logar um erro no console, se nenhum alvo for passado", function() {
      (new Cow()).greets();

      sinon.assert.notCalled(console.log);
      sinon.assert.calledOnce(console.error);
      sinon.assert.calledWithExactly(console.error, "missing target");
      });

    it("deveria logar a saudação no console", function() {
      var greetings = (new Cow("Kate")).greets("Baby");

      sinon.assert.notCalled(console.error);
      sinon.assert.calledOnce(console.log);
      sinon.assert.calledWithExactly(console.log, "Kate greets Baby");
      });
  });
});
```

Há diversos pontos a se levantar, aqui:
- `beforeEach` e `afterEach` são parte da API Mocha e permitem definir operações de preparo e finalização para cada teste;
- Sinon provê uma *caixa de areia* (sandboxing), basicamente, permitindo definir e anexar um conjunto de objetos simulados que você será capaz de restaura em um algum momento;
- Quando simulados, as funções verdadeiras **não são chamadas**, logo, obviamente, nada será impresso no console do navegador;
- Sinon vem com sua própria biblioteca de asserções, por isso, a chamada à `sinon.assert`; existe um plugin [sinon-chai](https://github.com/domenic/sinon-chai) para Chai, o qual você pode querer dar uma olhada.

**Há inúmeros outros aspectos bacanas de [Mocha](http://visionmedia.github.io/mocha/), [Chai](http://chaijs.com/) e [Sinon](http://sinonjs.org/), que não daria para cobrir nesse post, mas espero ter aberto seu apetite investigativo para ir pesquisar mais sobre eles. Bons testes!**
