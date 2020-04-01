---
title: Využití sys_schema – Databáze Azure pro MySQL
description: Zjistěte, jak pomocí sys_schema najít problémy s výkonem a udržovat databázi v Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/30/2020
ms.openlocfilehash: 59b8753007c3b9130c397dda30c571580cbb5326
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411094"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Použití sys_schema pro optimalizaci výkonu a údržbu databáze v Azure Database for MySQL

MySQL performance_schema, první k dispozici v MySQL 5.5, poskytuje instrumentaci pro mnoho důležitých serverových zdrojů, jako je přidělování paměti, uložené programy, zamykání metadat, atd. performance_schema však obsahuje více než 80 tabulek a získání potřebných informací často vyžaduje spojení tabulek v rámci performance_schema, stejně jako tabulky z information_schema. V návaznosti na performance_schema i information_schema poskytuje sys_schema výkonnou kolekci [uživatelsky přívětivých zobrazení](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) v databázi jen pro čtení a je plně povolená v Azure Database for MySQL verze 5.7.

![pohledy na sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

V sys_schema je 52 zobrazení a každé zobrazení má jednu z následujících předponek:

- Host_summary nebo VO: Vstupně-výstupní chodsouvisející chaná.
- InnoDB: Stav vyrovnávací paměti InnoDB a zámky.
- Paměť: Využití paměti hostitelem a uživateli.
- Schéma: Informace související se schématem, jako je automatický přírůstek, indexy atd.
- Prohlášení: Informace o příkazech SQL; může se jedná o příkaz, který vedl k úplnému prohledání tabulky nebo dlouhé době dotazu.
- Uživatel: Prostředky spotřebované a seskupené podle uživatelů. Příkladem jsou vstupně-styky se soubory, připojení a paměť.
- Počkejte: Počkejte události seskupené podle hostitele nebo uživatele.

Nyní se podívejme na některé běžné vzorce používání sys_schema. Nejprve seskupili vzorce využití do dvou kategorií: **Optimalizace výkonu** a **Údržba databáze**.

## <a name="performance-tuning"></a>Ladění výkonu

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

IO je nejdražší operace v databázi. Průměrnou latenci vi. můžeme zjistit dotazem na zobrazení *sys.user_summary_by_file_io.* S výchozí 125 GB zřízeného úložiště je latence vi mých vipřibližně 15 sekund.

![latence io: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Protože Azure Database for MySQL škáluje vstupně-výstupních služeb s ohledem na úložiště, po zvýšení mého zřízeného úložiště na 1 TB se latence vstupně-výstupních služeb sníží na 571 ms.

![latence io: 1 TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

I přes pečlivé plánování může mnoho dotazů stále vést k úplnému prohledávaní tabulky. Další informace o typech indexů a jejich optimalizaci naleznete v tomto článku: [Řešení potíží s výkonem dotazu](./howto-troubleshoot-query-performance.md). Úplné prohledává tabulky jsou náročné na prostředky a snížit výkon databáze. Nejrychlejší způsob, jak najít tabulky s úplnou prohledávací tabulka, je dotaz na zobrazení *sys.schema_tables_with_full_table_scans.*

![úplné prohledávací skeny stolů](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Chcete-li vyřešit problémy s výkonem databáze, může být užitečné identifikovat události, ke nimž dochází uvnitř databáze, a pomocí zobrazení *sys.user_summary_by_statement_type* může stačit.

![souhrn podle výpisu](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

V tomto příkladu Azure Database for MySQL strávil 53 minut vyprázdnění protokolu dotazu slog 44579 krát. To je dlouhá doba a mnoho iOs. Tuto aktivitu můžete snížit buď zakázat váš protokol pomalý dotaz nebo snížit četnost pomalé přihlášení dotazu portálu Azure.

## <a name="database-maintenance"></a>Údržba databáze

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

[!IMPORTANT]
> Dotazování na toto zobrazení může mít vliv na výkon. Doporučujeme provést toto řešení potíží mimo špičku pracovní doby.

Fond vyrovnávací paměti InnoDB je umístěn v paměti a je hlavní mechanismus mezipaměti mezi DBMS a úložištěm. Velikost fondu vyrovnávací paměti InnoDB je vázána na úroveň výkonu a nelze ji změnit, pokud není vybrána jiná skladová položka produktu. Stejně jako u paměti v operačním systému jsou staré stránky vyměněny, aby se vytvořil prostor pro čerstvější data. Chcete-li zjistit, které tabulky spotřebovávají většinu paměti fondu vyrovnávací paměti InnoDB, můžete dotaz ovat zobrazení *sys.innodb_buffer_stats_by_table.*

![Stav vyrovnávací paměti InnoDB](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

Na obrázku výše, je zřejmé, že jiné než systémové tabulky a zobrazení, každá tabulka v databázi mysqldatabase033, který hostí jeden z mých stránek WordPress, zabírá 16 KB, nebo 1 stránka, dat v paměti.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Indexy jsou skvělé nástroje pro zlepšení výkonu čtení, ale vznikají jim další náklady na vložení a úložiště. *Sys.schema_unused_indexes* a *sys.schema_redundant_indexes* poskytují přehled o nevyužitých nebo duplicitních indexech.

![nevyužité indexy](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![redundantní indexy](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Závěr

Stručně řečeno, sys_schema je skvělý nástroj pro ladění výkonu a údržbu databáze. Využijte tuto funkci v azure databázi pro MySQL. 

## <a name="next-steps"></a>Další kroky
- Chcete-li najít odpovědi na odpovědi na vaše nejzvětšenější otázky nebo zveřejnit novou otázku / odpověď, navštivte [fórum MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) nebo [Přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-database-mysql).
