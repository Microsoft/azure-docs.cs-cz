---
title: Terminologie – Azure synapse Analytics
description: Referenční příručka pro uživatele prostřednictvím Azure synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 828f37030ae567cacbaad25849b7ba24c561c20c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98132762"
---
# <a name="azure-synapse-analytics-terminology"></a>Terminologie Azure synapse Analytics

Tento dokument vás provede základními koncepty analýzy Azure synapse.

## <a name="basics"></a>Základy

**Pracovní prostor synapse** je zabezpečená hranice spolupráce pro cloudové podnikové analýzy v Azure. Pracovní prostor je nasazený v konkrétní oblasti a má přidružený ADLS Gen2 účet a systém souborů (pro ukládání dočasných dat). Pracovní prostor je pod skupinou prostředků.

Pracovní prostor umožňuje provádět analýzy pomocí SQL a Apache Sparku. Prostředky dostupné pro SQL a Spark Analytics jsou uspořádány do **fondů** SQL a Spark. 

## <a name="linked-services"></a>Propojené služby

Pracovní prostor může obsahovat libovolný počet **propojených služeb**, v podstatě připojovací řetězce, které definují informace o připojení potřebné k tomu, aby se pracovní prostor připojoval k externím prostředkům.

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** je schopnost provádět analýzy založené na T-SQL v pracovním prostoru synapse. Synapse SQL má dva modely spotřeby: vyhrazené a bez serveru.  Pro vyhrazený model použijte **vyhrazené fondy SQL**. Pracovní prostor může obsahovat libovolný počet těchto fondů. Pokud chcete používat model bez serveru, používejte **fondy SQL bez serveru**. Každý pracovní prostor má jeden z těchto fondů.

V synapse studiu můžete pracovat s fondy SQL vytvořením a spuštěním **skriptů SQL** .

## <a name="apache-spark-for-synapse"></a>Apache Spark pro synapse

Pokud chcete používat Spark Analytics, vytvořte a používejte **fondy Apache Spark bez serveru** v pracovním prostoru synapse. Když začnete používat fond Spark, pracovní prostory vytvoří **relaci Sparku** pro zpracování prostředků přidružených k této relaci. 

Existují dva způsoby, jak v rámci synapse použít Spark:
* **Poznámkové bloky Spark** pro práci s datovými vědy a inženýry, které používají Scala, PySpark, C# a SparkSQL
* **Definice úloh Sparku** pro spouštění úloh služby Batch Spark pomocí souborů JAR

## <a name="pipelines"></a>Pipelines

Kanály představují způsob, jakým Azure synapse zajišťuje integraci dat – umožňuje přesun dat mezi službami a orchestrace aktivit.

* **Kanál** je logické seskupení aktivit, které provádějí úkoly společně.
* **Aktivity** definují akce v rámci kanálu, které se mají provádět na datech, jako je kopírování dat, spuštění poznámkového bloku nebo skriptu SQL.
* **Toky dat** představují konkrétní druh aktivity, která poskytuje prostředí bez kódu pro transformaci dat, která používá synapse Spark v rámci – pokrývá.
* **Trigger** – spustí kanál. Dá se spustit ručně nebo automaticky (naplánování, zabubné okno nebo události).
* **Datová sada Integration** – pojmenovaná zobrazení dat, která jednoduše odkazují na data, která se mají použít v aktivitě jako vstup a výstup. Patří do propojené služby.

## <a name="next-steps"></a>Další kroky

* [Začínáme s Azure Synapse Analytics](get-started.md)
* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použití bezserverového fondu SQL](quickstart-sql-on-demand.md)

