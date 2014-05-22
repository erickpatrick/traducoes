Compreendendo as Funções Hash e Mantendo as Senhas Seguras
==========================================================
Créditos<br/>
<small>Autor original: [Burak Guzel](http://tutsplus.com/authors/burak-guzel)<br/>[Artigo Original](http://code.tutsplus.com/tutorials/understanding-hash-functions-and-keeping-passwords-safe--net-17577)<br/>Nível: Intermediário</small>

De tempos em tempos, servidores e bases de dados são roubadas ou comprometidas. Tendo isso em mente, é importante garantir que dados cruciais dos usuários, como senhas, não sejam descobertos. Hoje, nós aprenderemos o básico sobre funções *hash* e o que é preciso fazer para proteger as senhas em suas aplicações web.

## 1. Aviso
Criptologia é um assunto bem complicado e eu, de modo algum, sou um especialista no assunto. Há, sempre, pesquisas acontecendo na área, em diversas universidades e agências de segurança.

Nesse artigo, tentarei facilitar ao máximo possível e, ao mesmo tempo, apresentar um método seguro o suficiente para armazenar senhas em suas aplicações web.

## 2. O Que um "Hash" Faz?
> Hash converte um pedaço de dado (seja grande ou pequeno), em um pedaço relativamente pequeno de dados, como um sequência de caracteres (string) ou um inteiro.

Isso é alcançado através de funções de mão única. "Mão única" significa que é bem difícil (ou praticamente impossível) reverter essa conversão.

Um exemplo bem comum de função hash é a [`md5()`](http://php.net/manual/en/function.md5.php). Ela é bastante comum é diversas linguagens de programação e sistemas.

```php
$data = "Hello World";
$hash = md5($data);
echo $hash; // b10a8db164e0754105b7a99be72e3fe5
```

Com a função `md5()`, o resultado sempre será uma cadeia de caracteres com 32 caracteres de comprimento. Porém, ela só contem caracteres hexadecimais. Tecnicamente, ela também pode ser representada como um inteiro 128-bi (16 bytes). Você pode converter dados e cadeias de caracteres muito maiores, usando `md5()`, e sempre obterá um hash com esse comprimento. Só esse fator já deve dar um dica do porque ser considerada um função de "mão única".

## 3. Usando uma Função Hash para Salvar Senhas
