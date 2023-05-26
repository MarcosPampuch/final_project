# Projeto final HOW bootcamp Engenharia de Dados

## Overview
Neste projeto sera desenvolvida uma ETL a qual conectara a API de eventos do Twitter `a uma instancia Redshift (utilizando o servico Spectrum) com dados prontos para serem analisados. 
Atraves de consultas no Redshift, sera possivel agregar, transformar e modelar os dados usando SQL.

Mais informacoes sobre a API do Twitter podem ser encontradas [aqui](https://developer.twitter.com/en/docs/twitter-api/tweets/search/introduction)

## Motivacao
Apos o periodo de tratamento, a nova base de dados tratada podera ser usada para fomentar analises comportamentais segregadas por regiao ou tema especifico. 
Os dados modelados tambem podem ser usados para treinar modelos de inteligencial artificial (NLP por exemplo).

## Resultados

Como key results desta ETL teremos:
- Um Data Warehouse (OLAP) com schema e tabelas externas ligadas diretamente `a prefixes de um bucket do S3.  

- Dados tratados presentes no datalake os quais terao como formato, parquet snappy de modo a otimizar tanto consultas quanto o armazenamento dos mesmos.

- Atualizacao da base com novos tweets por batches em um intervalo fixo de 5 minutos.

## Methodos

1) Inicialmente, os dados serao extraidos da API do Twitter atraves de uma task ECS a qual sera acionada a cada 5 minutos por uma rule no cloudwatch;
2) Apos receber os dados da requisicao, o container ECS redireciona a resposta (dados semi-estruturados) para 
um Delivery Stream do Kinesis;
3) O Kinesis, por sua vez, acumula e redireciona os dados armazenados para o S3 (com as rules de 128M ou 5 minutos) em batches no formato de JSONs.
4) Com os dados ja armazenados de forma crua no S3, uma instancia Glue acionada pelo Cloudwatch sera responsavel por extrair esses novos arquivos do S3 (utilizando bookmarks do Glue). Esses dados serao normalizados e enviados de forma particionada para outro prefix do bucket no formato Parquet Snappy.
5) Apos a normalizacao dos dados, o consumo e modelagem dos mesmos sera feito atraves de uma instancia Redshift usando o servico Spectrum.

**Podera ser utilizado para consumo dos dados tambem, as ferramentas Athena + Glue Catalog, a escolha do Redshift se da simplesmente pela facilidade do mesmo de centralizar todas as possiveis extracoes de dados em um ambiente unico (Data Warehouse).

## Servicos
- AWS Cloudwatch;
- AWS Elastic Container Service (and Registry);
- AWS Kinesis Firehose;
- AWS S3;
- AWS Redshift;


# Ilustracao Arquitetura

![Ilustracao img](https://github.com/MarcosPampuch/final_project/assets/47998378/6a125ac6-9801-44d6-81e0-91a4d56c0df6)






