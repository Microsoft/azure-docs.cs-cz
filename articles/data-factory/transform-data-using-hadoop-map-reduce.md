---
title: Transformace dat pomocí aktivity Hadoop MapReduce
description: Zjistěte, jak zpracovat data spuštěním programů Hadoop MapReduce v clusteru Azure HDInsight z datové továrny Azure.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 5d38e3126442bcf34c96cead2b2ea59507b50b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912866"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity Hadoop MapReduce v Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-map-reduce.md)
> * [Aktuální verze](transform-data-using-hadoop-map-reduce.md)

Aktivita HDInsight MapReduce v [kanálu](concepts-pipelines-activities.md) data factory vyvolá program MapReduce na [vlastní](compute-linked-services.md#azure-hdinsight-linked-service) cluster nebo cluster HDInsight [na vyžádání.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Tento článek vychází z článku [aktivit transformace dat,](transform-data.md) který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

Pokud jste s Azure Data Factory tečeme na [úvodním úvodu do Azure Data Factory](introduction.md) a proveďte kurz: Transformace [dat](tutorial-transform-data-spark-powershell.md) před přečtením tohoto článku.

Podrobnosti [Hive](transform-data-using-hadoop-hive.md) o spouštění skriptů Pig/Hive v clusteru HDInsight z kanálu pomocí aktivit HDInsight Pig a Hive najdete v tématu [Pig](transform-data-using-hadoop-pig.md) and Hive.

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

## <a name="syntax-details"></a>Podrobnosti syntaxe

| Vlastnost          | Popis                              | Požaduje se |
| ----------------- | ---------------------------------------- | -------- |
| jméno              | Název aktivity                     | Ano      |
| description       | Text popisující, k čemu se aktivita používá | Ne       |
| type              | Pro Aktivitu MapReduce je typem aktivity HDinsightMapReduce | Ano      |
| linkedServiceName | Odkaz na cluster HDInsight registrovaný jako propojená služba v datové továrně. Další informace o této propojené službě najdete v článku [Výpočetní propojené služby.](compute-linked-services.md) | Ano      |
| Classname         | Název třídy, která má být provedena         | Ano      |
| jarLinkedService  | Odkaz na azure storage propojené služby používané k ukládání souborů Jar. Pokud tuto propojenou službu nezadáte, použije se služba Propojená úložištěm Azure definovaná ve službě HDInsight Linked Service. | Ne       |
| jarFilePath       | Zadejte cestu k souborům Jar uloženým ve službě Azure Storage, na které odkazuje jarLinkedService. Název souboru rozlišuje malá a velká písmena. | Ano      |
| jarlibs           | Pole řetězce cesty k souborům knihovny Jar odkazuje úlohy uložené v Azure Storage definované v jarLinkedService. Název souboru rozlišuje malá a velká písmena. | Ne       |
| getDebugInfo      | Určuje, kdy se soubory protokolu zkopírují do úložiště Azure, které používá cluster HDInsight (nebo) určené jarLinkedService. Povolené hodnoty: Žádné, Vždy nebo Selhání. Výchozí hodnota: Žádný. | Ne       |
| Argumenty         | Určuje pole argumentů pro úlohu Hadoop. Argumenty jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| Definuje           | Zadejte parametry jako dvojice klíč/hodnota pro odkazování ve skriptu Hive. | Ne       |



## <a name="example"></a>Příklad
Aktivita HDInsight MapReduce můžete použít ke spuštění libovolného souboru dlaně MapReduce v clusteru HDInsight. V následující ukázce json definice kanálu aktivity HDInsight je nakonfigurován tak, aby spouštět soubor Mahout JAR.

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
V části **argumenty** můžete zadat libovolné argumenty pro program MapReduce. Za běhu se zobrazí několik dalších argumentů (například mapreduce.job.tags) z rozhraní MapReduce. Chcete-li odlišit argumenty s Argumenty MapReduce, zvažte použití možnosti a hodnoty jako argumenty, jak je znázorněno v následujícím příkladu (-s, --input, --output atd., jsou možnosti bezprostředně následované jejich hodnotami).

## <a name="next-steps"></a>Další kroky
Další články vysvětlují, jak transformovat data jinými způsoby:

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita úlu](transform-data-using-hadoop-hive.md)
* [Aktivita prasat](transform-data-using-hadoop-pig.md)
* [Aktivita streamování hadoopu](transform-data-using-hadoop-streaming.md)
* [Aktivita jiskry](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita dávkového spuštění strojového učení](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
