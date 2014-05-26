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

