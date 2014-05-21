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