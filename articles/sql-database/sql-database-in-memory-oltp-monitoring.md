---
title: Monitorování úložiště XTP v paměti
description: Odhadnout a monitorovat využití úložiště XTP v paměti, kapacity; vyřešit chybu s kapacitou 41823
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: d67a6d151f65252faab40f8f7289fe9e884eff17
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689808"
---
# <a name="monitor-in-memory-oltp-storage"></a>Monitorování úložiště OLTP v paměti

Při použití [OLTP v paměti](sql-database-in-memory.md)se data v paměťově optimalizovaných tabulkách a proměnných tabulek nacházejí v úložišti OLTP úložiště v paměti. Každá úroveň služby Premium a Pro důležité obchodní informace má maximální velikost úložiště OLTP v paměti. Viz omezení [prostředků na základě DTU –](sql-database-dtu-resource-limits-single-databases.md)izolovaná databáze, [omezení prostředků založené na DTU – elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md),[omezení prostředků založené na Vcore – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založené na Vcore – elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).

Po překročení tohoto limitu můžou operace INSERT a Update začít selhat s chybou 41823 pro izolované databáze a chybou 41840 pro elastické fondy. V tomto okamžiku potřebujete buď odstranit data pro uvolnění paměti, nebo upgradovat úroveň služby nebo výpočetní velikost vaší databáze.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Určení, jestli se data vejdou do OLTP úložiště v paměti

Určete velikost úložiště pro různé úrovně služeb. Viz omezení [prostředků na základě DTU –](sql-database-dtu-resource-limits-single-databases.md)izolovaná databáze, [omezení prostředků založené na DTU – elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md),[omezení prostředků založené na Vcore – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založené na Vcore – elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).

Odhad požadavků na paměť pro paměťově optimalizovanou tabulku funguje stejným způsobem jako v Azure SQL Database SQL Server. Projděte si tento článek na [webu MSDN](https://msdn.microsoft.com/library/dn282389.aspx), abyste si převzali několik minut.

Řádky a proměnné tabulky a také indexy se počítají směrem k maximální velikosti uživatelských dat. Kromě toho příkaz ALTER TABLE potřebuje dostatek místa, aby bylo možné vytvořit novou verzi celé tabulky a jejích indexů.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování
Využití úložiště v paměti můžete monitorovat jako procentuální hodnotu limitu úložiště pro vaši výpočetní velikost v [Azure Portal](https://portal.azure.com/): 

1. V okně databáze vyhledejte pole využití prostředků a klikněte na Upravit.
2. Vyberte `In-Memory OLTP Storage percentage`metriky.
3. Pokud chcete přidat výstrahu, kliknutím na pole využití prostředků otevřete okno metrika a pak klikněte na Přidat výstrahu.

Nebo použijte následující dotaz k zobrazení využití úložiště v paměti:

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Opravte situaci OLTP úložiště při nedostatku paměti – chyby 41823 a 41840

Zasáhne se limit úložiště OLTP v paměti ve vaší databázi, ve kterém dojde k selhání operací vložení, aktualizace, ALTER a CREATE s chybovou zprávou 41823 (pro izolované databáze) nebo chybou 41840 (pro elastické fondy). Obě chyby způsobují přerušení aktivní transakce.

Chybové zprávy 41823 a 41840 označují, že paměťově optimalizované tabulky a proměnné tabulky v databázi nebo ve fondu dosáhly maximální velikosti úložiště OLTP v paměti.

Chcete-li vyřešit tuto chybu, proveďte jednu z těchto akcí:

* Odstraňte data z paměťově optimalizovaných tabulek, která mohou překládat data na tradiční tabulky založené na disku. ani
* Upgradujte vrstvu služby na jednu s dostatečným úložištěm v paměti pro data, která potřebujete k uchování v paměťově optimalizovaných tabulkách.

> [!NOTE] 
> Ve výjimečných případech můžou být chyby 41823 a 41840 přechodné, což znamená, že je k dispozici dostatek paměťového úložiště OLTP a opakování operace proběhne úspěšně. Proto doporučujeme monitorovat celkové dostupné úložiště OLTP v paměti a opakovat pokus při prvním výskytu chyby 41823 nebo 41840. Další informace o logice opakování najdete v tématu [zjištění konfliktů a logika opakování s OLTP v paměti](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Další kroky
Pokyny k monitorování najdete v tématu [monitorování Azure SQL Database pomocí zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md).
