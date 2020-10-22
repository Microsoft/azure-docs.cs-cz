---
title: Transformace dat pomocí aktivity podregistru Hadoop
description: Přečtěte si, jak můžete použít aktivitu podregistru v Azure Data Factory ke spouštění dotazů na podregistr na vyžádání nebo vlastní cluster HDInsight.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 05/08/2019
ms.openlocfilehash: 5381fea4c492c6bcf61c392ea8a8f9bee8860644
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368923"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Transformuje data pomocí aktivity podregistru Hadoop v Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-hive-activity.md)
> * [Aktuální verze](transform-data-using-hadoop-hive.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita podregistru HDInsight v [kanálu](concepts-pipelines-activities.md) Data Factory spouští dotazy na podregistry na [vašem](compute-linked-services.md#azure-hdinsight-linked-service) clusteru HDInsight nebo [na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)  . Tento článek se sestavuje na článku [aktivity transformace dat](transform-data.md) , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

Pokud Azure Data Factory teprve začínáte, přečtěte si [Úvod do Azure Data Factory](introduction.md) a udělejte si [kurz: transformace dat](tutorial-transform-data-spark-powershell.md) před čtením tohoto článku. 

## <a name="syntax"></a>Syntax

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
| Vlastnost            | Popis                                                  | Povinné |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | Název aktivity                                         | Ano      |
| Popis         | Text popisující, k čemu se aktivita používá                | Ne       |
| typ                | V případě aktivity podregistru je typ aktivity HDinsightHive.        | Ano      |
| linkedServiceName   | Odkaz na cluster HDInsight registrovaný jako propojená služba v Data Factory. Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) . | Ano      |
| scriptLinkedService | Odkaz na propojenou službu Azure Storage, která se používá k uložení skriptu podregistru, který se má spustit. Tady se podporují jenom propojené služby **[Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)** a **[adls Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)** . Pokud tuto propojenou službu nezadáte, použije se propojená služba Azure Storage definovaná v propojené službě HDInsight.  | Ne       |
| scriptPath          | Zadejte cestu k souboru skriptu uloženému v Azure Storage, na kterou odkazuje scriptLinkedService. V názvu souboru se rozlišují malá a velká písmena. | Ano      |
| GetDebugInfo –        | Určuje, kdy se soubory protokolu zkopírují do Azure Storage používaného clusterem HDInsight (nebo) zadaným pomocí scriptLinkedService. Povolené hodnoty: žádné, vždy nebo chyba. Výchozí hodnota: Žádný. | Ne       |
| náhodné           | Určuje pole argumentů pro úlohu Hadoop. Argumenty jsou předány jako argumenty příkazového řádku pro každý úkol. | Ne       |
| definuje             | Zadejte parametry jako páry klíč/hodnota pro odkazování v rámci skriptu podregistru. | Ne       |
| queryTimeout        | Hodnota časového limitu dotazu (v minutách) Platí v případě, že je cluster HDInsight s povoleným Balíček zabezpečení podniku. | No       |

## <a name="next-steps"></a>Další kroky
Podívejte se na následující články, které vysvětlují, jak transformovat data jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita prasete](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Sparku](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita spuštění dávky Azure Machine Learning Studio (Classic)](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
