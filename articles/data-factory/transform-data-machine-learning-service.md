---
title: Spouštění kanálů Azure Machine Learning ve vašich Azure Data Factorych kanálech | Microsoft Docs
description: Naučte se spouštět kanály Azure Machine Learning ve vašich Azure Data Factorych kanálech.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: c05a0fde7019d9162a051a04696c2251301d70dc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490594"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Spouštění Azure Machine Learningch kanálů v Azure Data Factory

Spusťte kanály Azure Machine Learning jako krok v Azure Data Factorych kanálech. Aktivita spuštění kanálu Machine Learning umožňuje scénáře předpovědi dávek, jako je identifikace možných výchozích hodnot půjček, určení mínění a analýza vzorců chování zákazníků.

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
type | Typ aktivity je "AzureMLExecutePipeline". | Řetězec | Ano
linkedServiceName | Propojená služba k Azure Machine Learning | Odkaz na propojenou službu | Ano
mlPipelineId | ID publikovaného kanálu Azure Machine Learning | Řetězec (nebo výraz s hodnotou resultType řetězce) | Ano
experiment | Název experimentu historie spuštění Machine Learning spuštění kanálu | Řetězec (nebo výraz s hodnotou resultType řetězce) | Ne
mlPipelineParameters | Páry klíč-hodnota, které se mají předat publikovanému koncovému bodu kanálu Azure Machine Learning. Klíče se musí shodovat s názvy parametrů kanálu definovaných v publikovaném kanálu Machine Learning. | Objekt s páry klíč-hodnota (nebo výraz s objektem resultType) | Ne
mlParentRunId | ID spuštění nadřazeného Azure Machine Learning kanálu | Řetězec (nebo výraz s hodnotou resultType řetězce) | Ne
continueOnStepFailure | Určuje, jestli se má pokračovat v provádění dalších kroků v Machine Learning spuštění kanálu v případě, že dojde k chybě. | Boolean | Ne

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
