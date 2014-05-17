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

```yml
formatter.name: pretty
suites:
  todo_suite:
    namespace: PEtersuhm\Todo
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
