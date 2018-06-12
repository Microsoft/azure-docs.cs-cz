---
title: Postup použití sys_schema optimalizace výkonu a údržby databáze v databázi Azure pro databázi MySQL
description: Tento článek popisuje, jak používat sys_schema najít problémy s výkonem a udržovat databázi v databázi Azure pro databázi MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 74bb59a8db70d4a01fcd3bd07054f1cbac50bf40
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266148"
---
# <a name="how-to-use-sysschema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Jak používat sys_schema pro výkon ladění a údržby databáze v databázi Azure pro databázi MySQL

MySQL performance_schema, první dostupné v MySQL 5.5 poskytuje instrumentace pro mnoho důležitých serveru prostředků jako přidělení paměti, uložené programy, metadata uzamčení atd. Ale performance_schema obsahuje více než 80 tabulky a získávání nezbytné informace často vyžaduje připojení tabulek v rámci performance_schema, jakož i tabulky z information_schema. Sestavování na performance_schema i information_schema, sys_schema poskytuje výkonné kolekci [uživatelsky přívětivý zobrazení](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) v databázi jen pro čtení a je plně povolena ve službě Azure Database pro databázi MySQL verzi 5.7.

![zobrazení sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Existují 52 zobrazení v sys_schema a jednotlivých zobrazení má jednu následujícími předponami:

- Host_summary nebo vstupně-výstupní operace: vstupně-výstupních operací souvisejících s latence.
- InnoDB: Stav InnoDB vyrovnávací paměti a zámky.
- Paměť: Využití paměti hostiteli a uživatelů.
- Schémat: Schéma související informace, například automatické zvýšení, indexy, atd.
- Příkaz: Informace o příkazech SQL; může být příkaz, který způsobil prohledávání úplnou tabulky nebo dlouho dobu dotazu.
- Uživatel: Prostředky spotřebované a seskupených podle uživatele. Příklady jsou soubor vstupně-výstupních operací, připojení a paměti.
- Čekání: Počkejte, než události seskupené podle hostitele nebo uživatele.

Nyní Podíváme se na některé běžné způsobu použití sys_schema. Na začátku vzorce používání budete skupiny do dvou kategorií: **optimalizace výkonu** a **databáze údržby**.

## <a name="performance-tuning"></a>Ladění výkonu

### <a name="sysusersummarybyfileio"></a>*sys.user_summary_by_file_io*

Vstupně-výstupní operace je nejvíce náročná operace v databázi. Průměrná latence vstupně-výstupní operace jsme můžete zjistit pomocí dotazu *sys.user_summary_by_file_io* zobrazení. S výchozím 125 GB zřízené úložiště, my vstupně-výstupní operace latence je asi 15 sekund.

![vstupně-výstupní operace latence: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Protože Azure Database pro databázi MySQL škáluje vstupně-výstupní operace s ohledem na úložiště, po zvětšení zřízené úložiště na 1 TB, snižuje latence Moje vstupně-výstupní operace do 571 ms, představující 26 zvýšení výkonu X!

![vstupně-výstupní operace latence: 1TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschematableswithfulltablescans"></a>*sys.schema_tables_with_full_table_scans*

Navzdory pečlivé plánování, může způsobit mnoho dotazů stále prohledávání celé tabulky. Další informace o typech indexů a optimalizaci je najdete v tomto článku: [postup řešení potíží s výkonem dotazu](./howto-troubleshoot-query-performance.md). Prohledávání úplnou tabulky jsou prostředky a snížit výkon databáze. Nejrychlejší způsob, jak najít tabulky s prohledávání úplnou tabulky je dotaz *sys.schema_tables_with_full_table_scans* zobrazení.

![prohledávání úplnou tabulky](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysusersummarybystatementtype"></a>*sys.user_summary_by_statement_type*

K odstraňování problémů s výkonem databáze, může být užitečné k identifikaci událostí děje uvnitř vaší databáze a pomocí *sys.user_summary_by_statement_type* zobrazení může provádět jenom podvodné.

![Souhrn podle – příkaz](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

V tomto příkladu stráví Azure Database pro databázi MySQL 53 minut, abyste vyprázdnili protokol dotazu slog 44579 časy. To je dlouhou dobu a mnoho IOs. Můžete snížit této aktivity podle zakažte protokolu pomalé dotazu nebo snížit frekvenci pomalé dotazu přihlášení portálu Azure.

## <a name="database-maintenance"></a>Údržby databáze

### <a name="sysinnodbbufferstatsbytable"></a>*sys.innodb_buffer_stats_by_table*

InnoDB fondu vyrovnávací paměti je umístěn v paměti a hlavní mezipaměti mechanismus mezi databázového systému a úložiště. Velikost fondu vyrovnávací paměti InnoDB je vázaný na úroveň výkonu a nelze je změnit, dokud různých produktů je zvolen SKU. Stejně jako u paměti v operačním systému, staré stránky odložit aby uvolnil prostor pro zobrazení nejnovějších možných dat. Chcete-li zjistit, které tabulky využívat většinu InnoDB vyrovnávací paměti fondu paměti, můžete zadat dotaz *sys.innodb_buffer_stats_by_table* zobrazení.

![Stav InnoDB vyrovnávací paměti](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

Na obrázku výše je zřejmé, že než systémové tabulky a zobrazení, každá tabulka v databázi mysqldatabase033, který hostiteli jedné z mé lokality WordPress, zabírá 1 stránka, dat v paměti nebo 16 KB.

### <a name="sysschemaunusedindexes--sysschemaredundantindexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Indexy jsou skvělý nástroje pro zvýšení výkonu pro čtení, ale jejich způsobit dodatečné náklady pro vložení a úložiště. *Sys.schema_unused_indexes* a *sys.schema_redundant_indexes* poskytují přehled o indexy nepoužívá, nebo duplicitní.

![nepoužívané indexy](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![redundantní indexy](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Závěr

Souhrnně sys_schema je skvělý nástroj pro obě výkonu ladění a údržby databáze. Ujistěte se, že využít této funkce ve službě Azure Database pro databázi MySQL. 

## <a name="next-steps"></a>Další postup
- Vyhledání partnera odpovědí na nejvíce dotčených dotazy nebo nové otázek a odpovědí, najdete [fórum MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) nebo [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
