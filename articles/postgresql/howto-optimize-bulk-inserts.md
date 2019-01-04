---
title: Optimalizace Hromadná vložení ve službě Azure Database pro PostgreSQL server
description: Tento článek popisuje, jak můžete optimalizovat hromadné operace insert v databázi Azure pro PostgreSQL server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 9d2bfcddc649e4fff68bdba49df0945e88067036
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545225"
---
# <a name="optimizing-bulk-inserts-and-use-of-transient-data-on-azure-database-for-postgresql-server"></a>Optimalizace Hromadná vložení a použití přechodných dat v databázi Azure pro PostgreSQL server 
Tento článek popisuje, jak je možné optimalizovat operace hromadného vložení a použití přechodných dat ve službě Azure Database pro PostgreSQL server.

## <a name="using-unlogged-tables"></a>Použití neprotokolovaného tabulek
Pro zákazníky, kteří mají operace úlohy, které zahrnují přechodných dat nebo, který hromadné vložení velkých datových sad zvažte použití neprotokolovaného tabulky.

Neprotokolovaného tabulek je funkce, PostgreSQL, která umožňuje efektivní optimalizaci Hromadná vložení. PostgreSQL využívá dávky zápisu protokolování (WAL), která poskytuje atomicitu a odolnosti dvě vlastnosti ACID ve výchozím nastavení. Vkládání do neprotokolovaného tabulky by střední PostgreSQL děláte vloží bez zápisu do protokolu transakcí, které se vstupně-výstupní operace, provádění těchto tabulek zkopírují podstatně rychleji než běžné tabulky.

Níže jsou uvedeny možnosti pro vytváření neprotokolovaného tabulky:
- Vytvořte novou tabulku neprotokolovaného pomocí syntaxe: `CREATE UNLOGGED TABLE <tableName>`
- Převést existující přihlášení tabulky na tabulku neprotokolovaného pomocí syntaxe: `ALTER <tableName> SET UNLOGGED`.  To je možné vrátit zpět pomocí syntaxe: `ALTER <tableName> SET LOGGED`

## <a name="unlogged-table-tradeoff"></a>Kompromis neprotokolovaného tabulky
Neprotokolovaného tabulky nejsou bezpečné při selhání. Neprotokolovaného tabulky automaticky zkrácen po zhroucení nebo v souladu vypnuly. Obsah neprotokolovaného tabulky také se nereplikují do pohotovostního servery. Všechny indexy vytvořené na neprotokolovaného tabulky jsou také automaticky neprotokolovaného.  Po dokončení operace insert může převést tabulka má zaznamenána insert je trvalý.

Ale v některých úloh zákazníka, kterými jsme se přibližně 15 až 20 procent zkrátil při použití neprotokolovaného tabulky.

## <a name="next-steps"></a>Další postup
Zkontrolujte vaše úlohy pro použití přechodných dat a velké Hromadná vložení.  

Projděte si následující dokumentaci k PostgreSQL - [SQL příkazy Create Table](https://www.postgresql.org/docs/current/static/sql-createtable.html)