---
title: Optimalizace hromadných vložek – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete optimalizovat operace hromadného vkládání v databázi Azure pro PostgreSQL – jeden server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4c4bac16917be0064ebb111328753d378d462a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770131"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Optimalizace hromadných vložení a použití přechodných dat v databázi Azure pro PostgreSQL – jeden server 
Tento článek popisuje, jak můžete optimalizovat operace hromadného vkládání a používat přechodná data v databázi Azure pro postgreSQL server.

## <a name="use-unlogged-tables"></a>Použití neprotokolovaných tabulek
Pokud máte operace pracovního vytížení, které zahrnují přechodná data nebo které vkládají velké datové sady hromadně, zvažte použití neprotokolovaných tabulek.

Neprotokolované tabulky je funkce PostgreSQL, kterou lze efektivně použít k optimalizaci hromadných vložek. PostgreSQL používá protokolování na předzápis (WAL). Poskytuje nedělitost a trvanlivost, ve výchozím nastavení. Atomicita, konzistence, izolace a trvanlivost tvoří vlastnosti ACID. 

Vložení do neprotokolované tabulky znamená, že PostgreSQL vloží bez zápisu do transakčního protokolu, což je sama o sobě vstupně-nezaujatá operace. V důsledku toho jsou tyto tabulky podstatně rychlejší než běžné tabulky.

K vytvoření nezaprotokolované tabulky použijte následující možnosti:
- Vytvořte novou neodhlazenou `CREATE UNLOGGED TABLE <tableName>`tabulku pomocí syntaxe .
- Převeďte existující protokolovnou tabulku na nezaprotokolovankou pomocí syntaxe `ALTER TABLE <tableName> SET UNLOGGED`.  

Chcete-li proces obrátit, `ALTER TABLE <tableName> SET LOGGED`použijte syntaxi .

## <a name="unlogged-table-tradeoff"></a>Neohlášený kompromis od stolu
Nezaprotokolované stoly nejsou bezpečné při selhání. Neprotokolovaná tabulka je automaticky zkrácena po chybě nebo podléhá nečistému vypnutí. Obsah nezaprotokolované tabulky také nejsou replikovány na pohotovostní servery. Všechny indexy vytvořené v neprotokolované tabulce jsou také automaticky neprotokolovány. Po dokončení operace vložení převeďte tabulku na protokolovitou tak, aby vložení bylo trvanlivé.

U některých úloh zákazníků došlo při použití neprotokolovaných tabulek ke zlepšení výkonu přibližně o 15 až 20 procent.

## <a name="next-steps"></a>Další kroky
Zkontrolujte úlohy pro použití přechodných dat a velkých hromadných vložení. Podívejte se na následující postgreSQL dokumentaci:
 
- [Vytvořit příkazy SQL tabulky](https://www.postgresql.org/docs/current/static/sql-createtable.html)
