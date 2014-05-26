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
