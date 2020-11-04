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
ms.openlocfilehash: c8ae47f89dcddd2253abd21239eb1a9bffee27c7
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307828"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Tabulka tahák analýzy Azure synapse

[!INCLUDE [preview](includes/note-preview.md)]

List tahák Analytics pro Azure synapse vás provede základními koncepty služby a důležitých příkazů. Tento článek je užitečný pro nové a ty, kteří chtějí zvýrazňovat základní témata týkající se synapse Azure.

## <a name="basics"></a>Základy

**Pracovní prostor synapse** je zabezpečená hranice spolupráce pro cloudové podnikové analýzy v Azure. Pracovní prostor je nasazený v konkrétní oblasti a má přidružený ADLS Gen2 účet a systém souborů (pro ukládání dočasných dat). Pracovní prostor je pod skupinou prostředků.

Pracovní prostor umožňuje provádět analýzy pomocí SQL a Apache Sparku. Prostředky dostupné pro SQL a Spark Analytics jsou uspořádány do **fondů** SQL a Spark. 

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** je schopnost provádět analýzy založené na T-SQL v pracovním prostoru synapse. Synapse SQL má dva modely spotřeby: vyhrazené a bez serveru.  Pro vyhrazený model použijte **vyhrazené fondy SQL**. Pracovní prostor může obsahovat libovolný počet těchto fondů. Pokud chcete používat model bez serveru, používejte **fondy SQL bez serveru**. Každý pracovní prostor má jeden z těchto fondů.

## <a name="apache-spark-for-synapse"></a>Apache Spark pro synapse

Pokud chcete používat Spark Analytics, vytvořte a používejte **fondy Apache Spark bez serveru** v pracovním prostoru synapse.

## <a name="sql-terminology"></a>Terminologie SQL
| Pojem                         | Definice      |
|:---                                 |:---                 |
| **Požadavek SQL**  |   Operace, jako je třeba dotaz, se spouští prostřednictvím vyhrazeného fondu SQL nebo bez serveru SQL. |

## <a name="spark-terminology"></a>Terminologie Spark
| Pojem                         | Definice      |
|:---                                 |:---                 |
|**Apache Spark pro synapse** | Čas spuštění Sparku používaný ve fondu Spark bez serveru Aktuální podporovaná verze je Spark 2,4 s Pythonem 3.6.1, Scala 2.11.12, podpora rozhraní .NET pro Apache Spark 0,5 a rozdílový Lake 0,3.  | 
| **Fond Apache Spark**  | do pracovního prostoru můžete nasadit prostředky z 0 do N Spark zřízené pomocí jejich odpovídajících databází. Fond Spark se dá automaticky pozastavit, obnovit a škálovat.  |
| **Aplikace Spark**  |   Skládá se z procesu ovladače a sady procesů prováděcího modulu. Aplikace Spark běží na fondu Spark bez serveru.            |
| **Relace Spark**  |   Sjednocený vstupní bod aplikace Spark. Poskytuje způsob, jak pracovat s různými funkcemi Sparku a s menším počtem konstrukcí. Chcete-li spustit Poznámkový blok, je třeba vytvořit relaci. Relaci lze nakonfigurovat tak, aby běžela na určitém počtu prováděcích modulů určité velikosti. Výchozí konfigurace pro relaci poznámkového bloku se spouští ve dvou prováděcích modulech střední velikosti. |
|**Integrace dat**| Poskytuje schopnost ingestovat data mezi různými zdroji a orchestrovat aktivity běžící v rámci pracovního prostoru nebo mimo pracovní prostor.| 
|**Artifacts**| Pojem, který zapouzdřuje všechny objekty, které jsou nezbytné pro uživatele ke správě zdrojů dat, vývoji, Orchestrace a vizualizaci.|
|**Poznámkový blok**| Interaktivní a znovu aktivní datové vědy a inženýrské rozhraní, které podporuje Scala, PySpark, C# a SparkSQL. |
|**Definice úlohy Spark**|Rozhraní k odeslání úlohy Sparku pomocí sestavení jar obsahujícího kód a jeho závislosti.|
|**Data Flow**|  Poskytuje plně vizuální prostředí bez nutnosti kódování, které by vyžadovalo transformaci velkých objemů dat. Veškerá optimalizace a provádění jsou zpracovávána způsobem bez serveru. |
|**Skript SQL**| Sada příkazů SQL uložených v souboru. Skript SQL může obsahovat jeden nebo více příkazů SQL. Dá se použít ke spouštění požadavků SQL prostřednictvím vyhrazeného fondu SQL nebo bez serveru SQL.|
|**Kanál**| Logické seskupení aktivit, které provádí úlohu společně.|
|**Aktivita**| Definuje akce, které se mají provádět na datech, jako je kopírování dat, spuštění poznámkového bloku nebo skriptu SQL.|
|**Trigger**| Spustí kanál. Dá se spustit ručně nebo automaticky (naplánování, zabubné okno nebo události).|
|**Propojená služba**| Připojovací řetězce, které definují informace o připojení potřebné pro připojení k externím prostředkům v pracovním prostoru.|
|**Integrován**|  Pojmenované zobrazení dat, které jednoduše odkazuje na data, která se mají použít v aktivitě jako vstup a výstup. Patří do propojené služby.|

## <a name="next-steps"></a>Další kroky

- [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
- [Použití sady Synapse Studio](quickstart-synapse-studio.md)
- [Vytvoření vyhrazeného fondu SQL](quickstart-create-sql-pool-portal.md)
- [Vytvoření fondu Apache Spark bez serveru](quickstart-create-apache-spark-pool-portal.md)
- [Použít fond SQL bez serveru](quickstart-sql-on-demand.md)

