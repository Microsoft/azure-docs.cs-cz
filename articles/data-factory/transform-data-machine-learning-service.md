---
title: Spuštění kanálů Azure Machine Learning
description: Zjistěte, jak spustit kanály Azure Machine Learning v kanálech Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: f033651eb7e52ba60cce9b74941a4ef0eb376d2b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418996"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Spouštění kanálů Azure Machine Learning v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Spusťte kanály Azure Machine Learning jako krok v kanálech Azure Data Factory. Aktivita kanálu spuštění strojového učení umožňuje scénáře predikce dávky, jako je identifikace možných výchozích hodnot půjčky, určení mínění a analýza vzorců chování zákazníků.

Níže uvedené video obsahuje šestiminutový úvod a ukázku této funkce.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Syntaxe

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

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
jméno | Název aktivity v kanálu | Řetězec | Ano
type | Typ aktivity je "AzureMLExecutePipeline" | Řetězec | Ano
linkedServiceName | Propojená služba s Azure Machine Learning | Odkaz na propojenou službu | Ano
mlPipelineId | ID publikovaného kanálu Azure Machine Learning | Řetězec (nebo výraz s resultType řetězce) | Ano
název_experimentu | Spuštění názvu experimentu historie spuštění kanálu Machine Learning | Řetězec (nebo výraz s resultType řetězce) | Ne
mlPipelineParameters | Klíč, dvojice hodnot, které mají být předány publikovaným koncovým bodem kanálu Azure Machine Learning. Klíče musí odpovídat názvům parametrů kanálu definovaným v publikovaném kanálu Machine Learning. | Objekt s dvojicemi hodnot klíče (nebo Výraz s objektem resultType) | Ne
mlParentRunId | ID spuštění nadřazeného kanálu Azure Machine Learning | Řetězec (nebo výraz s resultType řetězce) | Ne
continueOnStepFailure | Jestli pokračovat v provádění dalších kroků v kanálu Machine Learning spustit, pokud krok selže | Boolean | Ne

## <a name="next-steps"></a>Další kroky
Další články vysvětlují, jak transformovat data jinými způsoby:

* [Spustit aktivitu toku dat](control-flow-execute-data-flow-activity.md)
* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita úlu](transform-data-using-hadoop-hive.md)
* [Aktivita prasat](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování hadoopu](transform-data-using-hadoop-streaming.md)
* [Aktivita jiskry](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
