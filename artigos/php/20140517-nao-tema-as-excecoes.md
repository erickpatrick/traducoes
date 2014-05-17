Não tema as exceções
==============================================
Créditos<br/>
<small>Autor original: [Muhammad Usman](http://usman.it/)<br/>[Artigo Original](http://usman.it/dont-fear-exceptions/)</small>

Se você não é um poucos que sabem um pouco mais, com certeza fica com medo ao ver alguma coisa assim:

```php
Fatal error: Uncaught exception 'Exception' with message 'Not found' in /home/<user>/www/test.php on line 27

Fatal error: Uncaught exception 'UnexpectedValueException' in /home/<user>/www/test.php on line 27
```

Quando você vê uma tela vermelha ou laranja com uma `Exception`, você teme ter cometido algum pecado e voltado num pulo para o código. Parabéns, você faz parte da maioria.

**E se eu dissesse que `Exception` não são pecados, muito pelo contrário, são bençãos da programação orientada a objetos?

E se eu dissesse que uma `Exception` não significa nada além que seu código contém algum erro?**

Deixe-me mostra-lo.

Para código de exemplo, usarei a *framework* PHP Laravel. Contudo, esse conteúdo não se limita à Laravel. Esse artigo assume que você sabe o básico sobre `Exceptions` e sabe como capturá-las.

## Um exemplo
Eu acredito em exemplos práticos, então, ao invés de pregar, mostrarei a você um exemplo da vida real.

É muito comum, quando você busca algo no banco de dados, que você verifique se houve algum retorno. Se não existir, você retornará um erro.

```php
$person = Person::find($id);

if (! $person) {
	// Mostrar mensagem: Pessoa não encontrada
}

// Pessoa encontrada
return View::make('person.show', compact('person'));
```

Você lida com esse tipo de problema usando `if` em todos os lugares? Por que você, simplesmente, não lança uma `Exception` quando alguma coisa não existe? Faça assim, ó:

```php
$person = Person:findOrFail($id); // Isso lançará uma exceção caso a pessoa não exista
return View::make('person.show', compact('person'));
```

E, em apenas um lugar, capture esse erro. No Laravel, você pode colocar o código abaixo no arquvo `app/start/global.php`:

```php
APP::error(function(\Illuminate\Database\Eloquent\ModelNotFoundException $e) {
	// Ou você pode mostrar uma página de erro e/ou fazer qualquer outra coisa que quiser
	return 'Nada encontrado'; 
});
```

Se você não usa Laravel então capture a exceção usando `try-catch` quando você estiver carregando sua aplicação.

## Vantagens em usar exceções

**Uma exceção não passa de algo não esperado**. Seja destemido e lance uma `Exception` toda vez que você achar que algo não esperado pode acontecer em uma função, método ou mesmo a nível de aplicação e que deva parar a execução.

Uma das grandes vantagens de usar `Exceptions` ao invés de usar o tradicional 'redirect', o par `die/exit` ou mesmo o `die(var_dump())` é que você separa o código do seu domínio da camada de transporte (ex: requisições HTTP).

Por exemplo, você usa um redirecionamente HTTP quando a validação do formulário falha. E se o usuário final não está usando um navegador? Se ele está usando um *app mobile* que usa a mesma lógica do domínio através de uma API? E se você está realizando testes unitários? E se estiver rodando um trabalho em plano de fundo através da linha de comando? Nada de requisições HTTP, como fica?

Você está certo, se tiver pensado em `Exception`. Crie uma classe `ValidationException` que estende `\Exception` (você tem usado `namespace`, não tem?). Toda vez que uma validação falhar:

```php
throw new ValidationException('Olá, você não preencheu o formulário corretamente. Tente novamente');
```

Agora, capture essa exceção:
- Em caso de uma requisção HTTP normal, realize um `redirect`;
- Em caso de API, sete um código de estado, talvez munido de um mensagem em JSON;
- Em caso de linha de comando, apenas imprima a mensagem de erro.

No exemplo acima, você pode perceber como deixamos nosso código menos repetitivo (Lembre-se: Não se repita&ndashDRY). Há inúmeras outras vantagens que não discutirei agora. Por favor, [verifique esse artigo](http://docs.oracle.com/javase/tutorial/essential/exceptions/advantages.html), se não estiver convencido.

## Por fim
Eu só peço uma coisa: Faça um favor a você mesmo usando das melhores práticas da orientação a objeto onde melhor servir (como utilizar `Exception`). Você não se arrependerá. Nossa comunidade PHP está tanto atrasada quanto a isso, mas a revolução está a caminho.