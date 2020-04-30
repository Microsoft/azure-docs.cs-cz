---
title: List tahák – Azure synapse Analytics
description: Referenční příručka pro uživatele prostřednictvím Azure synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: aa93a816fc11158d928978bdec2dbf42119fa149
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424654"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Tabulka tahák analýzy Azure synapse

[!INCLUDE [preview](includes/note-preview.md)]

List tahák Analytics pro Azure synapse vás provede základními koncepty služby a důležitých příkazů. Tento článek je užitečný pro nové a ty, kteří chtějí zvýrazňovat základní témata týkající se synapse Azure.

## <a name="architecture"></a>Architektura

> [!div class="mx-imgBorder"]
>![Architektura synapse](media/overview-cheat-sheet/azure-synapse-architecture-cheat-sheet.png)

## <a name="concepts"></a>Koncepty
| Podstatná jména a operace                         | Co dělá       |
|:---                                 |:---                 |
| **Pracovní prostor synapse (Preview)** | Zabezpečená hranice spolupráce pro cloudové podnikové analýzy v Azure. Pracovní prostor je nasazený v konkrétní oblasti a má přidružený účet ADLSg2 a systém souborů (pro ukládání dočasných dat). Pracovní prostor je pod skupinou prostředků. |
| **SQL Analytics**   | Spusťte analytiky s využitím fondů nebo možností na vyžádání.  |
| **Fond SQL**   | od 0 do N prostředků SQL zřízené s odpovídajícími databázemi se dají nasadit v pracovním prostoru. Každý fond SQL má přidruženou databázi. Fond SQL se dá škálovat, pozastavit a obnovit ručně nebo automaticky. Fond SQL se může škálovat z 100 DWU až 30 000 DWU.       |
| **SQL na vyžádání (Preview)**   | Distribuovaný systém zpracování dat sestavený pro rozsáhlá data, který umožňuje spouštění dotazů T-SQL nad daty v Data Lake. Nejedná se o server, takže nemusíte spravovat infrastrukturu.       |
|**Apache Spark** | Čas spuštění Sparku používaný ve fondu Spark. Aktuální podporovaná verze je Spark 2,4 s Pythonem 3.6.1, Scala 2.11.12, podpora rozhraní .NET pro Apache Spark 0,5 a rozdílový Lake 0,3.  | 
| **Fond Apache Spark (Preview)**  | do pracovního prostoru můžete nasadit prostředky z 0 do N Spark zřízené pomocí jejich odpovídajících databází. Fond Spark se dá automaticky pozastavit, obnovit a škálovat.  |
| **Aplikace Spark**  |   Skládá se z procesu ovladače a sady procesů prováděcího modulu. Aplikace Spark běží ve fondu Spark.            |
| **Relace Spark**  |   Sjednocený vstupní bod aplikace Spark. Poskytuje způsob, jak pracovat s různými funkcemi Sparku a s menším počtem konstrukcí. Chcete-li spustit Poznámkový blok, je třeba vytvořit relaci. Relaci lze nakonfigurovat tak, aby běžela na určitém počtu prováděcích modulů určité velikosti. Výchozí konfigurací pro relaci poznámkového bloku je spuštění ve dvou prováděcích modulech střední velikosti. |
| **Požadavek SQL**  |   Operace, jako je dotaz spouštěný prostřednictvím fondu SQL nebo SQL na vyžádání. |
|**Integrace dat**| Poskytuje schopnost ingestovat data mezi různými zdroji a orchestrovat aktivity běžící v rámci pracovního prostoru nebo mimo pracovní prostor.| 
|**Artefakty**| Pojem, který zapouzdřuje všechny objekty, které jsou nezbytné pro uživatele ke správě zdrojů dat, vývoji, Orchestrace a vizualizaci.|
|**Poznámkový blok**| Interaktivní a znovu aktivní datové vědy a inženýrské rozhraní, které podporuje Scala, PySpark, C# a SparkSQL. |
|**Definice úlohy Spark**|Rozhraní k odeslání úlohy Sparku pomocí sestavení jar obsahujícího kód a jeho závislosti.|
|**Data Flow**|  Poskytuje plně vizuální prostředí bez nutnosti kódování, které by vyžadovalo transformaci velkých objemů dat. Veškerá optimalizace a provádění jsou zpracovávána způsobem bez serveru. |
|**Skript SQL**| Sada příkazů SQL uložených v souboru. Skript SQL může obsahovat jeden nebo více příkazů SQL. Dá se použít ke spouštění požadavků SQL prostřednictvím fondu SQL nebo SQL na vyžádání.|
|**Kanál**| Logické seskupení aktivit, které provádí úlohu společně.|
|**Akce**| Definuje akce, které se mají provádět na datech, jako je kopírování dat, spuštění poznámkového bloku nebo skriptu SQL.|
|**Trigger**| Spustí kanál. Dá se spustit ručně nebo automaticky (naplánování, zabubné okno nebo události).|
|**Propojená služba**| Připojovací řetězce, které definují informace o připojení potřebné pro připojení k externím prostředkům v pracovním prostoru.|
|**Integrován**|  Pojmenované zobrazení dat, které jednoduše odkazuje na data, která se mají použít v aktivitě jako vstup a výstup. Patří do propojené služby.|

## <a name="next-steps"></a>Další kroky

- [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
- [Použití synapse studia](quickstart-synapse-studio.md)
- [Vytvoření fondu SQL](quickstart-create-sql-pool.md)
- [Používání SQL na vyžádání](quickstart-sql-on-demand.md)
- [Vytvoření fondu Apache Spark](quickstart-create-apache-spark-pool.md)

