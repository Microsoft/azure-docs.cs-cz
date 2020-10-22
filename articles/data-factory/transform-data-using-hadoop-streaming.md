---
title: Transformace dat pomocí aktivity streamování Hadoop
description: Vysvětluje, jak použít aktivitu streamování Hadoop v Azure Data Factory k transformaci dat spuštěním programů pro streamování Hadoop v clusteru Hadoop.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: 7cc8e2e02aef9e323da9859ce6fd0bebea2ce036
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368906"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity streamování Hadoop v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Aktuální verze](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita streamování HDInsight v [kanálu](concepts-pipelines-activities.md) Data Factory spouští programy pro streamování Hadoop na [vašem](compute-linked-services.md#azure-hdinsight-linked-service) clusteru HDInsight nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Tento článek se sestavuje na článku [aktivity transformace dat](transform-data.md) , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

Pokud Azure Data Factory teprve začínáte, přečtěte si [Úvod do Azure Data Factory](introduction.md) a udělejte si [kurz: transformace dat](tutorial-transform-data-spark-powershell.md) před čtením tohoto článku. 

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

## <a name="syntax-details"></a>Podrobnosti syntaxe

| Vlastnost          | Popis                              | Povinné |
| ----------------- | ---------------------------------------- | -------- |
| name              | Název aktivity                     | Ano      |
| Popis       | Text popisující, k čemu se aktivita používá | Ne       |
| typ              | Pro aktivitu streamování Hadoop je typ aktivity HDInsightStreaming | Ano      |
| linkedServiceName | Odkaz na cluster HDInsight registrovaný jako propojená služba v Data Factory. Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) . | Ano      |
| Mapper            | Určuje název spustitelného souboru mapovače. | Ano      |
| redukce           | Určuje název spustitelného souboru nástroje pro zmenšení. | Ano      |
| kombinační          | Určuje název spustitelného souboru kombinované aplikace. | Ne       |
| fileLinkedService | Odkaz na propojenou službu Azure Storage, která se používá k uložení programů pro mapování, kombinování a snižování. Tady se podporují jenom propojené služby **[Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)** a **[adls Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)** . Pokud tuto propojenou službu nezadáte, použije se propojená služba Azure Storage definovaná v propojené službě HDInsight. | Ne       |
| filePath          | Poskytněte pole cesty k programům mapovače, kombinování a snižování, které jsou uložené v Azure Storage, na kterou odkazuje fileLinkedService. V této cestě se rozlišují velká a malá písmena. | Ano      |
| vstup             | Určuje cestu WASB ke vstupnímu souboru pro mapovač. | Ano      |
| output            | Určuje cestu WASB k výstupnímu souboru pro tuto redukci. | Ano      |
| GetDebugInfo –      | Určuje, kdy se soubory protokolu zkopírují do Azure Storage používaného clusterem HDInsight (nebo) zadaným pomocí scriptLinkedService. Povolené hodnoty: žádné, vždy nebo chyba. Výchozí hodnota: Žádný. | Ne       |
| náhodné         | Určuje pole argumentů pro úlohu Hadoop. Argumenty jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| definuje           | Zadejte parametry jako páry klíč/hodnota pro odkazování v rámci skriptu podregistru. | No       | 

## <a name="next-steps"></a>Další kroky
Podívejte se na následující články, které vysvětlují, jak transformovat data jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita v podregistru](transform-data-using-hadoop-hive.md)
* [Aktivita prasete](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita Sparku](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita spuštění dávky Azure Machine Learning Studio (Classic)](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
