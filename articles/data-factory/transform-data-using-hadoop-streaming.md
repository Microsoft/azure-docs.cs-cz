---
title: Transformace dat pomocí Hadoop streamované aktivitě v Azure Data Factory | Microsoft Docs
description: Vysvětluje, jak používat streamované aktivitě Hadoop v Azure Data Factory k transformaci dat spuštěním streamování Hadoop programy na clusteru Hadoop.
services: data-factory
documentationcenter: ''
author: shengcmsft
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 7c882e6fd826adb415b0452c9b441405d2ff90d7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformace dat pomocí Hadoop streamované aktivitě v Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-hadoop-streaming-activity.md)
> * [Verze 2 – Preview](transform-data-using-hadoop-streaming.md)

HDInsight streamované aktivitě v datové továrně [kanálu](concepts-pipelines-activities.md) provede streamování Hadoop programy na [vlastní](compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight. Tento článek vychází [aktivit transformace dat](transform-data.md) článek, který poskytne Obecné přehled o transformaci dat a aktivity podporované transformace.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [streamované aktivitě Hadoop v V1](v1/data-factory-hadoop-streaming-activity.md).

Pokud jste do Azure Data Factory nové, přečtěte si [Úvod do Azure Data Factory](introduction.md) a proveďte [kurz: transformovat data](tutorial-transform-data-spark-powershell.md) před přečtení tohoto článku. 

## <a name="json-sample"></a>Ukázka JSON
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

## <a name="syntax-details"></a>Syntaxe podrobnosti

| Vlastnost          | Popis                              | Požaduje se |
| ----------------- | ---------------------------------------- | -------- |
| jméno              | Název aktivity                     | Ano      |
| description       | Text popisující, co se používá aktivitu pro | Ne       |
| type              | Typ aktivity streamované aktivitě Hadoop, je HDInsightStreaming | Ano      |
| linkedServiceName | Referenční dokumentace ke clusteru HDInsight registrován jako propojené služby ve službě Data Factory. Další informace o této propojené služby najdete v tématu [výpočetní propojené služby](compute-linked-services.md) článku. | Ano      |
| Mapper            | Určuje název spustitelného souboru mapper | Ano      |
| reduktorem           | Určuje název spustitelného souboru reduktorem | Ano      |
| kombinační          | Určuje název spustitelného souboru kombinační | Ne       |
| fileLinkedService | Odkaz na propojenou službu úložiště Azure používají k ukládání Mapper, kombinační a reduktorem spouštění programů. Pokud tato propojená služba nezadáte, použije se propojené služby Azure Storage definované v propojené službě HDInsight. | Ne       |
| filePath          | Zadejte pole cesty k Mapper, kombinační, a programy reduktorem uložené ve službě Azure Storage, na které se odkazuje fileLinkedService. V této cestě se rozlišují velká a malá písmena. | Ano      |
| Vstup             | Určuje WASB cestu k souboru vstupního souboru Mapper. | Ano      |
| output            | Určuje cestu WASB do výstupního souboru pro reduktorem. | Ano      |
| getDebugInfo      | Určuje, kdy soubory protokolu se zkopírují do úložiště Azure používaný v clusteru HDInsight (a) zadaný ve scriptLinkedService. Povolené hodnoty: None, vždy nebo selhání. Výchozí hodnota: žádné. | Ne       |
| Argumenty         | Určuje pole argumentů pro úlohy Hadoop. Argumenty, které jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| definuje           | Zadejte parametry pro odkazování v rámci skriptu Hive jako páry klíč/hodnota. | Ne       | 

## <a name="next-steps"></a>Další postup
Najdete v následujících článcích, které vysvětlují, jak k transformaci dat jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita Hive](transform-data-using-hadoop-hive.md)
* [Pig aktivity](transform-data-using-hadoop-pig.md)
* [Činnost MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Spark aktivity](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning dávkového spuštění aktivity](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
