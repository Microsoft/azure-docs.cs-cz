---
title: Monitorování úložiště v paměti XTP | Dokumentace Microsoftu
description: Použít odhadu a sledování XTP úložiště v paměti, kapacity; Opravte chyby kapacity 41823
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jodebrui
ms.author: jodebrui
ms.reviewer: genemi
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: ac7b568d95b9a2c382b1c167965942f0733012c4
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874919"
---
# <a name="monitor-in-memory-oltp-storage"></a>Úložiště OLTP v paměti monitorování
Při použití [OLTP v paměti](sql-database-in-memory.md), data v paměťově optimalizovaných tabulkách a proměnné tabulky se nachází v úložiště OLTP v paměti. Každá úroveň služby úrovně Premium a pro důležité obchodní informace má maximální velikost úložiště OLTP v paměti. V tématu [omezení prostředků založený na DTU – izolované databáze](sql-database-dtu-resource-limits-single-databases.md), [omezení prostředků založený na DTU - elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md),[omezení prostředků na základě virtuálních jader – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založený na virtuálních jádrech - elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md).

Jakmile je tento limit překročen, vkládací a aktualizační operace mohou začít selhávat, chyba 41823 pro izolované databáze a elastických fondů v podrobnostech o chybě 41840. V tomto okamžiku budete muset buď odstranit data uvolnit paměť, nebo upgradovat na vrstvu služby nebo vypočítat velikost vaší databáze.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Určení, zda data vešla do kapacita úložiště OLTP v paměti
Určení limitů úložiště z různými úrovněmi služeb. V tématu [omezení prostředků založený na DTU – izolované databáze](sql-database-dtu-resource-limits-single-databases.md), [omezení prostředků založený na DTU - elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md),[omezení prostředků na základě virtuálních jader – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založený na virtuálních jádrech - elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md).

Odhadnout požadavky na paměť pro paměťově optimalizované tabulky funguje stejným způsobem pro SQL Server jako to dělá ve službě Azure SQL Database. Trvat pár minut a přečtěte si tento článek na [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Tabulky a proměnné řádky tabulky, jakož i indexy, počítají velikost dat maximální počet uživatelů. Příkaz ALTER TABLE navíc musí dostatek volného místa k vytvoření nové verze celou tabulku a jeho indexy.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování
Můžete monitorovat využití úložiště v paměti jako procento z limitu úložiště pro vaše výpočetní velikost v [webu Azure portal](https://portal.azure.com/): 

1. V okně databáze najít pole využití prostředků a klikněte na Upravit.
2. Vyberte metriku `In-Memory OLTP Storage percentage`.
3. Přidat upozornění, klikněte na pole využití prostředků a otevřete tak okno metriky a potom klikněte na Přidat upozornění.

Nebo použijte tento dotaz k zobrazení využití úložiště v paměti:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Opravte-v-paměti OLTP úložiště situacích – chyby 41823 a 41840
Dosažení limitu úložiště OLTP v paměti ve výsledcích databáze v vložit, aktualizovat, ALTER a vytvoření operací služeb při selhání s chybovou zprávu 41823 (pro izolované databáze) nebo Chyba 41840 (pro elastické fondy). Obě chyby způsobit aktivní transakci pro přerušení.

Chybové zprávy 41823 a 41840 naznačují, že paměťově optimalizovaných tabulkách a proměnné tabulky v databázi nebo fond dosáhl maximální velikosti úložiště OLTP v paměti.

Chcete-li vyřešit tuto chybu, buď:

* Odstranění dat z paměťově optimalizované tabulky, potenciálně snižování zátěže data, která mají tabulkách tradiční, založené na disku. nebo,
* Upgrade na úrovni služby do jednoho s dostatečným místem úložiště v paměti pro data, která je potřeba nechat v paměťově optimalizovaných tabulkách.

> [!NOTE] 
> Ve výjimečných případech může být chyby 41823 a 41840 přechodné, což znamená, je k dispozici dostatek dostupného úložiště OLTP v paměti a opakováním operace proběhne úspěšně. Proto doporučujeme pro oba sledování celkové dostupné úložiště OLTP v paměti a opakovat, pokud nejprve zjištění chyby 41823 nebo 41840. Další informace o logiku opakování, naleznete v tématu [zjišťování konfliktů a logika opakování s OLTP v paměti](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Další postup
Pokyny k monitorování, najdete v části [monitorování Azure SQL Database pomocí zobrazení dynamické správy](sql-database-monitoring-with-dmvs.md).
