---
title: Transformace dat pomocí Hadoop Pig aktivity v Azure Data Factory | Microsoft Docs
description: Zjistěte, jak můžete pomocí aktivity Pig v objektu pro vytváření dat Azure ke spouštění skriptů Pig na na vyžádání nebo vaše vlastní cluster HDInsight.
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
ms.openlocfilehash: 9e769cc436011defe89b12680150e6f9c3b3faf8
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049311"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Transformace dat pomocí Hadoop Pig aktivity v Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-pig-activity.md)
> * [Aktuální verze](transform-data-using-hadoop-pig.md)

Aktivita HDInsight Pig v datové továrně [kanálu](concepts-pipelines-activities.md) provádí Pig dotazy na [vlastní](compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight. Tento článek vychází [aktivit transformace dat](transform-data.md) článek, který poskytne Obecné přehled o transformaci dat a aktivity podporované transformace.

Pokud jste do Azure Data Factory nové, přečtěte si [Úvod do Azure Data Factory](introduction.md) a proveďte [kurz: transformovat data](tutorial-transform-data-spark-powershell.md) před přečtení tohoto článku. 

## <a name="syntax"></a>Syntaxe

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
## <a name="syntax-details"></a>Syntaxe podrobnosti

| Vlastnost            | Popis                              | Požaduje se |
| ------------------- | ---------------------------------------- | -------- |
| jméno                | Název aktivity                     | Ano      |
| description         | Text popisující, co se používá aktivitu pro | Ne       |
| type                | Hive aktivity typ aktivity je HDinsightPig | Ano      |
| linkedServiceName   | Referenční dokumentace ke clusteru HDInsight registrován jako propojené služby ve službě Data Factory. Další informace o této propojené služby najdete v tématu [výpočetní propojené služby](compute-linked-services.md) článku. | Ano      |
| scriptLinkedService | Odkaz na propojenou službu úložiště Azure používají k ukládání Pig skript, který chcete provést. Pokud tato propojená služba nezadáte, použije se propojené služby Azure Storage definované v propojené službě HDInsight. | Ne       |
| scriptPath          | Zadejte cestu k souboru skriptu, který je uložený ve službě Azure Storage, na které odkazuje scriptLinkedService. Název souboru je malá a velká písmena. | Ne       |
| getdebuginfo –        | Určuje, kdy soubory protokolu se zkopírují do úložiště Azure používaný v clusteru HDInsight (a) zadaný ve scriptLinkedService. Povolené hodnoty: None, vždy nebo selhání. Výchozí hodnota: žádné. | Ne       |
| argumenty           | Určuje pole argumentů pro úlohy Hadoop. Argumenty, které jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| definuje             | Zadejte parametry pro odkazování v rámci skript Pig jako páry klíč/hodnota. | Ne       |

## <a name="next-steps"></a>Další postup
Najdete v následujících článcích, které vysvětlují, jak k transformaci dat jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita Hive](transform-data-using-hadoop-hive.md)
* [Činnost MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streamované aktivitě](transform-data-using-hadoop-streaming.md)
* [Spark aktivity](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning dávkového spuštění aktivity](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
