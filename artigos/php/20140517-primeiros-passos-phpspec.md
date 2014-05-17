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
