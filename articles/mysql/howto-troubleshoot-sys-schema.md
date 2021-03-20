---
title: Využití sys_schema-Azure Database for MySQL
description: Naučte se používat sys_schema k nalezení potíží s výkonem a údržbě databáze v Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/30/2020
ms.openlocfilehash: a20510ee2800a54f9a51a2f498ee8ae8a3e51d55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94543145"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Použití sys_schema k vyladění výkonu a údržbě databáze v Azure Database for MySQL

Performance_schema MySQL, která je v MySQL 5,5 poprvé dostupná, poskytuje instrumentaci pro spoustu důležitých prostředků serveru, jako je přidělování paměti, uložené programy, uzamykání metadat atd. Performance_schema však obsahuje více než 80 tabulek a získávání potřebných informací často vyžaduje spojení tabulek v rámci performance_schema a také tabulek z information_schema. Při sestavování na performance_schema i information_schema poskytuje sys_schema výkonnou kolekci uživatelsky [přívětivých zobrazení](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) v databázi jen pro čtení a je plně zapnutá v Azure Database for MySQL verze 5,7.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/sys-schema-views.png" alt-text="zobrazení sys_schema":::

V sys_schema se nachází 52 zobrazení a každé zobrazení má jednu z následujících předpon:

- Host_summary nebo IO: latence v/v související s/O.
- InnoDB: InnoDB stav vyrovnávací paměti a zámky.
- Paměť: využití paměti pro hostitele a uživatele.
- Schéma: informace týkající se schématu, jako je například Automatický přírůstek, indexy atd.
- Příkaz: informace o příkazech SQL; může se jednat o příkaz, který vyplynule úplnou kontrolu tabulky nebo dlouhou dobu dotazování.
- Uživatel: prostředky spotřebované a seskupené podle uživatelů. Příkladem jsou vstupně-výstupy souborů, připojení a paměť.
- Čekání: Počkejte události seskupené podle hostitele nebo uživatele.

Teď se podíváme na některé běžné způsoby použití sys_schema. Aby bylo možné začít používat, seskupte vzory využití do dvou kategorií: **optimalizace výkonu** a **Údržba databáze**.

## <a name="performance-tuning"></a>Ladění výkonu

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

V/v databáze je nejdražší operace v databázi. Průměrná latence v/v se dá zjistit dotazem na zobrazení *Sys.user_summary_by_file_io* . S výchozí 125 GB zřízeného úložiště je moje latence v/v asi 15 sekund.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-125GB.png" alt-text="vstupně-výstupní latence: 125 GB":::

Vzhledem k tomu, že Azure Database for MySQL škáluje vstupně-výstupní operace s ohledem na úložiště, po zvýšení zřízeného úložiště na 1 TB se latence v/v snižuje na 571 MS.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-1TB.png" alt-text="vstupně-výstupní latence: 1 TB":::

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

Bez ohledu na pečlivé plánování může mnoho dotazů pořád vést k kompletním kontrolám tabulky. Další informace o typech indexů a o tom, jak je optimalizovat, najdete v tomto článku: [jak řešit potíže s výkonem dotazů](./howto-troubleshoot-query-performance.md). Úplné prohledávání tabulek je náročné na prostředky a snižuje výkon databáze. Nejrychlejší způsob, jak najít tabulky pomocí úplného prohledávání tabulky, je dotazování zobrazení *Sys.schema_tables_with_full_table_scans* .

:::image type="content" source="./media/howto-troubleshoot-sys-schema/full-table-scans.png" alt-text="úplné prohledávání tabulek":::

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Aby bylo možné řešit problémy s výkonem databáze, může být užitečné identifikovat události, které se provedou v rámci vaší databáze, a použít zobrazení *Sys.user_summary_by_statement_type* může jednoduše dělat štych.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/summary-by-statement.png" alt-text="Summary podle – příkaz":::

V tomto příkladu Azure Database for MySQL strávila 53 minut vyprázdnit protokol dotazu slog 44579 krát. To je dlouhou dobu a mnoho IOs. Tuto aktivitu můžete zkrátit tím, že zakážete protokol pomalých dotazů nebo snížíte frekvenci pomalých přihlašovacích Azure Portal dotazů.

## <a name="database-maintenance"></a>Údržba databáze

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

[!IMPORTANT]
> Dotaz na toto zobrazení může mít vliv na výkon. Tento problém se doporučuje provést v době mimo špičku v pracovní době.

Fond vyrovnávacích pamětí InnoDB se nachází v paměti a jedná se o mechanismus hlavní mezipaměti mezi systémy DBMS a úložištěm. Velikost fondu vyrovnávací paměti InnoDB je svázána s úrovní výkonu a nelze jej změnit, pokud není zvolena jiná SKU produktu. Stejně jako u paměti v operačním systému jsou staré stránky zaměněny, aby uvolnily místo pro data z čerstvého počítače. Pokud chcete zjistit, které tabulky využívají většinu paměti fondu vyrovnávací paměti InnoDB, můžete zadat dotaz na zobrazení *Sys.innodb_buffer_stats_by_table* .

:::image type="content" source="./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png" alt-text="Stav vyrovnávací paměti InnoDB":::

Na obrázku výše je zřejmé, že kromě systémových tabulek a zobrazení je každá tabulka v databázi mysqldatabase033, která hostuje jeden z mých webů WordPress, zabírá 16 KB nebo 1 stránku dat v paměti.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *Sys.schema_redundant_indexes*

Indexy jsou skvělé nástroje pro zlepšení výkonu čtení, ale u vkládání a ukládání se účtují další poplatky. *Sys.schema_unused_indexes* a *Sys.schema_redundant_indexes* poskytují přehled o nevyužitých nebo duplicitních indexech.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/unused-indexes.png" alt-text="nepoužívané indexy":::

:::image type="content" source="./media/howto-troubleshoot-sys-schema/redundant-indexes.png" alt-text="nadbytečné indexy":::

## <a name="conclusion"></a>Závěr

V souhrnu je sys_schema skvělým nástrojem pro ladění výkonu i údržbu databáze. Ujistěte se, že jste tuto funkci využili ve svém Azure Database for MySQL. 

## <a name="next-steps"></a>Další kroky
- Pokud chcete najít rovnocenné odpovědi na příslušné otázky nebo Odeslat novou otázku či odpověď, přejděte na [stránku Microsoft Q&na stránku s otázkou](/answers/topics/azure-database-mysql.html) nebo [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).