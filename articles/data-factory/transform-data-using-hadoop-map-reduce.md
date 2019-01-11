---
title: Transformace dat pomocí aktivity Hadoop MapReduce ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak můžete zpracovávat data pomocí spouštění Hadoop MapReduce programů v clusteru Azure HDInsight ze služby Azure data factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 4543982f731feb44a8f02581c11714dec2b206f9
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214500"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity Hadoop MapReduce ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-map-reduce.md)
> * [Aktuální verze](transform-data-using-hadoop-map-reduce.md)

Aktivita HDInsight MapReduce ve službě Data Factory [kanálu](concepts-pipelines-activities.md) vyvolá MapReduce program na [vlastní](compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight. Tento článek vychází [aktivity transformace dat](transform-data.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace.

Pokud do služby Azure Data Factory začínáte, přečtěte si [Úvod do služby Azure Data Factory](introduction.md) a udělat kurz: [Kurz: transformace dat](tutorial-transform-data-spark-powershell.md) před čtením tohoto článku.

Zobrazit [Pig](transform-data-using-hadoop-pig.md) a [Hive](transform-data-using-hadoop-hive.md) Další informace o spouštění Pigu a Hivu skripty v HDInsight clusteru z kanálu pomocí aktivity HDInsight Pigu a Hivu.

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Podrobnosti o syntaxi

| Vlastnost          | Popis                              | Požaduje se |
| ----------------- | ---------------------------------------- | -------- |
| jméno              | Název aktivity                     | Ano      |
| description       | Text popisující, k čemu aktivita slouží | Ne       |
| type              | Pro činnost MapReduce typ aktivity je HDinsightMapReduce | Ano      |
| linkedServiceName | Odkaz na clusteru HDInsight zaregistrovaný jako propojenou službu ve službě Data Factory. Další informace o tuto propojenou službu, najdete v článku [propojené služby Compute](compute-linked-services.md) článku. | Ano      |
| Název třídy         | Název třídy, který se spustí         | Ano      |
| jarLinkedService  | Odkaz na propojená služba Azure Storage pro ukládání souborů Jar. Pokud nezadáte tuto propojenou službu, použije se propojená služba Azure Storage, definované v propojené službě HDInsight. | Ne       |
| jarFilePath       | Zadejte cestu pro soubory Jar uložené ve službě Azure Storage, která odkazují jarLinkedService. Název souboru je velká a malá písmena. | Ano      |
| jarlibs           | Pole cesty k souborům knihovny soubor Jar odkazovat úlohy ve službě Azure Storage definované v jarLinkedService řetězce. Název souboru je velká a malá písmena. | Ne       |
| getDebugInfo      | Určuje, kdy se zkopírují soubory protokolů do služby Azure Storage používaný v clusteru HDInsight (a) určená jarLinkedService. Povolené hodnoty: NONE, vždy, nebo selhání. Výchozí hodnota: Žádné. | Ne       |
| argumenty         | Určuje pole argumentů pro úlohy Hadoopu. Argumenty jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| definuje           | Zadejte parametry pro odkazování v rámci skriptu Hive jako páry klíč/hodnota. | Ne       |



## <a name="example"></a>Příklad:
Aktivita MapReduce HDInsight můžete použít ke spuštění souboru jar jakékoli MapReduce v clusteru HDInsight. V následující definici JSON ukázkový kanál je nakonfigurován na aktivitu HDInsight umožňuje spustit soubor Mahout JAR.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
        "arguments": [
            "-s",
            "SIMILARITY_LOGLIKELIHOOD",
            "--input",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
            "--output",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
            "--maxSimilaritiesPerItem",
            "500",
            "--tempDir",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
        ]
    }
}
```
Můžete zadat všechny argumenty pro MapReduce program **argumenty** oddílu. Za běhu, se zobrazí několik dalších argumentů (například: mapreduce.job.tags) z rozhraní MapReduce. K rozlišení vaše argumenty s argumenty MapReduce, zvažte použití možnosti a hodnoty jako argumenty, jak je znázorněno v následujícím příkladu (- s, – vstup, – výstup atd., jsou možnosti bezprostředně následovat jejich hodnoty).

## <a name="next-steps"></a>Další postup
Viz následující články, které vysvětlují, jak transformovat data dalšími způsoby:

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita hivu](transform-data-using-hadoop-hive.md)
* [Aktivita pig](transform-data-using-hadoop-pig.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Spark](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita provedení dávky služby Learning počítače](transform-data-using-machine-learning.md)
* [Aktivita uložená procedura](transform-data-using-stored-procedure.md)
