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
