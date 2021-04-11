---
title: Spustit Azure Machine Learning kanály
description: Naučte se spouštět kanály Azure Machine Learning ve vašich Azure Data Factorych kanálech.
ms.service: data-factory
ms.topic: conceptual
ms.author: weetok
author: dcstwh
ms.date: 07/16/2020
ms.openlocfilehash: d0b941325d6bbabcb083b5f71a40c145d3ef48d0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103466"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Spouštění Azure Machine Learningch kanálů v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Spusťte kanály Azure Machine Learning jako krok v Azure Data Factorych kanálech. Aktivita spuštění kanálu Machine Learning umožňuje scénáře předpovědi dávek, jako je identifikace možných výchozích hodnot půjček, určení mínění a analýza vzorců chování zákazníků.

Níže uvedené video funkce obsahují šest minut Úvod a ukázku této funkce.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Syntax

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | --------
name | Název aktivity v kanálu | Řetězec | Yes
typ | Typ aktivity je "AzureMLExecutePipeline". | Řetězec | Yes
linkedServiceName | Propojená služba k Azure Machine Learning | Odkaz na propojenou službu | Yes
mlPipelineId | ID publikovaného kanálu Azure Machine Learning | Řetězec (nebo výraz s hodnotou resultType řetězce) | Yes
experiment | Název experimentu historie spuštění Machine Learning spuštění kanálu | Řetězec (nebo výraz s hodnotou resultType řetězce) | No
mlPipelineParameters | Páry klíč-hodnota, které se mají předat publikovanému koncovému bodu kanálu Azure Machine Learning. Klíče se musí shodovat s názvy parametrů kanálu definovaných v publikovaném kanálu Machine Learning. | Objekt s páry klíč-hodnota (nebo výraz s objektem resultType) | No
mlParentRunId | ID spuštění nadřazeného Azure Machine Learning kanálu | Řetězec (nebo výraz s hodnotou resultType řetězce) | No
dataPathAssignments | Slovník, který se používá pro změnu DataPath ve službě Azure Machine Learning. Umožňuje přepínat mezi datacestami. | Objekt s páry klíč-hodnota | No
continueOnStepFailure | Určuje, jestli se má pokračovat v provádění dalších kroků v Machine Learning spuštění kanálu v případě, že dojde k chybě. | boolean | No

> [!NOTE]
> K naplnění položek rozevíracího seznamu Machine Learning název a ID kanálu musí mít uživatel oprávnění k vypsání kanálů ML. UŽIVATELSKÉ prostředí ADF volá rozhraní API AzureMLService přímo pomocí přihlašovacích údajů přihlášeného uživatele.  

## <a name="next-steps"></a>Další kroky
Podívejte se na následující články, které vysvětlují, jak transformovat data jinými způsoby:

* [Spustit aktivitu toku dat](control-flow-execute-data-flow-activity.md)
* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita v podregistru](transform-data-using-hadoop-hive.md)
* [Aktivita prasete](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Sparku](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
