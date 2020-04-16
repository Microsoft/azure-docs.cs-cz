---
title: Monitorování aktivity kopírování
description: Přečtěte si, jak sledovat provádění aktivit kopírování v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 47824095e892ca3c919d2d871feb612758ab2308
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417840"
---
# <a name="monitor-copy-activity"></a>Monitorování aktivity kopírování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak sledovat provádění aktivity kopírování v Azure Data Factory. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="monitor-visually"></a>Vizuální monitorování

Po vytvoření a publikování kanálu v Azure Data Factory ho můžete přidružit k aktivační události nebo ručně zahájit ad hoc spuštění. Všechny vaše kanály běží nativně v uživatelském prostředí Azure Data Factory. Další informace o monitorování Azure Data Factory obecně z [Vizuálně monitorovat Azure Data Factory](monitor-visually.md).

Chcete-li sledovat spuštění aktivity kopírování, přejděte na ui **&.** Na kartě **Monitor** se zobrazí seznam spuštění kanálu, kliknutím na odkaz **název kanálu** pro přístup k seznamu aktivit běží v kanálu spustit.

![Sledovat spuštění aktivity kopírování](./media/copy-activity-overview/monitor-pipeline-run.png)

Na této úrovni můžete zobrazit odkazy na kopírování vstupu aktivity, výstupu a chyb (pokud se nezdaří spuštění aktivity kopírování), stejně jako statistiky, jako je doba trvání/stav. Kliknutím na tlačítko **Podrobnosti** (brýle) vedle názvu aktivity kopírování získáte podrobné informace o provádění aktivity kopírování. 

![Sledovat spuštění aktivity kopírování](./media/copy-activity-overview/monitor-copy-activity-run.png)

V tomto grafickém zobrazení monitorování Azure Data Factory vám představuje informace o spuštění aktivity kopírování, včetně dat číst/zapsat svazek, počet souborů nebo řádků dat zkopírovaných ze zdroje do jímky, propustnost, konfigurace použité pro scénář kopírování, kroky, které aktivita kopírování prochází s odpovídající doby trvání a podrobnosti a další. Viz [tato tabulka](#monitor-programmatically) o každé možné metriky a její podrobný popis. 

V některých případech při spuštění aktivity kopírování v datové továrně se zobrazí **"Tipy pro optimalizaci výkonu"** v horní části zobrazení sledování aktivity kopírování, jak je znázorněno v příkladu. Tipy vám řeknou kritické místo identifikované adf pro konkrétní spuštění kopie, spolu s návrhem na to, co změnit pro zvýšení propustnost kopírování. Přečtěte si další informace o [tipech](copy-activity-performance-troubleshooting.md#performance-tuning-tips)pro automatické ladění výkonu .

Podrobnosti **o dolním spuštění a doby trvání** popisují klíčové kroky, kterými prochází vaše aktivita kopírování, což je užitečné zejména pro řešení potíží s výkonem kopírování. Kritickým bodem spuštění kopírování je ten s nejdelší dobou trvání. Informace o tom, co jednotlivé fáze představují, naleznete v části Poradce při potížích s [výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md) a podrobnými pokyny pro řešení potíží.

**Příklad: Kopírování z Amazonu S3 do Azure Data Lake Storage Gen2**

![Sledování podrobností spuštění aktivity kopírování](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Programové monitorování

Podrobnosti spuštění aktivity kopírování a charakteristiky výkonu jsou také vráceny v > **části** Výstup **spuštění aktivity kopírování,** která se používá k vykreslení zobrazení monitorování ui. Následuje úplný seznam vlastností, které mohou být vráceny. Zobrazí se pouze vlastnosti, které jsou použitelné pro váš scénář kopírování. Informace o tom, jak sledovat spuštění aktivity programově obecně, najdete v [tématu Programmatically sledování azure factory dat](monitor-programmatically.md).

| Název vlastnosti  | Popis | Jednotka ve výstupu |
|:--- |:--- |:--- |
| dataČíst | Skutečné množství dat čtených ze zdroje. | Hodnota Int64 v bajtech |
| dataWritten | Skutečné připojení dat zapsaných nebo potvrzených do jímky. Velikost se může `dataRead` lišit od velikosti, protože se týká, jak každé úložiště dat ukládá data. | Hodnota Int64 v bajtech |
| souboryČíst | Počet souborů přečtených ze zdroje založeného na souboru. | Hodnota Int64 (bez jednotky) |
| filesWritten | Počet souborů zapsaných nebo potvrzených do jímky založené na souboru. | Hodnota Int64 (bez jednotky) |
| sourcePeakConnections | Maximální počet souběžných připojení vytvořených do úložiště zdrojových dat během spuštění aktivity kopírování. | Hodnota Int64 (bez jednotky) |
| sinkPeakConnections | Maximální počet souběžných připojení vytvořených k úložišti dat jímky během spuštění aktivity kopírování. | Hodnota Int64 (bez jednotky) |
| řádkyČíst | Počet řádků přečtených ze zdroje (neplatí pro binární kopii). | Hodnota Int64 (bez jednotky) |
| rowsCopied | Počet řádků zkopírovaných do jímky (neplatí pro binární kopii). | Hodnota Int64 (bez jednotky) |
| rowsSkipped | Počet nekompatibilních řádků, které byly přeskočeny. Můžete povolit nekompatibilní řádky, které `enableSkipIncompatibleRow` mají být přeskočeny nastavením true. | Hodnota Int64 (bez jednotky) |
| copyDuration | Doba trvání kopírování spustit. | Hodnota Int32 v sekundách |
| throughput | Rychlost přenosu dat. | Číslo s plovoucí desetinnou tázkou v kb/s |
| sourcePeakConnections | Maximální počet souběžných připojení vytvořených do úložiště zdrojových dat během spuštění aktivity kopírování. | Hodnota Int32 (bez jednotky) |
| sinkPeakConnections| Maximální počet souběžných připojení vytvořených k úložišti dat jímky během spuštění aktivity kopírování.| Hodnota Int32 (bez jednotky) |
| sqlDwPolyBase | Určuje, zda se polybase používá při kopírování dat do datového skladu SQL. | Logická hodnota |
| redshiftUnload | Určuje, zda se při kopírování dat z Redshiftu používá unload. | Logická hodnota |
| hdfsDistcp | Určuje, zda se při kopírování dat z hdfs používá DistCp. | Logická hodnota |
| effectiveIntegrationRuntime | Integrační runtime (IR) nebo runtime používané k `<IR name> (<region if it's Azure IR>)`napájení aktivity spustit ve formátu . | Text (řetězec) |
| usedDataIntegrationUnits | Efektivní jednotky integrace dat během kopírování. | Hodnota Int32 |
| usedParallelCopies | Efektivní parallelCopies během kopírování. | Hodnota Int32 |
| redirectRowPath | Cesta k protokolu přeskočených nekompatibilních řádků v úložišti objektů blob, které nakonfigurujete ve vlastnosti. `redirectIncompatibleRowSettings` Viz [Odolnost proti chybám](copy-activity-overview.md#fault-tolerance). | Text (řetězec) |
| executionDetails | Další podrobnosti o fázích, kterými prochází aktivita kopírování, a o odpovídajících krocích, dobu trvání, konfiguracích a tak dále. Nedoporučujeme analyzovat tuto část, protože se může změnit. Chcete-li lépe pochopit, jak vám pomůže pochopit a řešit problémy s výkonem kopírování, podívejte se na [monitor vizuálně](#monitor-visually) části. | Pole |
| perfDoporučení | Zkopírujte tipy pro ladění výkonu. Podrobnosti najdete v [tématu Tipy pro ladění výkonu.](copy-activity-performance-troubleshooting.md#performance-tuning-tips) | Pole |

**Příklad:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Další kroky
Podívejte se na další články aktivity kopírování:

\-[Kopírovat přehled aktivit](copy-activity-overview.md)

\- [Výkon aktivity kopírování](copy-activity-performance.md)