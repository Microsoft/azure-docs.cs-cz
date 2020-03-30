---
title: Transformace dat pomocí aktivity streamování Hadoop
description: Vysvětluje, jak používat aktivitu streamování Hadoop v Azure Data Factory k transformaci dat spuštěním programů Hadoop Streaming v clusteru Hadoop.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 1c12a10dfdf8e69cf05ab30d0e6aa48fea5803a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912900"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity streamování Hadoop v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Aktuální verze](transform-data-using-hadoop-streaming.md)

Aktivita streamování HDInsight v [kanálu](concepts-pipelines-activities.md) Data Factory provádí programy Hadoop Streaming ve vašem vlastním clusteru HDInsight nebo [na vyžádání.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) [your own](compute-linked-services.md#azure-hdinsight-linked-service) Tento článek vychází z článku [aktivit transformace dat,](transform-data.md) který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

Pokud jste s Azure Data Factory tečeme na [úvodním úvodu do Azure Data Factory](introduction.md) a proveďte [kurz: transformujte data](tutorial-transform-data-spark-powershell.md) před přečtením tohoto článku. 

## <a name="json-sample"></a>Vzorek JSON
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

## <a name="syntax-details"></a>Podrobnosti syntaxe

| Vlastnost          | Popis                              | Požaduje se |
| ----------------- | ---------------------------------------- | -------- |
| jméno              | Název aktivity                     | Ano      |
| description       | Text popisující, k čemu se aktivita používá | Ne       |
| type              | Pro aktivitu streamování Hadoop je typem aktivity HDInsightStreaming | Ano      |
| linkedServiceName | Odkaz na cluster HDInsight registrovaný jako propojená služba v datové továrně. Další informace o této propojené službě najdete v článku [Výpočetní propojené služby.](compute-linked-services.md) | Ano      |
| Mapovač            | Určuje název spustitelného souboru mapovače. | Ano      |
| Redukce           | Určuje název spustitelného souboru reduktoru. | Ano      |
| kombinátor          | Určuje název spustitelného souboru kombinátoru. | Ne       |
| fileLinkedService | Odkaz na službu propojenou úložištěm Azure, která slouží k ukládání programů Mapper, Combiner a Reducer, které mají být provedeny. Pokud tuto propojenou službu nezadáte, použije se služba Propojená úložištěm Azure definovaná ve službě HDInsight Linked Service. | Ne       |
| Filepath          | Poskytněte pole cesty k programům Mapper, Combiner a Reducer uloženým ve službě Azure Storage, na kterou odkazuje fileLinkedService. V této cestě se rozlišují velká a malá písmena. | Ano      |
| vstup             | Určuje cestu WASB ke vstupnímu souboru mapovače. | Ano      |
| output            | Určuje cestu WASB k výstupnímu souboru reduktoru. | Ano      |
| getDebugInfo      | Určuje, kdy se soubory protokolu zkopírují do úložiště Azure, které používá cluster HDInsight (nebo) určené scriptLinkedService. Povolené hodnoty: Žádné, Vždy nebo Selhání. Výchozí hodnota: Žádný. | Ne       |
| Argumenty         | Určuje pole argumentů pro úlohu Hadoop. Argumenty jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| Definuje           | Zadejte parametry jako dvojice klíč/hodnota pro odkazování ve skriptu Hive. | Ne       | 

## <a name="next-steps"></a>Další kroky
Další články vysvětlují, jak transformovat data jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita úlu](transform-data-using-hadoop-hive.md)
* [Aktivita prasat](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita jiskry](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita dávkového spuštění strojového učení](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
