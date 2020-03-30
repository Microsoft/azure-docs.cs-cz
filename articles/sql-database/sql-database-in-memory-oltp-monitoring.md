---
title: Úložiště xtp v paměti monitoru
description: Odhad a sledování využití úložiště XTP v paměti, kapacita; vyřešení chyby kapacity 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 22ff83b1ccd009624082e45073123a45006df70f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209437"
---
# <a name="monitor-in-memory-oltp-storage"></a>Monitor v paměti OLTP úložiště

Při použití [oltp v paměti](sql-database-in-memory.md)jsou data v tabulkách optimalizovaných pro paměť a proměnné tabulky uložena v úložišti OLTP v paměti. Každá úroveň služby Premium a Business Critical má maximální velikost úložiště OLTP v paměti. Viz [Limity prostředků založených na DTU – jedna databáze](sql-database-dtu-resource-limits-single-databases.md), Omezení prostředků založených na [DTU - elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md),[omezení prostředků založených na virtuálních jádrech – limity prostředků založené na single databases](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založených na virtuálních jádrech – elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).

Po překročení tohoto limitu může dojít k selhání operací vložení a aktualizace s chybou 41823 pro jednotlivé databáze a chybou 41840 pro elastické fondy. V tomto okamžiku je třeba buď odstranit data pro paměť, nebo upgradovat úroveň služby nebo výpočetní velikost databáze.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Určení, zda se data vejdou do uzávěru úložiště OLTP v paměti

Určete limity úložiště různých úrovní služeb. Viz [Limity prostředků založených na DTU – jedna databáze](sql-database-dtu-resource-limits-single-databases.md), Omezení prostředků založených na [DTU - elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md),[omezení prostředků založených na virtuálních jádrech – limity prostředků založené na single databases](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založených na virtuálních jádrech – elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).

Odhad požadavků na paměť pro tabulku optimalizovanou pro paměť funguje pro SQL Server stejným způsobem jako v Azure SQL Database. Věnovat několik minut, aby přezkoumala tento článek o [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Řádky proměnných tabulky a tabulky a indexy se počítají do maximální velikosti uživatelských dat. Kromě toho ALTER TABLE potřebuje dostatek místa k vytvoření nové verze celé tabulky a jejích indexů.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování
Použití úložiště v paměti můžete sledovat jako procento limitu úložiště pro velikost výpočetních prostředků na [webu Azure Portal](https://portal.azure.com/): 

1. V okně Databáze vyhledejte pole Využití prostředků a klikněte na Upravit.
2. Vyberte `In-Memory OLTP Storage percentage`metriku .
3. Chcete-li přidat výstrahu, kliknutím na pole Využití prostředků otevřete okno Metrika a klikněte na Přidat výstrahu.

Nebo použijte následující dotaz k zobrazení využití úložiště v paměti:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Oprava situací úložiště OLTP mimo paměť – chyby 41823 a 41840

Stisknutí limitu úložiště OLTP v paměti v databázi má za následek selhání operací INSERT, UPDATE, ALTER a CREATE s chybovou zprávou 41823 (pro jednotlivé databáze) nebo chybou 41840 (pro elastické fondy). Obě chyby způsobit přerušení aktivní transakce.

Chybové zprávy 41823 a 41840 označují, že tabulky optimalizované pro paměť a proměnné tabulky v databázi nebo fondu dosáhly maximální velikost úložiště OLTP v paměti.

Chcete-li tuto chybu vyřešit, buď:

* Odstraňte data z tabulek optimalizovaných pro paměť a potenciálně je přetáčíte na tradiční tabulky založené na disku. Nebo
* Upgradujte úroveň služby na úroveň s dostatečným úložištěm v paměti pro data, která potřebujete uchovávat v tabulkách optimalizovaných pro paměť.

> [!NOTE] 
> Ve výjimečných případech mohou být chyby 41823 a 41840 přechodné, což znamená, že je k dispozici dostatek úložiště OLTP v paměti a opakování operace proběhne úspěšně. Proto doporučujeme sledovat celkové dostupné úložiště OLTP v paměti a opakovat při prvním výskytu chyby 41823 nebo 41840. Další informace o logice opakování naleznete [v tématu Conflict Detection and Retry Logic with In-Memory OLTP](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Další kroky
Pokyny pro monitorování najdete [v tématu Monitorování azure sql database pomocí zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md).
