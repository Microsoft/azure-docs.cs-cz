---
title: Transformace dat pomocí aktivity Hadoop MapReduce
description: Naučte se zpracovávat data spuštěním programů Hadoop MapReduce v clusteru Azure HDInsight z objektu pro vytváření dat Azure.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: ab7bb96c6b367b3520676a36c3d52b49ba90eb26
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368940"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity Hadoop MapReduce v Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-map-reduce.md)
> * [Aktuální verze](transform-data-using-hadoop-map-reduce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita HDInsight MapReduce v [kanálu](concepts-pipelines-activities.md) Data Factory vyvolá program MapReduce na [vašem](compute-linked-services.md#azure-hdinsight-linked-service) clusteru HDInsight, nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Tento článek se sestavuje na článku [aktivity transformace dat](transform-data.md) , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

Pokud Azure Data Factory teprve začínáte, přečtěte si [Úvod do Azure Data Factory](introduction.md) a udělejte si kurz: [kurz: transformace dat](tutorial-transform-data-spark-powershell.md) před čtením tohoto článku.

Podrobnosti o spouštění skriptů pro vepřové zobrazení a podregistr v clusteru HDInsight z kanálu pomocí aktivit prasete a podregistru služby HDInsight najdete v tématu věnovaném [prase](transform-data-using-hadoop-pig.md) a [podregistru](transform-data-using-hadoop-hive.md) .

## <a name="syntax"></a>Syntax

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

## <a name="syntax-details"></a>Podrobnosti syntaxe

| Vlastnost          | Popis                              | Povinné |
| ----------------- | ---------------------------------------- | -------- |
| name              | Název aktivity                     | Ano      |
| Popis       | Text popisující, k čemu se aktivita používá | Ne       |
| typ              | Pro aktivitu MapReduce je typ aktivity HDinsightMapReduce. | Ano      |
| linkedServiceName | Odkaz na cluster HDInsight registrovaný jako propojená služba v Data Factory. Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) . | Ano      |
| NázevTřídy         | Název třídy, která se má provést         | Ano      |
| jarLinkedService  | Odkaz na propojenou službu Azure Storage, která se používá k ukládání souborů JAR Tady se podporují jenom propojené služby **[Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)** a **[adls Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)** . Pokud tuto propojenou službu nezadáte, použije se propojená služba Azure Storage definovaná v propojené službě HDInsight. | Ne       |
| jarFilePath       | Zadejte cestu k souborům jar uloženým v Azure Storage, na kterou odkazuje jarLinkedService. V názvu souboru se rozlišují malá a velká písmena. | Ano      |
| jarlibs           | Pole řetězce cesty pro soubory knihovny jar, na které odkazuje úloha uložená v Azure Storage definované v jarLinkedService. V názvu souboru se rozlišují malá a velká písmena. | Ne       |
| GetDebugInfo –      | Určuje, kdy se soubory protokolu zkopírují do Azure Storage používaného clusterem HDInsight (nebo) zadaným pomocí jarLinkedService. Povolené hodnoty: žádné, vždy nebo chyba. Výchozí hodnota: Žádný. | Ne       |
| náhodné         | Určuje pole argumentů pro úlohu Hadoop. Argumenty jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| definuje           | Zadejte parametry jako páry klíč/hodnota pro odkazování v rámci skriptu podregistru. | Ne       |



## <a name="example"></a>Příklad
Aktivitu HDInsight MapReduce můžete použít ke spuštění všech souborů JAR MapReduce v clusteru HDInsight. V následující ukázkové definici JSON kanálu je aktivita HDInsight nakonfigurovaná tak, aby spouštěla soubor Mahout JAR.

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
V části **argumenty** můžete zadat argumenty pro program MapReduce. Za běhu vidíte několik dalších argumentů (například: MapReduce. job. Tags) z rozhraní MapReduce. Chcete-li odlišit argumenty pomocí argumentů MapReduce, zvažte použití možnosti i hodnoty jako argumentů, jak je znázorněno v následujícím příkladu (-s,--Input,--Output atd., jsou možnosti ihned následovány jejich hodnotami).

## <a name="next-steps"></a>Další kroky
Podívejte se na následující články, které vysvětlují, jak transformovat data jinými způsoby:

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita v podregistru](transform-data-using-hadoop-hive.md)
* [Aktivita prasete](transform-data-using-hadoop-pig.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Sparku](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita spuštění dávky Azure Machine Learning Studio (Classic)](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
