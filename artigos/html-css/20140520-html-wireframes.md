Wireframes em HTML
==============================================
Créditos<br/>
<small>Autor original: [Brad Frost](http://bradfrostweb.com/)<br/>[Artigo Original](http://bradfrostweb.com/blog/post/html-wireframes/)<br/>Nível: Iniciante</small>

Durantes os primeiros anos da minha carreira, eu não sabia que wireframes existiam. E isso durou até eu começar a trabalhar em uma grande agência, quando descobri o mundo preto-e-branco do InDesign, onde inúmeras versões de PDFs com mais de 100 páginas eram criados para apresentar os trabalhos aos clientes.

Embora seja colocado muito esforço nesses wireframes de alta fidelidade, o artefato real cria inúmeros problemas.

## Problemas com Wireframes Estáticos de Alta Fidelidade
> Quando alguém te entrega um panfleto, é o mesmo que eles dizerem "Pegue isso e jogo foda" &mdash; *Mitch Hedberg*

- **São abstrações** &ndash; Wireframes de alta fidelidade são gigantescas abstrações irreais. Elas tem largura fixa, [não são capazes de demonstrar interatividade](http://www.cennydd.co.uk/2012/why-i-dont-wireframe-much) e ainda matam árvores. Gerar centenas de composições no Photoshop [não faz mais muito sentido](http://bradfrostweb.com/blog/post/the-post-psd-era/), e eu diria que, passar tanto tempo gerando um monte de wireframes fixos e não interativos, também não faz muito sentido.
- **São cheios de suposições** &ndash; Esse tipo de wireframe [dita a regra do layout](http://www.eleganthack.com/words-on-wireframes/), da estética, da ordem do código e tudo mais. Na minha experiência, o pessoal que cria esses wireframes não tem background/estudos voltados para front-end ou design visual, o que acaba fazendo com que esses documentos [limitem](http://www.eleganthack.com/whats-wrong-with-wireframes/) as possibilidades de design e técnicas.
- **São verbosos** &ndash; Nem sempre é verdade, porém, trabalhei em vários projetos onde os wireframes de alta fidelidade eram, desnecessariamente, muito detalhados e tentavam explicar coisas que, na verdade, seriam melhor entendidas se fossem demonstradas. As anotações pareciam capítulos de livros. "Sou o Ismael. Quando o usuário passar o mouse sobre o dropdown, ele mostrará..."
- **São muletas** &ndash; Escrever anotações que masi parecem capítulos de livro é uma péssima alternativa com relação a comunicar e colaborar, de verdade, com os clientes e colegas de trabalho. Não há matéria que tenha todas as respostas. É cada vez mais importante encorajar conversas e colaborações de verdade, ao invés de ter um documento gigantesco tentando explicar aquilo que você pensa.

## Sempre Comece por Baixo
> Ideas quererm ser feias &mdash; *Jason Santa Maria*

Enquanto os wireframes de alta fidelidade tem vários tipos de problemas, no fim das contas, eles tentam responder as seguintes perguntas: "O que deve aparecer nessa tela e em qual ordem?"

Essas são perguntas mais que pertinentes e, felizmente, não precisamos chegar ao nível de alta fidelidade para conseguir algumas respostas. Esboçar é uma maneira extremamente eficaz de conseguir ideias rapidamente e dá a você, seus clientes e colegas de trabalho, a oportunidade de conversar sem, necessariamente, ir muito a fundo nos detalhes.

O Jason Santa Maria tem falado, já há algum tempo, sobre o [método de wireframe da caixa cinza](http://v3.jasonsantamaria.com/archive/2004/05/24/grey_box_method.php. A ideia é, simplesmente, "simular" o conteúdo na forma de caixas cinza para definir o conteúdo e sua hierarquia geral. Para o redesign público do *Greater Pittsburgh Community Food Bank*, temos usado essa técnica com eficácia considerável:

[![Wireframe Caixa Cinza do Redesign da Greater Pittsburgh Community Food Banck](http://bradfrostweb.com/wp-content/uploads/2014/05/wireframes-food-bank-1.png "Wireframe Caixa Cinza do Redesign da Greater Pittsburgh Community Food Banck")](http://foodbank.bradfrostweb.com/patternlab/v1/?p=templates-program-detail)

Isso nos permite perguntar ao cliente "Ei, tudo o que você quer falar, está presente na página? As coisas estão aparecendo na ordem certa, de acordo com a importância delas?". E, enquanto o conteúdo evoluíra, naturalmente, com o tempo, não precisamos perder tanto tempo nesses wireframes para estabelecer uma boa direção.

## Wireframes em HTML
É uma boa ideia criar wireframes em HTML, direto no navegador, e fico feliz que caras inteligentes como o [Matt Griffin, concordam](http://alistapart.com/column/start-coding-with-wireframes). Aqui estão alguns dos vários benefícios em criar wireframes em HTML:
- **Chegam aos navegadores bem rápido** &ndash; De cara, criar wireframes em HTML permite que vocês, seus colegas e clientes interajam com a interface no ambiente final dela. Você pode abrir os wireframes em diversos aparelhos, o que lhe dá um noção maior de como o design está indo;
- **Reforçam a ideia da construção de um site** &ndash; Quando PDFs, JPEGs e outros artefatos estáticos são mostrados por aí, há a tendência de esquecer o meio para o qual você está projetando as coisas. Os clientes acabam imprimindo esses artefatos e escrevendo nas margens, como fossem brochuras. Wireframes em HTML reinforçam o fato que você está criando algo que estará vivo na Web;
- **São interativos** &ndash; Clientes e colegas podem verificar o fluxo através dos *hyperlinks*, o que é muito mais realista que "por favor, vá até a página 65" em um PDF. E, caso queira demonstrar outros detalhes, você pode chegar a mostrar as interações para estados ativos de menus, dropdowns, animações de elementos, etc;
- **Permitem a sobrevivência das anotações** &ndash; Anotações em wireframes estáticos, geralmente, perdem-se pelo caminho. Isso é triste, porque, muitas vezes, há muitas ideias e conhecimentos bons nessas anotações. Usar ferramentas como a [annotations feature](http://patternlab.io/) da Pattern Lab ou a [Metaframe](http://aha.elliance.com/2013/05/28/responsive-wireframes/) de Elliance, permite que as anotações cheguem aos navegadores também. Feito da maneira certa, essas anotações podem viver em um guia de estilos/padrões (style guide/pattern);
- **Servem como base do produto final** &ndash; No começo dos meus projetos eu uso [Pattern Lab](http://patternlab.io/) para começar a estabelecer meu sistema de interface e já ter algo pronto para meu código final. Enquanto faço os wireframes, tenho vários trechos de código que parecem com `<div class="fpo">Pagination</div>`, mas, ajustando esses trechos, estou, simultanemante, preparando a base para o meu sistema de design que será utilizado no meu código final, de produção;
- **Permite iterações** &ndash; Mudar coisas de lugar no código, é muito mais eficiente que ter de ajustar vários layouts em alguma ferramenta de design estático. Wireframes em código lhe permitem pegar seus esboços iniciais e iteram inúmeras vezes até chegar em algo que se tornará o produto final.

## Do Wireframe ao Produto Final