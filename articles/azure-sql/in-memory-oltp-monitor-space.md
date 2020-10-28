---
title: Monitorování úložiště XTP v paměti
description: Odhadnout a monitorovat využití úložiště XTP v paměti, kapacity; vyřešit chybu s kapacitou 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 84e4ec1e7b3b5ec935bb9d5d819b4562e7fd3e5a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791694"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>Monitorování In-Memory OLTP úložiště v Azure SQL Database a spravované instanci Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Při použití [OLTP v paměti](in-memory-oltp-overview.md)se data v paměťově optimalizovaných tabulkách a proměnných tabulek nacházejí v ÚLOŽIŠTI In-Memory OLTP.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Určení, jestli se data vejdou do In-Memory limit úložiště OLTP

Určete velikost úložiště pro různé úrovně služeb. Každá úroveň služby Premium a Pro důležité obchodní informace má maximální velikost úložiště In-Memory OLTP.

- [Omezení prostředků na základě DTU – samostatná databáze](database/resource-limits-dtu-single-databases.md)
- [Omezení prostředků na základě DTU – elastické fondy](database/resource-limits-dtu-elastic-pools.md)
- [omezení prostředků založené na vCore – samostatné databáze](database/resource-limits-vcore-single-databases.md)
- [omezení prostředků založené na vCore – elastické fondy](database/resource-limits-vcore-elastic-pools.md)
- [omezení prostředků založené na vCore – spravovaná instance](managed-instance/resource-limits.md)

Odhad požadavků na paměť pro paměťově optimalizovanou tabulku funguje stejným způsobem jako SQL Server v Azure SQL Database a spravované instanci Azure SQL. Kontrola [požadavků na paměť odhadem](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables)trvá několik minut.

Řádky a proměnné tabulky a také indexy se počítají směrem k maximální velikosti uživatelských dat. Kromě toho příkaz ALTER TABLE potřebuje dostatek místa, aby bylo možné vytvořit novou verzi celé tabulky a jejích indexů.

Po překročení tohoto limitu můžou operace INSERT a Update začít selhat s chybou 41823 pro izolované databáze v Azure SQL Database a databází ve spravované instanci SQL Azure a chyba 41840 pro elastické fondy v Azure SQL Database. V tomto okamžiku potřebujete buď odstranit data pro uvolnění paměti, nebo upgradovat úroveň služby nebo výpočetní velikost vaší databáze.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

Využití úložiště v paměti můžete monitorovat jako procentuální hodnotu limitu úložiště pro vaši výpočetní velikost v [Azure Portal](https://portal.azure.com/):

1. V okně databáze vyhledejte pole využití prostředků a klikněte na Upravit.
2. Vyberte metriku `In-Memory OLTP Storage percentage` .
3. Pokud chcete přidat výstrahu, kliknutím na pole využití prostředků otevřete okno metrika a pak klikněte na Přidat výstrahu.

Nebo použijte následující dotaz k zobrazení využití úložiště v paměti:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Opravte situaci OLTP úložiště při nedostatku paměti – chyby 41823 a 41840

Při neúspěchu In-Memory OLTP úložiště ve vaší databázi dojde k selhání operací vložení, aktualizace, ALTER a CREATE s chybovou zprávou 41823 (pro izolované databáze) nebo chybou 41840 (pro elastické fondy). Obě chyby způsobují přerušení aktivní transakce.

Chybové zprávy 41823 a 41840 označují, že paměťově optimalizované tabulky a proměnné tabulky v databázi nebo ve fondu dosáhly maximální velikosti úložiště In-Memory OLTP.

Chcete-li vyřešit tuto chybu, proveďte jednu z těchto akcí:

- Odstraňte data z paměťově optimalizovaných tabulek, která mohou překládat data na tradiční tabulky založené na disku. ani
- Upgradujte vrstvu služby na jednu s dostatečným úložištěm v paměti pro data, která potřebujete k uchování v paměťově optimalizovaných tabulkách.

> [!NOTE]
> Ve výjimečných případech můžou být chyby 41823 a 41840 přechodné, což znamená, že je k dispozici dostatek In-Memory úložiště OLTP a opakování operace proběhne úspěšně. Proto doporučujeme monitorovat celkové dostupné In-Memory úložiště OLTP a opakovat pokus při prvním výskytu chyby 41823 nebo 41840. Další informace o logice opakování najdete v tématu [zjištění konfliktů a logika opakování s In-Memory OLTP](/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Další kroky

Pokyny k monitorování najdete v tématu [monitorování pomocí zobrazení dynamické správy](database/monitoring-with-dmvs.md).