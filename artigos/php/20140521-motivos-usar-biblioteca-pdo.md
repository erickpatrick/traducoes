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