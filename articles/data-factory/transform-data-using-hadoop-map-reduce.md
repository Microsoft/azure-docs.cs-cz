---
title: Transformace dat pomocí činnost MapReduce s Hadoop v Azure Data Factory | Microsoft Docs
description: Zjistěte, jak ke zpracování dat systémem Hadoop MapReduce programy v clusteru Azure HDInsight ze služby Azure data factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: cb7009d0e7f31b2f503ac51d378fd117fff9f9b2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049947"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transformace dat pomocí činnost MapReduce s Hadoop v Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-map-reduce.md)
> * [Aktuální verze](transform-data-using-hadoop-map-reduce.md)

Činnost HDInsight MapReduce v datové továrně [kanálu](concepts-pipelines-activities.md) vyvolá MapReduce program na [vlastní](compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight. Tento článek vychází [aktivit transformace dat](transform-data.md) článek, který poskytne Obecné přehled o transformaci dat a aktivity podporované transformace.

Pokud jste nový do Azure Data Factory, pročtěte [Úvod do Azure Data Factory](introduction.md) a proveďte kurz: [kurz: transformovat data](tutorial-transform-data-spark-powershell.md) před přečtení tohoto článku. 

V tématu [Pig](transform-data-using-hadoop-pig.md) a [Hive](transform-data-using-hadoop-hive.md) podrobnosti o spouštění Pig nebo Hive skripty v HDInsight clusteru z kanálu pomocí aktivity HDInsight Pig a Hive. 

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

## <a name="syntax-details"></a>Syntaxe podrobnosti

| Vlastnost          | Popis                              | Požaduje se |
| ----------------- | ---------------------------------------- | -------- |
| jméno              | Název aktivity                     | Ano      |
| description       | Text popisující, co se používá aktivitu pro | Ne       |
| type              | Pro činnost MapReduce typ aktivity je HDinsightMapReduce | Ano      |
| linkedServiceName | Referenční dokumentace ke clusteru HDInsight registrován jako propojené služby ve službě Data Factory. Další informace o této propojené služby najdete v tématu [výpočetní propojené služby](compute-linked-services.md) článku. | Ano      |
| Název třídy         | Název třídy, které by šlo spustit         | Ano      |
| jarLinkedService  | Odkaz na propojenou službu úložiště Azure používají k ukládání souborů Jar. Pokud tato propojená služba nezadáte, použije se propojené služby Azure Storage definované v propojené službě HDInsight. | Ne       |
| jarFilePath       | Zadejte cestu k souborům Jar uložené ve službě Azure Storage, na které odkazuje jarLinkedService. Název souboru je malá a velká písmena. | Ano      |
| jarlibs           | Pole cesty k souborům knihovny Jar odkazuje úlohu ve službě Azure Storage definované v jarLinkedService řetězec. Název souboru je malá a velká písmena. | Ne       |
| getdebuginfo –      | Určuje, kdy soubory protokolu se zkopírují do úložiště Azure používaný v clusteru HDInsight (a) zadaný ve jarLinkedService. Povolené hodnoty: None, vždy nebo selhání. Výchozí hodnota: žádné. | Ne       |
| argumenty         | Určuje pole argumentů pro úlohy Hadoop. Argumenty, které jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| definuje           | Zadejte parametry pro odkazování v rámci skriptu Hive jako páry klíč/hodnota. | Ne       |



## <a name="example"></a>Příklad:
Činnost MapReduce HDInsight můžete spustit libovolný soubor jar MapReduce v clusteru služby HDInsight. V následující definici JSON ukázkový kanál aktivita HDInsight je nakonfigurována pro spuštění soubor Mahout JAR.

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
Můžete zadat všechny argumenty pro MapReduce program **argumenty** části. V době běhu zobrazí několik další argumenty (například: mapreduce.job.tags) z rozhraní MapReduce. Chcete-li rozlišit vaší argumenty s argumenty MapReduce, zvažte, pomocí možnosti a hodnoty jako argumenty, jak je znázorněno v následujícím příkladu (- s, – vstup, – výstupní atd., jsou možnosti bezprostředně následované jejich hodnoty).

## <a name="next-steps"></a>Další postup
Najdete v následujících článcích, které vysvětlují, jak k transformaci dat jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita Hive](transform-data-using-hadoop-hive.md)
* [Pig aktivity](transform-data-using-hadoop-pig.md)
* [Hadoop streamované aktivitě](transform-data-using-hadoop-streaming.md)
* [Spark aktivity](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning dávkového spuštění aktivity](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
