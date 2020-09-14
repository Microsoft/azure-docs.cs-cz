---
title: List tahák – Azure synapse Analytics (Preview z pracovních prostorů)
description: Referenční příručka pro uživatele prostřednictvím Azure synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 0e4b96092107e3411f33411f1044fd7cc839f132
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90051984"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Tabulka tahák analýzy Azure synapse

[!INCLUDE [preview](includes/note-preview.md)]

List tahák Analytics pro Azure synapse vás provede základními koncepty služby a důležitých příkazů. Tento článek je užitečný pro nové a ty, kteří chtějí zvýrazňovat základní témata týkající se synapse Azure.

## <a name="basics"></a>Základy

**Pracovní prostor synapse** je zabezpečená hranice spolupráce pro cloudové podnikové analýzy v Azure. Pracovní prostor je nasazený v konkrétní oblasti a má přidružený ADLS Gen2 účet a systém souborů (pro ukládání dočasných dat). Pracovní prostor je pod skupinou prostředků.

Pracovní prostor umožňuje provádět analýzy pomocí SQL a Apache Sparku. Prostředky dostupné pro SQL a Spark Analytics jsou uspořádané do **fondů**. Existují tři druhy fondů.
* **Fondy SQL**. Poskytují **vyhrazenou** kapacitu analýzy SQL.
* **Fond na vyžádání SQL**. Každý pracovní prostor má právě jeden fond SQL na vyžádání. To zajišťuje kapacitu SQL Analytics bez **serveru** na základě dotazu.
* **Fondy Spark**. Poskytují kapacitu Spark Analytics.
## <a name="terminology"></a>Terminologie
| Označení                         | Definice      |
|:---                                 |:---                 |
| **Pracovní prostor synapse** | Zabezpečená hranice spolupráce pro cloudové podnikové analýzy v Azure. Pracovní prostor je nasazený v konkrétní oblasti a má přidružený ADLS Gen2 účet a systém souborů (pro ukládání dočasných dat). Pracovní prostor je pod skupinou prostředků. |
| **Synapse SQL**   | Spusťte analytiky s využitím fondů nebo možností na vyžádání.  |
| **Fond SQL**   | od 0 do N prostředků SQL zřízené s odpovídajícími databázemi se dají nasadit v pracovním prostoru. Každý fond SQL má přidruženou databázi. Fond SQL se dá škálovat, pozastavit a obnovit ručně nebo automaticky. Fond SQL se může škálovat z 100 DWU až 30 000 DWU.       |
| **SQL na vyžádání**   | Distribuovaný systém zpracování dat sestavený pro rozsáhlá data, který umožňuje spouštění dotazů T-SQL nad daty v Data Lake. Nejedná se o server, takže nemusíte spravovat infrastrukturu.       |
|**Apache Spark pro synapse** | Čas spuštění Sparku používaný ve fondu Spark. Aktuální podporovaná verze je Spark 2,4 s Pythonem 3.6.1, Scala 2.11.12, podpora rozhraní .NET pro Apache Spark 0,5 a rozdílový Lake 0,3.  | 
| **Fond Apache Spark**  | do pracovního prostoru můžete nasadit prostředky z 0 do N Spark zřízené pomocí jejich odpovídajících databází. Fond Spark se dá automaticky pozastavit, obnovit a škálovat.  |
| **Aplikace Spark**  |   Skládá se z procesu ovladače a sady procesů prováděcího modulu. Aplikace Spark běží ve fondu Spark.            |
| **Relace Spark**  |   Sjednocený vstupní bod aplikace Spark. Poskytuje způsob, jak pracovat s různými funkcemi Sparku a s menším počtem konstrukcí. Chcete-li spustit Poznámkový blok, je třeba vytvořit relaci. Relaci lze nakonfigurovat tak, aby běžela na určitém počtu prováděcích modulů určité velikosti. Výchozí konfigurací pro relaci poznámkového bloku je spuštění ve dvou prováděcích modulech střední velikosti. |
| **Požadavek SQL**  |   Operace, jako je dotaz spouštěný prostřednictvím fondu SQL nebo SQL na vyžádání. |
|**Integrace dat**| Poskytuje schopnost ingestovat data mezi různými zdroji a orchestrovat aktivity běžící v rámci pracovního prostoru nebo mimo pracovní prostor.| 
|**Artifacts**| Pojem, který zapouzdřuje všechny objekty, které jsou nezbytné pro uživatele ke správě zdrojů dat, vývoji, Orchestrace a vizualizaci.|
|**Poznámkový blok**| Interaktivní a znovu aktivní datové vědy a inženýrské rozhraní, které podporuje Scala, PySpark, C# a SparkSQL. |
|**Definice úlohy Spark**|Rozhraní k odeslání úlohy Sparku pomocí sestavení jar obsahujícího kód a jeho závislosti.|
|**Data Flow**|  Poskytuje plně vizuální prostředí bez nutnosti kódování, které by vyžadovalo transformaci velkých objemů dat. Veškerá optimalizace a provádění jsou zpracovávána způsobem bez serveru. |
|**Skript SQL**| Sada příkazů SQL uložených v souboru. Skript SQL může obsahovat jeden nebo více příkazů SQL. Dá se použít ke spouštění požadavků SQL prostřednictvím fondu SQL nebo SQL na vyžádání.|
|**Kanál**| Logické seskupení aktivit, které provádí úlohu společně.|
|**Aktivita**| Definuje akce, které se mají provádět na datech, jako je kopírování dat, spuštění poznámkového bloku nebo skriptu SQL.|
|**Trigger**| Spustí kanál. Dá se spustit ručně nebo automaticky (naplánování, zabubné okno nebo události).|
|**Propojená služba**| Připojovací řetězce, které definují informace o připojení potřebné pro připojení k externím prostředkům v pracovním prostoru.|
|**Datová sada**|  Pojmenované zobrazení dat, které jednoduše odkazuje na data, která se mají použít v aktivitě jako vstup a výstup. Patří do propojené služby.|

## <a name="next-steps"></a>Další kroky

- [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
- [Použití sady Synapse Studio](quickstart-synapse-studio.md)
- [Vytvoření fondu SQL](quickstart-create-sql-pool-portal.md)
- [Vytvoření fondu Apache Spark](quickstart-create-apache-spark-pool-portal.md)
- [Používání SQL na vyžádání](quickstart-sql-on-demand.md)

