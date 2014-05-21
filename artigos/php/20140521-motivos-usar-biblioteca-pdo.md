Porque Você Deveria Usar a Biblioteca PDO para Acessar Bases de Dados
=====================================================================
Créditos<br/>
<small>Autor original: [Erik Wurzer](http://tutsplus.com/authors/erik-wurzer)<br/>[Artigo Original](http://code.tutsplus.com/tutorials/why-you-should-be-using-phps-pdo-for-database-access--net-12059)<br/>Nível: Básico-Intermediário</small>

Muitos programadores PHP aprenderam a acessar bases de dados usando as extensões MySQL ou MySQLi. Desde o PHP 5.1, há uma maneira muito melhor. Os [Objetos de Dados do PHP (PHP Data Objects - PDO)](http://www.php.net/manual/en/intro.pdo.php) provê métodos para *sentenças preparadas* e para trabalhar com objetos que permitirão você ser muito mais produtivo!

## Introdução ao PDO
> PDO &ndash; PHP Data Objects &ndash; é uma camada de acesso a base de dados que provê uma maneira uniforme de acessar bases de dados diferentes.

Isso não leva em consideração as sintaxes específicas das bases de dados, mas permite que o processo de mudança de bases de dados e plataformas seja, praticamente, sem problemas, simplesmente mudando os dados de conexão.

![PDO - Camada de acesso a base de dados](https://s3.amazonaws.com/nettuts/693_pdo/pdo-to-db.png "PDO - Camada de acesso a base de dados")

Esse tutorial não é um tutorial completo sobre SQL. Ele foi escrito, primariamente, para as pessoas que, atualmente, usam as extensões MySQL e MySQLi, para ajudá-las a mudar de hábito e passar a usar a mais poderosa e portátil, PDO.

## Suporte as Bases de Dados
Essa extensão dá suporte a qualquer base de dados para o qual o driver PDO tenha sido criado. Até o momento dessa tradução, os drivers de bases de dados a seguir estão disponíveis:

|Nome do driver| Bases de dados suportadas|
|--------------|--------------------------|
|PDO_CUBRID|CUBRID|
|PDO_DBLIB|FreeTDS / Microsoft SQL Server / Sybase|
|PDO_FIREBIRD|Firebird|
|PDO_IBM|IBM DB2|
|PDO_INFORMIX|IBM Informix Dynamic Server|
|PDO_MYSQL|MySQL 3.x/4.x/5.x|
|PDO_OCI|Oracle Call Interface|
|PDO_ODBC|ODBC v3 (IBM DB2, unixODBC e win32 ODBC)|
|PDO_PGSQL|PostgreSQL|
|PDO_SQLITE|SQLite 3 e SQLite 2|
|PDO_SQLSRV|Microsoft SQL Server / SQL Azure|
|PDO_4D|4D|

Todos esse drivers não estão, necessariamente, disponíveis em seu sistema. Eis uma maneira rápida para descobrir quais os drivers disponíveis:

```php
print_r(PDO::getAvailableDrivers());
```

## Conexão
Bases de dados diferentes podem ter métodos de conexão ligeiramente diferentes. Abaixo, os métodos para conectar-se às bases de dados mais populares são mostrados. Você percberá que as três primeiras são idênticas, a não ser pelo tipo da base de dados &ndash; e, então, a SQLite tem sua própria sintaxe.

![Sintaxe para conexão à base de dados via PDO](https://s3.amazonaws.com/nettuts/693_pdo/connection_string.png "Sintaxe para conexão à base de dados via PDO")

```php
try {
	# Conexão com MS SQL SERVER e Sybase usando PDO_DBLIB
	$DBH = new PDO("mssql:host=$servidor;dbname=$baseDeDados, $usuario, $senha");
	$DBH = new PDO("sybase:host=$servidor;dbname=$baseDeDados, $usuario, $senha");

	# Conexão com MySQL via PDO_MYSQL
	$DBH = new PDO("mysql:host=$servidor;dbname=$baseDeDados, $usuario, $senha");

	# Conexão com SQLite usando PDO_SQLITE
	$DBH = new PDO("sqlite:caminho/para/minha/base/de/dados/baseDeDados.db");
} catch (PDOException $e) {
	echo $e->getMessage();
}
```

Por favor, atente ao bloco de código do `try/catch` &ndash; você sempre deve envolver suas operações PDO em um bloco `try/catch` e usar o mecanismo de exceção. Logo mais saberá o porque disso. Geralmente, você só fará uma única conexão &ndash; somente listamos várias para poder exemplificar a sintaxe. `$DBH`, em inglês, significa *database handle*, que, em português seria "manipulador de base de dados". Nós usaremos essa nomenclatura pelo resto do tutorial.

Você pode encerrar a conexão de forma simples. Basta atribuir o valor `null` ao manipulador:

```php
# fecha a conexão
$DBH = null;
```

Você pode conseguir masi informações sobre opções ou itens de conexão específicos para cada base de dados, direto do [PHP.net](http://www.php.net/manual/en/pdo.drivers.php).

## Exceções e PDO
A PDO pode usar exceções para manipular erros, significando que, qualquer coisa que você fizer com PDO, deverá estar envolta em um bloco `try-catch`. Você pode forçar a PDO a acessar o banco em três diferentes modos, ao usar o atributo de modo de erro em seu manipulador de base de dados recém criado. Eis a sintaxe:

```php
$DBH->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_SILENT );
$DBH->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_WARNING );
$DBH->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
```

Independente do modo de erro que você indicar, um erro de conexão sempre produzirá uma exceção. É por isso que o código de criação de uma conexão sempre deve vir dentro de um bloco `try-catch`.

### PDO::ERRMODE_SILET
Esse é o modo de erro padrão. Se você o deixar nesse modo, você terá de checar os erros da maneira que você, provavelmente, está acostumado a fazer com as extensões MySQL e MySQLi. Os outros dois métodos são ideiais para programação DRY (do inglês, Don't Repeat Yourself &mdash; Não se repita).

### PDO::ERRMODE_WARNING
Esse modo lançará um aviso padrão do PHP e permitirá que o programa continue executando. É um método útil para depuração.

### PDO::ERRMODE_EXCEPTION
Esse é o modo que você deveria usar na maioria das situações. Ele lança uma exceção, permitindo que você lide com os erros de forma graciosa e esconda os dados que possa permitir alguém a tirar proveito, explorar seu sistema. Eis um exemplo lançando mão das exceções:

```php
# Conecta-se à base de dados
try {
  $DBH = new PDO("mysql:host=$servidor;dbname=$baseDeDados", $usuario, $senha);
  $DBH->setAttribute( PDO:ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );

  # Oh, não! Digitou DELECT ao invés de SELECT!
  $DBH->prepare('DELECT name FROM people');
} catch (PDOException $e) {
  echo 'Desculpe, cara. Sinto que não posso fazer isso';
  file_put_contents('PDOErrors.txt', $e->getMessage(), FILE_APPEND);
}
```

Há um erro intencional na consulta `select` acima. Esse erro causará uma exceção. A exceção envia os detalhes do erro para o arquivo de log, e mostrar uma mensagem amigável (talvez nem tão amigável) para o usuário.

## Inserção e Atualização
Inserir novos dados ou atualiza algum dado que já exista são das operações mais comuns em bases de dados. Usando PDO, isso costuma ser um processo de duas etapas. Tudo que explicarmos nessa seção servirá, igualmente, para as operações `INSERT` e `UPDATE`.

![Esquema do processo de inserção/atualização de dados](https://s3.amazonaws.com/nettuts/693_pdo/prepare-bind-execute.png "Esquema do processo de inserção/atualização de dados")

Veja um exemplo do tipo mais básico de inserção:

```php
# STH significa "Statement handle" ou "manipulador de sentença"
$STH->$DBH->prepare("INSERT INTO folks ( first_name ) values ( 'Cathy' )");
$STH->execute();
```

Você também poderia realizar essa operação através do método `exec()`, usando uma chamada de método a menos. Na maioria das situações, você usará o método mais long para que possa aproveitar das sentenças preparadas. Mesmo se você usa-la uma única vez, usar sentenças preparadas ajudará você a se proteger de ataques de injeção de SQL.

## Sentenças Preparadas
> Usar sentenças preparadas ajudará você a se proteger de injeções de SQL
