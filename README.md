<p align="center">
<br>Tópicos em Bancos de Dados  
<br>Processamento Massivo de Dados
<br>Profa. Sahudy Montenegro González </p>
<h1> NewSQL e Suporte à Replicação e Propriedades ACID </h1>

# Autores
- Andreza Silva Areão
- Bernardo Moreira Zabadal
- Edinei Gonçalves Figueiredo

# Sumário
- [Autores](#autores)
- [Sumário](#sumário)
- [Introdução](#1-introdução)
- [Como usar este tutorial](#2-como-usar-este-tutorial)
- [Visão geral sobre sgbds newsql](#3-visão-geral-sobre-sgbds-newsql)
 - [SQL Server](#31-sql-server)
 - [CockroachDB](#32-cockroachdb)
 - [Comparativo entre SQL Server e CcockroachDB](#33-comparativo-entre-sql-server-e-cockroachdb)
- [Instalação e importação base de dados](#4-instalação-e-importação-base-de-dados)
 - [CockroachDB](#41-cockroachdb)
 - [Etapas Iniciais](#411-etapas-iniciais)
  - [Criando e Iniciando os Nós](#412-criando-e-iniciando-os-nós)
   - [Criando os nós](#4121-criando-os-nós)
   - [Adicionar variáveis de ambiente ZONEINFO Windows](#4122-adicionar-variáveis-de-ambiente-zoneinfo-windows)
   - [Iniciando os Nós](#4123-iniciando-os-nós)
  - [Importação dos dados](#413-importação-dos-dados)
   - [Importar os dados da máquina local para o Docker](#4131-importar-os-dados-da-máquina-local-para-o-docker)
   - [Acesso ao banco de dados](#4132-acesso-ao-banco-de-dados)
    - [Criação da base de dados](#41321-criação-da-base-de-dados)
    - [Criação das tabelas e importando os dados](#41322-criação-das-tabelas-e-importando-os-dados)
  - [SQL Server](#42-sql-server)
   - [Importação dos dados](#421-importação-dos-dados)
- [Replicação](#5-replicação)
 - [CockroachDB](#51-cockroachdb)
 - [SQL Server](#52-sql-server)
- [Propriedades ACID](#6-propriedades-acid)
 - [CockroachDB](#61-cockroachdb)
 - [SQL Server](#62-sql-server)
- [Benchmarking CockroachDB x SQL Server](#7-benchmarking-cockroachdb-x-sql-server)
- [Conclusão](#8-conclusão)
- [Referências](#referências)
- [Anexos](#anexos)











# 1. INTRODUÇÃO

Este tutorial nasce de um projeto da disciplina Tópicos em Bancos de Dados - Processamento Massivo de Dados, ministrada pela professora Dra. Sahudy Montenegro González, na UFSCar, câmpus Sorocaba.
O objetivo é mostrar, de forma clara e prática, os principais pontos relacionados ao NewSQL e Suporte à Replicação e Propriedades ACID.
Os bancos de dados escolhidos foram o Microsoft SQL Server o CockroachDB, o primeiro por ser um dos mais utilizados no mercado corporativo e o segundo pela afinidade da equipe.
A base de dados definida para o projeto foi a NorthWind, base já utilizada em diversos tutoriais, cursos e exemplos, criada como exemplo no Microsoft Access e difundida até hoje.
Utilizamos como referências principais as documentações  oficiais dos sistemas escolhidos: COCKROACH LABS (EUA) - https://www.cockroachlabs.com/docs/stable/ e Microsoft SQL documentation - https://docs.microsoft.com/en-us/sql/?view=sql-server-ver15.


# 2. COMO USAR ESTE TUTORIAL

É possível fazer uma navegação sequencial, desde a instalação, importação dos dados e utilização dos bancos de dados apresentados, bem como ir diretamente ao ponto desejado clicando no sumário.

# 3. VISÃO GERAL SOBRE SGBDS NEWSQL

O paradigma de banco de dados mais conhecido é Banco de Dados Relacionais (BDRs), mas com o grande volume de dados gerados e necessários na atualidade surgiu o paradigma NoSQL, porém ele não trazia em seu escopo as propriedades ACID, entre outras características consideradas importantes para um Banco de Dados, primando pela escalabilidade e agilidade em consultas. Tentando unir o melhor dos dois mundos, NoSQL e BDRs, recentemente, surgiu o paradigma NewSQL. Apesar de ser um paradigma, cada sistema executa as operações de manipulação de dados de forma diferente.
Knob et al (2019) afirma que este paradigma surgiu com “o propósito de combinar os benefícios do paradigma relacional com o tratamento de Big Data do paradigma NoSQL”. O autor traz os sistemas NewSQL como soluções modernas, que visam ter o mesmo desempenho escalável dos BDs NoSQL para cargas de trabalho de Processamento de Transações Online e com suporte completo as propriedades ACID.
Fatores importantes a serem considerados em um banco de dados NewSQL são: controle de concorrência de esquema lock-free; e, arquitetura distribuída shared-nothing, Stonebraker (2012) apud KNOB et al (2019).


## 3.1 SQL SERVER

O sistema de gerenciamento de banco de dados relacional desenvolvido pela Microsoft é Microsoft SQL Server (MICROSOFT, 2020). Teve seu lançamento em 1989 e está na versão SQL Server 2019. É escrito em C e C ++. Tendo suporte aos sistemas operacionais: Linux, Microsoft Windows Server e Microsoft Windows.
No site da Microsoft, de venda de licenças, o SQL Server 2019 Standard Edition é apresentado com o marketing de que “pode ser o hub para seu acervo de dados, com a capacidade de consultar qualquer banco de dados, como Oracle, MongoDB, Cassandra e Hadoop, de um modo altamente seguro e com desempenho elevado”, conforme apresentado na figura a seguir. (https://www.microsoft.com/pt-br/store/b/software?icid=CNavSoftware).

![Propaganda Microsoft SQL Server](images/sqlServerProp.png)


Fonte: https://www.microsoft.com/pt-br/p/sql-server-2019-standard-edition/dg7gmgf0fkx9?icid=Cat-Professional-Software-Feature2-SQL-2019  


## 3.2 COCKROACHDB  

O CockroachDB é um banco de dados SQL distribuído, com código-fonte disponível gratuitamente, construído em um armazenamento de chave-valor transacional e fortemente consistente. Teve a sua primeira versão em 2015. É dimensionado horizontalmente; sobrevive a falhas de disco, máquina, rack e até mesmo de datacenter com interrupção de latência mínima e sem intervenção manual; suporta transações ACID fortemente consistentes; e fornece uma API SQL familiar para estruturar, manipular e consultar dados. O CockroachDB foi inspirado nas tecnologias Spanner e F1 do Google (COCKROACH LABS, 2020).
Segundo Knob et al (2019) é possível ter uma instância levantada em um computador pessoal comum e ajudar no processamento de requisições. E, não utiliza armazenamento final em memória principal, sendo feito o aproveitamento de uma estrutura de clocks atômicos para escrita de blocos, facilitando o suporte às características ACID nas transações.
O Cockroach Labs (2020) informa que distribui o CockRoachDB nas versões Core,  Enterprise e CockroachCloud, sendo a primeira gratuita.


## 3.3 COMPARATIVO ENTRE SQL SERVER E COCKROACHDB  

Trazemos a comparação das propriedades dos sistemas CockroachDB e Microsoft SQL Server, realizado pelo site DB-Engines, disponível no endereço https://db-engines.com/en/system/CockroachDB%3BMicrosoft+SQL+Server.

O CockroachDB é um banco de dados mais jovem, com cerca de 5 anos de desenvolvimento, enquanto o Microsoft SQL Server tem mais de 30 anos, o que lhe traz uma certa robustez e clientes já consolidados.
O CockroachDB permite a utilização em mais sistemas operacionais, tem mais suporte as linguagens de programação fora das linguagens da Microsoft.
O SQL Server tem mais APIs e outros métodos de acesso que o CockroachDB, que possui apenas o método JDBC. Além do SQL Server permitir Scripts do lado do servidor e Triggers enquanto o CockroachDB não.
O CockroachDB possui replicação de múltiplas fonte utilizando RAFT e o SQL Server oferece métodos de replicação dependendo da versão adquirida.


![Comparativo SQL Server e CockRoachDB](images/comparativo.png)


Fonte: https://db-engines.com/en/system/CockroachDB%3BMicrosoft+SQL+Server


# 4. INSTALAÇÃO E IMPORTAÇÃO BASE DE DADOS

Abordaremos os passos iniciais para a instalação e operação dos sistemas Microsoft SQL Server e CockRoachDB, bem como a importação da base de dados NorthWind.


## 4.1 COCKROACHDB

### 4.1.1 ETAPAS INICIAIS

Para iniciar os trabalhos, é preciso baixar o cockroachDB através do seguinte link:
https://www.cockroachlabs.com/docs/stable/install-cockroachdb-windows.html

OBS: Todas as versões do CockroachDB são distribuídas em formato binário (sem instalador).
Com o cockroackDB baixado, crie uma pasta chamada “CockroachDB” no disco C e copie o executável. Essa etapa é importante para garantir que os comandos do cockroach serão conhecidos pelo sistema sem nenhum tipo de problema.
Após essa etapa, é necessário baixar e instalar o Docker. Segue link com o instalador:
https://www.docker.com/products/docker-desktop
Com o Docker instalado, inicie o mesmo.
Com o Docker iniciado corretamente, reinicie a máquina (se necessário) e, após a reinicialização, realize o seguinte comando no CMD ou pelo POWERSHELL (como administrador):

```bash
docker pull cockroachdb/cockroach:v20.1.8
```

Esse comando realiza o download da BOX do cockroachDB dentro da pasta criada anteriormente. Caso o comando não funcione, verifique se o Docker foi instalado corretamente, se a pasta “CockroachDB” foi criada na raiz do disco C e se você está realizando o comando como administrador.
Com a box baixada corretamente, acesse o CMD ou o POWERSHELL (como administrador), e realize o seguinte comando:

```bash
cd C:\CockroachDB\cockroach-v20.1.8.windows-6.2-amd64
```

Esse comando realiza o acesso a pasta aonde a box do cockroachDB foi baixada. Verifique se a pasta que está sendo acessada está escrita corretamente. Após isso, para verificar que o cockroachDB está funcionando normalmente, realize o seguinte comando:

```bash
.\cockroach.exe version
```

Devemos ter como resposta a seguinte mensagem:

OBS: é necessário utilizar “.\” todas as vezes que algum comando do cockroach for utilizado no Windows (powershell ou CMD)
Com o docker e o Cockroach instalados corretamente, precisamos criar uma bridge, que vai facilitar a comunicação entre os contêineres. É possível fazer isso com o seguinte comando

```bash
docker network create -d bridge roachnet
```

OBS: roachnet é o nome da bridge e pode ser alterado. Caso o nome seja alterado, não esqueça de alterar ele quando realizar outros comandos.


### 4.1.2 CRIANDO E INICIANDO OS NÓS

##### 4.1.2.1 CRIANDO OS NÓS

Com a bridge criada, podemos iniciar a criação dos nós. Os 3 comandos abaixo criarão 3 nodes:

```bash
docker run -d --name=roach1 --hostname=roach1 --net=roachnet -p 26257:26257 -p 8080:8080 -v "C:\CockroachDB\cockroach-v20.1.8.windows-6.2-amd64/cockroach-data/roach1:/cockroach/cockroach-data"  cockroachdb/cockroach:v20.1.8 start --insecure --join=roach1,roach2,roach3

docker run -d --name=roach2 --hostname=roach2 --net=roachnet -p 26258:26258 -p 8081:8081 -v "C:\CockroachDB\cockroach-v20.1.8.windows-6.2-amd64/cockroach-data/roach2:/cockroach/cockroach-data" cockroachdb/cockroach:v20.1.8 start --insecure --join=roach1,roach2,roach3

docker run -d --name=roach3 --hostname=roach3 --net=roachnet -p 26259:26259 -p 8082:8082 -v "C:\CockroachDB\cockroach-v20.1.8.windows-6.2-amd64/cockroach-data/roach3:/cockroach/cockroach-data" cockroachdb/cockroach:v20.1.8 start --insecure --join=roach1,roach2,roach3
```

OBS: --name designa o nome do node, --hostname designa o nome da máquina, --net designa o nome da rede entre eles (no nosso caso colocamos o nome da bridge criada anteriormente), --p designa a porta que será utilizada para acesso no docker e o segundo --p é a porta utilizada para acesso local, --insecure determina que os nodes serão criados sem nenhum protocolo de segurança, não precisando criar certificados para que os mesmos se comuniquem, facilitando os testes (caso os nodes sejam aplicados em um ambiente real e não de testes, é fortemente recomendado a criação de certificados para validar a comunicação, evitando acessos externos indesejados), e --join é utilizado para unir as 3 máquinas, permitindo a comunicação entre elas.

OBS: verifique se os nomes das máquinas não estão em duplicidade, se as portas não estão em uso, se a box utilizada está correta, se a pasta que vai armazenar os nodes existe e possui permissão de escrita e leitura, se o parâmetro --insecure está sendo utilizado e se o comando está sendo executado como administrador.

#### 4.1.2.2 ADICIONAR VARIÁVEIS DE AMBIENTE ZONEINFO (WINDOWS)

Pode ser necessário adicionar esse arquivo para que o os nós iniciem normalmente. Para isso, realize os seguintes passos (Windows 10):

Baixar o arquivo ZONEINFO no seguinte link:
https://www.cockroachlabs.com/docs/dev/known-limitations.html#location-based-time-zone-names

Para adicionar esse arquivo dentro das variáveis de ambiente, siga este caminho (Windows 10):
Sistema 🡪 Variáveis de ambiente 🡪 novo 🡪 Adicionar um nome e o arquivo e salvar.
Caso seja necessário, reinicie a máquina.

#### 4.1.2.3 INICIANDO OS NÓS

Com os nós criados, precisamos iniciar os mesmos, para que possamos utilizar os nodes. Precisamos iniciar cada nó separadamente através desses comandos:

```bash
.\cockroach start --insecure --store=node1 --listen-addr=localhost:26257 --http-addr=localhost:8080 --join=localhost:26257,localhost:26258,localhost:26259

.\cockroach start --insecure --store=node2 --listen-addr=localhost:26258 --http-addr=localhost:8081 --join=localhost:26257,localhost:26258,localhost:26259

.\cockroach start --insecure --store=node3 --listen-addr=localhost:26259 --http-addr=localhost:8082 --join=localhost:26257,localhost:26258,localhost:26259
```

OBS1: o --insecure inicia os nós de forma insegura, não necessitando de qualquer certificado, --store é a pasta de armazenamento de cada nó, --listem-addr e http-addr são as portas que serão utilizadas para comunicação e acesso aos nodes.
OBS2: é possível iniciar os nós manualmente caso tenha problemas no Windows 10.

Com os comandos executados corretamente, é possível verificar através do Docker que os nodes estão funcionando ou pelo navegador, acessando a “http:addr”.


### 4.1.3 IMPORTAÇÃO DOS DADOS

#### 4.1.3.1 IMPORTAR OS DADOS DA MÁQUINA LOCAL PARA O DOCKER

Inicialmente, baixe os .CSV do banco de dados NORTHWIND no seguinte link e coloque todos esses arquivos em uma pasta:

https://github.com/graphql-compose/graphql-compose-examples/tree/master/examples/northwind/data/csv

Posteriormente, vá a “C:\CockroachDB” e crie a pasta “CSV_NorthWind”, descompacte o .rar e coloque todos os documentos dentro desta pasta.
Com os arquivos baixados, é necessário identificar os nomes dos nós com esse comando:

```bash
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Names}}" #(descreve os nomes dos nós e os organiza em formato de uma tabela)
```

O retorno esperado do comando deve ser assim, contendo o ID de cada node, a versão instalada de cada node e o nome de cada node:

Após essa etapa, é preciso acessar cada réplica, através do DOCKER nesse local:

Ao abrir o node, é necessário criar as seguintes pastas (essas pastas vão armazenar os arquivos .CSV). OBS: Pode ser necessário fazer essa etapa para todos os nós, caso a réplica não esteja funcionando adequadamente.

```bash
mkdir extern
cd extern
mkdir CSV_NorthWind
```

Agora, devemos copiar cada arquivo .CSV separadamente para dentro do node utilizando o Docker. OBS: Pode ser necessário fazer essa etapa para todos os nós, caso a réplica não esteja funcionando adequadamente.
O comando utilizado é o seguinte:

```bash
docker cp <path-file-host>/doc.csv <CONTAINER ID:/PATH-NEW-FILE>
```

OBS:  verificar se o nome do arquivo e seu local estão escritos corretamente, bem como se o ID do node e a pasta criada estão escritos corretamente. Os comandos podem ser executados de forma separada ou juntos. Se o comando for concluído sem erros, o CMD ou o POWERSHELL não vão retornar nenhuma linha de informação.

```bash
docker cp C:\CockroachDB\CSV_NorthWind\categories.csv 3cac3b4ec737:/cockroach/cockroach-data/extern/CSV_NorthWind/categories.csv

docker cp C:\CockroachDB\CSV_NorthWind\customers.csv 3cac3b4ec737:/cockroach/cockroach-data/extern/CSV_NorthWind/customers.csv

docker cp C:\CockroachDB\CSV_NorthWind\employee_territories.csv 3cac3b4ec737:/cockroach/cockroach-data/extern/CSV_NorthWind/employee_territories.csv

docker cp C:\CockroachDB\CSV_NorthWind\employees.csv 3cac3b4ec737:/cockroach/cockroach-data/extern/CSV_NorthWind/employees.csv

docker cp C:\CockroachDB\CSV_NorthWind\order_details.csv 3cac3b4ec737:/cockroach/cockroach-data/extern/CSV_NorthWind/order_details.csv

docker cp C:\CockroachDB\CSV_NorthWind\orders.csv 3cac3b4ec737:/cockroach/cockroach-data/extern/CSV_NorthWind/orders.csv

docker cp C:\CockroachDB\CSV_NorthWind\products.csv 3cac3b4ec737:/cockroach/cockroach-data/extern/CSV_NorthWind/products.csv

docker cp C:\CockroachDB\CSV_NorthWind\regions.csv 3cac3b4ec737:/cockroach/cockroach-data/extern/CSV_NorthWind/regions.csv

docker cp C:\CockroachDB\CSV_NorthWind\shippers.csv 3cac3b4ec737:/cockroach/cockroach-data/extern/CSV_NorthWind/shippers.csv

docker cp C:\CockroachDB\CSV_NorthWind\suppliers.csv 3cac3b4ec737:/cockroach/cockroach-data/extern/CSV_NorthWind/suppliers.csv

docker cp C:\CockroachDB\CSV_NorthWind\territories.csv 3cac3b4ec737:/cockroach/cockroach-data/extern/CSV_NorthWind/territories.csv
```

#### 4.1.3.2 ACESSO AO BANCO DE DADOS

Com os arquivos copiados corretamente, utilize este comando para acessar o banco de dados do node1 (roach1, ID 3cac3b4ec737) pelo POWERSHELL ou CMD (como administrador):

```bash
.\cockroach sql --host=localhost --port=26257 --insecure
```

###### 4.1.3.2.1 CRIAÇÃO DA BASE DE DADOS

Com o acesso ao banco de dados, podemos criar nosso banco para que possamos importar os arquivos .CSV. Crie e selecione o banco de dados com estes comandos:

```bash
    CREATE DATABASE NorthWind;
    USE NorthWind;
```


###### 4.1.3.2.2 CRIAÇÃO DAS TABELAS E IMPORTANDO OS DADOS

Após o banco ser criado, podemos criar as tabelas e inserir os dados. Como o arquivo .CSV já possui a estrutura da tabela, precisamos apenas importar os dados através do comando IMPORT TABLE, criando a tabela no processo. Abaixo segue a importação completa.
OBS: as tabelas devem ser importadas separadamente, cada uma com o seu arquivo. Verificar se os nomes dos campos estão corretos, se o tipo de dado inserido é correto e escolher qual será a chave primária de cada tabela. Também é importante, ao criar o nome da tabela, que ele seja parecido com o nome do arquivo .CSV, para evitar o erro de, por exemplo, criar uma tabela “empregado” e inserir os dados da tabela “produto”.

OBS2: esta etapa retornará o total de linhas cadastradas, verificar com o site que disponibiliza os arquivos .CSV se o total de linhas inseridas são idênticas ao número de linhas cadastradas

OBS3: pode ser necessário acessar cada arquivo .CSV e apagar a primeira linha. Esta linha contém os nomes das tuplas das tabelas, e pode causar erros quando o IMPORT TABLE for utilizado. O erro acontece na hora que os dados são transformados para o tipo de dados descritos então, por exemplo, pode haver erros quando um campo for INT e, na primeira linha, o nome da tabela é uma STRING.

```bash
IMPORT TABLE Categories (
	CategoryID INT PRIMARY KEY,
	CategoryName STRING NOT NULL,
	Description STRING NOT NULL,
	Picture STRING
	)
	CSV DATA ("nodelocal://1/CSV_NorthWind/categories.csv");

IMPORT TABLE Customers (
CustomerID STRING PRIMARY KEY,
CompanyName STRING NOT NULL,
ContactName STRING NOT NULL,
ContactTitle STRING NOT NULL,
Address STRING NOT NULL,
City STRING NOT NULL,
Region STRING,
Postalcode STRING NOT NULL,
Country STRING NOT NULL,
Phone STRING,
Fax STRING
)
CSV DATA (“nodelocal://1/CSV_NorthWind/customers.csv”);

IMPORT TABLE Regions (
RegionID INT PRIMARY KEY,
RegionDescription STRING NOT NULL
)
	CSV DATA (“nodelocal://1/CSV_NorthWind/regions.csv”);

IMPORT TABLE Shippers (
shipperID INT PRIMARY KEY,
companyName STRING NOT NULL,
phone STRING NOT NULL
)
	   CSV DATA (“nodelocal://1/CSV_NorthWind/shippers.csv”);

IMPORT TABLE Suppliers (
supplierID INT PRIMARY KEY,
companyName STRING NOT NULL,
contactName STRING NOT NULL,
contactTitle STRING NOT NULL,
address STRING NOT NULL,
city STRING NOT NULL,
region STRING,
postalCode STRING NOT NULL,
country STRING NOT NULL,
phone STRING NOT NULL,
fax STRING,
homePage STRING
)
	   CSV DATA (“nodelocal://1/CSV_NorthWind/suppliers.csv”);

IMPORT TABLE Territories (
territoryID STRING PRIMARY KEY,
territoryDescription STRING NOT NULL,
regionID BIGINT NOT NULL
)
CSV DATA (“nodelocal://1/CSV_NorthWind/territories.csv”);

IMPORT TABLE Employees (
EmployeeID INT PRIMARY KEY,
LastName STRING NOT NULL,
FirstName STRING NOT NULL,
Title STRING NOT NULL,
TitleOfCourtesy STRING NOT NULL,
birthDate DATE NOT NULL,
hireDate DATE NOT NULL,
address STRING NOT NULL,
city STRING NOT NULL,
region STRING,
postalCode STRING NOT NULL,
country STRING NOT NULL,
homePhone STRING NOT NULL,
extension INT NOT NULL,
photo STRING NOT NULL,
notes STRING NOT NULL,
reportsTo STRING,
photoPath STRING NOT NULL
)
CSV DATA (“nodelocal://1/CSV_NorthWind/employees.csv”);

IMPORT TABLE Order_details (
OrderID INT NOT NULL,
ProductID INT NOT NULL,
UnitPrice STRING NOT NULL,
Quantity INT NOT NULL,
Discount STRING NOT NULL
)
CSV DATA (“nodelocal://1/CSV_NorthWind/order_details.csv”);

IMPORT TABLE Orders (
OrderID INT PRIMARY KEY,
CustomerID STRING NOT NULL,
EmployeeID INT NOT NULL,
OrderDate  DATE NOT NULL,
RequiredDate DATE NOT NULL,
ShippedDate STRING NOT NULL,
shipVia INT NOT NULL,
freight STRING NOT NULL,
shipName STRING NOT NULL,
shipAddress STRING NOT NULL,
shipCity STRING NOT NULL,
shipRegion STRING,
shipPostalCode STRING NOT NULL,
shipCountry STRING NOT NULL
)
CSV DATA (“nodelocal://1/CSV_NorthWind/orders.csv”);

IMPORT TABLE Products (
ProductID INT NOT NULL,
ProductName STRING NOT NULL,
SuplierID INT NOT NULL,
CategoryID INT NOT NULL,
QuantityPerUnity STRING NOT NULL,
UnitPrice STRING NOT NULL,
UnitsInStock INT NOT NULL,
UnitsOnOrder INT NOT NULL,
ReorderLevel INT NOT NULL,
Discontinued INT NOT NULL
)
CSV DATA (“nodelocal://1/CSV_NorthWind/products.csv”);

IMPORT TABLE Employee_territories (
EmployeeID INT NOT NULL,
TerritoryID STRING NOT NULL
)
CSV DATA (“nodelocal://1/CSV_NorthWind/employee_territories.csv”);
```

Com o banco de dados criado e com todos os dados importados, realize alguns comandos para testar se os dados foram inseridos corretamente. Abaixo seguem alguns comandos que podem ser utilizados para este fim.

```bash
SHOW TABLES;
SELECT * FROM EMPLOYEES;
SELECT * FROM ORDERS;
SELECT * FROM ORDERS WHERE SHIPCOUNTRY='USA';
```


## 4.2 SQL SERVER

O seguinte link contém a box que será utilizada para o trabalho (Linux com SQL server):

https://hub.docker.com/r/microsoft/mssql-server-linux/

Para utilizar esta box, é necessário abrir o Docker e executar o seguinte comando:

```bash
docker pull microsoft/mssql-server-linux
```

Caso queira utilizar uma imagem mais atual, podemos utilizar o comando abaixo:

```bash
docker pull mcr.microsoft.com/mssql/servera
```

Iniciar uma instancia do docker com a imagem baixada:

```bash
docker run -d --name Homer -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=P@ssw0rd!' -p 1433:1433 microsoft/mssql-server-linux
```

OBS: Caso ocorra um erro nesta etapa, refazer o comando substituindo o password por um mais forte.

### 4.2.1 IMPORTAÇÃO DOS DADOS
Executar a instalação do Azure Data Studio através do link:
https://docs.microsoft.com/en-gb/sql/azure-data-studio/download-azure-data-studio?view=sql-server-2017

Conectar ao banco de dados, com as seguintes informações:

```bash
Server Name: localhost
Authentication Type: SQL Login
User name: sa
Password: <password>
Database Name: <default>
Server Group: <default>
```

Executar script de criação de banco, abaixo:

```bash
create database northwind;
```

Selecionar banco de dados northwind.
Executar script de criação de tabelas abaixo:


#### Nome: categories; Type: tabela; Schema: public; Owner: -; Tablespace:

```bash
create TABLE categories (
    category_id int NOT NULL PRIMARY KEY,
    category_name character varying(15) NOT NULL,
    description text,
    picture image
);
```

#### Nome: customer_demographics

```bash
CREATE TABLE customer_demographics (
    customer_type_id varchar(10) NOT NULL PRIMARY KEY,
    customer_desc text
);
```


#### Nome: customers

```bash
CREATE TABLE customers (
    customer_id varchar(10) NOT NULL PRIMARY KEY,
    company_name character varying(40) NOT NULL,
    contact_name character varying(30),
    contact_title character varying(30),
    address character varying(60),
    city character varying(15),
    region character varying(15),
    postal_code character varying(10),
    country character varying(15),
    phone character varying(24),
    fax character varying(24)
);
```

#### Nome: customer_customer_demo

```bash
CREATE TABLE customer_customer_demo (
    customer_id varchar(10) NOT NULL,
    customer_type_id varchar(10) NOT NULL,
    PRIMARY KEY (customer_id, customer_type_id),
    FOREIGN KEY (customer_type_id) REFERENCES customer_demographics,
    FOREIGN KEY (customer_id) REFERENCES customers
);
```

#### Nome: employees

```bash
create TABLE employees (
    employee_id int NOT NULL PRIMARY KEY,
    last_name varchar(20) NOT NULL,
    first_name varchar(10) NOT NULL,
    title varchar(30),
    title_of_courtesy varchar(25),
    birth_date date,
    hire_date date,
    address varchar(60),
    city varchar(15),
    region varchar(15),
    postal_code varchar(10),
    country varchar(15),
    home_phone varchar(24),
    extension varchar(4),
    photo image,
    notes text,
    reports_to smallint,
    photo_path character varying(255)
);
```


#### Nome: suppliers

```bash
CREATE TABLE suppliers (
    supplier_id smallint NOT NULL PRIMARY KEY,
    company_name character varying(40) NOT NULL,
    contact_name character varying(30),
    contact_title character varying(30),
    address character varying(60),
    city character varying(15),
    region character varying(15),
    postal_code character varying(10),
    country character varying(15),
    phone character varying(24),
    fax character varying(24),
    homepage text
);
```


#### Nome: products

```bash
CREATE TABLE products (
    product_id smallint NOT NULL PRIMARY KEY,
    product_name character varying(40) NOT NULL,
    supplier_id smallint,
    category_id int,
    quantity_per_unit character varying(20),
    unit_price real,
    units_in_stock smallint,
    units_on_order smallint,
    reorder_level smallint,
    discontinued integer NOT NULL
	FOREIGN KEY (category_id) REFERENCES categories,
	FOREIGN KEY (supplier_id) REFERENCES suppliers
);
```


#### Nome: region

```bash
create TABLE region (
    region_id int NOT NULL PRIMARY KEY,
    region_description varchar(20) NOT NULL
);
```


#### Nome: shippers

```bash
CREATE TABLE shippers (
    shipper_id smallint NOT NULL PRIMARY KEY,
    company_name character varying(40) NOT NULL,
    phone character varying(24)
);
```


#### Nome: orders

```bash
CREATE TABLE orders (
    order_id smallint NOT NULL PRIMARY KEY,
    customer_id varchar(10),
    employee_id int,
    order_date date,
    required_date date,
    shipped_date date,
    ship_via smallint,
    freight real,
    ship_name character varying(40),
    ship_address character varying(60),
    ship_city character varying(15),
    ship_region character varying(15),
    ship_postal_code character varying(10),
    ship_country character varying(15),
    FOREIGN KEY (customer_id) REFERENCES customers,
    FOREIGN KEY (employee_id) REFERENCES employees,
    FOREIGN KEY (ship_via) REFERENCES shippers
);
```


#### Nome: territories

```bash
create TABLE territories (
    territory_id varchar(20) NOT NULL PRIMARY KEY,
    territory_description varchar(50) NOT NULL,
    region_id int NOT NULL,
	FOREIGN KEY (region_id) REFERENCES region
);
```

#### Nome: employee_territories

```bash
CREATE TABLE employee_territories (
    employee_id int NOT NULL,
    territory_id character varying(20) NOT NULL,
    PRIMARY KEY (employee_id, territory_id),
    FOREIGN KEY (territory_id) REFERENCES territories,
    FOREIGN KEY (employee_id) REFERENCES employees
);
```


#### Nome: order_details

```bash
CREATE TABLE order_details (
    order_id smallint NOT NULL,
    product_id smallint NOT NULL,
    unit_price real NOT NULL,
    quantity smallint NOT NULL,
    discount real NOT NULL,
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (product_id) REFERENCES products,
    FOREIGN KEY (order_id) REFERENCES orders
);
```


#### Nome: us_states

```bash
CREATE TABLE us_states (
    state_id smallint NOT NULL PRIMARY KEY,
    state_name character varying(100),
    state_abbr character varying(2),
    state_region character varying(50)
);
```

Para popular o Banco de Dados utilizar o Script disponível no anexo.


# 5. REPLICAÇÃO

## 5.1 COCKROACHDB

Instalar minikube através do seguinte link:
https://minikube.sigs.k8s.io/docs/start/
Pode ser necessário instalar o Hyper V para que tudo funcione normalmente. Para fazer isso, realize o seguinte comando:

```bash
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V –All
```

Depois disso, reinicie a máquina e execute o seguinte comando (como administrador):

```bash
minikube start --no-vtx-check --alsologtostderr
```

OBS: O parâmetro --no-vtx-check pula a etapa de verificação de compatibilidade com o VTX da inter e o parâmetro --alsologtostderr exibe o log completo com todas as etapas realizadas na inicialização do contâiner. Pode ser útil caso seja necessário procurar por algum erro.
OBS2: Caso a instalação de erro, será necessário reiniciar a instalação. Primeiro é necessário deletar a vm que foi criada com o seguinte comando:

```bash
minikube delete
```

Com o kubernetes instalado corretamente, realize o seguinte comando para criar 3 pods:

```bash
kubectl create –f https://raw.githubusercontent.com/cockroachdb/cockroach/master/cloud/kubernetes/cockroachdb-statefulset.yaml
```

Depois realize esse comando para verificar se os pods foram criados corretamente:

```bash
kubectl get pods
```

a resposta deve ser essa

Depois, precisamos fazer com que os 3 nodes iniciem juntos em um mesmo cluster. Podemos fazer isso com o seguinte comando:

```bash
kubectl create -f https://raw.githubusercontent.com/cockroachdb/cockroach/master/cloud/kubernetes/cluster-init.yaml
```

Podemos verificar se o cluster foi criado corretamente com esse comando:

```bash
kubectl get job cluster-init
```

O resultado esperado é mostrado abaixo:


Use o comando abaixo para acessar ao SQL:

```bash
kubectl run cockroachdb -it --image=cockroachdb/cockroach:v20.2.3 --rm --restart=Never -- sql --insecure --host=cockroachdb-public
```

com acesso ao banco, podemos criar uma base de dados para realizar pequenos testes:

```bash
CREATE DATABASE bank;
CREATE TABLE bank.accounts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
      balance DECIMAL
  );
  ```
Com a base de dados criada, insira alguns dados para testar se tudo está funcionando:

```bash
INSERT INTO bank.accounts (balance)
  VALUES
      (1000.50), (20000), (380), (500), (55000);
SELECT * FROM bank.accounts;
```

O resultado esperado para esses comando deve ser parecido com isso:


Realize o seguinte comando para redirecionar as portas para a 8080 no host e permitir acesso ao node via navegador:

```bash
kubectl port-forward service/cockroachdb-public 8080
```
Vá ao navegador e entre em http://localhost:8080/ e verifique se você tem acesso ao cluster e se existem 3 nodes.
Também é possível verificar se os 3 clusters estão criados com esse comando:

```bash
kubectl get pods
```

Para aumentar a quantidades de réplicas ativas, utilize esse comando:

```bash
kubectl scale statefulset cockroachdb --replicas=5
```

Agora realize esse comando para verificar se os 5 clusters estão ativos e funcionando normalmente:
```bash
kubectl get pods
```
Com os 5 clusters funcionando, tente deletar um deles ( o quarto) com esse comando:

```bash
kubectl delete pod cockroachdb-4
```

É possível perceber que o pod é deletado e restaurado depois de poucos segundos. Isso ocorre pois foi definido anteriormente que devem existir 5 réplicas ativas. Assim, mesmo deletando um pod, o cockroach vai criar este pod novamente, já que a quantidade mínima aceitável, neste caso, são de 5 réplicas ativas.

Agora, depois do teste realizado, realize este comando para tornar como padrão, novamente, a quantidade de réplicas ativas em 3:

```bash
kubectl scale statefulset cockroachdb --replicas=3
```

Verifique se 2 dos 5 pods anteriores foram excluídos com o seguinte comando:

```bash
kubectl get pods
```

Ao utilizar o comando, será possível perceber que os 2 últimos pods estão sendo encerrados. Isso acontece pois agora a quantidade mínima de réplicas são de apenas 3, e como o banco nao precisa das 5 réplicas para funcionar normalmente, as 2 réplicas que estão sobrando são excluídas automaticamente.

## 5.2 SQL SERVER

Instalar pacotes necessários para que o Docker engine funcione corretamente:

```bash
sudo yum install yum-utils
```

Adicionar o repositório ao sistema:

```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

Instalar o Docker Engine:

```bash
sudo yum install  -y docker-ce
```

Executar os Daemons do Docker e ativar o serviço na inicialização do sistema:

```bash
sudo systemctl start docker
sudo systemctl enable docker
```


Adicionar o usuário atual logado no grupo de usuário Docker:

```bash
sudo gpasswd -a "${USER}" docker
```

Com isso realizado, reinicie o sistema:

```bash
sudo reboot
```

Após o sistema iniciar normalmente, utilize esse comando para testar o Docker:

```bash
docker ps -a
```

Instalar o pacote do repositório EPEL (versão 8):

```bash
sudo rpm -Uvh http://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm
```

Instalar os pacotes do DRDB:

```bash
sudo yum install drbd90-utils kmod-drbd90
```

Ativar o carregamento do módulo do kernel durante o boot:

```bash
sudo chmod 777 -R /etc/modules-load.d/drbd.conf

echo drbd > /etc/modules-load.d/drbd.conf
```

Instalar pacotes necessários para utilizar a ferramenta drbdmanage:

```bash
yum install wget pygobject2 -y
wget http://www.linbit.com/downloads/drbdmanage/drbdmanage-0.99.14.tar.gz
tar -xvzf drbdmanage-0.99.14.tar.gz -C /opt/
cd /opt/drbdmanage-0.99.14/
./setup.py build
./setup.py install
```

Alterar o nome padrão para containersData:

```bash
sed -r ‘s/^#(.*drbdctrl-vg.*)$/\1/’ -i /etc/drbdmanaged.cfg
sed ‘s/drbdpool/containersData\n/g’ -i /etc/drbdmanaged.cfg
```

Criar partição primária na segunda unidade de disco:

```bash
pvcreate /dev/sdb1
```

Criar um volume físico e um volume lógico chamado containersData:

```bash
vgcreate containersData /dev/sdb1
```

Criar um par de chaves para permitir comunicação via SSH:

[containernode01]

```bash
ssh-keygen -t rsa
scp .ssh/id_rsa.pub root@192.168.0.162:/root/.
```

[containernode02]

```bash
ssh-keygen -t rsa
scp .ssh/id_rsa.pub root@192.168.0.161:/root/.
cat id_rsa.pub >> .ssh/authorized_keys
```

[containernode01]

```bash
cat id_rsa.pub >> .ssh/authorized_keys
```

Editar o arquivo /etc/hosts nos dois nodes:

```bash
192.168.0.161 containernode01 containernode01.lab.local
192.168.0.162 containernode02 containernode02.lab.local
```

Testar se a comunicação está funcionando:

[containernode01]

```bash
ssh 192.168.0.162 hostname
ssh containernode02 hostname
```

OBS: devemos ter como resposta de ambos os comandos o ip 192.168.0.162.

Testar se a comunicação está funcionando:

[continernode02]

```bash
ssh 192.168.0.161 hostname
ssh containernode01 hostname
```

OBS: devemos ter como resposta de ambos os comandos o ip 192.168.0.161.

Com os nodes conversando entre si, podemos acessar o drdbmanage com o seguinte comando (no containernode01):

```bash
drbdmanage init 192.168.0.161
```

Verificar se os 2 volumes DRDB foram criados:

```bash
drbdadm status
```

Associar o segundo nó ao cluster (containernode01):

```bash
drbdmanage add-node containernode02.lab.local 192.168.0.162
```

Listar nós:

```bash
drbdmanage list-nodes
```

Verificar o status da replicação:

```bash
drbdadm status
```

Em ambos os nós do cluster, realizar o download desses plugins:

```bash
wget http://www.linbit.com/downloads/connectors/drbdmanage-docker-volume-0.7.tar.gz
tar -xvzf drbdmanage-docker-volume-0.7.tar.gz -C /opt/
cd /opt/drbdmanage-docker-volume-0.7/
make
make install
wget https://raw.githubusercontent.com/LINBIT/drbdmanage-docker-volume/master/systemd/docker-drbdmanage-plugin.service
mv docker-drbdmanage-plugin.service /etc/systemd/system/multi-user.target.wants/.
systemctl daemon-reload
systemctl start docker-drbdmanage-plugin
systemctl status docker-drbdmanage-plugin
```

Criar um volume de dados (containernode01):

```bash
docker volume create -d drbdmanage –name=volumeDatabase  –opt fs=ext4 –opt size=400
docker volume ls
lvs
```

Criar um container para executar o Microsoft SQL Server:

```bash
docker run –name SQLServer01 -e ‘ACCEPT_EULA=Y’ \
-e ‘SA_PASSWORD=#AdminSQLServer2017’  \
-p 1433:1433  -v volumeDatabase:/var/opt/mssql  \
-d microsoft/mssql-server-linux
```

Verificar se o container está em execução:

```bash
docker ps -a
```

Verificar se as propriedades dos volumes estão corretas:

```bash
docker volume inspect volumeDatabase
```

Confirmar que o volume está sob um device de replicação em bloco DRBD:

```bash
mount | grep volumeDatabase
```

Listar os datafiles do serviço do SQL Server:

```bash
ls /var/run/docker/drbdmanage/mnt/volumeDatabase/
```

executar o sqlcmd:

```bash
docker exec -it SQLServer01 /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P#AdminSQLServer2017
```

# 6. PROPRIEDADES ACID

ACID é a sigla utilizada para expressar um conjunto de propriedades de transação em banco de dados, como Atomicidade, Consistência, Isolamento e Durabilidade, do inglês: Atomicity, Consistency, Isolation, Durability.

## 6.1 COCKROACHDB

Testar Atomicidade:

```bash
Begin Transaction
INSERT INTO Shippers VALUES (100, “Boituva”, 551533333333);
UPDATE Shippers set phone = 5515666666666 where shipperID = 100;
COMMIT
```

Testar Durabilidade:

```bash
SELECT *FROM Shippers;
```

## 6.2 SQL SERVER

Atomicidade

```bash
SELECT * from categories;
insert into categories values (1,'Fish','Fish','\x');
```

Consistência

```bash
SELECT * from customers;
delete from customers
where customer_id = 'ALFKI';
```


Isolamento e Durabilidade

```bash
SELECT * from shippers;
delete from shippers
where shipper_id=7;
insert into shippers values(7,'Correios','3003-0100');
```

# 7. BENCHMARKING COCKROACHDB X SQL SERVER

Não foi encontrado na literatura um benchmarking entre o CockroachDB e SQL Server. Nos benckmarking encontrados entre alguns outros sistemas NewSQL e o CockroachDB este ficou nas últimas posições.


# 8. CONCLUSÃO
Nota-se que o CockroachDB nasceu "NewSQL", por este motivo traz as funcionalidades e características esperadas para um sistema de banco de dados neste paradigma, já o SQL Server é um sistema antigo, que vem se ajustando ao longo de sua história para atender os requisitos do mercado em que atua.
Os clientes SQL Server analisando o custo-benefício de uma troca de banco de dados e muitas vezes preferem ficar com o sistema atual, com isto a Microsoft tem um público considerável, disponibilizando suporte e atualizações para atender a este novo paradigma.
 

# REFERÊNCIAS

- COCKROACH LABS (EUA). CockroachDB   Docs. New York: Cockroach Labs, 2020. Disponível em: https://www.cockroachlabs.com/docs/stable/. Acesso em: 21  dez. 2020.
- KNOB, Ronan; SCHREINER, Geomar; FROZZA, Angelo; MELLO, Ronaldo dos Santos. Uma Análise de Soluções NewSQL. In: ESCOLA REGIONAL DE BANCO DE DADOS (ERBD), 15.2019, Chapecó. Anais [...]. Porto Alegre: Sociedade Brasileira de Computação, 2019 . p. 21-30. ISSN 2595-413X. DOI: https://doi.org/10.5753/erbd.2019.8475.
- MICROSOFT. Microsoft SQL documentation. [S. I.]: Microsoft, 2020. Disponível em: https://docs.microsoft.com/en-us/sql/?view=sql-server-ver15. Acesso em: 18 out. 2020.




# ANEXOS

Anexo Código completo

Anexo Glossário
