---
title: Aktivita toku dat
description: Jak spustit toky dat z kanálu datové továrny.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 03/16/2020
ms.openlocfilehash: 115cb3e499117457629e130b6432a1cbc2224edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79463046"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Aktivita toku dat ve Službě Azure Data Factory

Aktivita Tok dat slouží k transformaci a přesouvání dat prostřednictvím mapování datových toků. Pokud s toky dat teču nejste, přečtěte si části [Mapování toku dat](concepts-data-flow-overview.md)

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
Dataflow | Odkaz na prováděný tok dat | DataFlowReference | Ano
integraceDoba běhu | Výpočetní prostředí, ve které běží tok dat. Pokud není zadán, bude použit a automaticky přeložit za běhu integrace Azure | IntegrationRuntimeReference | Ne
compute.coreCount | Počet jader použitých v jiskřícím clusteru. Lze zadat pouze v případě, že se používá runtime integrace Azure. | 8, 16, 32, 48, 80, 144, 272 | Ne
compute.computeType | Typ výpočetních prostředků použitých v clusteru jiskry. Lze zadat pouze v případě, že se používá runtime integrace Azure. | "Obecné", "ComputeOptimized", "MemoryOptimized" | Ne
staging.linkedService | Pokud používáte zdroj NEBO jímku SQL DW, účet úložiště použitý pro pracovní polybase | Odkaz služby LinkedServiceReference | Pouze v případě, že tok dat čte nebo zapisuje do SQL DW
staging.folderPath | Pokud používáte zdroj nebo jímku SQL DW, cesta ke složce v účtu úložiště objektů blob použitá pro pracovní prostředí PolyBase | Řetězec | Pouze v případě, že tok dat čte nebo zapisuje do SQL DW

![Spustit tok dat](media/data-flow/activity-data-flow.png "Spustit tok dat")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Výpočetní výpočetní tok dat s dynamickou velikostí za běhu

Vlastnosti Počet jádra a Typ výpočetního výkonu lze dynamicky nastavit tak, aby se přizpůsobily velikosti příchozích zdrojových dat za běhu. Pomocí aktivit kanálu, jako je vyhledávání nebo získat metadata, můžete najít velikost dat zdrojové datové sady. Potom použijte přidat dynamický obsah ve vlastnostech aktivity toku dat.

![Dynamický tok dat](media/data-flow/dyna1.png "Dynamický tok dat")

[Zde je stručný instruktážní video vysvětluje tuto techniku](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Za běhu integrace toku dat

Zvolte, který integrační běh ový čas se má použít pro spuštění aktivity toku dat. Ve výchozím nastavení bude Data Factory používat runtime integrace Azure s automatickým překladem se čtyřmi pracovními jádry a žádný čas na život (TTL). Tento infračervený přenos má typ výpočetních prostředků pro obecné účely a běží ve stejné oblasti jako vaše továrna. Můžete vytvořit vlastní runtimes integrace Azure, které definují konkrétní oblasti, typ výpočetních prostředků, počty jader a TTL pro spuštění aktivity toku dat.

Pro spuštění kanálu clusteru je cluster úloh, který trvá několik minut ke spuštění před spuštěním spuštění. Pokud není zadán žádný TTL, tato doba spuštění je vyžadována při každém spuštění kanálu. Pokud zadáte TTL, teplý fond clusteru zůstane aktivní po dobu určenou po posledním spuštění, což má za následek kratší časy spuštění. Například pokud máte TTL 60 minut a spustit tok dat na něm jednou za hodinu, fond clusteru zůstane aktivní. Další informace najdete v [tématu Azure integration runtime](concepts-integration-runtime.md).

![Doba běhu integrace Azure](media/data-flow/ir-new.png "Doba běhu integrace Azure")

> [!NOTE]
> Výběr integračního běhu v aktivitě toku dat se vztahuje pouze na *spuštěná spuštění* vašeho kanálu. Ladění kanálu s toky dat běží na clusteru určeném v relaci ladění.

### <a name="polybase"></a>PolyBase

Pokud používáte Azure SQL Data Warehouse jako jímku nebo zdroj, musíte zvolit pracovní umístění pro dávkové zatížení PolyBase. PolyBase umožňuje hromadné dávkové načítání namísto načítání dat řádek po řádku. PolyBase výrazně snižuje dobu načítání do SQL DW.

## <a name="parameterizing-data-flows"></a>Parametrizace toků dat

### <a name="parameterized-datasets"></a>Parametrizované datové sady

Pokud tok dat používá parametrizované datové sady, nastavte hodnoty parametrů na kartě **Nastavení.**

![Spustit parametry toku dat](media/data-flow/params.png "Parametry")

### <a name="parameterized-data-flows"></a>Parametrizované toky dat

Pokud je tok dat parametrizován, nastavte dynamické hodnoty parametrů toku dat na kartě **Parametry.** Můžete použít buď jazyk výrazu kanálu ADF (pouze pro typy řetězců), nebo jazyk výrazu toku dat k přiřazení dynamických nebo literálových hodnot parametrů. Další informace naleznete v [tématu Parametry toku dat](parameters-data-flow.md).

![Příklad parametru toku dat](media/data-flow/parameter-example.png "Příklad parametru")

### <a name="parameterized-compute-properties"></a>Parametrizované výpočetní vlastnosti.

Pokud použijete runtime s automatickou resolveu integrace Azure a zadáte hodnoty pro compute.coreCount a compute.computeType, můžete parametrizovat počet jader nebo typ výpočetní doby.

![Příklad parametru toku dat](media/data-flow/parameterize-compute.png "Příklad parametru")

## <a name="pipeline-debug-of-data-flow-activity"></a>Ladění kanálu aktivity toku dat

Chcete-li spustit kanál ladění se spuštěnou aktivitou toku dat, musíte zapnout režim ladění toku dat pomocí jezdce **Ladění toku dat** na horním panelu. Režim ladění umožňuje spustit tok dat proti aktivnímu clusteru Spark. Další informace naleznete [v tématu Režim ladění](concepts-data-flow-debug-mode.md).

![Tlačítko Ladění](media/data-flow/debugbutton.png "Tlačítko Ladění")

Kanál ladění je spuštěn proti aktivnímu ladicímu clusteru, nikoli integračnímu runtime prostředí určenému v nastavení aktivity toku dat. Při spuštění režimu ladění můžete zvolit výpočetní prostředí ladění.

## <a name="monitoring-the-data-flow-activity"></a>Sledování aktivity toku dat

Aktivita toku dat má speciální prostředí monitorování, kde můžete zobrazit dělení, čas fáze a informace o datové masivu. Otevřete podokno monitorování pomocí ikony brýlí v části **Akce**. Další informace naleznete v [tématu Sledování toků dat](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Použití výsledků aktivity toku dat v následné aktivitě

Aktivita toku dat výstupy metriky týkající se počtu řádků zapsaných do každého jímky a řádky číst z každého zdroje. Tyto výsledky jsou `output` vráceny v části výsledek spuštění aktivity. Vrácené metriky jsou ve formátu níže json.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Chcete-li například získat počet řádků zapsaných do jímky s názvem "sink1" `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`v aktivitě s názvem "dataflowActivity", použijte .

Chcete-li získat počet řádků číst ze zdroje s názvem 'source1', který byl použit v tomto jímce, použijte `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`.

> [!NOTE]
> Pokud jímky má napsané nula řádků, nezobrazí se v metriky. Existenci lze ověřit `contains` pomocí funkce. Například `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` zkontroluje, zda byly zapsány všechny řádky do jímky1.

## <a name="next-steps"></a>Další kroky

Viz aktivity toku řízení podporované společností Data Factory: 

- [Aktivita podmínky If](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita For Each](control-flow-for-each-activity.md)
- [Získat aktivitu metadat](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
