---
title: Ingestování dat z Kafky do Průzkumníka dat Azure
description: V tomto článku se dozvíte, jak ingestovat (načíst) data do Průzkumníka dat Azure z Kafky.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 03b46ff50683149a22c71ccb155480a0f08455bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66497282"
---
# <a name="ingest-data-from-kafka-into-azure-data-explorer"></a>Ingestování dat z Kafky do Průzkumníka dat Azure
 
Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Data Explorer nabízí ingestování (načítání dat) z Kafky. Kafka je distribuovaná streamovací platforma, která umožňuje vytvářet datové kanály datových proudů v reálném čase, které spolehlivě přesouvají data mezi systémy nebo aplikacemi.
 
## <a name="prerequisites"></a>Požadavky
 
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete. 
 
* [Testovací cluster a databáze](create-cluster-database-portal.md).
 
* [Ukázková aplikace,](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/kafka) která generuje data a odesílá je Kafkovi.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) pro spuštění ukázkové aplikace.
 
## <a name="kafka-connector-setup"></a>Nastavení konektoru Kafka

Kafka Connect je nástroj pro škálovatelné a spolehlivé streamování dat mezi Apache Kafka a dalšími systémy. Usnadňuje rychlé definování konektorů, které přesouvají velké kolekce dat do a z Kafky. Umyvadlo ADX Kafka slouží jako konektor od společnosti Kafka.
 
### <a name="bundle"></a>Svazek

Kafka může `.jar` načíst jako plugin, který bude fungovat jako vlastní konektor. Chcete-li `.jar`vytvořit takové , budeme klonovat kód místně a sestavení pomocí Maven. 

#### <a name="clone"></a>Klonování

```bash
git clone git://github.com:Azure/kafka-sink-azure-kusto.git
cd ./kafka-sink-azure-kusto/kafka/
```

#### <a name="build"></a>Sestavení

Sestavte místně s Maven vytvořit `.jar` kompletní s závislostmi.

* JDK >= 1,8 [ke stažení](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Maven [ke stažení](https://maven.apache.org/install.html)
 

Uvnitř kořenového adresáře *kafka-sink-azure-kusto*, spusťte:

```bash
mvn clean compile assembly:single
```

### <a name="deploy"></a>Nasazení 

Načíst plugin do Kafka. Příklad nasazení pomocí dockeru lze nalézt na [kafka-sink-azure-kusto](https://github.com/Azure/kafka-sink-azure-kusto#deploy)
 

Podrobnou dokumentaci ke konektorům Kafka a jejich nasazení naleznete na [adrese Kafka Connect](https://kafka.apache.org/documentation/#connect) 

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
 
## <a name="create-a-target-table-in-adx"></a>Vytvoření cílové tabulky v adxu
 
Vytvořte tabulku v adxu, do které může Kafka odesílat data. Vytvořte tabulku v clusteru a databázi zřízené v **části Požadavky**.
 
1. Na webu Azure Portal přejděte do svého clusteru a vyberte **Dotaz**.
 
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

Teď, když je cluster Kafka připojený k ADX, použijte [ukázkovou aplikaci,](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) kterou jste stáhli ke generování dat.

### <a name="clone"></a>Klonování

Klonujte ukázkovou aplikaci místně:

```cmd
git clone git://github.com:Azure/azure-kusto-samples-dotnet.git
cd ./azure-kusto-samples-dotnet/kafka/
```

### <a name="run-the-app"></a>Spuštění aplikace

1. Otevřete řešení ukázkové aplikace v sadě Visual Studio.

1. V `Program.cs` souboru aktualizujte konstantu `connectionString` na připojovací řetězec Kafka.

    ```csharp    
    const string connectionString = @"<YourConnectionString>";
    ```

1. Sestavte a spusťte aplikaci. Aplikace odesílá zprávy do clusteru Kafka a vytiskne svůj stav každých 10 sekund.

1. Po odeslání několika zpráv aplikace přejděte k dalšímu kroku.
 
## <a name="query-and-review-the-data"></a>Dotaz a kontrola dat

1. Chcete-li se ujistit, že během požití nedošlo k žádným chybám:

    ```Kusto
    .show ingestion failures
    ```

1. Chcete-li zobrazit nově požitá data:

    ```Kusto
    TestTable 
    | count
    ```

1. Zobrazení obsahu zpráv:
 
    ```Kusto
    TestTable
    ```
 
    Sada výsledků by měla vypadat takto:
 
    ![Sada výsledků dotazu na zprávy](media/ingest-data-event-hub/message-result-set.png)
 
## <a name="next-steps"></a>Další kroky
 
* [Dotazovat se na data v Průzkumníku dat Azure](web-query-data.md)
