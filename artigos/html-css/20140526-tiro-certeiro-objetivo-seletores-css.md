Tiro Certeiro; Objetivo dos Seletores CSS
=========================================
Créditos<br/>
<small>Autor original: [Harry Roberts](http://csswizardry.com/)<br/>[Artigo Original](http://csswizardry.com/2012/07/shoot-to-kill-css-selector-intent/)<br/>Nível: Iniciante-Intermediário</small>

Um particular modo de codificar CSS, que me faz ter calafrios toda vez que vejo é aquele com *seletores de objetivos fracos*. Ter seletores fracos é o mesmo que ter um carpete repleto de bombas. Esses seletores fracos tem um alcance muito abrangente. Seletores como `.header ul {}` ao invés de `.main-nav {}`; `.widget h2 {}` ao invés de `.widget-title`; `article > p>first-child {}` ao invés de `.intro {}`. Seletores os quais o objetivos não são especificos o suficiente.

> É interessante notar que *objetivo de seletor* é algo que eu criei; se você tem um nome melhor para isso, por favor, me avise!

Vamos verificar melhor o exemplo `.header ul {}`. Imaginemos que `ul` é, realmente, a principal navegação do nosso site. Ela fica no cabeçalho, como esperado, e é a única `ul` por lá. Então, `.header ul` é uma boa escolha, certo? Não verdade, *não*. Claro, ele pode funcionar, mas não é muito bom. Ele não está muito preparado para o futuro e, certamente, não é específico o suficiente. Assim que precisarmos adicionar outro `ul` ao cabeçalho, ele terá o mesmo estilo que a nossa navegação principal, e as chances é que não queremos que isso aconteça. Isso significa que teremos de refatorar um bocado de código *ou* desfazer vários estilos nas próximas `ul`s de dentro do `.header`, para remover os efeitos desse seletor abrangente.

O objetivo do seu seletor deve combinar com o motivo de você estilizar algo; pergunte-se **estou selecionando isso porque é uma `ul` dentro de um `.header` ou porque é a navegação principal do meu site?**. A resposta para essa pergunta determinará o seu seletor.

## Tudo depende do seletor chave...

O que determina o impacto do seletor é o *seletor chave*. O seletor chave é algo muito importante no mundo do CSS, uma vez que os navegadores leem as seletores da *direita para a esquerda*. Isso significa que o seletor chave é o aquele que vem logo antes das chaves de abertura (`{`). Por exemplo:

```css
.header il { /*-ul- é o seletor chave*/ }
.ul li a { /*-a- é o seletor chave*/ }
.p:last-child { /*-last-child- é o seletor chave*/ }
```

No artigo (Escrevendo seletores CSS eficientes)[http://csswizardry.com/2011/09/writing-efficient-css-selectors/], afirmo que o seletor chave tem uma grande importância na eficiência do CSS, então, é importante ter isso em mente, mas em relação ao *objetivo do seletor*, você precisa, basicamente, saber o quão abrangente o seu seletor é. `html > body > section.content > article span {}` é um seletor ridiculamente grande e terrível, que niguém, em lugar algum, deveria escrevê-lo (certo?), mas, apesar da exagerada e mosntruosa especificidade dele, o seletor chave (`span`), ainda é muito, muito abrangente. Não importamente muito o que vem antes do seu seletor chave, é ele, o seletor chave, quem interessa.

Como *regra geral*, você deveria evitar qualquer seletor chave que é um seletor de tipo (basicamente, qualquer elemento, como `ul` ou `span` ou algo parecido) ou um objeto base (por exemplo, `.nav` ou `.media`). Só porque só existe um objeto `.media` no seu conteúdo, não quer dizer que isso sempre será assim.

Vamos olhar o exemplo `.header ul`. Assumamos que nosso código html é assim, uma vez que estamos usando a [abstração de navegação de um post anterior](http://csswizardry.com/2011/09/the-nav-abstraction/):

```html
<div class="header">
	<ul class="nav">
		[links]
	</ul>
</div>
```

Nós poderiamo selecioná-lo de várias maneiras:

```css
.header ul {
	[regras dos estilos da navegação principal]
}
```

Isso é tão errado e ruim porque, assim que precisar adicionar qualquer outro `ul` em nosso cabeçalho, ele terá o mesmo visual da nossa navegação principal. Isso é perigoso, mas, felizmente, é facilmente evitável.

Nós poderiamos fazer assim:

```css
.header .nav {
	[regras dos estilos da navegação principal]
}
```

Isso é, ligeiramente, melhor que o `.header ul`, mas, por pouco. Agora, nós podemos adicionar, seguramente, outro `ul`, mas não podemos adicionar nenhuma outra coisa com a classe `.nav`; isso significa que adicionar sub-navegações ou migalhas de pão (*breadcumbs*) será um pesadelo.

Finalmente, nossa melhor solução seria adicionar um segundo seletor de classe à `ul`; uma classe chamada `.main-nav`:

```html
<div class="header">
	<ul class="nav main-nav">
		[links]
	</ul>
</div>
```

```css
.main-nav {
	[regras dos estilos da navegação principal]
}
```

Esse é um bom seletor com um bom objetivo. Nós estamos selecionando esse elemento pelo *verdadeiro* motivo, não por motivos circunstâncias/coincidentes. Agora, podemos adicionar quantos `ul`s e `.nav`s ao `.header` e o escopo de nossa navegação principal nunca alcançará outros elementos. Não estamos masi pisando em um carpete cheio de bombas!

Mantenha seus seletores tão explícitos e específicos quanto puder, de preferência que ele seja uma classe a qualquer outra coisa. Aplicar estilos específicos a um seletor vago é muito perigoso. Um seletor genérico sempre deveria carregar estilos genéricos e se você quer mirar algo em particular, você deveria criar uma classe. **Objetivos específicos requerem seletores específicos**.

## Exemplo da vida real
Um exemplo muito bom de quando eu me embananei com relação a isso, foi em um projeto que fiz para a Sky. Eu tinha um seletor que era, simplesmente `#content table {}` (ARGH! até usei um ID!!!). Esse é um seletor muito problemático por três razões: primeiro, ele [usa um id e não deveria fazê-lo](http://csswizardry.com/2011/09/when-using-ids-can-be-a-pain-in-the-class/); segundo, ele tem uma especificidade muito maior do que precisa ter; e, terceiro&mdash;e mais importante&mdash;ele tem um objetivo de seletor muito ruim. Eu não estava estilizando essas tabelas por elas estavam dentro de  `#content`, isso aconteceu porque foi como a DOM veio e acabei escolhendo essa maneira de mirá-los. *Erro todo meu*, nessa.

Nas primeiras semanas, estava tudo ótimo, mas, de repente, precisamos adicionar outras tabelas dentro de `#content` que não eram para parecer, nem um pouco, com as anteriores. Oh, não! Meu seletor antigo era muito abrangente, agora, tinha de desfazer os estilos de todas as tabelas dentro de `#content div`. Se tivesse criado um seletor com melhor objetivo. Ao invés disso:

```css
#content table {
	[estilos da tabela foo]
}

#content .bar {
	[desfazer os estilos foo]
	[criar estilos da tabela .bar]
}
```

Eu deveria ter feito isso:

```css
.foo {
	[estilos foo]
}
.bar {
	[estilos bar]
}
```

E teria muito menos dores de cabeça. Pensando no futuro e tendo um seletor com muito mais objetivo, eu não teria tido tantos problemas...

## Exceções
Claro, sempre há *exceções*. É prefeitamente aceitável ter seletores como `.main-nav > li`, onde seu seletor chave é um seletor de tipo. Faz todo o sentido selecionar tdoso os `a` dentro de algo, mais ou menos assim:

```css
html {
	color:#333;
	background-color:#fff;
}

/*Esquema de cores invertidos para itens promocionais*/
.promo {
	color:#fff;
	background-color:#333;
}
	.promo a {
		color:#fff;
		text-decoration:underline;
	}
```

Essa é uma maneira considerável de utilizar um seletor abrangente para estilizar todos os `a`s, apesar de parecer um carpete cheio de bombas.

## Palavras finais
Em geral, ao invés de criar um carpete de bombas com seus elementos e seletores, seja certeiro; seja específico e explícito. Garanta que o objetivo do seletor é preciso e direcionado.

Pense bem sobre o porque você querer direcionar algo e escolha o seletor mais específico possível. Refine o objetivo do seu seletor. Você quis isso:

```css
.header em {}
```

ou isso?

```css
.tagline {}
```

Você quer isso:

```css
.footer p {}
```

ou você, realmente, quer isso?

```css
.copyright {}
```

É inteligente fazer isso:
```css
.sidebar form {}
```

ao invés disso?
```css
.search-form {}
```

Considere o objetivo dos seus seletores CSS. Você está sendo específico o suficiente? Os seus seletores estão combinando, perfeitamente, com as coisas que você quer pelas razões certas? Ou tudo está saindo sem querer? Seja certeiro. Seja um *sniper* do CSS, não um cara que coloca bombas, correndo perigo.

A propósito, optar por mudar seletores longos como `.header ul` por algo como `.main-nav` também ajudará a reduzir a especificidade e aumentará a eficiência dos seletores: vitória-vitória-vitória!

É importante lembrar que o [Jonathan Snook] escreveu algo parecido, chamado [*profundidade de aplicação*](http://smacss.com/book/applicability)...