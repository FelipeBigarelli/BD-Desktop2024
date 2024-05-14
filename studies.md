# Introdução à conectividade Java com Banco de Dados usando JDBC
- BD relacional: armazena dados em tabelas
- Linha/coluna: registro/campo

## Tipos de dados SQL x Java
- SQL / JAVA
  - INTEGER or INT / int
  - REAL / float
  - DOUBLE / double
  - DECIMAL(m, n) / m.n
  - BOOLEAN / boolean
  - VARCHAR(n) / Variable length String of length up to N
  - CHARACTER(n) or CHAR(n) / Fixed length String of length  N

***

## SQL
- Maioria dos bd relacionais: segue padrão SQL
- Exemplo comando SQL:
  - CREATE TABLE Product (
      Product_Code CHAR(7),
      Description VARCHAR(40),
      Price FLOAT
  )

- SQL é case insensitive, mas o padrão é:
  - Maiúsculo para palavras SQL
  - Misto para nomes de tabelas e colunas
***

## JDBC - Java Database Connectivity
- java.sql.*
- Provê acesso ao bd a partir de um programa, utilizando drivers
- Bds diferentes requerem drivers diferentes
- Programa envia comandos SQL -> driver os encaminha para o bd, permitindo o programa receber os resultados:
  - Java Program <-> JDBC Driver <-> Database Server <-> Database Tables
*** 

## Programação com banco de dados em Java
- **Inteface Connection**: define a estrutura para o acesso ao BD a partir da programação Java
- **Classe DriveManager**: método estático **getConnection** que implementa a interface Connection e retorna objetos dessa interface

- Java 6: carrega driver automaticamente. Caso contrário:
  - Class.forName("driver"); // carrega o "driver"

- A classe **DriveManager** implementa os métodos:
  - getConnection(String url)
  - getConnection(String url, Properties info)
  - getConnection(String url, String user, String password)

- Esses métodos podem ser usados para criar o objeto de conexão. Ex:
  - String url = jdbc:postgresql://localhost:5432/postgres;
  - String username = "postgres";
  - String password = "postgres";
  - Connection conn = DriverManager.getConnection(url, username, password);
  - conn.setAutoCommit(false);

- Com a conexão definida, o objeto **conn** é usado para criar objetos da interface **Statement** a partir do método:
  - createStatement()
  - createStatement(int resultSetType, int resultSetConcurrency)

- Objetos da inteface **Statement**: necessários para executar comandos SQL
- Objetos **Statement** criados usando método sem parâmetro serão, por padrão, do tipo **TYPE_FORWARD_ONLY** e terão nível de concorrência **CONCUR_READ_ONLY**
  - TYPE_FORWARD_ONLY: define o tipo de ResultSet e só pode ser percorrido para frente
  - CONCUR_READ_ONLY: define o nível de concorrência do ResultSet como "read-only" -> não pode atualizar linhas do conjunto de resultados deste.
    - usado para consultas de leitura simples
  
- Statement stat = conn.createStatement(int tipo, int concorrencia)
  - TIPO:
    - ResultSet.TYPE_FORWARD_ONLY,
    - ResultSet.TYPE_SCROLL_INSENSITIVE: percorre p frente ou p trás. Insensível a alterações externas -> quaisquer alterações no BD após execução da consulta não reflete no ResultSet
    - ResultSet.TYPE_SCROLL_SENSITIVE: percorre p frente ou p trás. Sensível a alterações feitas no BD e reflete no ResultSet
  
  - CONCORRÊNCIA:
    - ResultSet.CONCUR_READ_ONLY
    - ResultSet.CONCUR_UPDATABLE

- Essas constantes são usadas ao criar um Statement. Ex:
  - Statement stmt = connection.createStatement(ResultSet.TYPE_FORWARD_ONLY, ResultSet.CONCUR_READ_ONLY);

- **Statement** possui 3 métodos principais:
  - boolean execute(String sql):
    - executa instrução DDL (Data Definition Language): CREATE, ALTER ou DROP, com retorno booleano que indica se foi executado com sucesso

  - int executeUpdate(String sql):
    - executa instrução DML (Data Manipulation Language): INSERT, UPDATE ou DELETE. Retorna um int -> indica quantidade de linhas (registros) alterados no bd pela execução da instrução SQL

  - ResultSet executeQuery(String sql):
    - executa instrução DQL (Data Query Language): SELECT -> retorno é um objeto ResultSet

- **ResultSet**: objeto que mantém conjunto de dados retornados por uma consulta SQL
  - objeto possui métodos para navegar entre dados:
    - first(), next(), previous() e last()

- Para recuperar dados de um objeto ResultSet, existem métodos que retornam o valor da coluna de acordo com seu valor: número, string, data, etc
- Para cada tipo de dado -> 2 métodos:
  - Primeiro: recebe parâmetro inteiro (posição da coluna)
  - Segundo: recebe parâmetro String (nome da coluna)
  - Ex: 
    - String productCode = resultset.getString(1);
    - String productCode = resultset.getString("product_code");
    - int quantity = resultset.getInt("quantity");
    - double price = resultset.getDouble("price");
***

## Tratamento de Exceções
- SQLException: exceção padrão para possíveis erros de acesso ao BD
- ClassNotFoundException: tratamento de possível erro na identificação e carregamento do driver de acesso ao BD