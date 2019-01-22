---
title: Azure SQL Data Warehouse poznámky k verzi z prosince 2018 | Dokumentace Microsoftu
description: Zpráva k vydání verze pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 074276e4550b9b2e347e5cd30c597a1d09f6cb2f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440216"
---
# <a name="whats-new-in-azure-sql-data-warehouse-version---100101060"></a>Co je nového ve verzi Azure SQL Data Warehouse – 10.0.10106.0?
Azure SQL Data Warehouse (SQL data Warehouse) průběžně zlepšují. Tento článek popisuje nové funkce a změny, které byly zavedeny v SQL data Warehouse verze 10.0.10106.0.

## <a name="query-restartability---ctas-and-insertselect"></a>Dotaz Restartability - CTAS a vložit/vybrat
Spuštění v Azure SQL data Warehouse může selhat v dotazech výjimečné situace (to znamená, přerušované problémy s připojením sítě, selhání uzlu). Už systémem příkazy, jako například [vytvořit TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) a operace INSERT SELECT, jsou vystaveny další potenciální potíže. V této vydané verzi Azure SQL data Warehouse implementuje logika opakovaných pokusů pro CTAS a Vložit vyberte příkazy (navíc jsme dřív oznámili příkazy SELECT), umožňuje systém transparentně zpracovával tyto přechodné problémy a dotazy brání selhání. Počet opakovaných pokusů a seznam zpracování přechodných chyb jsou nakonfigurované systému.

## <a name="return-order-by-optimization"></a>Vrátí pořadí podle optimalizace
VYBERTE... Klauzule ORDER BY dotazů získat zvýšení výkonu v této verzi.  Dříve by dotazu spouštěcí modul pořadí výsledky na každém výpočetním uzlu a Streamovat je do ovládacího prvku uzlu, na kterém by pak sloučit výsledky. S toto vylepšení součástí všech výpočetních uzlů k odesílání jejich výsledky pro jeden výpočetní uzel, který pak sloučí je a vrátí seřazených výsledků pro uživatele prostřednictvím výpočetním uzlu.  To nabízí významné výkonnější, když obsahuje velký počet řádků sady výsledků dotazu.

## <a name="data-movement-enhancements-for-partitionmove-and-broadcastmove"></a>Vylepšení přesunu dat pro PartitionMove a BroadcastMove
V Azure SQL Data Warehouse Gen2 kroky pro přesun dat typu ShuffleMove využít rychlé data přesun techniky popsané v [zde na blog vylepšení výkonu](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/).  V této verzi datové typy přesun PartitionMove a BroadcastMove nyní také využívají stejné techniky rychlé data pohyb.  Uživatelské dotazy, které využívají tyto druhy kroky pro přesun dat se zobrazí zvýšení výkonu.  Abyste mohli využívat tyto zvýšení výkonu se vyžaduje žádné změny kódu.

## <a name="next-steps"></a>Další postup
Teď, když už víte o něco o SQL Data Warehouse, zjistěte, jak rychle [vytvořit SQL Data Warehouse][create a SQL Data Warehouse]. Pokud jste ještě do Azure, můžete zjistit [Glosář Azure] [ Azure glossary] užitečné při učení, nový terminologií. Můžete se také podívat na některé z těchto dalších zdrojů ke službě SQL Data Warehouse.  

* [Úspěšné zákaznické implementace]
* [Blogy]
* [Žádosti o funkce]
* [Videa]
* [Blogy zákaznického poradního týmu]
* [Fórum Stack Overflow]
* [Twitter]


[Blogy]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogy zákaznického poradního týmu]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Úspěšné zákaznické implementace]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Žádosti o funkce]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videa]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
