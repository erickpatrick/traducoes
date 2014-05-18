Primeiros Passos com Phpspec
==============================================
Créditos<br/>
<small>Autor original: [Peter Suhm](https://tutsplus.com/authors/petersuhm)<br/>[Artigo Original](https://code.tutsplus.com/tutorials/getting-started-with-phpspec--cms-20919)<br/>Nível: Intermediário</small>

Nesse pequeno, porém fácil, tutorial, nós falaremos sobre desenvolvimento movido a comportamento (do inglês, *Behavior Drive Development*, ou *BDD*), com [phpspec](http://www.phpspec.net/). No geral, será uma introdução à ferramenta phpspec, mas, enquanto a apresentemos, falaremos sobre os diferentes conceitos de *BDD*. *BDD* é um dos assuntos do momento e o phpspec ganhou bastante atenção na comunidade PHP, recentemente.

## SpecBDD & Phpspec
DBB é sobre descrever o comportamente do aplicativo, de modo que tenhamos o projeto da forma correta. Ele é, geralmente, associado ao TDD (do inglês, *Test Driven Development*, ou, em português, desenvolvimento movido a testes), mas, enquanto o TDD foca em *testar* sua aplicação, o BDD está mais para descrever o comportamento da mesma. Usar a abordagem do BDD forçará você a considerar, constantemente, os requerimentos verdadeiros e o comportamente desejado do software que está construindo.

Duas ferramentas para BDD ganharam bastante atenção da comunidade PHP, recentemente: [Behat](http://behat.org/) e [Phpspec](http://phpspec.net/). Behat ajuda você a descrever o *comportamente externo* da sua aplicação, usando a linguagem de fácil leitura, Gherkin. phpspec, por sua vez, ajuda a descrever o *comportamente interno* da sua aplicação, ao escrever *pequenas "especificações"* usando PHP - daí o nome SpecBDD (Spec de specification). Essas especificações testam se seu código tem o comportamente desejado.

## O Que Nós Faremos
Nesse tutorial, cobriremos tudo relacionado a começa com o phpspec. No caminho, criaremos a base de uma aplicação do tipo "lista de tarefas", passo-a-passo, usando a abordagem ddo SpecBDD. Enquanto programamos, lançaremos mão do phpspec.

*Nota: Esse é um artigo intermediário sobre PHP. Estou assumindo que você tem conhecimento suficiente sobre orientação a objetos com PHP*

##Instalação
Para esse tutorial, assumiremos que você tem as seguintes ferramentas instaladas e funcionando:

- Ambiente de desenvolvimento PHP (versão mínima 5.3)
- [Composer](http://getcomposer.org)

Instalar o phpspec usado Composer é a forma mais fácil. Tudo que você precisa fazer é rodar o seguinte comando no seu terminal:

```bash
$ composer require phpspec/phpspec
Please provide a version constraint for the phpspec/phpspec requirement: 2.0.*@dev
```

Para garantir que tudo esteja em seu devido lugar e funcionando, rode o comando `phpspec` e veja se obtém o seguinte retorno:

```bash
$ vendor/bin/phpspec run

0 specs
0 examples
0ms
```

## Configuração
Antes de começarmos, nós precisamos fazer pequenas modificações na configuração. Quando phpspec rodar, ele procurará por um arquivo chamado `phpspec.yml`. Já que usaremos `namespace` em nossos códigos, precisamos fazer com que o phpspec saiba disso. Aproveitaremos, também, para garantir que nossas especificações apareçam de forma fácil e bonita de se ver quando mandarmos executá-las.

Vá em frente e crie o arquivo com o conteúdo a seguir:

```yaml
formatter.name: pretty
suites:
  todo_suite:
    namespace: Petersuhm\Todo
```

Há inúmeras outras opções de configuração. Você pode ler mais sobre elas na [documentação](http://phpspec.net/cookbook/configuration.html).

Outra coisa que precisamos fazer, é dizer ao Composer para carregar automaticamente nosso código. phpspec usará o autoarregador do Composer, logo, isso é obrigatório que nossas especificações executem.

Adicione um elemento `autoload` ao arquivo `composer.json` que o Composer criou para você:

```json
{
  "require": {
    "phpspec/phpspec": "2.0.*@dev"
  },
  "autoload": {
    "psr-0": {
      "Petersuhm\\Todo": "src"
    }
  }
}
```

Executar o comando `composer dump-autoload` atualizará o autocarregador após essa mudança.

## Nossa Primeira Especificação
Agora, que já estamos prontos, é hora de escrever nossa primeira especificação. Começaremos descrevendo a classe chamada `TaskCollection`. Faremos com que phpspec gere uma classe de especificação para nós, usando o comendo `describe` (ou a versão abreviada, `desc`).

```bash
$ vendor/bin/phpspec describe "Petersuhm\Todo\TaskCollection"
$ vendo/bin/phpspec run
Do you want me to create 'Petersuhm\Todo\TaskCollection' for you? y
```

O que acabou de acontecer aqui? Primeiro, nós pedimos para o phpspec criar a classe `TaskCollection`. Segundo, nós executamos o conjunto de especificações e, automagicamente, o phpspec ofereceu para criar a classe `TaskCollection` para nós. Legal, não é?

Vá em frente, rode o conjunto novamente e verá que já temos um exemplo em nossas especificações (logo veremos o que é um exemplo):

```bash
$ vendor/bin/phpspec run
    Petersuhm\Todo\TaskCollection
  10 ✔ is initializable

1 specs
1 examples (1 passed)
7ms
```

Desse retorno, podemos ver que a classe `TaskCollection` é *inicializável*. O que isso significa? Veja o arquivo de especificação que o phpspec criou e tudo ficará claro:

```php
<?php

namespace spec\Petersuhm\Todo;

use PhpSpec\ObjectBehavior;
use Prophecy\Argument;

class TaskCollectionSpec extends ObjectBehavior
{
  function it_is_initializable()
  {
    $this->shouldHaveType('Petersuhm\Todo\TaskCollection');
  }
}
```

A frase 'is initializable' é detivada da função `it_is_initializable()`, a qual o phpspec adiciona à classe `TaskCollectionSpec`. Essa função é o que nós chamamos de *exemplo* (*example*, no retorno anterior). Nesse exemplo em particular, nós temos o que podemos chamar de combinador, chamado de `shouldHaveType()`, que verifica o tipo da nossa classe `TaskCollection`. Se você mudar o parâmetro passado para essa função para qualquer outra coisa e roda a especificação novamente, verá que ela falhará. Antes de entender isso completamente, preciamos descobrir a que, precisamente, se refere a variável `$this` em nossa especificação.

## O Que É `$this`?
Obviamente, `$this` refere-se à instância da classe `TaskCollectionSpec`,  uma vez que isso, nada mais é, que um código PHP comum. Mas, com phpspec, você tem de tratar o `$this` de forma diferente com o que está acostumado, já que, por baixo dos panos, ele se refere, na verdade, ao objeto em teste, que, nesse caso, é a classe `TaskCollection`. Esse comportamente é herdado da classe `ObjectBehavior`, que garante que as chamadas às funções sejam redirecionadas (através de um Proxy) à classe verdadeira. Isso significa que `AlgumaClasseSpec` redirecionará as chamadas dos métodos para uma instância de `AlgumaClasse`. phpspec envolverá essas chamadas de métodos para garantir que os seus resultados sejam rodados a algum combinador como o que você acabou de ver.

Você não precisa entender perfeitamente isso para usar o phpspec, apenas se lembre que a `$this`, na verdade, refere-se ao objeto sob teste.

## Construindo Nossa Coleção de Tarefas
Até agora, não fizemos muita coisas por conta própria. Mas o phpspec criou uma classe `TaskCollection` para nós usarmos. Agora, é hora de escrever um pouco de código e tornar essa classe útil. Nós adicionaremos dois métodos: um `add()`, para adicionar tarefas, e um `count()`, para contar o número de tarefas na coleção.

### Adicionando uma Tarefa
Antes de escrevermos qualquer código de verdade, nós deveríamos criar um exemplo em nossa especificação. Em nosso exemplo, nós queremos tentar adicionar uma tarefa à nossa coleção e, então, garantir que essa tarega foi adicionada de fato. Para fazer isso, ´precismos de uma instância da classe `Task` (ainda não existente). Se adicionarmos essa dependência como parâmetro para a nossa função de especificação, o phpspec nos dará, automaticamente, uma instância para que possamos usar. Na verdade, a instância não é de verdade, mas algo que o phpspec identifica como `Collaborator`. Esse objeto agirá como o verdadeiro objeto, mas o phpspec permitirá que façamos muitas coisas com ele. Algo que veremos em breve, inclusive. Embora a classe `Task` não exista ainda, por hora, finja que ela existe. Abra a classe `TaskCollectionSpec` e adicione a instrução `use` para adicionar a classe `Task` e, depois, adicionae o exemplo `it_adds_a_task_to_the_collection()`:

```php
use Petersuhm\Todo\Task;

...

function it_adds_a_task_to_the_collection(Task $task)
{
  $this->add($task);
  $this->tasks[0]->shouldBe($task);
}
```

Em nosso exemplo, escrevemos o código que "gostaríamos de ter". Nós chamamos o método `call()` e então tentamos passá-lo uma `$task`. Checamos, então, se a tarefa foi, de fato, adicionada à variável de instância, `$tasks`. O combinador `shouldBe()` é um combinador de identidade idêntico ao comparador `===` do PHP. Você usar tanto o `shouldBe()`, `shouldBeEqualTo()`, `shouldEqual()` ou `shouldReturn()` - todos fazem a mesma coisa.

Ao executar o phpspec, teremos alguns erros, já que não temos a classe `Task` ainda.

Façamos com que o phpspec arrume isso para nós:

```bash
$ vendor/bin/phpspec describe "Petersuhm\Todo\Task"
$ vendor/bin/phpspec run
Do you want me to create 'Petersuhm\Todo\Task' for you? y
```

Executando o phpspec novamente, algo interessante acontece:

```bash
$ vendor/bin/phpspec run
Do you want me to create 'Petersuhm\Todo\TaskCollection::add()' for you? y
```

Perfeito! Se você for olhar o arquivo `TaskCollection.php`, verá que o phpspec criou uma função `add()` para que a preenchamos.

```php
<?php

namespace Petersuhm\Todo;

class TaskCollection
{
  public function add($argument1)
  {
    // TODO: write logic here
  }
}
```

Porém, phpspec ainda reclama. Nós não temos nossa array `$tasks`. Então, vamos criá-ça e adicionar nossa tarefa a ela:

```php
<?php

namespace Petersuhm\Todo;

class TaskCollection
{
  public $tasks;

  public function add(Task $task)
  {
    $this->tasks[] = $task;
  }
}
```

Agora, nossas especificações estão legais e todas verdes. Note que fiz questão de lançar mão da checagem de tipos no parâmetro `$task`;

Só para garantir que nós fizemos tudo certo, vamos adicionar outra tarefa:

```php
function it_adds_a_task_to_the_collection(Task $task, Task $anotherTask)
{
  $this->add($task);
  $this->tasks[0]->shouldBe($task);

  $this->add($anotherTask);
  $this->tasks[1]->shouldBe($anotherTask);
}
```

Ao rodar phpspec, tudo estará legal.

### Implementando a Interface `Countable`
Querer saber quantas taremos existem em uma coleção é uma excelente razão para usaarmos uma das [interfaces](http://www.php.net/manual/pt_BR/spl.interfaces.php) da [Bbiblioteca Padrão do PHP](http://www.php.net/manual/pt_BR/book.spl.php) (do inglês, Standard PHP Library, SPL), mais especificamente a `Countable` interface.

Mais cedo, nós usamos o combinador `shouldHaveType()`, que é um combinador de *tipo*. Ele usa o comparador `instanceof` do PHP para validar se um objeto é uma instância de uma dada classe. Há quatro combinadores de tipo, os quais fazem a mesma coisa. Um deles é o `shouldImplement()`, que é perfeito para nosso objetivo, então vamos usá-lo em nosso exemplo:

```php
function it_is_countable()
{
  $this->shouldImplement('Countable');
}
```

Você percebe o quão bonito e simple é de ler? Vamos executar o exemplo e deixar que o phpspec nos guie:

```bash
$ vendor/bin/phpspec run

      Petersuhm\Todo\TaskCollection
  25 ✘ is countable
      expected an instance of Countable, but got [obj:Petersuhm\Todo\TaskCollection].
```

Certo, então. Nosso código não é uma instância de `Countable`, uma vez que não a implementamos ainda. Vamos atualizar o código da nossa class `TaskCollection`:

```php
  class TaskCollection implements \Countable
```

Nossos testes não executarão, já que a interface `Countable` tem um método abstrato, `count()`, que nós devemos implementar. Um método vazia funcionará, por hora:

```php
public function count()
{
  // ...
}
```

E voltamos ao verde. Nesse momento, nosso método `count()` não faz muita coisa e, na verdade, é bem inútil. Escrevamos uma especificação para o comportamente que desejamos que ele tenha. Primeiro, sem tarefas, é esperado retornar zero de nossa função:

```php
function it_counts_elements_of_the_collection()
{
  $this->count()->shouldReturn(0);
}
```

It returns `null`, não `0`. Para fazermos o teste ficar verde, vamos conserta-la ao modo TDD/BDD:

```php
public function count()
{
  return 0;
}
```

Agora, estamos verde e tudo está bem, exceto que esse não é o provável comportamento que queremos. Ao invés disso, expandamos nossa especificação e adicionemos algo à array `$tasks`:

```php
function it_counts_elements_of_the_collection()
{
  $this->count()->shouldReturn(0);

  $this->tasks = ['foo'];
  $this->count()->shouldReturn(1);
}
```

Obviamente, nosso código ainda retorna `0` e nós temos um passo em vermelho. Ajustar isso não é muito difícil e nossa class `TaskCollection` deve parecer com isso, agora:

```php
<?php

namespace Petersuhm\Todo;

class TaskCollection implements \Countable {
  public $tasks;

  public function add(Task $task)
  {
    $this->tasks[] = $task;
  }

  public function count()
  {
    return count($this->tasks);
  }
}
```

Agora, nós temos um teste verde e nosso método `count()` funciona. Que legal!

## Expectativas e Promessas
Você se lembra que eu falei que o phpspec permite você fazer várias coisas legais com as intâncias da classe `Collaborator`, também conhecidas como as instâncias que são injetadas automaticamente pelo phpspec? Se você já escreveu testes antes, você sabe o que são os objetos simulados (*mocks*) e as funções substitutas (*stubs*). Se você não sabe, não se preocupe quanto a isso. São só jargões. Eles se referem ao objetos "falsos" que agirão no lugar dos objetos de verdade, que permitem você fazer testes isolados. phpspec, automaticamente, transforma as instâncias de `Collaborator` em *mocks* e *stubs* se você precisar em seus objetos.

Isso é incrível. Por trás, phpspec usa a biblioteca [Prophecy](https://github.com/phpspec/prophecy), que é um arcabouço (*framework*) bem particular que trabalha bem com phpspec (e é construída pelos mesmos criadores do phpspec).
 Você pode estabelecer uma expectativa em um colaborador (*mocking*), algo como "esse método *deveria ser invocado*" e pode adicionar promessas (*stubbing*), como "esse método *retornará* esse valor". Com o phpspec, essa tarefa é bem simples e nós faremos as duas, logo a seguir.

 Criemos uma classe, chamada de `TodoList`, que faz uso de nossa coleção:

 ```bash
 $ vendor/bin/phpspec desc "Petersuhm\Todo\TodoList"
 $ vendor/bin/phpspec run
 Do you want me to create "Petersuhm\Todo\TodoList" for you? y
 ```

### Adicionando Tarefas
O primeiro exemplo que adicionaremos é um para adicionar tarefas. Nós criaremos um método `addTask()`, que nada mais faz que adicionar uma tarefa à nossa coleção. Ele simplesmente direciona a chamada ao método `add()` da coleção, logo, aqui é um lugar perfeito para usar uma Expectativa. Nós não queremos invocar o método `add()` de verdade, nós só queremos garantir que ele tente fazê-lo. Além do mais, nós queremos garantir que ele seja chamado apenas uma vez. Veja como podemos faz isso com phpspec:

```php
<?php

namespace spec\Petersuhm\Todo;

use Phpspec\ObjectBehavior;
use Prophecy\Argument;
use Petersuhm\Todo\TaskCollection;
use Petersuhm\Todo\Task;

class TodoListSpec extends ObjectBehavior
{
  function it_is_initializable()
  {
    $this->shouldHaveType('Petersuhm\Todo\TodoList');
  }

  function it_adds_a_task_to_the_list(TaskCollection $tasks, Task $taks)
  {
    $tasks->add($task)->shouldBeCalledTimes(1);
    $this->tasks = $tasks;

    $this->addTask($task);
  }
}
```

Primeiro, fazemos com phpspec proveja dois colaboradores que precisamos: uma coleção de tarefas e uma tarefa. Depois disso, estabelecemos uma expectativa no colaborador da coleção de tarefas que, basicamente, diz: "O método `add()` deverá ser chamado, exatamente, 1 vez com a variável `$task` como parâmetro". É assim que preparamos nosso colaborador, que agora é um *mock*, antes de atribuí-lo à propriedade `$tasks` de `TodoList`. Finalmente, nós tentamos invocar o método `addTask()` de verdade.

Certo, o que o phpspec tem a dizer sobre isso:

```bash
$ vendor/bin/phpspec run

        Petersuhm/Todo/TodoList
  17  ! adds a task to the list
        property tasks not found.
```

A propriedade `$tasks` não existe. Ajustar esse problema é bem fácil:

```php
<?phpspec

namespace Petersuhm\Todo;

class TodoList
{
  public $tasks;
}
```

Tente novamente e deixe o phpspec ser nosso guia:

```bash
$ vendor/bin/phpspec run
Do you want me to create 'Petersuhm\Todo\TodoList::addTask()' for you? y
$ vendor/bin/phpspec run

        Petersuhm/Todo/TodoList
  17  ✘ adds a task to the list
        some predictions failed:
          Double\Petersuhm\Todo\TaskCollection\P4:
            Expected exactly 1 calls that match:
              Double\Petersuhm\Todo\TaskCollection\P4->add(exact(Double\Petersuhm\Todo\Task\P3:000000002544d76d0000000059fcae53))
            but none were made.
```

Okay, algo interessante aconteceu agora. Viua mensagem "*Expected exactly 1 calls that match: ....*"? Essa é nossa expectativa falha. Isso acontece porque, depois de invocar o método `addTask()`, o método `add()` na coleção não foi invocado, o que era esperado acontecer.

Para voltar a termos tudo verde, coloque o seguinte código dentro do método `addTask()`:

```php
<?php

namespace Petersuhm\Todo;

class TodoList
{
  public $tasks;

  public function addTask(Task $task)
  {
    $this->tasks->add($task);
  }
}
```

Tudo verde, novamente. Isso é muito bom, não é?

### Verificando Tarefas
Façamos algo relacionado a promessas, também. Nós queremos um método que nos diga se há alguma tarefa em nossa coleção. Para isso, simplesmente verificaremos o valor de retorno do método `count()` da coleção. Novamente, nós não precisaremos de uma instância de verdade com um método `count()` de verdade. Nós só precisamos garantir que nosso código chame algum método `count()` e realize alguma tarefa, dependendo do valor de retorno do método.

Veja o seguinte exemplo:

```php
function it_checks_whether_it_has_any_tasks(TaskCollection $tasks)
{
  $tasks->count->willReturn(0);
  $this->tasks = $tasks;

  $this->hasTasks()->shouldReturn(false);
}
```

Nós temos um colaborador de coleção de tarefas que tem um método `count()` que *retornará zero*. Essa é nossa promessa. Isso significa que, toda vez que alguém chamar o método `count()`, ele retornará zero. Nós, então, atribuímos o colaborador preparado à propriedade `$tasks` do nosso objeto. Por fim, nós tentamos chamar um método, `hasTasks()`, e garantir que ele retorna `false`.

O que a especificação tem a nos dizer sobre isso?

```bash
$ vendor/bin/phpspec run
Do you want me to create 'Petersuhm\Todo\TodoList::hasTasks()' for you? y
$ vendor/bin/phpspec run

        Petersuhm/Todo/TodoList
  25  ✘ checks whether it has any tasks
        expected false, but got null.
```

Legal. phpspec criou para nós um método `hasTasks()` e, sem surpresas, ele retorna `null` e não `false`.

Novamente, essa é uma tarefa fácil de resolver:

```php
public function hasTasks()
{
  return false;
}
```

Obtivemos tudo verde, mas, isso não é bem o que queremos. Verifiquemos pelas tarefas quando tivermos 20 delas. Dessa vez, deverá retornar `true`:

```php
function it_checks_wheter_it_has_any_tasks(TaskCollection $tasks)
{
  $tasks->count()->willReturn(0);
  $this->tasks = $tasks;

  $this->hasTasks()->shouldReturn(false);

  $tasks->count()->willReturn(20);
  $this->tasks = $tasks;

  $this->hasTasks()->shouldReturn(true);
}
```

Execute phpspec e você terá:

```bash
$ vendor/bin/phpspec run

        Petersuhm\Todo\TodoList
  25  ✘ checks wheter it has any tasks
        expected true, but got false.
```

Certo, `false` não é `true`, então, precisamos melhorar nosso código. Vamos usar o método `count()` para verificar se há tarefas ou não:

```php
public function hasTasks()
{
  if ($this->tasks->count() > 0)
    return true;

  return false;
}
```

Tah dah! Tudo verde, de novo!

## Construindo Combinadores Customizados
Parte de criar boas especificações é fazê-las o mais legível possível. Nosso último exemplo pode, na verdade, ser aprimorado um pouco, graças aos combinadores customizados do phpspec. É fácil de implementar combinadores customizados -tudo que temos de fazer é sobrescrever o método `getMatchers()` que é herdado de `ObjectBehavior`. Ao implementarmos dois combinadores customizados, nossa especificação pode ser alterada para parecer com isso:

```php
function it_checks_wheter_it_has_any_tasks(TaskCollection $tasks)
{
  $tasks->count()->willReturn(0);
  $this->tasks = $tasks;

  $this->hasTasks()->shouldBeFalse();

  $this->count()->willReturn(20);
  $this->tasks = $tasks;

  $this->hasTasks()->shouldBeTrue();
}

function getMatchers()
{
  return [
    'beTrue' => function($subject) {
      return $subject === true;
    },
    'beFalse' => function($subject) {
      return $subject === false;
    },
  ];
}
```

Eu acredito que isso esteja parecendo bem bacana. Lembre-se que refatorar suas especificações é importante para mante-las atualizadas. Implementar seus próprios combinadores customizados pode limpar suas especificações e torná-las mais legíveis.

Na verdade, nós podemos usar a negação dos combinadores também:

```php
function it_checks_whether_it_has_any_tasks(TaskCollection $tasks)
{
  $tasks->count()->willReturn(0);
  $this->tasks = $tasks;

  $this->hasTasks()->shouldNotBeTrue();

  $tasks->count()->willReturn(20);
  $this->tasks = $tasks;

  $this->hasTasks()->shouldNotBeFalse();
}
```

Bem legal!

## Conclusão
Todas as nossas especificações estão verdes e veja como elas documentam de forma legal nosso código!

```bash
        Petersuhm\Todo\TaskCollection

    10 ✔ is initializable
    15 ✔ adds a task to the collection
    24 ✔ is countable
    29 ✔ counts elements of the collection

        Petersuhm\Todo\Task

    10 ✔ is initializable

        Petersuhm\Todo\TodoList

    11 ✔ is initializable
    16 ✔ adds a task to the list
    24 ✔ checks whether it has any tasks


3 specs
8 examples (8 passed)
16ms
```

Nós descrevemos de forma efetiva e alcançamos o comportamento desejado de nosso código. Sem mencionar que, uma vez que nosso código está coberto por nossas especificações, a refatoração do mesmo não será uma experiência pavorosa.

Por ter seguido esse tutorial, espero ter inspirado você a dar uma chance ao phpspec. Ele é mais que uma ferramenta de testes - é uma ferramenta de design. Uma vez acostumado a usar o phpspec (e suas maravilhosas ferramentas de geração de código), você terá problemas em largar dele novamente! As pessoas, frequentemente, reclamam que seguir o caminho TDD ou BDD diminui a velocidade deles. Depois de incorporar o phpspec no meu fluxo de trabalho, eu sinto exatamente o contrário - minha produtividade melhorou bastante. Sem falar que meu código está muito mais sólido!
