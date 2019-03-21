---
title: Zpráva k vydání verze služby Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Zpráva k vydání verze pro Azure SQL Data Warehouse.
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 0b0d302dcd86f18eef6bf7600dd93878d0d9bd99
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57902684"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Poznámky k verzi Azure SQL Data Warehouse
Tento článek shrnuje nové funkce a vylepšení v posledních verzích [Azure SQL Data Warehouse](sql-data-warehouse-overview-what-is.md). V článku jsou uvedeny také důležité aktualizace obsahu, které jsou přímo související s vydáním ale publikovat ve stejném časovém rámci. Vylepšení k jiným službám Azure, najdete v části [aktualizace služeb](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>SQL Data Warehouse verze 10.0.10106.0 (leden)

### <a name="service-improvements"></a>Vylepšení služby

| Vylepšení služby | Podrobnosti |
| --- | --- |
|**Vrátí pořadí podle optimalizace**|VYBERTE... Klauzule ORDER BY dotazů získat zvýšení výkonu v této verzi.   Teď součástí všech výpočetních uzlů odesílat že své výsledky do jediné výpočetního uzlu, sloučení, který Seřadí výsledky, které jsou následně vráceny uživateli prostřednictvím výpočetním uzlu.  Slučování přes jeden výpočetní uzel, výsledkem zvýšení výkonu, když obsahuje velký počet řádků sady výsledků dotazu. Dříve by dotazu spouštěcí modul pořadí výsledky na každém výpočetním uzlu a Streamovat je do ovládacího prvku uzlu, na kterém by pak sloučit výsledky.|
|**Vylepšení přesunu dat pro PartitionMove a BroadcastMove**|V Azure SQL Data Warehouse Gen2, kroky pro přesun dat typu ShuffleMove, použijte rychlé data přesun techniky popsané v [blogu vylepšení výkonu](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/). V této verzi datové typy přesun PartitionMove a BroadcastMove nyní také využívají stejné techniky rychlé data pohyb. Uživatelské dotazy, které využívají tyto druhy kroky pro přesun dat se spustí s vyšším výkonem. Žádné změny kódu je potřebné k využití těchto vylepšení výkonu.|
|**Důležité chyby**|Nesprávná verze Azure SQL Data Warehouse – "vyberte@VERSION" může vrátit nesprávné verze 10.0.9999.0. 10.0.10106.0 je správná verze na aktuální vydání. Tato chyba byla nahlášena a je pod kontrolu.

### <a name="documentation-improvements"></a>Dokumentace k vylepšení

| Dokumentace k vylepšení | Podrobnosti |
| --- | --- |
|žádný | |
| | |

## <a name="next-steps"></a>Další postup
- [vytvořit SQL Data Warehouse](./create-data-warehouse-portal.md)

## <a name="more-information"></a>Další informace
- [Blog – Azure SQL Data Warehouse](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Blogy zákaznického poradního týmu](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [Úspěšné zákaznické implementace](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [Videa](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Glosář Azure](../azure-glossary-cloud-terminology.md)
