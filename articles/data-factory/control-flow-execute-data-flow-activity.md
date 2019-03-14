---
title: Spuštění aktivity toku dat ve službě Azure Data Factory | Dokumentace Microsoftu
description: Spuštění aktivit toku dat spouštět datové toky.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 856f4bd9c2b04ff10ed598c5e641955e1de99398
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792408"
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

Použijte na datový tok ladění využívat topným zařízením clusteru pro interaktivní testování datové toky v kanálu ladění spustit. Použijte možnost Pipleine ladění k testování vašich toků dat v kanálu.

### <a name="compute-type"></a>Typ výpočtu

Můžete zvolit obecné účely, – Compute optimalizované nebo paměťově optimalizované, v závislosti na požadavcích datový tok.

### <a name="core-count"></a>Počet jader

Zvolte počet jader, kterou chcete přiřadit k úloze. Pro úlohy menší méně jádry fungují lépe.

### <a name="staging-area"></a>Pracovní oblasti

Pokud vaše data jsou vnořování do služby Azure Data Warehouse, je třeba zvolit pracovní umístění pro Polybase zatížení služby batch.

## <a name="parameterized-datasets"></a>Parametry datové sady

Pokud použijete parametry datové sady, nezapomeňte nastavit hodnoty parametrů.

![Spustit parametry toku dat](media/data-flow/params.png "parametry")

### <a name="debugging-parameterized-data-flows"></a>Ladění s parametry datové toky

Můžete ladit pouze datové toky s parametry datové sady z kanálu ladění spuštěn pomocí aktivity toku dat spouštět. V současné době interaktivní ladicích relací v toku dat ADF nefungují s parametry datové sady. Spuštění kanálu a spuštění ladění bude fungovat s parametry.

## <a name="next-steps"></a>Další postup
Zobrazit další aktivity toku řízení podporovaných službou Data Factory: 

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
