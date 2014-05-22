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

### Sentenças Preparadas
> Usar sentenças preparadas ajudará você a se proteger de injeções de SQL

Uma sentença preparada é uma sentença SQL precompilada que pode ser usada inúmeras vezes, enviando somente os dados para o servidor. Elas tem a vantagem de, automaticamente, tornar seguros, em relação a ataques de injeção SQL, os dados usados nos espaços reservados.

Você faz uso de uma sentença preparada ao incluir espaços reservados em suas SQL. Logo abaixo, temos três exemplos: uma sem espaços reservados, uma com espaços sem nomes e a última com espaços nomeados.

```php
# sem espaços reservados - pronto para injeção de SQL!
$STH = $DBH->prepare("INSERT INTO folks (name, addr, city) VALUES ($name, $addr, $city)");

# espaços reservados sem nomes
$STH = $DBH->prepare("INSERT INTO folks (name, addr, city) VALUES (?, ?, ?)");

# espaços reservados com nomes
$STH = $DBH->prepare("INSERT INTO folks (name, addr, city) VALUES (:name, :addr, :city)");
```

Você deve evitar o primeiro método, uma vez que está aqui só para comparação. A escolha por espaços reservados com ou sem nome afetará como você atribui os dados em suas sentenças.

### Espaços Reservados Sem Nome
```php
# atribui as variáveis a cada espaço reservado, usando índices de 1 a 3
$STH->bindParam(1, $name);
$STH->bindParam(2, $addr);
$STH->bindParam(3, $addr);

# insere um novo registro
$name = "Daniel";
$addr = "Rua São João";
$city = "Teresina";
$STH->execute();

# insere outro registro, com outros valores
$name = "Jonas";
$addr = "Rua Albertão";
$city = "Rio de Janeiro";
$STH->execute();
```

Há dois passos aqui. Primeiro, nós indicamos os vários espaços reservados (linhas 2 a 4). Depois, atribuimos valores a esses espaços reservados e executamos as sentenças. Para enviar outro conjunto de dados, basta modificar os valores dessas variáveis e executar a sentença novamente.

Isso parece um pouco difícil de lidar quando se tem vários parâmetros? E é! Entretanto, se seus dados são guardados em um vetor, há um atalho:

```php
# os dados que queremos inserir
$dados = ["Cathy", "Rua Borboleta Mecânica", "Lugar nenhum"];

$STH = $DBH->prepare("INSERT INTO folks (name, addr, city) VALUES (?, ?, ?)");
$STH->execute($data);
```

Tão simples!

Os dados no vetor devem estar na ordem que serão aplicados aos espaços reservados. `$dados[0]` irá para o primeiro espaço reservado, `$data[1]` para o segundo e assim por diante. Entretanto, se os índices do seu vetor não estão em ordem, isso não funcionará apropriadamente e precisará reordenar o vetor.

### Espaços Reservados Romeados
Você, provavelmente, já deve imaginar a sintaxe, mas aqui vai um exemplo:

```php
# O primeiro argumento é o espaço reservado nomeado
# Atente: espaços reservados nomeados sempre começam com o `:` (dois pontos)
$STH->bindParam(':name', $name);
```

Você pode usar o atalho aqui, também, mas ele funciona com vetores associativos. Veja o exemplo:

```php
# Os dados que queremos inserir
$dados = [
  'name' => 'Cathy',
  'addr' => 'Rua Borboleta Mecânica',
  'city' => 'Lugar Nenhum'
];

# O atalho!
$STH = $DBH->prapre("INSERT INTO folks (name, addr, city) VALUES (:name, :addr, :city)");
$STH->execute($data);
```

As chaves do seu vetor não precisam começar com `:`, mas precisam combinar com o nome dos espaços reservados. Se você tem um vetor de vetores, pode iterá-los e, simplesmente, chamar o método `execute()` para cada vetor de dados.

Outra característica interessante dos espaços reservados nomeados é a habilidade de inserir objetos, diretamente, em sua base de dados, assumindo que as propriedades do objeto combinam com os campos nomeados. Eis um exemplo de um objeto e de como faria para inseri-lo na base de dados:

```php
# Um simples objeto
class Pessoa {
  public $name;
  public $addr;
  public $city;

  public function __construct($name, $addr, $city)
  {
    $this->name = $name;
    $this->addr = $addr;
    $this->city = $city;
  }

  # etc ...
}

$cathy = new Pessoa('Cathy', 'Rua Borboleta Mecânica', 'Lugar Nenhum');

# Eis a parte legal
$STH = $DBH->prepare("INSERT INTO folks (name, addr, city) VALUES (:name, :addr, :city)");
$STH->execute((array)$cathy);
```

Ao converter o objeto em uum vetor dentro da execução, as propriedades são tratadas como chaves de um vetor.

## Selecionando Dados
![Selecionando dados com PDO](https://s3.amazonaws.com/nettuts/693_pdo/fetch_obj_array.png "Selecionando dados com PDO")

Dados são retornados pelo método `fetch()`. Antes de chamá=lo, é melhor apontar ao PDO qual o tipo de retorno que você prefere. Você tem as seguintes opções:

- **PDO::FETCH_ASSOC**: retorna um vetor com índices associados aos nomes das colunas da tabela;
- **PDO::FETCH_BOTH (default)**: retonra um vetor com índices númericos e índeces associados aos nomes das colunas da tabela;
- **PDO::FETCH_BOUND**: Associa os valores de suas colunas às variáveis atribuídas com o método `binColumnd()`;
- **PDO::FETCH_CLASS**: Associa os valores das colunas da tabela às propriedades da classe nomeada. Proprieades serão criadas se não houver propriedades combinantes;
- **PDO::FETCH_INTO**: Atualiza a instância de uma classe nomeada;
- **PDO::FETCH_LAZY**: Combina **PDO::FETCH_BOTH/PDO::FETCH_OBJ**, criando os nomes das propriedades do objetos de acordo com que elas forem usadas;
- **PDO::FETCH_NUM**: retorna um vetor com índices numéricos;
- **PDO::FETCH_OBJ**: retorna um objeto anônimo com nomes de propriedades que correspondem aos nomes das colunas da tabela;

Na verdde, há três opções que cobrem a maioria das situações: FETCH_ASSOC, FETCH_CLASS e FETCH_OBJ. Para indicar o método de retorno, a sintaxe abaixo é usada:

```php
$STH->setFetchMode(PDO::FETCH_ASSOC);
```

### FETCH_ASSOC
Esse tipo de busca cria um vetor associativo, indexado por nome de coluna. Ele deve ser bem familiar para qualquer pessoa que já usou as extensões MySQL/MySQLi. Eis um exemplo de como selecionar dados com esse método:

```php
# usando o método atalho `query()`, uma vez que não há valores
# para preparar nessa seleção
$STH = $DBH->query("SELECT name, addr, city FROM folks");

# Definindo o tipo de busca/retorno
$STH->setFetchMode(PDO::FETCH_ASSOC);

while($row = $STH->fetch()) {
  echo $row['name'] . '\n';
  echo $row['addr'] . '\n';
  echo $row['city'] . '\n';
}
```

Essa repetição passará por todo os resultados, um de cada vez.

### FETCH_OBJ
Esse tipo de busca cria um objeto do tipo `STDCLASS` para cada registro retornado. Eis um exemplo:

```php
# criando a sentença
$STH = $DBH->query("SELECT name, addr, city FROM folks");

# Definindo o tipo de busca/retorno
$STH->setFetchMode(PDO::FETCH_OBJ);

# Mostrando os resultados
while ($row = $STH->fetch()) {
  echo $row->name . "\n";
  echo $row->addr . "\n";
  echo $row->city . "\n";
}
```

### FETCH_CLASS
> As propriedades do seu objeto são atribuidas ANTES do construtor ser chamado. Isso é importante.

Esse modo permite você buscar dados diretamente em uma classe de sua escolha. Quando você usa o `FETCH_CLASS`, as propriedades do seu objeto são atribuidas ***ANTES** do construtor ser chamado. Leia novamente, isso é importante. Se qualquer campo da tabela não tiver uma propriedade equivalente, será criado uma propriedade pública na classe para você.

Isso significa que se seus dados precisam de qualquer transformação depois que eles vem da base de dados, essa transformação pode ser feita automaticamente pelo seu objeto de acordo assim que cada um deles é criado.

Como um exemplo, imagina uma situação onde o endereço precisa ser modificado para cada registro. Nós poderíamos fazer isso, alterando essa propriedade dentro do método construtor. Veja um exemplo:

```php
class PessoaSecreta {
  public $name;
  public $addr;
  public $city;
  public $other_data;

  public function __construct($other = '')
  {
    $this->address = preg_replace('/[a-z]/', 'x', $this->address);
    $this->other_data = $other;
  }
}
```

Assim que um dos registro é vinculado a essa classe (lembre, uma classe é uma espécie de módelo/forma para objetos), o endereço tem todos seus caracteres alfabéticos substituídos pela letra *x*. Agora, usar essa classe e fazer com que os dados sejam transformados é totalmente transparente:

```php
$STH = $DBH->query("SELECT name, addr, city FROM folks");
$STH->setFetchMode(PDO::FETCH_CLASS, 'PessoaSecreta');

while ($obj = $STH->fetch()) {
  echo $obj->addr;
}
```

Se o endereço era "Rua Borboleta 5", você verá "Rxx Bxxxxxxx 5" como resultado. Claro, há inúmeras outras situações onde você pode querer que o método construtor seja chamado antes dos dados serem vinculados. A PDO também permite isso:

```php
$STH->setFetchMode(PDO::FETCH_CLASS | PDO::FETCH_PROPS_LATE, 'PessoaSecreta');
```

Agora, quando você repetir o exemplo anterior, usando esse modo (PDO::FETCH_PROPS_LATE), o endereço não será obscurecido, uma vez que o construtor será chamado e as propriedades só depois serão atribuidas.

Finalmente, se você realmente precisar, você pode passar argumentos para o método construtor enquanto busca os dados e passa-os para objetos usando PDO:

```php
$STH->setFetchMode(PDO::FETCH_CLASS, 'PessoaSecreta', ['dados']);
```

Se precisar passar ddos diferentes para cada objeto, você pode atribuir o tipo de busca/retorno dentro do método `fetch()`:

```php
$i = 0;
while ($registroObj = $STH->fetch(PDO::FETCH_CLASS, 'PessoaSecreta', [$i])) {
  // Realize as operações
  $i ++;
}
```

## Outros Métodos Úteis
