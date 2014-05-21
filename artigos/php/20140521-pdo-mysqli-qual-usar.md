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
