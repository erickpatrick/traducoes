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
O processo normal, durante o registro de um usuário é:

- Usuário preenche o formulário de cadastro, incluindo o campo da senha;
- O *script* web guarda toda essa informação em uma base de dados;
- Entretanto, a senha passar por uma função hash, antes de ser salva;
- A versão original da senha não é salva em qualquer lugar, então, tecnicamente, ela é descartada.

E o processo de login:

- Usuário digita o nome de usuário escolhido (ou e-mail) e a senha;
- O *script* roda a senha pela mesma função hash usada anteriormente;
- O *script* procura pelo usuário na base de dados e, se encontrar algum, lê a cadeia hash salva;
- Ambos os valores (senha pós script e hash), garantindo acesso se elas combinarem;

Uma vez que escolhermos um método hash decente para as senhas, nós iremos implementar o processo mais para o final do artigo.

Perceba que a senha original nunca foi guardada em lugar algum. Se a base de dados for roubada, o login do usuário não estará comprometido, certo? Bem, a resposta é "depende". Vejamos alguns problemas em potencial.

## 4. Problema #1: Colisão de Hash
"Colisão" de hash ocorre quando dois dados de entrada produzem a mesma cadeia hash final. A probabilidade disso acontecer depende de qual função você estiver usando.

### Como isso pode ser explorado?
Como exemplo, tenho visto *scripts* antigos que usavam o função [`crc32()`](http://php.net/manual/en/function.crc32.php) para converter as senhas. Essa função um inteiro 32-bit como resultado. Isso significa que só existem 2^32 (2 elevado a 32 potência, ou seja, 4.294.967.296) possibilidades de resultados.

Vamos converter uma senha:

```php
echo crc32('superscretpassword');
// retorna 323322056
```

Agora, assumamos a posição de alguém que roubou uma base de dados e tem as cadeias hash. Nós até não podemo converter o valor 323322056 em "supersecretpassword", entretanto, podemos descobrir outra senha que, ao ser convertida, terá esse mesmo valor, através de um simples *script*:

```php
set_time_limit(0);
$i = 0;
while (true) {
	if (crc32(base64_encode($i)) == 323322056) {
		echo base64_encode($i);
		exit;
	}

	$i++;
}
```

Esse script pode até rodar por um tempo, porém, eventualmente, ele retornará uma cadeia de caracteres. Nós poderemos usar a cadeia retorn &ndash ao invés de "supersecretpassword" &ndash; e ela nos permitirá acessar a conta da pessoa em questão.

Por exemplo, depois de rodar esse mesmo *script* por um tempo em meu computador, obtive a cadeia de caracteres `MTIxMjY5MTAwNg==`. Vamos testar:

```php
echo crc32('supersecretpassword');
// retorna 323322056

echo crc32('MTIxMjY5MTAwNg==');
// retorna 323322056
```

### Como isso pode ser prevenido?
Hoje em dia, um computador doméstico, poderoso o suficiente, pode ser usado para executar funções hash quase que 1 bilhão de vezes por segundo. Então, precisamos de uma função a qual a cadeia hash de retorno tenha um alcance **bem** grande.

Por exemplo, a função `md5()` pode servir, uma vez que ela gera cadeias hashes de 128-bit. Isso é o equivalente a 340.282.366.920.938.463.463.374.607.431.768.211.456 possibilidades. É impossível executar vezes o suficiente para encontrar colisões. Contudo, algumas pessoas acharam [maneiras de fazer isso](http://www.mscs.dal.ca/~selinger/md5collision/).

### Sha1
[`Sha1()`] é uma alternativa e ela gera uma cadeia has ainda maior, com 160-bit.

## 5. Problema #2: Tabelas Mágicas
Mesmo que nós resolvamos o problema da colisão, ainda não estamos seguros, uma vez que existem as Tabelas Mágicas (em inglês, *Rainbow Tables*).

> Uma tabela mágica é construída calculando o valor hash de palavras comumente usadas, bem como suas combinações.

Essas tabelas podem ter milhões ou mesmo bilhões de registros.

Por exemplo, você pode pegar as palavras contidas em um dicionário e gerar cadeias hash para cada uma dessas palavras. Você também pode começar combinando algumas palavras e criar cadeias hash dessas mesmas palavras. E não é tudo. você pode até mesmo adicionar números antes, depois ou entre as palavras, gerar a cadeia hash e guardá-las nas tabelas também.

Considerando o quão barato é o armazenamento hoje em dia, Tabelas Mágicas gigantes podem ser produzidas e utilizadas.

### Como isso pode ser explorado?
Imaginemos que uma base de dados grande foi roubada e, nela, tínhamos 10 milhões de cadeias hash de senhas. É muito simples vasculhar uma Tabela Mágica atrás de cada uma dessas cadeias. De fato, nem todas serão encontradas, mas, de qualquer maneira, algumas delas serão!

### Como isso pode ser prevenido?
Podemos tentar adicionar uma cadeia '*salt*'. Eis um exemplo:

```php
$password = "easypassword";

// isso pode ser, facilmente, encontrado numa
// tabela mágica, já que contem a combinação de 2 palavras comuns
echo sha1($password); // 6c94d3b42518febd4ad747801d50a8972022f956

// use uma cadeia de caracteres randômicos, mais longos que isso, até
$salt = "#@V)Hu^%Hgfds";

// Isso não será encontrado em uma tabela mágica pré-construída
eco sha1($salt . $password); // cd56a16759623378628c0d9336af69b74d9d71a5
```

O que fizemos foi, basicamente, combinar a cadeia *salt* com a senha, antes de converte-los. A cadeia resultante, obviamente, não existirá em uma tabela mágica pré-construída. Mas, ainda não estamos a salvo!

## 6. Problema #3: Tabelas Mágicas (novamente)