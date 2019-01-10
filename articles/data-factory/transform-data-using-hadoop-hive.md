---
title: Transformace dat pomocí aktivity Hivu Hadoop ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak můžete pomocí aktivity Hivu ve službě Azure data factory ke spouštění dotazů Hive v clusteru HDInsight na vyžádání/svůj vlastní.
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
ms.openlocfilehash: 6dc702b4bcffc0d1dec299093aa05ba480e9bbf8
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019975"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity Hivu Hadoop ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-hive-activity.md)
> * [Aktuální verze](transform-data-using-hadoop-hive.md)

Aktivita HDInsight Hive ve službě Data Factory [kanálu](concepts-pipelines-activities.md) spustí dotazy Hive na [vlastní](compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight. Tento článek vychází [aktivity transformace dat](transform-data.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace.

Pokud do služby Azure Data Factory začínáte, přečtěte si [Úvod do služby Azure Data Factory](introduction.md) a proveďte [kurz: transformace dat](tutorial-transform-data-spark-powershell.md) před čtením tohoto článku. 

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
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
| Vlastnost            | Popis                              | Požaduje se |
| ------------------- | ---------------------------------------- | -------- |
| jméno                | Název aktivity                     | Ano      |
| description         | Text popisující, k čemu aktivita slouží | Ne       |
| type                | Pro aktivitu Hive je aktivitu typu HDinsightHive | Ano      |
| linkedServiceName   | Odkaz na clusteru HDInsight zaregistrovaný jako propojenou službu ve službě Data Factory. Další informace o tuto propojenou službu, najdete v článku [propojené služby Compute](compute-linked-services.md) článku. | Ano      |
| scriptLinkedService | Odkaz na propojená služba Azure Storage se využívá k uložení skriptu Hive, který se spustí. Pokud nezadáte tuto propojenou službu, použije se propojená služba Azure Storage, definované v propojené službě HDInsight. | Ne       |
| ScriptPath          | Zadejte cestu k souboru skriptu ve službě Azure Storage odkazuje scriptLinkedService. Název souboru je velká a malá písmena. | Ano      |
| getDebugInfo        | Určuje, kdy se zkopírují soubory protokolů do služby Azure Storage používaný v clusteru HDInsight (a) zadaný ve scriptLinkedService. Povolené hodnoty: NONE, vždy, nebo selhání. Výchozí hodnota: Žádné. | Ne       |
| argumenty           | Určuje pole argumentů pro úlohy Hadoopu. Argumenty jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| definuje             | Zadejte parametry pro odkazování v rámci skriptu Hive jako páry klíč/hodnota. | Ne       |

## <a name="next-steps"></a>Další postup
Viz následující články, které vysvětlují, jak transformovat data dalšími způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita pig](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Spark](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita provedení dávky služby Learning počítače](transform-data-using-machine-learning.md)
* [Aktivita uložená procedura](transform-data-using-stored-procedure.md)

