---
title: Tahák - Azure Synapse Analytics
description: Referenční příručka procházet uživatelem přes Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: aa93a816fc11158d928978bdec2dbf42119fa149
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424654"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Azure Synapse Analytics tahák

[!INCLUDE [preview](includes/note-preview.md)]

Seznam cheatůRŮ Azure Synapse Analytics vás provede základními koncepty služby a důležitými příkazy. Tento článek je užitečný jak pro nové studenty, tak pro ty, kteří chtějí nejdůležitější témata Azure Synapse.

## <a name="architecture"></a>Architektura

> [!div class="mx-imgBorder"]
>![Synapse architektura](media/overview-cheat-sheet/azure-synapse-architecture-cheat-sheet.png)

## <a name="concepts"></a>Koncepty
| Ta slovesa a slovesa                         | Co dělá       |
|:---                                 |:---                 |
| **Synapse Workspace (náhled)** | Hranice seřizovatelné spolupráce pro provádění cloudové podnikové analýzy v Azure. Pracovní prostor je nasazen v určité oblasti a má přidružený účet ADLSg2 a systém souborů (pro ukládání dočasných dat). Pracovní prostor je pod skupinou prostředků. |
| **SQL Analytics**   | Spouštět analýzy s fondy nebo s funkcemi na vyžádání.  |
| **Fond SQL**   | 0-to-N SQL zřízených prostředků s jejich odpovídající databáze lze nasadit v pracovním prostoru. Každý fond SQL má přidruženou databázi. Fond SQL lze škálovat, postavovat a obnovit ručně nebo automaticky. Fond SQL lze škálovat od 100 DWU až 30 000 DWU.       |
| **SQL na vyžádání (náhled)**   | Distribuovaný systém zpracování dat vytvořený pro rozsáhlá data, která umožňují spouštět dotazy T-SQL přes data v datovém jezeře. Je bez serveru, takže nemusíte spravovat infrastrukturu.       |
|**Apache Spark** | Zapalte run-time používaný ve fondu Spark. Aktuální podporovaná verze je Spark 2.4 s Pythonem 3.6.1, Scala 2.11.12, podpora .NET pro Apache Spark 0.5 a Delta Lake 0.3.  | 
| **Fond Apache Spark (náhled)**  | 0-n Spark zřízených prostředků s jejich odpovídající databáze lze nasadit v pracovním prostoru. Fond Spark může být automaticky pozastaven, obnoven a škálován.  |
| **Aplikace Spark**  |   Skládá se z procesu ovladače a sady procesů vykonavatele. Aplikace Spark běží ve fondu Spark.            |
| **Zapalovací relace**  |   Sjednocený vstupní bod aplikace jiskry. Poskytuje způsob, jak komunikovat s různými funkcemi Spark a s menším počtem konstrukcí. Chcete-li spustit poznámkový blok, je třeba vytvořit relaci. Relaci lze nakonfigurovat tak, aby byla spuštěna na určitém počtu vykonavatelů určité velikosti. Výchozí konfigurace relace poznámkového bloku je spuštění na 2 středně velké vykonavatelé. |
| **Požadavek SQL**  |   Operace, jako je například dotaz spustit prostřednictvím fondu SQL nebo SQL na vyžádání. |
|**Integrace dat**| Umožňuje ingestovat data mezi různými zdroji a organizovat aktivity spuštěné v pracovním prostoru nebo mimo pracovní prostor.| 
|**Artefakty**| Koncept, který zapouzdřuje všechny objekty nezbytné pro uživatele ke správě zdrojů dat, vývoj, orchestraci a vizualizaci.|
|**Poznámkový blok**| Interaktivní a reaktivní datové vědy a inženýrství rozhraní podporující Scala, PySpark, C # a SparkSQL. |
|**Definice úlohy Spark**|Rozhraní pro odeslání úlohy Spark pomocí nádoby sestavení obsahující kód a jeho závislosti.|
|**Data Flow**|  Poskytuje plně vizuální prostředí bez nutnosti kódování k provedení transformace velkých objemů dat. Všechny optimalizace a provádění jsou zpracovány bez serveru způsobem. |
|**Skript SQL**| Sada příkazů SQL uložených v souboru. Skript SQL může obsahovat jeden nebo více příkazů SQL. Lze jej použít ke spuštění požadavků SQL prostřednictvím fondu SQL nebo SQL na vyžádání.|
|**Kanál**| Logické seskupení aktivit, které provádějí úkol společně.|
|**Činnosti**| Definuje akce, které mají být u dat, jako je kopírování dat, spuštění poznámkového bloku nebo skriptu SQL.|
|**Trigger**| Provede kanál. Lze jej spustit ručně nebo automaticky (plán, omílání okna nebo události založené na událostech).|
|**Propojená služba**| Připojovací řetězce, které definují informace o připojení potřebné pro pracovní prostor pro připojení k externím prostředkům.|
|**Dataset**|  Pojmenované zobrazení dat, které jednoduše odkazuje nebo odkazuje na data, která mají být použita v aktivitě jako vstup a výstup. Patří propojené službě.|

## <a name="next-steps"></a>Další kroky

- [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
- [Použití Synapse Studio](quickstart-synapse-studio.md)
- [Vytvoření fondu SQL](quickstart-create-sql-pool.md)
- [Použití sql na vyžádání](quickstart-sql-on-demand.md)
- [Vytvoření fondu Apache Spark](quickstart-create-apache-spark-pool.md)

