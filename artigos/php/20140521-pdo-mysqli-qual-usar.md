PDO ou  MySQLi: Qual você deveria usar?
==============================================
Créditos<br/>
<small>Autor original: [Dejan Marjanovic](https://tutsplus.com/authors/dejan-marjanovic)<br/>[Artigo Original](https://code.tutsplus.com/tutorials/pdo-vs-mysqli-which-should-you-use--net-24059)<br/>Nível: Básico-Intermediário</small>

Ao querer acessar uma base de dados, nós temos duas opções: [MySQLi](http://www.php.net/manual/en/book.mysqli.php) e [PDO](http://www.php.net/manual/en/book.mysqli.php) - [Não, MySQL não é opção](http://php.net/manual/en/migration55.deprecated.php). Então, o que você deveria saber antes de fazer sua escolha? As diferenças, suporte aos variados gerenciadores de banco de dados, estabilidade e performance são alguns dos pontos levantados nesse artigo.

## Resumo
| |PDO|MySQLi|
|------|------|------|
|**Suporte a Bancos de Dados**|12 drivers diferentes|Somente MySQL|
|**API**|Orientada a Objetos|Orientada a Objetos + Procedural|
|**Conexão**|Fácil|Fácil|
|**Parâmetros Nomeados**|Sim|Não|
|**Mapeamento de Obejtos**|Sim|Sim|
|**Sentenças Preparadas (lado do cliente)**|Sim|Não|
|**Performance**|Rápido|Rápido|
|**Procedimentos Armazenados**|Sim|Sim|

## Conexão
Criar uma conexão com uma base de dados é moleza, com ambos:

```php
// PDO
$pdo = new PDO("mysql:host=servidor;dbname=banco_de_dados", 'usuario', 'senha');

// MySQLi, forma procedural
$mysqli = mysqli_connect('servidor', 'usuario', 'senha', 'banco_de_dados');

// MySQLi, forma via orientação a objetos
$mysqli = new mysqli('servidor', 'usuario', 'senha', 'banco_de_dados');
```

Por favor, considere que esses objetos de conexões/recursos estejam presentes durante o resto desse tutorial.

## Suporte de API
Tanto PDO quanto MySQli oferecem um API orientada a objetos, mas o MySQLi também oferece uma API procedural - facilitando o entendimento por parte dos novatos. Se você já é familiarizado com o driver nativo do MySQl para PHP, migrar para a interface procedural do MySQLi será extremamente simples. Por outro lado, uma vez que você compreender a PDO, você poderá usá-la com qualquer banco de dados que desejar!

## Suporte a Bancos de Dados
![Lista de Bancos de Dados Suportados pelo PDO vs. MySQLi](https://cdn.tutsplus.com/net/uploads/legacy/2013_phpvsmysqli/tutorial_3.png "Lista de Bancos de Dados Suportados pelo PDO vs. MySQLi")

A principal vantagem do PDO sobre o MySQLi é seu suporte aos drivers de diversos bancos de dados. No momento em que traduzo esse artigo, o **PDO dá suporte a 12 tipos diferents de dirvers**, enquanto o MySQLi só dá **suporte ao MySQL**.

Caso queira saber quais os drivers que o PDO dá suporte no momento, use o código a seguir:

```php
var_dump(PDO::getAvailableDrivers());
```
O que isso significa? Bem, ~em situações que você precisar usar outro banco de dados, a PDO torna esse processo transparente. Então, *a única coisa que você precisará fazer* é mudar os parâmetros de conexão e algumas consultas &ndash isso se elas usarem algum método que não seja suportado por outra base de dados. Já com o MySQLi, você precisará *reescrever todo o código que lida com banco de dados* &ndash; inclusive as consultas.

## Parâmetros Nomeados
Essa é outra característica importante que o PDO tem; vincular parâmetros com PDO é muito mais fácil usando a vinculação nomeada:

```php
$params = [':usuario' => 'teste', ':email' => $email => ':ultimo_login' => time() - 3600];

$pdo->prepare('SELECT * FROM users WHERE username = :usuario AND email = :email AND las_login = :ultimo_login');

$pdo->execute($params);
```

Ao contrário da maneira do MySQLi:

```php
$query = $mysqli->prepare('SELECT * FROM users WHERE username = ? AND email = ? AND lasT_login = ?');

$query->bind_param('teste', $mail, time() - 3600);
$query->execute();
```

A vinculação dos parâmetros na forma de interrogação pode parecer menor, mas, nem de longe, é tão flexível quanto aos parâmetros nomeados, devido ao fato do desenvolvedor ser obrigado a saber a ordem dos parâmetros. Ela parece um *hack* feito para permitir vinculação de parâmetros em algumas circunstâncias.

Infelizmente, **MySQLi não dá suporte a parâmetros nomeados**.

## Mapeamento de Objetos
Tanto PDO quanto MySQLi podem mapear resultados em objetos. Isso vem bem a calhar se você não quer usar uma camada de abstração de banco de dados customizada, mas ainda quer um comportamento semelhante ao de um *ORM*. Vamos imaginar que nós temos uma classe `User` com algumas propriedades nomeadas de forma idêntica aos campos da tabela do Banco de Dados:

```php
class User {
	public $id;
	public $first_name;
	public $last_name;

	public function info()
	{
		return "#" . $this->id . ": " . $this->first_name . " " . $this->last_name;
		}
}
```

Sem o mapeamento de objetos, nós precisaríamos preencher cada um dos campos (fosse manualmente ou através de um método construtor), anes de podermos usar o método `info()` corretamente.

Isso permite que nós predefinamos as propriedades antes mesmo do objeto ser construído! Por exemplo:

```php
$query = "SELECT id, first_name, last_name FROM users";

// PDO
$result = $pdo->query($query);
$result->setFetchMode(PDO::FETCH_CLASS, 'User');

while ($user = $result->fetch()) {
	echo $user->info() . "\n";
}

// MySQLi, forma procedural
if ($result = mysqli_query($mysqli, $query)) {
	while ($user = mysqli_fetch_object($result, 'User')) {
		echo $user->info() . "\n";
	}
}

// MySQLi, forma orientada a objetos
if ($result = $mysqli->query($query)) {
	while ($user = $result->fetch_object('User')) {
		echo $user->info() . "\n";
	}
}
```

## Segurança
![Consulta SQL identificando problema de segurança](https://cdn.tutsplus.com/net/uploads/legacy/2013_phpvsmysqli/tutorial_3.png "Consulta SQL identificando problema de segurança")

> Ambas as bibliotecas proveem **segurança contra injeção de SQL, desde que o desenvolvedor as use da maneira que elas foram planejadas para usar** (leia-se: limpando variáveis, vinculando parâmetros com sentenças preparadas).

Digamos que um *hacker* esteja tentando injetar algum código malicioso através de um parâmetro 'username' de uma requisição HTTP GET:

```php
$_GET['username'] = "'; DELETE FROM users; /*"
```

Se nós falharmos em limpá-lo, ele será incluído na consulta exatamente como está &ndash e deletará todas linhas da tabela `users` (tanto PDO quanto MySQLi suporta múltiplas consultas).

```php
// PDO, limpeza manual
$username = PDO::quote($_GET['username']);

$pdo->query("SELECT * FROM users WHERE username = $username");

// MySQLi, limpeza manual
$username = mysqli_real_escape_string($_GET['username']);

$mysqli->query("SELECT * FROM users WHERE username = '$username'");
```

Como pode ver, `PDO::quote()` **não só valida, mas fixa as aspas**. Por outro lado, `mysqli_real_escape_string()` só limpará, forçando você a fixar as aspas manualmente.

```php
// PDO com sentenças preparadas
$pdo->prepare('SELECT * FROM users WHERE username = :username');
$pdo->execute([':username' => $_GET['username']]);

// MySQLi, sentenças preparadas
$query = $mysqli->prepare('SELECT * FROM users WHERE username = ?');
$query->bind_param('s', $_GET['username']);
$query->execute();
```

> Recomendo que você sempre use sentenças preparadas com consultas vinculadas ao invés da `PDO::quote()` e da `mysqli_real_scape_string()`.

## Performance
Tanto PDO quanto MySQLi são bem rápidas, porém, MySQLi se sai, insignificantemente, mais rápida nos comparativos *ndash; ~2,5% para sentenças não preparadas e ~6,5% para as sentenças preparadas. Porém, a extensão nativa do MySQL é ainda mais rápida que ambas. Mas, como afirmei no começo, ela não é mais uma opção.

## Conclusão
No fim das contas, PDO ganha essa, e fácil. Com suporte a 12 drivers de bases de dados diferentes e parâmetros nomeados, nós podemos ignorar a pequena perda de performance que ela tem, comparada à MySQLi. Com relação à segurança, ambas são seguras, desde que o desenvolvedor saiba usá-las da forma correta.

> Então, se você ainda está usando MySQLi, talvez seja hora de mudar!