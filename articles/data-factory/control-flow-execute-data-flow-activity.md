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
ms.openlocfilehash: 24b27c16573a35b1d8749d7ff381fbef970f4bd0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471661"
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

### <a name="debugging-pipelines-with-data-flows"></a>Ladění kanálů s toky dat

![Ladění tlačítko](media/data-flow/debugbutton.png "tlačítko ladit.")

Použijte na datový tok ladění využívat topným zařízením clusteru pro interaktivní testování datové toky v kanálu ladění spustit. Použijte možnost kanálu ladění k testování vašich toků dat v kanálu.

### <a name="run-on"></a>Spusťte

Toto je povinné pole, která definuje, které prostředí IR pro váš tok dat provádění aktivity. Ve výchozím nastavení bude služba Data Factory používat výchozí prostředí Azure Integration runtime automaticky vyřešit. Můžete však vytvořit vlastní Azure prostředí Integration runtime, který definovat konkrétní oblasti, výpočetní typu, počty jader a hodnota TTL pro provádění aktivity toku vaše data.

Výchozí nastavení pro spuštění toku dat je 8jádrový obecné výpočetní s hodnotou TTL 60 minut.

Vyberte výpočetní prostředí pro toto spuštění toku data. Výchozí hodnota je výchozí prostředí IR Azure automaticky vyřešit. Tato volba spustí tok dat v prostředí Spark ve stejné oblasti jako svou datovou továrnu. Typ výpočtu bude clusteru úloh, což znamená, že prostředí compute bude trvat několik minut na spuštění.

Pro vaše aktivity toku dat máte kontrolu nad spouštěcí prostředí Spark. V [prostředí Azure integration runtime](concepts-integration-runtime.md) jsou nastavení typ výpočtu (obecné účely, paměťově optimalizované a optimalizováno pro výpočty), počet jader pro pracovníka a time-to-live tak, aby odpovídaly prováděcí modul s výpočetní toku dat požadavky. Také nastavení TTL umožňuje udržovat teplé clusteru, který je hned dostupný pro spuštění úlohy.

![Prostředí Azure Integration Runtime](media/data-flow/ir-new.png "prostředí Azure Integration Runtime")

> [!NOTE]
> Výběr prostředí Integration Runtime v rámci aktivity toku dat platí jenom pro *aktivuje spuštění* vašeho kanálu. Ladění svůj kanál s toky dat ladicí program se spustí v clusteru Spark 8jádrový výchozí.

### <a name="staging-area"></a>Pracovní oblasti

Pokud vaše data jsou vnořování do služby Azure Data Warehouse, je třeba zvolit pracovní umístění pro Polybase zatížení služby batch. Pracovní nastavení platí jenom pro úlohy Azure Data Warehouse.

## <a name="parameterized-datasets"></a>Parametry datové sady

Pokud použijete parametry datové sady, nezapomeňte nastavit hodnoty parametrů.

![Spustit parametry toku dat](media/data-flow/params.png "parametry")

## <a name="parameterized-data-flows"></a>Parametry datové toky

Pokud máte parametry vašeho toku dat, nastavíte dynamické hodnoty tady parametry toku dat v sekci parametrů spuštění toku dat aktivit. Jazyk výrazů kanálu ADF (pouze pro typy parametrů řetězce) nebo jazyk výrazů tok dat můžete použít k nastavení hodnoty parametrů s výrazy dynamické nebo statické hodnoty literálu.

![Spusťte příklad parametr toku dat](media/data-flow/parameter-example.png "parametr příklad")

### <a name="debugging-data-flows-with-parameters"></a>Ladění datové toky s parametry

V tuto chvíli aktuální můžete ladit pouze datové toky s parametry z kanálu ladění spuštěn pomocí aktivity toku dat spouštět. Interaktivní ladicích relací v ADF toku dat je již brzy. Spuštění kanálu a spuštění ladění, ale bude fungovat s parametry.

Dobrým postupem je vytvoření toku dat s statický obsah, abyste měli k dispozici šíření sloupce – plná metadata v době návrhu pro odstraňování poruch. Potom nahraďte statické datové sady s datovou sadou dynamické parametry při zprovoznění vašeho kanálu toku dat

## <a name="next-steps"></a>Další postup
Zobrazit další aktivity toku řízení podporovaných službou Data Factory: 

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
