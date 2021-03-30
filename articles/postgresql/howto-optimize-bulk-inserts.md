---
title: Optimalizace hromadných vložení – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete optimalizovat operace hromadného vkládání na Azure Database for PostgreSQL jednom serveru.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 4d10f06577738364e3f4a0ea40221d37ebfb31c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86116280"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Optimalizujte hromadné vkládání a používejte přechodná data na Azure Database for PostgreSQL-jednom serveru. 
Tento článek popisuje, jak můžete optimalizovat operace hromadného vkládání a používat přechodná data na serveru Azure Database for PostgreSQL.

## <a name="use-unlogged-tables"></a>Použití neprotokolovaných tabulek
Pokud máte operace s úlohami, které zahrnují přechodná data nebo hromadné vkládání velkých datových sad, zvažte použití neprotokolovaných tabulek.

Neprotokolované tabulky jsou funkcí PostgreSQL, která se dá efektivně použít k optimalizaci hromadných vložení. PostgreSQL používá protokolování Write-Ahead (WAL). Ve výchozím nastavení zajišťuje nedělitelnost a odolnost. Nedělitelnost, konzistence, izolace a odolnost tvoří vlastnosti KYSELosti. 

Vložením do neprotokolované tabulky znamená, že PostgreSQL vloží bez zápisu do transakčního protokolu, který je sám operací I/O. V důsledku toho jsou tyto tabulky mnohem rychlejší než běžné tabulky.

K vytvoření neprotokolované tabulky použijte následující možnosti:
- Vytvořte novou neprotokolovou tabulku pomocí syntaxe `CREATE UNLOGGED TABLE <tableName>` .
- Převeďte existující protokolovaných tabulek na neprotokolovaný stůl pomocí syntaxe `ALTER TABLE <tableName> SET UNLOGGED` .  

K opačnému procesu použijte syntaxi `ALTER TABLE <tableName> SET LOGGED` .

## <a name="unlogged-table-tradeoff"></a>Neprotokolovaný kompromis v tabulkách
Neprotokolované tabulky nejsou bezpečné pro selhání. Neprotokolovaná tabulka se automaticky zkrátí po chybě nebo v důsledku nečistého vypnutí. Obsah nezaznamenané tabulky se také nereplikuje na pohotovostní servery. Všechny indexy vytvořené v neprotokolovaných tabulkách jsou také automaticky odhlášeny. Po dokončení operace vložení převeďte tabulku do protokolu, aby bylo vkládání trvalé.

U některých úloh zákazníků se při použití neprotokolovaných tabulek vyskytlo přibližně 15 procent až 20 procent zlepšení výkonu.

## <a name="next-steps"></a>Další kroky
Projděte si úlohy pro použití přechodných dat a velkých hromadných vložení. Podívejte se na následující dokumentaci k PostgreSQL:
 
- [Příkazy CREATE TABLE SQL](https://www.postgresql.org/docs/current/static/sql-createtable.html)
