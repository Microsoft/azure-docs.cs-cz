---
title: Optimalizace Hromadná vložení na serveru Azure Database for PostgreSQL
description: Tento článek popisuje, jak můžete optimalizovat hromadné operace insert v databázi Azure pro PostgreSQL server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: fba109e04369c05f98e863b7dd0fa3d51f40d0ad
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810234"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql-server"></a>Optimalizujte operace hromadného vložení a používat přechodné data ve službě Azure Database pro PostgreSQL server 
Tento článek popisuje, jak můžete optimalizovat operace hromadného vložení a přechodné data ve službě Azure Database použít pro PostgreSQL server.

## <a name="use-unlogged-tables"></a>Použití neprotokolovaného tabulek
Pokud máte operace úlohy, které zahrnují přechodných dat nebo, který hromadné vložení velkých datových sad, zvažte použití neprotokolovaného tabulky.

Neprotokolovaného tabulek je funkce, PostgreSQL, která umožňuje efektivní optimalizaci Hromadná vložení. PostgreSQL využívá dávky zápisu protokolování (WAL). Ve výchozím nastavení poskytuje atomicitu a odolnost. Atomicitu, konzistence, izolace a odolnost tvoří vlastnosti ACID. 

Vkládání do prostředku neprotokolovaného tabulky této PostgreSQL vloží bez zápisu do transakce protokolu, která sama se vstupně-výstupní operace. V důsledku toho se tyto tabulky výrazně rychlejší než běžné tabulky.

K vytvoření neprotokolovaného tabulky pomocí následujících možností:
- Vytvořit novou tabulku neprotokolovaného pomocí syntaxe `CREATE UNLOGGED TABLE <tableName>`.
- Převést existující přihlášení tabulky na tabulku neprotokolovaného pomocí syntaxe `ALTER <tableName> SET UNLOGGED`.  

Chcete-li se vrátit do, použijte syntaxi `ALTER <tableName> SET LOGGED`.

## <a name="unlogged-table-tradeoff"></a>Kompromis neprotokolovaného tabulky
Neprotokolovaného tabulky nejsou bezpečné při selhání. Neprotokolovaného tabulky automaticky zkrácen po zhroucení nebo v souladu vypnuly. Obsah neprotokolovaného tabulky také se nereplikují do pohotovostního servery. Všechny indexy vytvořené na neprotokolovaného tabulky jsou také automaticky neprotokolovaného. Po vložení operace dokončí, převeďte tabulku protokolování tak, aby byla odolná insert.

Některé úlohy zákazníka došlo přibližně 15 až 20 procent zkrátil při neprotokolovaného tabulky byly použity.

## <a name="next-steps"></a>Další postup
Zkontrolujte vaše úlohy pro použití přechodných dat a velké Hromadná vložení. V následující dokumentaci k PostgreSQL:
 
- [Vytvoření tabulky SQL příkazy](https://www.postgresql.org/docs/current/static/sql-createtable.html)