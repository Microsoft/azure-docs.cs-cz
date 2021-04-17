---
title: Aktivita toku dat
description: Jak spouštět toky dat z kanálu služby Data Factory.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.author: makromer
ms.date: 04/16/2021
ms.openlocfilehash: da8d193d140d96d9742666429ebc85672c71ad4e
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567260"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Aktivita toku dat v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivitu toku dat můžete použít k transformaci a přesunutí dat prostřednictvím mapování toků dat. Pokud s toky dat začínáte, přečtěte si téma [mapování toku dat – přehled](concepts-data-flow-overview.md)

## <a name="syntax"></a>Syntax

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
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
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

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | --------
toku dat | Odkaz na prováděný tok dat | DataFlowReference | Yes
integrationRuntime | Výpočetní prostředí, na kterém se tok dat spouští. Pokud není zadaný, použije se automatické řešení Azure Integration runtime. | IntegrationRuntimeReference | No
Compute. coreCount | Počet jader používaných v clusteru Spark. Dá se zadat jenom v případě, že se používá prostředí Azure Integration runtime pro automatické rozpoznávání. | 8, 16, 32, 48, 80, 144, 272 | No
Compute. computeType | Typ výpočetní služby použitý v clusteru Spark. Dá se zadat jenom v případě, že se používá prostředí Azure Integration runtime pro automatické rozpoznávání. | "Obecné", "ComputeOptimized", "MemoryOptimized" | No
Příprava. linkedService | Pokud používáte zdroj nebo jímku Azure synapse Analytics, zadejte účet úložiště, který se používá pro základní fázování.<br/><br/>Pokud je váš Azure Storage nakonfigurovaný s koncovým bodem služby virtuální sítě, musíte použít spravované ověřování identity s povolenou možnost Povolit důvěryhodnou službu Microsoftu v účtu úložiště. Přečtěte si [dopad použití koncových bodů služby virtuální sítě se službou Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). Seznamte se také s potřebnými konfiguracemi pro [Azure Blob](connector-azure-blob-storage.md#managed-identity) a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) v uvedeném pořadí.<br/> | LinkedServiceReference | Jenom v případě, že tok dat čte nebo zapisuje do Azure synapse Analytics
Příprava. folderPath | Pokud používáte zdroj nebo jímku Azure synapse Analytics, cesta ke složce v účtu BLOB Storage použitá pro základní fázování | Řetězec | Jenom v případě, že tok dat čte nebo zapisuje do služby Azure synapse Analytics
traceLevel | Nastavení úrovně protokolování pro spuštění aktivity toku dat | Pokuta, hrubá, žádná | No

![Spustit tok dat](media/data-flow/activity-data-flow.png "Spustit tok dat")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Dynamická velikost výpočetního toku dat za běhu

Vlastnosti základního počtu a výpočetního typu se dají nastavit dynamicky tak, aby se při spuštění upravily na velikost příchozích zdrojových dat. Použijte aktivity kanálu, jako je Lookup nebo získat metadata, abyste našli velikost dat zdrojové datové sady. Pak použijte možnost Přidat dynamický obsah do vlastností aktivity toku dat.

![Tok dynamického toku dat](media/data-flow/dyna1.png "Tok dynamického toku dat")

[Toto je stručný kurz pro video, který vysvětluje tuto techniku.](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Prostředí Integration runtime toku dat

Vyberte, který Integration Runtime se má použít pro spuštění aktivity toku dat. Ve výchozím nastavení Data Factory použije automatické řešení Azure Integration runtime se čtyřmi jádry pracovních procesů. Tento IR má pro výpočetní typ pro obecné účely a běží ve stejné oblasti jako vaše továrna. U provozních kanálů důrazně doporučujeme vytvořit vlastní prostředí Azure Integration runtime, která definují konkrétní oblasti, výpočetní typ, počty jader a hodnotu TTL pro spuštění aktivity toku dat.

Minimální výpočetní typ Pro obecné účely (optimalizované pro výpočty se nedoporučuje u velkých úloh) s konfigurací 8 + 8 (16 celkových jader v jádrech) a 10 minutami je minimální doporučení pro většinu produkčních úloh. Nastavením malé hodnoty TTL může Azure IR udržovat teplé clustery, u kterých se nebudete zabývat několika minutami počátečního času pro studený cluster. Spouštění toků dat můžete zrychlit ještě více výběrem možnosti rychlé opakované použití při Azure IR konfiguracích toku dat. Další informace najdete v tématu [prostředí Azure Integration runtime](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> Výběr Integration Runtime v aktivitě toku dat se vztahuje pouze na *aktivované spouštění* vašeho kanálu. Ladění kanálu pomocí datových toků běží v clusteru zadaném v relaci ladění.

### <a name="polybase"></a>PolyBase

Pokud jako jímku nebo zdroj používáte Azure synapse Analytics, musíte zvolit pracovní umístění pro zatížení základní dávky. Základem je hromadné načtení dávkového načítání namísto načítání datových řádků po řádku. Základce významně zkracuje dobu načítání do Azure synapse Analytics.

## <a name="logging-level"></a>Úroveň protokolování

Pokud nepotřebujete, aby každé spuštění kanálu pro aktivity toku dat plně protokoloval všechny protokoly telemetrie, můžete nastavit úroveň protokolování na "Basic" nebo "none". Při provádění datových toků v režimu "Verbose" (výchozí) požadujete při transformaci dat na úrovni služby ADF úplný protokol aktivity na jednotlivých úrovních jednotlivých oddílů. To může být náročná operace, takže jenom při řešení potíží můžete zlepšit celkový tok dat a výkon kanálu. Režim "základní" bude pouze dobu trvání transformace protokolu, zatímco možnost None bude obsahovat pouze souhrn dob trvání.

![Úroveň protokolování](media/data-flow/logging.png "Nastavit úroveň protokolování")

## <a name="sink-properties"></a>Vlastnosti jímky

Funkce seskupení v datových proudech umožňuje nastavovat pořadí spouštění vašich umyvadel i seskupovat jímky pomocí stejného čísla skupiny. Aby bylo možné spravovat skupiny, můžete požádat o automatický pokus o spuštění jímky ve stejné skupině paralelně. Můžete také nastavit, aby skupina jímky pokračovala i po jedné z umyvadel dojde k chybě.

Výchozím chováním jímky toku dat je spuštění každé jímky postupně, sériového způsobu a selhání toku dat, když dojde k chybě v jímky. Kromě toho jsou všechny jímky nastavené na stejnou skupinu, pokud nepřejdete do vlastností toku dat a nakonfigurujete jiné priority pro jímky.

![Vlastnosti jímky](media/data-flow/sink-properties.png "Nastavení vlastností jímky")

## <a name="parameterizing-data-flows"></a>Toky dat Parametrizace

### <a name="parameterized-datasets"></a>Parametrizované datové sady

Pokud datový tok používá parametrizované datové sady, nastavte hodnoty parametrů na kartě **Nastavení** .

![Spustit parametry toku dat](media/data-flow/params.png "Parametry")

### <a name="parameterized-data-flows"></a>Parametrizované toky dat

Pokud je tok dat parametrizovaný, nastavte dynamické hodnoty parametrů toku dat na kartě **parametry** . K přiřazení hodnot parametrů Dynamic nebo Literal můžete použít jazyk výrazu kanálu ADF nebo jazyk výrazu toku dat. Další informace najdete v tématu [parametry toku dat](parameters-data-flow.md).

### <a name="parameterized-compute-properties"></a>Parametrizované výpočetní vlastnosti.

Pokud použijete automatické řešení Azure Integration runtime a zadáte hodnoty Compute. coreCount a COMPUTE. computeType, můžete parametrizovat typ jádra nebo Compute.

![Příklad spuštění parametru toku dat](media/data-flow/parameterize-compute.png "Příklad parametru")

## <a name="pipeline-debug-of-data-flow-activity"></a>Ladění kanálu aktivity toku dat

Chcete-li spustit ladicí kanál spuštěný s aktivitou toku dat, je nutné přepnout na režim ladění toku dat prostřednictvím posuvníku **ladění toku dat** na horním panelu. Režim ladění umožňuje spustit tok dat proti aktivnímu clusteru Spark. Další informace naleznete v tématu [režim ladění](concepts-data-flow-debug-mode.md).

![Snímek obrazovky, který ukazuje, kde je tlačítko ladit](media/data-flow/debug-button-3.png)

Ladicí kanál běží na aktivním ladicím clusteru, nikoli v prostředí Integration runtime zadaném v nastavení aktivity toku dat. Můžete zvolit prostředí ladění COMPUTE při spuštění režimu ladění.

## <a name="monitoring-the-data-flow-activity"></a>Monitorování aktivity toku dat

Aktivita toku dat má speciální prostředí pro monitorování, ve kterém můžete zobrazit oddíly, čas fáze a informace o datových řádcích. Otevřete podokno monitorování pomocí ikony brýlí v části **Akce**. Další informace najdete v tématu [monitorování toků dat](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Použití aktivity toku dat v důsledku následné aktivity

Aktivita toku dat výstupuje metriky týkající se počtu řádků zapsaných do každé jímky a řádků načtených z každého zdroje. Tyto výsledky se vrátí v `output` části výsledku spuštění aktivity. Vrácené metriky jsou ve formátu níže uvedeného formátu JSON.

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

Pokud například chcete získat počet řádků zapsaných do jímky s názvem ' sink1 ' v aktivitě s názvem ' dataflowActivity ', použijte `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` .

Chcete-li získat počet řádků načtených ze zdroje s názvem ' source1 ', který byl použit v této jímky, použijte `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` .

> [!NOTE]
> Pokud má jímka zapsána nula řádků, nebude zobrazena v metrikách. Existenci lze ověřit pomocí `contains` funkce. Například zkontroluje, `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` zda byly do sink1 zapsány nějaké řádky.

## <a name="next-steps"></a>Další kroky

Viz aktivity toku řízení podporované Data Factory: 

- [Aktivita podmínky Když](control-flow-if-condition-activity.md)
- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita For Each](control-flow-for-each-activity.md)
- [Aktivita získání metadat](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
- [Aktivita Until](control-flow-until-activity.md)
