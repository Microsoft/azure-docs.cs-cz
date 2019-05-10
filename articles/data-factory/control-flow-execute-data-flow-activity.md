---
title: Spuštění aktivity toku dat ve službě Azure Data Factory | Dokumentace Microsoftu
description: Jak provádět datové toky z uvnitř kanál datové továrny.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: e1d4ce355f34014d5099c4b46f4420d032363fce
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236671"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Spuštění aktivity toku dat ve službě Azure Data Factory
Spuštění toku dat ADF v ladění (sandbox) spuštění kanálu a spuštění kanálu aktivované pomocí aktivity toku dat spouštět.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>Typ vlastnosti

* ```dataflow``` je název entity toku dat, kterou chcete provést
* ```compute``` Popisuje spuštění prostředí Spark
* ```coreCount``` počet jader pro přiřazení k provádění této aktivity vašeho toku dat

![Spuštění toku dat](media/data-flow/activity-data-flow.png "provedení toku dat.")

### <a name="run-on"></a>Spustit v

Vyberte výpočetní prostředí pro toto spuštění toku data. Výchozí hodnota je výchozí prostředí IR Azure automaticky vyřešit. Tato volba spustí tok dat v prostředí Spark ve stejné oblasti jako svou datovou továrnu. Typ výpočtu bude clusteru úloh, což znamená, že prostředí compute bude trvat několik minut na spuštění.

### <a name="debugging-pipelines-with-data-flows"></a>Ladění kanálů s toky dat

![Ladění tlačítko](media/data-flow/debugbutton.png "tlačítko ladit.")

Použijte na datový tok ladění využívat topným zařízením clusteru pro interaktivní testování datové toky v kanálu ladění spustit. Použijte možnost kanálu ladění k testování vašich toků dat v kanálu.

### <a name="run-on"></a>Spustit v

Toto je povinné pole, která definuje, které prostředí IR pro váš tok dat provádění aktivity. Ve výchozím nastavení bude služba Data Factory používat výchozí prostředí Azure Integration runtime automaticky vyřešit. Můžete však vytvořit vlastní Azure prostředí Integration runtime, který definovat konkrétní oblasti, výpočetní typu, počty jader a hodnota TTL pro provádění aktivity toku vaše data.

Výchozí nastavení pro spuštění toku dat je 8jádrový obecné výpočetní s hodnotou TTL 60 minut.

### <a name="staging-area"></a>Pracovní oblasti

Pokud vaše data jsou vnořování do služby Azure Data Warehouse, je třeba zvolit pracovní umístění pro Polybase zatížení služby batch.

## <a name="parameterized-datasets"></a>Parametry datové sady

Pokud použijete parametry datové sady, nezapomeňte nastavit hodnoty parametrů.

![Spustit parametry toku dat](media/data-flow/params.png "parametry")

### <a name="debugging-parameterized-data-flows"></a>Ladění s parametry datové toky

Můžete ladit pouze datové toky s parametry datové sady z kanálu ladění spuštěn pomocí aktivity toku dat spouštět. V současné době interaktivní ladicích relací v toku dat ADF nefungují s parametry datové sady. Spuštění kanálu a spuštění ladění bude fungovat s parametry.

Dobrým postupem je vytvoření toku dat pomocí statické datové sady, abyste měli k dispozici šíření sloupce – plná metadata v době návrhu. Potom nahraďte statické datové sady s datovou sadou dynamické parametry při zprovoznění vašeho kanálu toku dat

## <a name="next-steps"></a>Další postup
Zobrazit další aktivity toku řízení podporovaných službou Data Factory: 

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
