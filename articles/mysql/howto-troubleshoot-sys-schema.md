---
title: Použití sys_schema k ladění výkonu a údržbě Azure Database for MySQL
description: Naučte se používat sys_schema k nalezení potíží s výkonem a údržbě databáze v Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: 7dc6b4744c74c56803127f63a8a6f29ca5a15090
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972796"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Jak používat sys_schema k vyladění výkonu a údržbě databáze v Azure Database for MySQL

Performance_schema MySQL, který je poprvé dostupný v MySQL 5,5, poskytuje instrumentaci pro spoustu důležitých prostředků serveru, jako je přidělování paměti, uložené programy, uzamykání metadat atd. Performance_schema ale obsahuje více než 80 tabulek a získávání potřebných informací často vyžaduje spojení tabulek v rámci performance_schema a také tabulek z INFORMATION_SCHEMA. Sys_schema poskytuje výkonnou kolekci uživatelsky [přívětivých zobrazení](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) v databázi jen pro čtení a je plně zapnutá Azure Database for MySQL verze 5,7, která je založená na obou performance_schema i INFORMATION_SCHEMA.

![zobrazení sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

V sys_schema se nachází 52 zobrazení a každé zobrazení má jednu z následujících předpon:

- Host_summary nebo IO: latence v/v související s/O.
- InnoDB: InnoDB stav vyrovnávací paměti a zámky.
- Paměť: využití paměti pro hostitele a uživatele.
- Schéma: informace týkající se schématu, jako je například Automatický přírůstek, indexy atd.
- Příkaz: informace o příkazech SQL; může se jednat o příkaz, který vyplynule úplnou kontrolu tabulky nebo dlouhou dobu dotazování.
- Uživatel: prostředky spotřebované a seskupené podle uživatelů. Příkladem jsou vstupně-výstupy souborů, připojení a paměť.
- Čekání: Počkejte události seskupené podle hostitele nebo uživatele.

Teď se podíváme na některé běžné vzorce používání sys_schema. Aby bylo možné začít používat, seskupte vzory využití do dvou kategorií: **optimalizace výkonu** a **Údržba databáze**.

## <a name="performance-tuning"></a>Ladění výkonu

### <a name="sysuser_summary_by_file_io"></a>*sys. user_summary_by_file_io*

V/v databáze je nejdražší operace v databázi. Průměrná latence v/v se dá zjistit dotazem na zobrazení *Sys. user_summary_by_file_io* . S výchozí 125 GB zřízeného úložiště je moje latence v/v asi 15 sekund.

![vstupně-výstupní latence: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Vzhledem k tomu, že Azure Database for MySQL škáluje vstupně-výstupní operace s ohledem na úložiště, po zvýšení zřízeného úložiště na 1 TB se latence v/v snižuje na 571 MS.

![vstupně-výstupní latence: 1 TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys. schema_tables_with_full_table_scans*

Bez ohledu na pečlivé plánování může mnoho dotazů pořád vést k kompletním kontrolám tabulky. Další informace o typech indexů a o tom, jak je optimalizovat, najdete v tomto článku: [jak řešit potíže s výkonem dotazů](./howto-troubleshoot-query-performance.md). Úplné prohledávání tabulek je náročné na prostředky a snižuje výkon databáze. Nejrychlejší způsob, jak najít tabulky pomocí úplného prohledávání tabulky, je dotazování zobrazení *Sys. schema_tables_with_full_table_scans* .

![úplné prohledávání tabulek](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys. user_summary_by_statement_type*

Pokud chcete řešit problémy s výkonem databáze, může být užitečné identifikovat události, které se provedou v databázi, a použít zobrazení *Sys. user_summary_by_statement_type* .

![Summary podle – příkaz](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

V tomto příkladu Azure Database for MySQL strávila 53 minut vyprázdnit protokol dotazu slog 44579 krát. To je dlouhou dobu a mnoho IOs. Tuto aktivitu můžete zkrátit tím, že zakážete protokol pomalých dotazů nebo snížíte frekvenci pomalých přihlašovacích Azure Portal dotazů.

## <a name="database-maintenance"></a>Údržba databáze

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys. innodb_buffer_stats_by_table*

Fond vyrovnávacích pamětí InnoDB se nachází v paměti a jedná se o mechanismus hlavní mezipaměti mezi systémy DBMS a úložištěm. Velikost fondu vyrovnávací paměti InnoDB je svázána s úrovní výkonu a nelze jej změnit, pokud není zvolena jiná SKU produktu. Stejně jako u paměti v operačním systému jsou staré stránky zaměněny, aby uvolnily místo pro data z čerstvého počítače. Pokud chcete zjistit, které tabulky využívají většinu paměti fondu vyrovnávací paměti InnoDB, můžete zadat dotaz na zobrazení *Sys. innodb_buffer_stats_by_table* .

![Stav vyrovnávací paměti InnoDB](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

Na obrázku výše je zřejmé, že kromě systémových tabulek a zobrazení je každá tabulka v databázi mysqldatabase033, která hostuje jeden z mých webů WordPress, zabírá 16 KB nebo 1 stránku dat v paměti.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys. schema_unused_indexes* & *Sys. schema_redundant_indexes*

Indexy jsou skvělé nástroje pro zlepšení výkonu čtení, ale u vkládání a ukládání se účtují další poplatky. *Sys. schema_unused_indexes* a *Sys. schema_redundant_indexes* poskytují přehled o nevyužitých nebo duplicitních indexech.

![nepoužívané indexy](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![nadbytečné indexy](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Závěr

Sys_schema je skvělý nástroj pro ladění výkonu i údržbu databáze. Ujistěte se, že jste tuto funkci využili ve svém Azure Database for MySQL. 

## <a name="next-steps"></a>Další kroky
- Pokud chcete najít rovnocenné odpovědi na příslušné otázky nebo Odeslat novou otázku či odpověď, navštivte [Fórum MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) nebo [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
