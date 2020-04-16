---
title: Transformace dat pomocí aktivity Hadoop Pig
description: Zjistěte, jak můžete pomocí aktivity prasete v datové továrně Azure spouštět skripty Pig na vyžádání nebo ve vlastním clusteru HDInsight.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 73ce8c670940a31af6a88f98bfd5880ede259e01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418894"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity Hadoop Pig v Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-pig-activity.md)
> * [Aktuální verze](transform-data-using-hadoop-pig.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita HDInsight Pig v [datové továrně](concepts-pipelines-activities.md) provádí dotazy Pig ve vašem vlastním clusteru HDInsight nebo [na vyžádání.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) [your own](compute-linked-services.md#azure-hdinsight-linked-service) Tento článek vychází z článku [aktivit transformace dat,](transform-data.md) který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

Pokud jste s Azure Data Factory tečeme na [úvodním úvodu do Azure Data Factory](introduction.md) a proveďte [kurz: transformujte data](tutorial-transform-data-spark-powershell.md) před přečtením tohoto článku. 

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
## <a name="syntax-details"></a>Podrobnosti syntaxe

| Vlastnost            | Popis                              | Požaduje se |
| ------------------- | ---------------------------------------- | -------- |
| jméno                | Název aktivity                     | Ano      |
| description         | Text popisující, k čemu se aktivita používá | Ne       |
| type                | Pro aktivitu Hive je typem aktivity HDinsightPig | Ano      |
| linkedServiceName   | Odkaz na cluster HDInsight registrovaný jako propojená služba v datové továrně. Další informace o této propojené službě najdete v článku [Výpočetní propojené služby.](compute-linked-services.md) | Ano      |
| skriptLinkedService | Odkaz na azure storage propojené služby slouží k ukládání pig skript, který má být proveden. Pokud tuto propojenou službu nezadáte, použije se služba Propojená úložištěm Azure definovaná ve službě HDInsight Linked Service. | Ne       |
| scriptPath          | Zadejte cestu k souboru skriptu uloženému ve službě Azure Storage, na kterou odkazuje scriptLinkedService. Název souboru rozlišuje malá a velká písmena. | Ne       |
| getDebugInfo        | Určuje, kdy se soubory protokolu zkopírují do úložiště Azure, které používá cluster HDInsight (nebo) určené scriptLinkedService. Povolené hodnoty: Žádné, Vždy nebo Selhání. Výchozí hodnota: Žádný. | Ne       |
| Argumenty           | Určuje pole argumentů pro úlohu Hadoop. Argumenty jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| Definuje             | Zadejte parametry jako páry klíč/hodnota pro odkazování ve skriptu Pig. | Ne       |

## <a name="next-steps"></a>Další kroky
Další články vysvětlují, jak transformovat data jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita úlu](transform-data-using-hadoop-hive.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování hadoopu](transform-data-using-hadoop-streaming.md)
* [Aktivita jiskry](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita dávkového spuštění strojového učení](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
