# Bem-vindo ao Mcp Quote API :money_with_wings:

Serviço responsável por consultar informações monetárias, cotações e moedas das APIs de dados abertos do governo.

# Introdução Arquitetura 
Durante o desenvolvimento foram tomadas algumas decisões com relação a tecnologias usadas. O backend foi desenvolvido utilizando o framework 
[Quarkus](https://quarkus.io/) por questões de produtividade e desempenho em relação a outras arquiteturas.

Algumas ferramentas foram adicionadas para monitoração e logs na aplicação, dentre elas: [Prometheus](https://quarkus.io/guides/microprofile-metrics), [Jaeger](https://quarkus.io/guides/opentracing), 
[Elasticsearh](https://quarkus.io/guides/centralized-log-management), [Logstash](https://quarkus.io/guides/centralized-log-management) e [Kibana](https://quarkus.io/guides/centralized-log-management). Utilizando as [integrações](https://quarkus.io/guides/) que o quarkus oferece. 

Aplicação foi pensada para ser resiliente e tolerante a falhas. 

---
**Observação: O projeto foi desenvolvido utilizando o Windows e por isso algumas configurações no docker/docker-compose podem ser diferentes 
para cada sistema operacional. O Docker do Windows não possui suporte ao modo 'host' na criação de rede entre containers e por isso 
foi utilizado em alguns casos a propriedade 'host.docker.internal' para contornar esse problema.** 

---

# Ferramentas complementares

Ferramenta | Descrição
--- | ---
[Prometheus](https://quarkus.io/guides/microprofile-metrics) | Sistema de monitoramento para analise de metricas das aplicações
[Jaeger](https://quarkus.io/guides/opentracing) | Sistema de rastramento para requisições
[ELK](https://quarkus.io/guides/centralized-log-management)| Sistema centralizador de logs. Elasticsearch, Logstash e Kibana
[Quarkus](https://quarkus.io/) | Framework utilizado para criar o backend

# Ambiente de desenvolvimento

Existem alguns passos para execução do projeto em ambiente local, necessário que alguns programas estejam corretamente instalados.

Framework/lib | Versão Recomendada | S.O Utilizado
--- | --- | ---
[Maven](https://maven.apache.org/download.cgi) | 3.6.3 | Windows
[JDK](https://openjdk.java.net/projects/jdk/11/) | 11 | Windows
[Docker](https://docs.docker.com/docker-for-windows/install/) | 19.03.13 | Windows
[Docker Compose](https://docs.docker.com/compose/install/) | 1.27.4 | Windows
[Intellij](https://www.jetbrains.com/pt-br/idea/)| - | Windows

## Execução do projeto 

### Passo 1
Faça o clone do projeto
```shell script
$ git clone http://github.com/Maikoncanuto/mcp-quote-api.git mcp-quote
```

### Passo 2 
Entre na pasta raiz do projeto

```shell script
$ cd mcp-quote
```

### Passo 3
Execute o comando para levantar toda infraestrutura necessária

```shell script
$ docker-compose up 
```

Acompanhar logs: 
```shell script
$ docker-compose logs -f
```

### Passo 4
Execute o comando para instalar as dependências necessárias

```shell script
$ mvn clean package 
```

### Passo 5
Execute o projeto com comando do quarkus

```shell script
$ ./mvnw compile quarkus:dev
```

ou 

```shell script
java -jar target/mcp-quotes-0.0.1-SNAPSHOT-runner.jar
```

# Serviços do mcp-quote-api

Serviço | URL | Status | Descrição
--- | --- | --- | ---
Swagger | http://localhost:8080/swagger-ui/#/ | :white_check_mark: | Informação dos endpoints na aplicação
Health Live | http://localhost:8080/health/live | :white_check_mark: | Responsável por identificar a sáude da aplicação
Health Ready | http://localhost:8080/health/ready | :white_check_mark: | Responsável por identificar se aplicação está pronta para uso
Jaeger | http://localhost:8180 | :white_check_mark: | Rastreamento de requisições
Prometheus | http://localhost:8280 | :white_check_mark: | Analise de Métricas da aplicação
Kibana | http://localhost:8380 | :white_check_mark: | Visualizar Logs e dashboards 

# Requisições para os Endpoints

## Buscar informação da cotação por data
**Url de requisição:**
```
http://localhost:8080/api/v1/cotacoes/cotacao-dolar-dia/11-05-2020
```
**Parametrôs:**

Parâmetro | Formato | Tipo | Descrição | Exemplo
--- | --- | --- | --- | ---  
dataCotacao | MM-dd-yyyy | string | Data da cotação do dolar | '11-05-2020'

**CURL:**
```shell script
curl -X GET "http://localhost:8080/api/v1/cotacoes/cotacao-dolar-dia/11-05-2020" -H  "accept: */*"
```

**Retorno:**
``` json
{
  "code": "200", //Código da requisição
  "data": [ // Objeto retornado
    {
      "cotacaoCompra": 5.5618,
      "cotacaoVenda": 5.5624,
      "dataHoraCotacao": "2020-11-05 13:09:31.961"
    }
  ],
  "erro": null //Mensagem de erro
}
```
---

## Buscar informação da cotação por período
**Url de requisição:**
```
http://localhost:8080/api/v1/cotacoes/cotacao-dolar-periodo?dataFinal=01-01-202&dataInicial='02-01-2020'
```
**Parametrôs Query:**

Parâmetro | Formato | Tipo | Descrição | Exemplo
--- | --- | --- | --- | ---  
dataInicial | MM-dd-yyyy | string | Data inicial da cotação | '01-01-2020'
dataFinal | MM-dd-yyyy | string | Data final da cotação | '02-01-2020'

**CURL:**
```shell script
curl -X GET "http://localhost:8080/api/v1/cotacoes/cotacao-dolar-periodo?dataFinal=01-01-202&dataInicial=02-01-2020" -H  "accept: */*"
```

**Retorno:**
``` json
{
  "code": "200", //Código da requisição
  "data": [ // Objeto retornado
    {
          "cotacaoCompra": 4.0207,
          "cotacaoVenda": 4.0213,
          "dataHoraCotacao": "2020-01-02 13:11:10.762"
        },
        {
          "cotacaoCompra": 4.0516,
          "cotacaoVenda": 4.0522,
          "dataHoraCotacao": "2020-01-03 13:06:22.606"
        },
        {
          "cotacaoCompra": 4.0548,
          "cotacaoVenda": 4.0554,
          "dataHoraCotacao": "2020-01-06 13:03:22.271"
        },
        {
          "cotacaoCompra": 4.0835,
          "cotacaoVenda": 4.0841,
          "dataHoraCotacao": "2020-01-07 13:06:14.601"
        },
        {
          "cotacaoCompra": 4.0666,
          "cotacaoVenda": 4.0672,
          "dataHoraCotacao": "2020-01-08 13:03:56.075"
        }
  ],
  "erro": null //Mensagem de erro
}
```
