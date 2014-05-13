Como Testes A/B Funcionam e Como Usá-los com Google Analytics
=============================================================
Créditos
<small>Autor original: [Paula Borowska](https://twitter.com/paulaborowska)<br/>[Artigo original](http://designmodo.com/ab-testing-google-analytics/)</small>

[Teste A/B](http://designmodo.com/ab-testing/) é um termo utilizado para indicar a comparação de um design em relação a outro, no sentido de maximizar as taxas de conversão. Esse método foi popularizado pelo mundo do marketing para uso com e-mails, propaganda e panfletos, para averiguar qual modelo traria melhores retornos. Você pode ter mais de duas variações e testar qualquer coisa, do tamanho das fontes utilizados no texto às cores de plano de fundo e imagens. Só depende de você.

## Como Isso Funciona?
Testes A/B trabalham a partir de estatísticas. Por exemplo, em um teste A/B, poderíamos ter 100 pessoas direcionadas para uma página A enquanto outras 100 pessoas seria direcionadas para uma página B, na tentativa de descobrir qual cor de botão, azul ou vermelho, dentro do design em questão, seria a melhor escolha. Se somente 30% clicassem no botão da página A (vermelho) enquanto 45% clicassem no botão da página B (azul), então, poderíamos afirmar que o botão azul é uma escolhar melhor que o botão vermelho.

**Compreender os resultados é bem simples**. Como obter resultados significantes que é complicado. Para os resultados finais serem válidos, o tamanho da amostra deve ser grande suficiente. O que *significante* quer dizer? Simplesmente significa que o tamanho da amostra - número de visitantes - é grande o suficiente para que os resultados sejam legítimos e não um produto da sorte (ou azar, se preferir assim).

Para dar uma ideia básica, se você quer que sua taxa de conversão seja alta, a sua amostra deve ser proporcionalmente grande. Logo, se você deseja que a taxa de conversão de um botão seja de 10%, você precisará de cerca de 600 visitantes para que os resultados sejam significativos. Se você quer que sua conversão seja de significantes 30%, você precisará de, pelo menos, 1400 visitantes. Isso, de forma alguma, é uma regra, mas passa a ideia geral. Quão mais demorar para você obter essas visitas e cliques, mais demorado será para você obter resultados válidos. Acredito que isso seja lógico, porém, percebi que esses valores acabam negligenciados. Se o seu site possui bastante tráfego, tal qual uma [Amazon](http://www.amazon.com), os resultados serão mais rápidos de atingir. Se seu site tem, talvez, 50 visitas por dias, o processo demorará um pouco mais.

### Pegue leve
Testes A/B é um artifício enviado pelos deuses quando queremos otimizar campanhas, seja para uma página de destino (landing page) ou uma campanha de e-mail. Entretanto, há algumas coisas que você precisa estar ciente. Primeiro, quanto mais variações você tiver, mais tempo levará para você ver os resultados. É aceitável tentar até 4 variaões de uma página, mas tenha em mente que o resultado pode levar 4 vezes mais para atingir resultados significantes. Além disso, você não deveria testar grandes variações. Não teste um redesenho muito grande, buscando pequenas modificações e somente em um elemento por vez. Seria difícil determinar o elemento que causou a mudanção de conversão se você está mudando demais as coisas.

## Como Realizar Testes A/B com o Google Analytics
Agora que você sabe o funcionamento dos testes A/B, é hora de realizar alguns testes. Há alguns serviços que permitem você realizar tais testes. Há opções pagas, mas o Google Analytics é gratuito além de não ser tão complicado de configurar. Vou considerar que você já possui [uma conta no Google Analytics](http://www.google.com/analytics) e se não possuir, essa é a hora para cadastrar-se.

Para fins desse tutorial, reescreverei alguns textos da [página inicial do meu portfolio](http://www.paulaborowska.com/) e também mudarei a imagem do plano de fundo.

![Versão A do portfolio de Paula Borowska](http://designmodo.com/wp-content/uploads/2014/05/testA.jpg "Versão A do portfolio de Paula Borowska")

![Versão B do portfolio de Paula Borowska](http://designmodo.com/wp-content/uploads/2014/05/testB.jpg "Versão B do portfolio de Paula Borowska")

Quando estiver logado na sua conta do Google Analytics, selecione o site que você gostaria de realizar os testes. Uma vez no painel relativo ao site escolhido, no menu de navegação da lateral esquerda, procure por Comportamente (Behavior, em inglês) e clique para expandir (é um dos último links, para falar a verdade). Uma vez lá, clique no link Experimentos (Experiment).

![Visualização do menu lateral esquerdo no Google Analitcs](http://designmodo.com/wp-content/uploads/2014/05/experiments.jpg "Visualização do menu lateral esquerdo no Google Analitcs")

Você será direcionado a uma nova página que não tem muita coisa. Isso é normal; nós adicionaremos um novo experimento - teste A/B - agora. Para fazer isso, clique no botão "Criar Experimento" (Create experiment).

![Página Experimentos no Google Analytics](http://designmodo.com/wp-content/uploads/2014/05/c.jpg "Página Experimentos no Google Analytics")

![Página Experimentos no Google Analytics com foco no botão](http://designmodo.com/wp-content/uploads/2014/05/create-experiment.jpg "Página Experimentos no Google Analytics com foco no botão")

### Preparando um experimento
Você será enviado para uma nova página onde você deverá dar um nome ao seu novo experimento.

![Página de crição de novo experimento](http://designmodo.com/wp-content/uploads/2014/05/content-experiments.jpg "Página de crição de novo experimento")

Há vários testes. Você pode ver o impacto das mudanças nas taxas de rejeição, no número de visualização de páginas ou se as pessoas clicam em um link específico. Depende só de você. Você pode verificar múltiplas páginas de uma só vez. Você faz tudo isso nos seção de objetivos do experimento. **Crie um objetivo customizado** para acompanhar dados únicos.

![Página de obejtivos do experimento](http://designmodo.com/wp-content/uploads/2014/05/Create-a-custom-objective-.jpg "Página de obejtivos do experimento")

Voltando ao conteúdo essencial, perceba que você pode setar a quantidade de tráfego que será afetada pelo seu teste. Quando você tiver 100% do seu tráfego selecionado, isso significa que todo os seus visitantes estarão sujeitos ao teste A/B. Claro, isso oferece resultados masi rápidos. Quando você tem somente 50%, apenas metado do seu tráfego estarásujeito ao teste A/B enquanto a outra metade verá a página original todas as vezes.

### Configurando o Experimento
Uma vez com seus objetivos em ordem, é hora de determinar quais página serão testadas. Você precisará fornecer a página original, bem como a URL da página alternativa. Para você testar pelo Google Analytics, você precisará de páginas separadas que são iguais, porém, com as implementações das variações na página alternativa para que seus visitantes sejam direcionados. Adicione quantas variações quiser.

![Seleção de páginas alvo do teste](http://designmodo.com/wp-content/uploads/2014/05/Configure-your-experiment.jpg "Seleção de páginas alvo do teste")

### Adicionando código
A última coisa que você precisa fazer é **adicionar o pedaço de código que o Google Analytics fornece** em amabas as páginas para que o sistema do Google Analytics as reconheça. Infelizmente (ou felizmente), o Google não sabe de tudo e só porque você o está direcionando a páginas diferentes não quer dizer que ele começara a gravar os dados.

![Código especifico ao experimento](http://designmodo.com/wp-content/uploads/2014/05/Adding-the-code.jpg "Código especifico ao experimento")

Cada um desses pedaços de código são especificos para cada teste. Siga as instruções se precisar de ajuda mas, basicamente, basta inserí-lo logo após a tag `<head>` de abertura e tudo estará certo.

![Posicionamento do código do experimento](http://designmodo.com/wp-content/uploads/2014/05/code.jpg "Posicionamento do código do experimento")

Assim que você receber a confirmação que o Google Analytics está conectado com o código, você será capaz de iniciar o experimento.

### Obtendo os resultados
Tenha um pouco de paciência, mas, no geral, você será capaz de ver os resultados tão logo que o pessoal começar a visitar as páginas (Pode ser que você tenha de esperar **até 24h para o Google Analytics** estar completamente conectado ao seu experimento). Verifique como os teste está indo. Monitorar com certa frequência ajudará a verificar qualquer problemas técnicos durante o teste. Se uma página está com resultados excelente e a outra não está lá essas coisas, pode ser que alguma coisa tenha saído errado durante a implementação do teste. Se ambas as páginas está se saindo extremamente bem, os objetivos podem ter sido mal escolhidos. Sempre monitore os resultados e verá o quanto será necessário esperar para obter resultados significativos.

Bons testes!