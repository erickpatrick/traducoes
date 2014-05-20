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