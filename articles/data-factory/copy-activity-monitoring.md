---
title: Monitorování aktivity kopírování
description: Přečtěte si, jak monitorovat provádění aktivit kopírování v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: jingwang
ms.openlocfilehash: 92119709aa260f3180c503a77064f6e80dece6e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440605"
---
# <a name="monitor-copy-activity"></a>Monitorování aktivity kopírování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak monitorovat provádění aktivit kopírování v Azure Data Factory. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="monitor-visually"></a>Vizuální monitorování

Po vytvoření a publikování kanálu v Azure Data Factory ho můžete přidružit k triggeru nebo ručně spustit ad hoc spuštění. Můžete monitorovat všechny spuštěné kanály nativně v prostředí Azure Data Factory koncového uživatele. Další informace o Azure Data Factory monitorování obecně od [vizuálně monitorovaného Azure Data Factory](monitor-visually.md).

Pokud chcete monitorovat běh aktivity kopírování, v uživatelském rozhraní služby Data Factory **Author & monitorujte** . Na kartě **monitorování** se zobrazí seznam spuštění kanálu, kliknutím na odkaz **název kanálu** získáte přístup k seznamu spuštění aktivit v běhu kanálu.

![Monitorování spuštění kanálu](./media/copy-activity-overview/monitor-pipeline-run.png)

Na této úrovni můžete zobrazit odkazy na vstup aktivity kopírování, výstup a chyby (Pokud se aktivita kopírování nezdařila) a také statistiku, jako je doba trvání/stav. Kliknutím na tlačítko **Podrobnosti** (brýlí) vedle názvu aktivity kopírování získáte podrobné informace o spuštění aktivity kopírování. 

![Monitorování spuštění aktivity kopírování](./media/copy-activity-overview/monitor-copy-activity-run.png)

V tomto zobrazení grafického monitorování vám Azure Data Factory ukáže informace o spuštění aktivity kopírování, včetně objemu načtených/zapsaných dat, počtu souborů/řádků dat zkopírovaných ze zdroje do jímky, propustnosti, konfigurací použitých pro váš scénář kopírování, kroků aktivity kopírování, včetně odpovídajících dob trvání a podrobností a dalších. V [této tabulce](#monitor-programmatically) najdete všechny možné metriky a její podrobný popis. 

V některých scénářích se při spuštění aktivity kopírování v Data Factory v horní části zobrazení monitorování aktivit kopírování zobrazí informace o **Vyladění výkonu**  , jak je znázorněno v příkladu. Tipy označují kritické body identifikované pomocí ADF pro konkrétní kopírování a návrhy na to, co se má změnit, aby se zvýšila propustnost kopírování. Přečtěte si další informace o [tipůch automatického ladění výkonu](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

Dolní **Podrobnosti a trvání spuštění** popisují klíčové kroky, kterými aktivita kopírování prochází, což je zvláště užitečné při řešení potíží s výkonem kopírování. Kritické místo pro váš běh kopírování je ten, který má nejdelší dobu trvání. Přečtěte si téma [řešení potíží s výkonem aktivity kopírování](copy-activity-performance-troubleshooting.md) v pro jednotlivé fáze představují a podrobné pokyny k odstraňování potíží.

**Příklad: zkopírování z Amazon S3 do Azure Data Lake Storage Gen2**

![Sledovat podrobnosti o spuštění aktivity kopírování](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Monitorování prostřednictvím kódu programu

V části výstup **výsledků spuštění aktivity kopírování**  >  **Output** , která se používá k vykreslování zobrazení monitorování uživatelského rozhraní, se také vrátí podrobnosti o spuštění aktivity kopírování a výkonnostní charakteristiky. Následuje úplný seznam vlastností, které mohou být vráceny. Uvidíte jenom vlastnosti, které se vztahují k vašemu scénáři kopírování. Informace o tom, jak programově monitorovat spuštění aktivit prostřednictvím kódu programu, najdete v tématu [programové sledování služby Azure Data Factory](monitor-programmatically.md).

| Název vlastnosti  | Description | Jednotka ve výstupu |
|:--- |:--- |:--- |
| Čtení z | Skutečné množství dat načtených ze zdroje. | Hodnota Int64, v bajtech |
| Napsáno | Skutečná připojená Data zapsaná nebo potvrzená do jímky. Velikost může být jiná než `dataRead` velikost, protože souvisí s tím, jak jednotlivé úložiště dat data ukládají. | Hodnota Int64, v bajtech |
| filesRead | Počet souborů načtených ze zdroje založeného na souboru. | Hodnota Int64 (žádná jednotka) |
| filesWritten | Počet souborů zapsaných nebo potvrzených do jímky založené na souborech. | Hodnota Int64 (žádná jednotka) |
| filesSkipped | Počet souborů, které byly vynechány ze zdroje založeného na souboru. | Hodnota Int64 (žádná jednotka) |
| dataConsistencyVerification | Podrobnosti ověření konzistence dat, kde můžete zjistit, zda byla zkopírovaná data ověřena pro zajištění konzistence mezi zdrojovým a cílovým úložištěm. Další informace najdete v [tomto článku](copy-activity-data-consistency.md#monitoring). | Pole |
| sourcePeakConnections | Nejvyšší počet souběžných připojení navázaných ke zdrojovému úložišti dat během spuštění aktivity kopírování. | Hodnota Int64 (žádná jednotka) |
| sinkPeakConnections | Nejvyšší počet souběžných připojení navázaných na úložiště dat jímky během spuštění aktivity kopírování. | Hodnota Int64 (žádná jednotka) |
| rowsRead | Počet načtených řádků ze zdroje Tato metrika se nevztahuje na kopírování souborů tak, jak jsou, bez jejich analýzy, například když jsou datové sady typu binární a jímka v binárním formátu nebo jiný typ formátu se shodným nastavením. | Hodnota Int64 (žádná jednotka) |
| rowsCopied | Počet řádků zkopírovaných do jímky. Tato metrika se nevztahuje na kopírování souborů tak, jak jsou, bez jejich analýzy, například když jsou datové sady typu binární a jímka v binárním formátu nebo jiný typ formátu se shodným nastavením.  | Hodnota Int64 (žádná jednotka) |
| rowsSkipped | Počet vynechaných nekompatibilních řádků. Můžete povolit přeskočení nekompatibilních řádků nastavením `enableSkipIncompatibleRow` na hodnotu true. | Hodnota Int64 (žádná jednotka) |
| copyDuration | Doba trvání spuštění kopírování | Hodnota Int32 v sekundách |
| throughput | Rychlost přenosu dat | Číslo s plovoucí desetinnou čárkou, v KB/s |
| sourcePeakConnections | Nejvyšší počet souběžných připojení navázaných ke zdrojovému úložišti dat během spuštění aktivity kopírování. | Hodnota Int32 (žádná jednotka) |
| sinkPeakConnections| Nejvyšší počet souběžných připojení navázaných na úložiště dat jímky během spuštění aktivity kopírování.| Hodnota Int32 (žádná jednotka) |
| sqlDwPolyBase | Určuje, jestli se používá základ, když se data zkopírují do služby Azure synapse Analytics (dřív SQL Data Warehouse). | Logická hodnota |
| redshiftUnload | Určuje, zda je při kopírování dat z RedShift použito uvolnění. | Logická hodnota |
| hdfsDistcp | Určuje, zda se při kopírování dat ze HDFS používá DistCp. | Logická hodnota |
| effectiveIntegrationRuntime | Prostředí Integration runtime (IR) nebo modulu runtime, které slouží k napájení spuštění aktivity, ve formátu `<IR name> (<region if it's Azure IR>)` . | Text (String) |
| usedDataIntegrationUnits | Efektivní jednotky integrace dat během kopírování. | Hodnota Int32 |
| usedParallelCopies | Efektivní parallelCopies během kopírování. | Hodnota Int32 |
| logPath | Cesta k protokolu relace vynechaných dat v úložišti objektů BLOB Podívejte se na odolnost [proti chybám](copy-activity-overview.md#fault-tolerance). | Text (String) |
| executionDetails | Další podrobnosti o fázích, které aktivita kopírování prochází, a o příslušných krocích, trváních, konfiguracích a tak dále. Nedoporučujeme tuto část analyzovat, protože by se mohla změnit. Abyste lépe pochopili, jak vám pomůže pochopit a řešit potíže s kopírováním, přečtěte si téma [monitorování vizuálně](#monitor-visually) . | Pole |
| perfRecommendation | Kopírování tipů pro ladění výkonu. Podrobnosti najdete v tématu [tipy pro ladění výkonu](copy-activity-performance-troubleshooting.md#performance-tuning-tips) . | Pole |
| billingReference | Spotřeba fakturace pro daný běh. Další informace o [monitorování spotřeby na úrovni spuštění aktivit](plan-manage-costs.md#monitor-consumption-at-activity-run-level). | Objekt |
| durationInQueue | Doba zařazení do fronty v sekundách, než se začne spouštět aktivita kopírování. | Objekt |

**Příklad:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "filesSkipped": 0,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "dataConsistencyVerification": 
    { 
        "VerificationResult": "Verified", 
        "InconsistentData": "None" 
    },
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
Další články o aktivitě kopírování najdete v článcích:

\- [Přehled aktivit kopírování](copy-activity-overview.md)

\- [Výkon aktivity kopírování](copy-activity-performance.md)