---
title: Transformace dat pomocí aktivity streamování Hadoop ve službě Azure Data Factory | Dokumentace Microsoftu
description: Vysvětluje způsob používání streamované aktivitě Hadoop ve službě Azure Data Factory, který umožňuje transformovat data spuštěním streamování Hadoop programy na clusteru Hadoop.
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
ms.openlocfilehash: b498e09e53f8b0844470bf3948a664d8ad4337b7
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022219"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity streamování Hadoop ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Aktuální verze](transform-data-using-hadoop-streaming.md)

Aktivita streamování HDInsight ve službě Data Factory [kanálu](concepts-pipelines-activities.md) spustí programy streamování Hadoop na [vlastní](compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight. Tento článek vychází [aktivity transformace dat](transform-data.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace.

Pokud do služby Azure Data Factory začínáte, přečtěte si [Úvod do služby Azure Data Factory](introduction.md) a proveďte [kurz: transformace dat](tutorial-transform-data-spark-powershell.md) před čtením tohoto článku. 

## <a name="json-sample"></a>Vzorek kódu JSON
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
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
| type              | Streamované aktivitě Hadoop typ aktivity je HDInsightStreaming | Ano      |
| linkedServiceName | Odkaz na clusteru HDInsight zaregistrovaný jako propojenou službu ve službě Data Factory. Další informace o tuto propojenou službu, najdete v článku [propojené služby Compute](compute-linked-services.md) článku. | Ano      |
| mapování            | Určuje název spustitelného souboru mapování | Ano      |
| redukční funkci           | Určuje název spustitelného souboru redukční funkci | Ano      |
| kombinační          | Určuje název spustitelného souboru kombinační | Ne       |
| fileLinkedService | Odkaz na propojená služba Azure Storage se využívá k uložení programy Mapovač kombinační a redukční funkci provádět. Pokud nezadáte tuto propojenou službu, použije se propojená služba Azure Storage, definované v propojené službě HDInsight. | Ne       |
| filePath          | Poskytuje pole Cesta Mapovač kombinační, a redukční funkci programy ve službě Azure Storage, na které se odkazuje fileLinkedService. V této cestě se rozlišují velká a malá písmena. | Ano      |
| vstup             | Určuje pro mapovač cesta WASB do vstupního souboru. | Ano      |
| output            | Určuje WASB cestu k výstupnímu souboru pro redukční funkci. | Ano      |
| getDebugInfo      | Určuje, kdy se zkopírují soubory protokolů do služby Azure Storage používaný v clusteru HDInsight (a) zadaný ve scriptLinkedService. Povolené hodnoty: NONE, vždy, nebo selhání. Výchozí hodnota: Žádné. | Ne       |
| argumenty         | Určuje pole argumentů pro úlohy Hadoopu. Argumenty jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| definuje           | Zadejte parametry pro odkazování v rámci skriptu Hive jako páry klíč/hodnota. | Ne       | 

## <a name="next-steps"></a>Další postup
Viz následující články, které vysvětlují, jak transformovat data dalšími způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita hivu](transform-data-using-hadoop-hive.md)
* [Aktivita pig](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita Spark](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita provedení dávky služby Learning počítače](transform-data-using-machine-learning.md)
* [Aktivita uložená procedura](transform-data-using-stored-procedure.md)
