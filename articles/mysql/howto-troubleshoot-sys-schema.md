---
title: Postup použití sys_schema pro optimalizaci výkonu a údržby databáze ve službě Azure Database for MySQL
description: Tento článek popisuje, jak pomocí sys_schema můžete najít problémy s výkonem a udržovat databáze ve službě Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/01/2018
ms.openlocfilehash: 993c77056c09c1dc21d5317ddbfe8e937341718d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542845"
---
# <a name="how-to-use-sysschema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Jak používat sys_schema pro výkon ladění a údržby databáze ve službě Azure Database for MySQL

MySQL performance_schema, první dostupná v MySQL 5.5, poskytuje instrumentace pro mnoho prostředků důležité server například přidělení paměti, uložené programy, metadata zamykání a tak podobně. Ale performance_schema obsahuje více než 80 tabulky a získání potřebných informací často vyžaduje spojování tabulek v rámci performance_schema, jakož i tabulek z information_schema. Staví na performance_schema a information_schema, sys_schema poskytuje výkonnou sadu [uživatelsky přívětivé zobrazení](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) v databázi jen pro čtení a je plně podporují ve službě Azure Database for MySQL 5.7 verze.

![zobrazení sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Existují 52 zobrazení v sys_schema a každé zobrazení má jednu z následující předpony:

- Host_summary nebo vstupně-výstupní operace: Vstupně-výstupní operace související s latencí.
- InnoDB: Stav InnoDB vyrovnávací paměti a zámky.
- Paměť: Využití paměti hostitele a uživatelé.
- Schéma: Schéma související informace, například automatické zvyšování čísla indexů, atd.
- příkaz: Informace o příkazech SQL; může být příkaz, který je v skenování celé tabulky nebo dlouhé době zpracování dotazu.
- Uživatel: Prostředky spotřebované a seskupených podle uživatele. Příklady souborů vstupně-výstupních operací, připojení a paměti.
- Počkej: Počkejte, než událostí seskupených podle hostitele nebo uživatele.

Nyní Pojďme se podívat na některé běžné vzory používání sys_schema. Než začneme budete seskupíme vzory používání do dvou kategorií: **Optimalizace výkonu** a **databáze údržby**.

## <a name="performance-tuning"></a>Ladění výkonu

### <a name="sysusersummarybyfileio"></a>*sys.user_summary_by_file_io*

Vstupně-výstupních operací je nejdražší operaci v databázi. Průměrná latence vstupně-výstupních operací jsme můžete zjistit pomocí dotazu *sys.user_summary_by_file_io* zobrazení. S výchozím 125 GB zřízeného úložiště, my vstupně-výstupní latence je asi 15 sekund.

![vstupně-výstupní latence: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Protože – Azure Database for MySQL škáluje vstupně-výstupní operace s ohledem na úložiště, zvětšete zřízeného úložiště na 1 TB, zmenší se Moje vstupně-výstupní latence 571 ms.

![vstupně-výstupní latence: 1TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschematableswithfulltablescans"></a>*sys.schema_tables_with_full_table_scans*

Bez ohledu na pečlivé plánování, může způsobit mnoho dotazů stále prohledávání celé tabulky. Další informace o typech indexů a optimalizaci jejich najdete v tomto článku: [Řešení potíží s výkonem dotazů](./howto-troubleshoot-query-performance.md). Prohledávání celé tabulky jsou náročná a snížit výkon vaší databáze. Je nejrychlejší způsob, jak najít tabulky s skenování celé tabulky dotaz *sys.schema_tables_with_full_table_scans* zobrazení.

![prohledávání celé tabulky](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysusersummarybystatementtype"></a>*sys.user_summary_by_statement_type*

Řešení problémů s výkonem databáze, může být výhodné k identifikaci událostí děje ve vaše databáze a pomocí *sys.user_summary_by_statement_type* zobrazení může právě provádět zdvih.

![Souhrn podle – příkaz](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

V tomto příkladu – Azure Database for MySQL – čas strávený 53 minut Vyčištění protokolu dotazu slog 44579 časy. To je dlouhou dobu a mnoho IOs. Můžete snížit tato aktivita buď zakázat protokol pomalých dotazů nebo snížit frekvenci pomalých dotazů přihlášení webu Azure portal.

## <a name="database-maintenance"></a>Databáze údržby

### <a name="sysinnodbbufferstatsbytable"></a>*sys.innodb_buffer_stats_by_table*

Fond vyrovnávacích pamětí InnoDB spočívá v paměti a mechanismus hlavní mezipaměti mezi systémy a úložiště. Velikost fondu vyrovnávacích pamětí InnoDB se váže na úroveň výkonu a nedá se změnit, pokud jiný produkt SKU je vybrán. Stejně jako u paměti v operačním systému, staré stránky výměnu aby uvolnil prostor pro zobrazení nejnovějších možných dat. Chcete-li zjistit, které tabulky využívat většinu InnoDB vyrovnávací paměti fondu paměti, můžete zadat dotaz *sys.innodb_buffer_stats_by_table* zobrazení.

![Stav InnoDB vyrovnávací paměti](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

Na obrázku výše je zřejmé, že jiné než systémové tabulky a zobrazení, každá tabulka v databázi mysqldatabase033, které hostitelem některé z mé lokality WordPress, zabírá 16 KB nebo 1 stránce dat v paměti.

### <a name="sysschemaunusedindexes--sysschemaredundantindexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Indexy jsou skvělé nástroje ke zlepšení výkonu při čtení, ale způsobují další náklady na operace vložení a úložiště. *Sys.schema_unused_indexes* a *sys.schema_redundant_indexes* poskytují přehled o nepoužívané nebo duplicitní indexy.

![nepoužité indexů](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![redundantní indexů](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Závěr

Stručně řečeno sys_schema je skvělým nástrojem k i výkon ladění a údržby databáze. Ujistěte se, že chcete využít výhod této funkce ve službě Azure Database for MySQL. 

## <a name="next-steps"></a>Další postup
- Najít partnera odpovědi na své otázky oblasti vašeho zájmu nebo odeslat nové otázek a odpovědí, najdete v tématu [fórum na webu MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) nebo [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
