# newsql_replicacao_acid
<p align="center">
<br>Tópicos em Bancos de Dados  
<br>Processamento Massivo de Dados
<br>Profa. Sahudy Montenegro González </p>
<h1> NewSQL e Suporte à Replicação e Propriedades ACID </h1>

## Autores
-Andreza Silva Areão 
-Bernardo Moreira Zabadal 
-Edinei Gonçalves Figueiredo 

## Sumário
- [Autores](#autores)
- [Sumário](#sumário)
- [Introdução](#introdução)
- [Como usar este tutorial](#como-usar-este-tutorial)
- [Cisão geral sobre sgbds newsql](#visão-geral-sobre-sgbds-newsql)
 - [SQL Server](#1-sql-server)
 - [CockroachDB](#1-cockroachdb)
 - [Comparativo entre SQL Server e CcockroachDB](#comparativo-entre-sql-server-e-cockroachdb)
- [Instalação e importação base de dados](#instalação-e-importação-base-de-dados)
 - [CockroachDB](#2cockroachdb)






## INTRODUÇÃO

Este tutorial nasce de um projeto da disciplina Tópicos em Bancos de Dados - Processamento Massivo de Dados, ministrada pela professora Dra. Sahudy Montenegro González, na UFSCar, câmpus Sorocaba.
O objetivo é mostrar, de forma clara e prática, os principais pontos relacionados ao NewSQL e Suporte à Replicação e Propriedades ACID.
Os bancos de dados escolhidos foram o Microsoft SQL Server o CockroachDB, o primeiro por ser um dos mais utilizados no mercado corporativo e o segundo pela afinidade da equipe.
A base de dados definida para o projeto foi a NorthWind, base já utilizada em diversos tutoriais, cursos e exemplos, criada como exemplo no Microsoft Access e difundida até hoje.

## COMO USAR ESTE TUTORIAL

É possível fazer uma navegação sequencial, desde a instalação, importação dos dados e utilização dos bancos de dados apresentados, bem como ir diretamente ao ponto desejado clicando no sumário.

## VISÃO GERAL SOBRE SGBDS NEWSQL

O paradigma de banco de dados mais conhecido é Banco de Dados Relacionais (BDRs), mas com o grande volume de dados gerados e necessários na atualidade surgiu o paradigma NoSQL, porém ele não trazia em seu escopo as propriedades ACID, entre outras características consideradas importantes para um Banco de Dados, primando pela escalabilidade e agilidade em consultas. Tentando unir o melhor dos dois mundos, NoSQL e BDRs, recentemente, surgiu o paradigma NewSQL.Apesar de ser um paradigma, cada sistema executa as operações de manipulação de dados de forma diferente. 
Knob et al (2019) afirma que este paradigma surgiu com “o propósito de combinar os benefícios do paradigma relacional com o tratamento de Big Data do paradigma NoSQL”. O autor traz os sistemas NewSQL como soluções modernas, que visam ter o mesmo desempenho escalável dos BDs NoSQL para cargas de trabalho de Processamento de Transações Online e com suporte completo as propriedades ACID. 
Fatores importantes a serem considerados em um banco de dados NewSQL são: controle de concorrência de esquema lock-free; e, arquitetura distribuída shared-nothing, Stonebraker (2012) apud KNOB et al (2019).
