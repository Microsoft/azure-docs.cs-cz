---
title: 'Rychlý start: Ingestovat data z platformy Kafka do Průzkumníku dat Azure'
description: V tomto rychlém startu se dozvíte, jak se přijmout data (načíst) do Průzkumníku dat Azure z platformy Kafka.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/19/2018
ms.openlocfilehash: 316d2b4ebf0a9b0799e2bdcdfda1d517713f413f
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52277011"
---
# <a name="quickstart-ingest-data-from-kafka-into-azure-data-explorer"></a>Rychlý start: Ingestovat data z platformy Kafka do Průzkumníku dat Azure
 
Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Průzkumník dat Azure nabízí zpracování (načítání dat) z platformy Kafka. Kafka je distribuovaná streamovací platforma, která umožňuje vytváření v reálném čase streamovaných datových kanálů, které spolehlivě přesunovat data mezi systémy nebo aplikace. 
 
## <a name="prerequisites"></a>Požadavky
 
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete. 
 
* [Testovací cluster a databázi](create-cluster-database-portal.md)
 
* [Ukázková aplikace](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/kafka) , který generuje data a odesílá je do Kafka

* [Visual Studio 2017 verze 15.3.2 nebo vyšší](https://www.visualstudio.com/vs/) ke spuštění ukázkové aplikace
 
## <a name="kafka-connector-setup"></a>Instalace konektoru Kafka
Připojit Kafka je nástroj pro scalably a spolehlivě streamovaná data mezi Apache Kafka a dalšími systémy. To zjednodušuje k rychlému definování konektorů, které přesun velkých sad dat do a z Kafka. Jímka Kafka ADX slouží jako konektor z platformy Kafka.
 
### <a name="bundle"></a>Sady prostředků 
Můžete načíst Kafka `.jar` jako modul plug-in, který bude sloužit jako vlastní konektor. K vytvoření, `.jar`, budeme klonování kódu místně a sestavení pomocí nástroje Maven. 

#### <a name="clone"></a>Klon

```bash
git clone git://github.com:Azure/kafka-sink-azure-kusto.git
cd ./kafka-sink-azure-kusto/kafka/
```
 
#### <a name="build"></a>Sestavení

Místně sestavovat pomocí Maven k vytvoření `.jar` dokončení se závislostmi.

* JDK > = 1.8 [stáhnout](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Maven [stáhnout](https://maven.apache.org/install.html)
 

V kořenovém adresáři *kafka jímky azure kusto*, spusťte:

```bash
mvn clean compile assembly:single
```
 
### <a name="deploy"></a>Nasazení 
 
Načtení modulu plug-in do Kafka. Příklad nasazení pomocí dockeru najdete tady [kafka jímky azure kusto](https://github.com/Azure/kafka-sink-azure-kusto#deploy)
 

Podrobné dokumentaci na konektory Kafka a jak je nasadit lze nalézt v [Kafka připojení](https://kafka.apache.org/documentation/#connect) 

### <a name="example-configuration"></a>Příklad konfigurace 
 
```config
name=KustoSinkConnector 
connector.class=com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector 
kusto.sink.flush_interval_ms=300000 
key.converter=org.apache.kafka.connect.storage.StringConverter 
value.converter=org.apache.kafka.connect.storage.StringConverter 
tasks.max=1 
topics=testing1 
kusto.tables.topics_mapping=[{'topic': 'testing1','db': 'daniel', 'table': 'TestTable','format': 'json', 'mapping':'TestMapping'}] 
kusto.auth.authority=XXX 
kusto.url=https://ingest-{mycluster}.kusto.windows.net/ 
kusto.auth.appid=XXX 
kusto.auth.appkey=XXX 
kusto.sink.tempdir=/var/tmp/ 
kusto.sink.flush_size=1000
```
 
## <a name="create-a-target-table-in-adx"></a>Vytvoření cílové tabulky v ADX
 
Vytvoření tabulky v ADX, ke kterému Kafka může odesílat data. Vytvoření tabulky v clusteru a zřizována jako databáze **požadavky**.
 
1. Na webu Azure Portal, přejděte do vašeho clusteru a vyberte **dotazu**.
 
    ![Dotaz – odkaz aplikace](media/ingest-data-event-hub/query-explorer-link.png)
 
1. Zkopírujte do okna následující příkaz a vyberte **Spustit**.
 
    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```
 
    ![Spuštění vytvářecího dotazu](media/ingest-data-event-hub/run-create-query.png)
 
1. Zkopírujte do okna následující příkaz a vyberte **Spustit**.
 
    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

    Tento příkaz namapuje příchozí data JSON na názvy sloupců a datové typy tabulky (TestTable).


## <a name="generate-sample-data"></a>Generování ukázkových dat

Teď, když Kafka cluster je připojený k ADX, použijte [ukázkovou aplikaci](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) jste si stáhli generují data.

### <a name="clone"></a>Klon
Naklonujte ukázkovou aplikaci místně:

```cmd
git clone git://github.com:Azure/azure-kusto-samples-dotnet.git
cd ./azure-kusto-samples-dotnet/kafka/
```
### <a name="run-the-app"></a>Spuštění aplikace
1. Otevřete řešení ukázkové aplikace v sadě Visual Studio.

1. V `Program.cs` soubor, aktualizovat `connectionString` konstanty pro připojovací řetězec systému Kafka.

    ```csharp    
    const string connectionString = @"<YourConnectionString>";
    ```

1. Sestavte a spusťte aplikaci. Aplikace odešle zprávy do clusteru Kafka a vytiskne na jeho stav každých deset sekund.

1. Po odeslání několik zpráv aplikace přesune k dalšímu kroku.
 
## <a name="query-and-review-the-data"></a>Dotazování a zkontrolujte data 

1. Aby nedošlo k žádným chybám při příjmu:

    ```Kusto
    .show ingestion failures
    ```

1. Chcete-li zobrazit nově přijatých dat:

    ```Kusto
    TestTable 
    | count
    ```

1. Chcete-li zobrazit obsah zprávy:
 
    ```Kusto
    TestTable
    ```
 
    Sadu výsledků dotazu by měl vypadat nějak takto:
 
    ![Sada výsledků dotazu na zprávy](media/ingest-data-event-hub/message-result-set.png)
 
## <a name="next-steps"></a>Další postup
 
> [!div class="nextstepaction"]
> [Rychlý start: Dotazování na data v Azure Data Exploreru](web-query-data.md)
