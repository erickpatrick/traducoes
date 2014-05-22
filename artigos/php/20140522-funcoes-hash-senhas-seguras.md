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
[`Sha1()`](http://us3.php.net/manual/en/function.sha1.php) é uma alternativa e ela gera uma cadeia has ainda maior, com 160-bit.

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
Lembre-se que uma Tabela Mágica pode ser criada do zero, até mesmo, depois da base de dados ser roubada.

### Como isso pode ser explorado?
Mesmo que tenha sido usada alguma cadeia *salt*, ela pode ter sido roubada junto da base de dados. Tudo que é preciso fazer é gerar uma nova Tabela Mágica, do zero, mas, dessa vez, concatenando a cadeia *salt* a cada palavra que eles estiverem colocando na tabela (para cada palavra do dicionário, como dito anteriormente).

Por exemplo, em uma Tabela Mágica genérica, `easypassword` pode existir. Contudo, nessa nova Tabela Mágica, eles também tem `f#@V)Hu^%Hgfdseasypassword`. Quando eles executarem contra todas as 10 milhões de cadeias hash roubadas, provavelmente, encontrarão alguma coisa.

### Como isso pode ser prevenido?
Nós podemos usar uma cadeia *salt* única para cada usuário, ao invés de uma única cadeia *salt* para todos os usuários.

Um candidato para esse tipo de cadeia *salt* é o valor `id` do usuário vindo da base de dados:

```php
$hash = sha1($user_id . $password);
```

Isso, claro, assumindo que o número `id` do usuário nunca mude, que, geralmente, é o caso.

Nós também podemos gerar uma cadeia randômica para cada usuário e usa-la como a cadeia *salt* única. Mas, teríamos de ter certeza que também guardamos esse valor na base de dados, em algum lugar.

```php
function unique_salt()
{
	return substr(sha1(mt_rand()), 0, 22);
}

$unique_salt = unique_salt();

$hash = sha1($unique_salt . $password);

// e não esqueça de salvar a cadeia *salt*
// ...
```

Esse método nos pretege de Tabelas Mágicas, porque toda e cada senha foi combinada com uma cadeia *salt* exclusiva. O *hacker* teria de gerar 10 milhões de Tabelas Mágicas diferentes, o que seria bem imprático.

## 7. Problema #4: Velocidade da Conversão
A maioria das funções hash foram projetas com velocidades em mente, porque, frequentemente, elas são usadas para calcular os valores *checksum* (soma de verificação) para conjudos de dados grandes, e também de arquivos, para verificar a integridade dos mesmos.

### Como isso pode ser explorado?
Como mencionei anteriormente, um computador moderno com um GPU poderosa (sim, placas de vídeo), podem ser programados para calcular alguns bilhões de cadeias hash por segundo. Dessa forma, eles podem ser usados para ataques de força bruta, tentando todo tipo de senha possível.

Você pode achar que requerer que a senhas de 8 caracteres de comprimento livrarão você de ataques de força bruta. Vamos verificar se isso é verdade:

- Se a senha puder ter somente letras minúsculas, maiúsculas e números, teremos 62 caracteres possíveis de utilizar;
- Uma senha com 8 caracteres de comprimento pode ter 62 ^ 8 possibilidades, ou um pouco mais de 218 trilhões;
- A uma taxa de 1 bilhão de cadeias hash pode segundo, podemos resolver esse "problema" em 60 horas.

E, para pessoas com comprimento de 6 caracteres, o que é bem comum, levaria menos de 1 minuto.

Sinta-se livre a requerer senhas com 9 ou 10 caracteres de comprimento, mas, você pode começar a chatear seus usuários.

### Como isso pode ser prevenido?
Use uma função hash mais lenta.

> Imagine que você usa uma função que só pode ser utilizada 1 milhão de vezes por segundo em um mesmo hardware, ao invés dos 1 bilhão de vezes. Levaria cerca de 1000x mais para tentar um ataque de força bruta. Ou seja, 60 horas virariam 70 anos!

Uma maneira de fazer isso seria implementar você mesmo:

```php
function myHash($password, $unique_salt)
{
	$salt = "f#@V)Hu^%Hgfds";
	$hash = sha1($unique_salt . $password);

	for ($i = 0; $i < 1000; $i++) {
		$hash = sha1($hash);
	}

	return $hash;
}
```

Ou talvez que usar um algoritmo que dê suporte ao uso do "parâmetro de custo", como o **BLOWFISH**. no PHP, ele pode ser usado através da função `crypt()`.

```php
function myHash($password, $unique_salt)
{
	// a cadeia salt para o algortimo BLOWFISH deve
	// possuir comprimento de 22 caracteres
	return crypt($password, "$2a$10$" . $unique_salt);
}
```

O segundo parâmetro da função `crypt()` contem alguns valores separados pelo sinal de dólar (`$`).

O primeiro valor é `$2a`, que indica que queremos usar o algoritmo BLOWFISH.

O segundo valor, `$10` nesse caso, é o "parâmetro de custo". Esse é o valor em base binária, de quantas iterações ele rodará (10 => 2 ^ 10 = 1024 iterações). Esse número pode variar de 04 a 31.

Vamos executar um exemplo:

```php
function myHash($password, $unique_salt)
{
	return crypt($password, '$2a$10$' . $unique_salt);
}

function unique_salt()
{
	return substr(sha1(mt_rand()), 0, 22);
}

$password = "verysecret";

echo myHash($password, unique_salt());
// resultado: $2a$10$dfda807d832b094184faeu1elwhtR2Xhtuvs3R9J1nfRGBCudCCzC
```

A cadeia hash resultante o algoritmo `$2a`, o parâmetro de custo `$10`, e a cadeia *salt* de 22 caracteres. O resto é o resultado do cálculo hash. Vamos executar um teste:

```php
// Assumamos que essa cadeia tenha sido obtida de uma base de dados
$hash = '$2a$10$dfda807d832b094184faeu1elwhtR2Xhtuvs3R9J1nfRGBCudCCzC';

// Assumamos que essa é a senha que o usuário usou para acessar novamente
$password = 'verysecret';

if (check_password($hash, $password)) {
	echo "Acesso Garantido!";
} else {
	echo "Acesso Negado!";
}

function check_password($hash, $password)
{
	// Os primeiros 29 caracteres incluem o algoritmo, custo e cadeia *salt*
	// Vamos chama-los de $full_salt
	$full_salt = substr($hash, 0, 29);

	// executemos a função hash na senha
	$new_hash = crypt($password, $full_hash);

	// retorna verdadeiro ou falso
	return ($hash == $new_hash);
}
```

Quando executado, você verá "Acesso Garantido!";

## 8. Juntando Tudo
Tendo em mente tudo o que foi falado, escrevamos um classe utilitária, baseada em tudo o que aprendemos até agora:

```php
class PassHash {

  // blowfish
  private static $algo = "$2a";

  // parâmetro de custo
  private static $cost = '$10';

  // criada, principalmente, para uso interno
  public static function unique_salt()
  {
    return substr(sha1(mt_rand()), 0, 22);
  }

  // isso será usado para gerar o hash
  public static function hash($password)
  {
    return crypt($password,
                  self::$algo .
                  self::$cost .
                  '$' . self::unique_salt());
  }

  // essa será usada para comparar a senha em relação ao hash
  public static function check_password($hash, $password)
  {
    $full_salt = substr($hash, 0, 29);

    $new_hash = crypt($password, $full_salt);

    return ($hash === $new_hash);
  }
}
```

E, aqui, o uso durante o registro do usuário:

```php
// inclua a classe
require ("PassHash.php");

// leia todos os dados de entrada através do $_POST
// ...

// valide os dados
// ...

// converta a senha
$pass_hash = PassHash: hash($_POST['password']);

// guarda todos os dados do usuário no banco, exceto $_POST['password']
// ao invés disso, guarde a cadeia hash, $pass_hash
// ...
```

E essa é a maneira de usa-la durante o processo de login:

```php
// inclua a classe
require ("PassHash.php");

// leia todos os dados de entrada através do $_POST
// ...

// busque o registro do usuário baseado em $_POST['username'] ou similar
// ...

// verifique se a senha usada bate com o hash guardado
if (PassHash::check_password($user['pass_hash'], $_POST['password'])) {
  // garanta o acesso
  // ...
} else {
  // negue o acesso
  // ...
}
```

## 9. Alguns Pontos em Relação à Disponibilidade do Algoritmo Blowfish
