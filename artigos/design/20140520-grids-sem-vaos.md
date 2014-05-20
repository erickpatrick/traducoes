Grades sem vãos
==============================================
Créditos<br/>
<small>Autor original: [Ilya Birman](http://ilyabirman.net/)<br/>[Artigo Original](http://ilyabirman.net/meanwhile/all/grids-with-no-gutters/)<br/>Nível: Iniciante</small>

O jeito mais simples de fazer grades (em inglês, *grid*) para seus layouts é levar em consideração os vãos (em inglês, *gutters*), parecido com isso:

![Grade com margem e vão](http://ilyabirman.net/meanwhile/pictures/grid-with-gutters.png "Grade com margem e vão")

Esse layout tem 760px de largura com seis colunas de 100px, cinco vãos de 20px e duas margens de 30px.

Agora, pense rápido: qual será a largura da coluna se nós aumentarmos os vãos para 25 pixels?

Essa é uma maneira muito complica e imprática de lidar com grades. Dadas a largura total e o número de colunas, é complicado calcular a largura da coluna. A largura de *n* colunas não é igual a *n x largura de 1 coluna*. Criar ou modificar uma grade como essa é um puta trabalho por si só. Isso é muito ruim.

Uma maneira melhor de lidar é removendo os vãos:

![Grade com margens e sem vãos](http://ilyabirman.net/meanwhile/pictures/grid-with-no-gutters.png "Grade com margens e sem vãos")

O layout continua sendo de 760px de largura com seis guias de 120px de intervalo e uma margem esquerda de 30px.

Mas, espere. Como você faz para garantir que o texto de duas colunas adjacentes não sucumba? Fácil: você adiciona um preenchimento (em inglês, *padding* - na imagem abaixo, em vermelho claro) aos recipientes:

![Grade com margem e preenchimento, e sem vãos](http://ilyabirman.net/meanwhile/pictures/grid-with-no-gutters-example.png "Grade com margem e preenchimento, e sem vãos")

Essa abordagem dá a liberade de ajustar o preenchimento em relação ao tamanho da fonte. Se o texto principal é duas vezes maior, o espaçamento padrão de 20px não será suficiente, então, você pode usar preenchimentos maiores para isso (e somente para isso) nos recipientes:

![Preenchimentos maior, baseado no tamanho do texto](http://ilyabirman.net/meanwhile/pictures/grid-with-no-gutters-example-2.png "Preenchimentos maior, baseado no tamanho do texto")

Alternativamente, e preferivelmente, você pode especificar o preenchimento em unidades relativas (por exemplo: 1.5**em**, 1.5**rem**, etc).

Aqui está outro exemplo, dessa vez, sem qualquer guia visível:

![Exemplo de grade com margem e preenchimento, sem vãos e sem guias visíveis](http://ilyabirman.net/meanwhile/pictures/grid-with-no-gutters-example-3.png "Exemplo de grade com margem e preenchimento, sem vãos e sem guias visíveis")

Na web, quase que exclusivamente, usa-se alinhamento à esquerda, então, perceber a grade como um conjunto de *guias* ao invés de um conjunto de *colunas* faz mais sentido. A única *grade* é a grade de linhas invisíveis as quais o texto é alinhado. Então, não se preocupe com vãos, eles não merecem seu tempo.