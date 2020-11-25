---
title: Terminologie – Azure synapse Analytics (ve verzi Preview pracovních prostorů)
description: Referenční příručka pro uživatele prostřednictvím Azure synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: c58ee46a608ccdcbb01a082ee278d9e0f8a07f6e
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030675"
---
# <a name="azure-synapse-analytics-terminology"></a>Terminologie Azure synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

Tento dokument vás provede základními koncepty analýzy Azure synapse.

## <a name="basics"></a>Základy

**Pracovní prostor synapse** je zabezpečená hranice spolupráce pro cloudové podnikové analýzy v Azure. Pracovní prostor je nasazený v konkrétní oblasti a má přidružený ADLS Gen2 účet a systém souborů (pro ukládání dočasných dat). Pracovní prostor je pod skupinou prostředků.

Pracovní prostor umožňuje provádět analýzy pomocí SQL a Apache Sparku. Prostředky dostupné pro SQL a Spark Analytics jsou uspořádány do **fondů** SQL a Spark. 

## <a name="linked-services"></a>Propojené služby

Pracovní prostor může obsahovat libovolný počet **propojených služeb**, v podstatě připojovací řetězce, které definují informace o připojení potřebné k tomu, aby se pracovní prostor připojoval k externím prostředkům.

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** je schopnost provádět analýzy založené na T-SQL v pracovním prostoru synapse. Synapse SQL má dva modely spotřeby: vyhrazené a bez serveru.  Pro vyhrazený model použijte **vyhrazené fondy SQL**. Pracovní prostor může obsahovat libovolný počet těchto fondů. Pokud chcete používat model bez serveru, používejte **fondy SQL bez serveru**. Každý pracovní prostor má jeden z těchto fondů.

* **Požadavek SQL** – operace, jako je dotaz spouštěný prostřednictvím vyhrazeného fondu SQL nebo bez serveru SQL
* **Skript SQL** – sada příkazů SQL, které jsou uložené v souboru. Skript SQL může obsahovat jeden nebo více příkazů SQL. Dá se použít ke spouštění požadavků SQL prostřednictvím vyhrazeného fondu SQL nebo bez serveru SQL.

## <a name="apache-spark-for-synapse"></a>Apache Spark pro synapse

Pokud chcete používat Spark Analytics, vytvořte a používejte **fondy Apache Spark bez serveru** v pracovním prostoru synapse. Když začnete používat fond Spark, pracovní prostory vytvoří **relaci Sparku** pro zpracování prostředků assosociated s touto relací. 

Existují dva způsoby, jak v rámci synapse použít Spark:
* **Poznámkové bloky Spark** pro práci s datovými vědy a inženýry, které používají Scala, PySpark, C# a SparkSQL
* **Definice úloh Sparku** pro spouštění úloh služby Batch Spark pomocí souborů JAR

Podpora verzí:
* Spark 2,4
* Python 3.6.1
* Scala 2.11.12
* .NET pro Apache Spark 1,0
* Rozdílový Lake 0,3.  

## <a name="pipelines"></a>Pipelines

* **Integrace dat** – poskytuje možnost ingestovat data mezi různými zdroji a orchestrovat aktivity běžící v rámci pracovního prostoru nebo mimo pracovní prostor.
* **Tok dat** – poskytuje plně vizuální prostředí bez nutnosti kódování, které by vyžadovalo transformaci velkých objemů dat. Veškerá optimalizace a provádění jsou zpracovávána způsobem bez serveru.
* **Kanál** – logické seskupení aktivit, které provádějí úkoly společně.
* **Activity** – definuje akce, které se mají provádět na datech, jako je kopírování dat, spuštění poznámkového bloku nebo skriptu SQL.
* **Trigger** – spustí kanál. Dá se spustit ručně nebo automaticky (naplánování, zabubné okno nebo události).
* **Datová sada Integration** – pojmenovaná zobrazení dat, která jednoduše odkazují na data, která se mají použít v aktivitě jako vstup a výstup. Patří do propojené služby.

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure synapse Analytics](get-started.md)
* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použití bezserverového fondu SQL](quickstart-sql-on-demand.md)

