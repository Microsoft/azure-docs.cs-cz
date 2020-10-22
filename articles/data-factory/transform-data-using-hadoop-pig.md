---
title: Transformace dat pomocí aktivity prasete systému Hadoop
description: Zjistěte, jak můžete pomocí aktivity prasete v datové továrně Azure spouštět skripty prasete na vyžádání nebo vlastním clusteru HDInsight.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: 8b6f75204f64f892f80abb4890796551bffd22a7
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368954"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity systému Hadoop pro vepřové použití v Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-pig-activity.md)
> * [Aktuální verze](transform-data-using-hadoop-pig.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita prasete v HDInsight v [kanálu](concepts-pipelines-activities.md) Data Factory spouští dotazy na prasaty na [vašem](compute-linked-services.md#azure-hdinsight-linked-service) clusteru HDInsight nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Tento článek se sestavuje na článku [aktivity transformace dat](transform-data.md) , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

Pokud Azure Data Factory teprve začínáte, přečtěte si [Úvod do Azure Data Factory](introduction.md) a udělejte si [kurz: transformace dat](tutorial-transform-data-spark-powershell.md) před čtením tohoto článku. 

## <a name="syntax"></a>Syntax

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
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

| Vlastnost            | Popis                              | Povinné |
| ------------------- | ---------------------------------------- | -------- |
| name                | Název aktivity                     | Ano      |
| Popis         | Text popisující, k čemu se aktivita používá | Ne       |
| typ                | V případě aktivity podregistru je typ aktivity HDinsightPig. | Ano      |
| linkedServiceName   | Odkaz na cluster HDInsight registrovaný jako propojená služba v Data Factory. Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) . | Ano      |
| scriptLinkedService | Odkaz na propojenou službu Azure Storage, která se používá k uložení skriptu prasete, který se má spustit. Tady se podporují jenom propojené služby **[Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)** a **[adls Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)** . Pokud tuto propojenou službu nezadáte, použije se propojená služba Azure Storage definovaná v propojené službě HDInsight. | Ne       |
| scriptPath          | Zadejte cestu k souboru skriptu uloženému v Azure Storage, na kterou odkazuje scriptLinkedService. V názvu souboru se rozlišují malá a velká písmena. | Ne       |
| GetDebugInfo –        | Určuje, kdy se soubory protokolu zkopírují do Azure Storage používaného clusterem HDInsight (nebo) zadaným pomocí scriptLinkedService. Povolené hodnoty: žádné, vždy nebo chyba. Výchozí hodnota: Žádný. | Ne       |
| náhodné           | Určuje pole argumentů pro úlohu Hadoop. Argumenty jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| definuje             | Zadejte parametry jako páry klíč/hodnota pro odkazování v rámci skriptu pro vepřové prostředí. | No       |

## <a name="next-steps"></a>Další kroky
Podívejte se na následující články, které vysvětlují, jak transformovat data jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita v podregistru](transform-data-using-hadoop-hive.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Sparku](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita spuštění dávky Azure Machine Learning Studio (Classic)](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
