---
title: Transformace dat pomocí aktivity Hadoop Hive
description: Zjistěte, jak můžete použít aktivitu Hive v datové továrně Azure ke spuštění dotazů Hive v clusteru HDInsight na vyžádání nebo ve vlastním clusteru HDInsight.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/15/2019
ms.openlocfilehash: 8c5c917e12b1314c40763f58a7723a4df787ffa0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418928"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity Hadoop Hive v Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-hive-activity.md)
> * [Aktuální verze](transform-data-using-hadoop-hive.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita HDInsight Hive v [kanálu](concepts-pipelines-activities.md) Datové továrny provádí dotazy Hive ve [vašem vlastním](compute-linked-services.md#azure-hdinsight-linked-service) clusteru HDInsight nebo na [vyžádání.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Tento článek vychází z článku [aktivit transformace dat,](transform-data.md) který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

Pokud jste s Azure Data Factory tečeme na [úvodním úvodu do Azure Data Factory](introduction.md) a proveďte [kurz: transformujte data](tutorial-transform-data-spark-powershell.md) před přečtením tohoto článku. 

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
## <a name="syntax-details"></a>Podrobnosti syntaxe
| Vlastnost            | Popis                                                  | Požaduje se |
| ------------------- | ------------------------------------------------------------ | -------- |
| jméno                | Název aktivity                                         | Ano      |
| description         | Text popisující, k čemu se aktivita používá                | Ne       |
| type                | Pro aktivitu Hive je typem aktivity HDinsightHive        | Ano      |
| linkedServiceName   | Odkaz na cluster HDInsight registrovaný jako propojená služba v datové továrně. Další informace o této propojené službě najdete v článku [Výpočetní propojené služby.](compute-linked-services.md) | Ano      |
| skriptLinkedService | Odkaz na službu propojenou úložištěm Azure, která slouží k ukládání skriptu Hive, který má být spuštěn. Pokud tuto propojenou službu nezadáte, použije se služba Propojená úložištěm Azure definovaná ve službě HDInsight Linked Service. | Ne       |
| scriptPath          | Zadejte cestu k souboru skriptu uloženému ve službě Azure Storage, na kterou odkazuje scriptLinkedService. Název souboru rozlišuje malá a velká písmena. | Ano      |
| getDebugInfo        | Určuje, kdy se soubory protokolu zkopírují do úložiště Azure, které používá cluster HDInsight (nebo) určené scriptLinkedService. Povolené hodnoty: Žádné, Vždy nebo Selhání. Výchozí hodnota: Žádný. | Ne       |
| Argumenty           | Určuje pole argumentů pro úlohu Hadoop. Argumenty jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| Definuje             | Zadejte parametry jako dvojice klíč/hodnota pro odkazování ve skriptu Hive. | Ne       |
| queryTimeout        | Hodnota časového limitu dotazu (v minutách). Platí v případě, že je cluster HDInsight vybaven povoleným balíčkem podnikového zabezpečení. | Ne       |

## <a name="next-steps"></a>Další kroky
Další články vysvětlují, jak transformovat data jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita prasat](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování hadoopu](transform-data-using-hadoop-streaming.md)
* [Aktivita jiskry](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita dávkového spuštění strojového učení](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
