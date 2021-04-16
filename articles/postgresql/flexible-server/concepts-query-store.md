---
title: Úložiště dotazů – Azure Database for PostgreSQL – Server Flex
description: Tento článek popisuje funkci úložiště dotazů v Azure Database for PostgreSQL-Flex serveru.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 6ab2ea6f6544bcf561e92f00b5afee693393c157
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558980"
---
# <a name="monitor-performance-with-query-store"></a>Monitorování výkonu pomocí úložiště dotazů
**Platí pro:** Azure Database for PostgreSQL – Server Flex verze 11 a vyšší

Funkce úložiště dotazů v Azure Database for PostgreSQL poskytuje způsob, jak sledovat výkon dotazů v průběhu času. Úložiště dotazů zjednodušuje výkon – řešení potíží tím, že vám pomůže rychle najít nejdéle běžící a nejvíce dotazů náročných na prostředky. Úložiště dotazů automaticky zachycuje historii dotazů a statistik za běhu a zachovává je pro vaši kontrolu. Seřadí data podle času, takže můžete vidět dočasné vzorce používání. Data pro všechny uživatele, databáze a dotazy jsou uložena v databázi s názvem **azure_sys** v instanci Azure Database for PostgreSQL.

> [!IMPORTANT]
> Neupravujte databázi **azure_sys** ani její schéma. Tím se zabrání správnému fungování úložiště dotazů a souvisejících funkcí výkonu.
## <a name="enabling-query-store"></a>Povoluje se úložiště dotazů.
Úložiště dotazů je funkce výslovného souhlasu, takže není ve výchozím nastavení na serveru povolená. Úložiště dotazů je povolené nebo zakázané globálně pro všechny databáze na daném serveru a nedá se zapnout nebo vypnout pro každou databázi.
### <a name="enable-query-store-using-the-azure-portal"></a>Povolit úložiště dotazů pomocí Azure Portal
1. Přihlaste se k Azure Portal a vyberte Azure Database for PostgreSQL Server.
2. V části **Nastavení** v nabídce vyberte **parametry serveru** .
3. Vyhledejte `pg_qs.query_capture_mode` parametr.
4. Nastavte hodnotu na `TOP` nebo `ALL` a **uložte**.
Umožňuje až 20 minut první dávky dat pro uchování v databázi azure_sys.
## <a name="information-in-query-store"></a>Informace v úložišti dotazů
Úložiště dotazů má jedno úložiště:
- Úložiště statistik modulu runtime pro uchování informací o statistice provádění dotazů.

Mezi běžné scénáře použití úložiště dotazů patří:
- Určení počtu spuštění dotazu v daném časovém intervalu
- Porovnání průměrné doby provádění dotazu napříč časovými okny pro zobrazení velkých rozdílů
- Určení nejdelších spuštěných dotazů za posledních několik hodin pro minimalizaci využití místa, Statistika spuštění modulu runtime v úložišti statistik modulu runtime jsou agregovány v pevně nastaveném časovém intervalu. Informace v těchto úložištích lze dotazovat pomocí zobrazení.
## <a name="access-query-store-information"></a>Přístup k informacím o úložišti dotazů
Data úložiště dotazů se ukládají do databáze azure_sys na serveru Postgres. Následující dotaz vrátí informace o dotazech v úložišti dotazů:
```sql
SELECT * FROM query_store.qs_view; 
``` 

## <a name="configuration-options"></a>Možnosti konfigurace
Když je povoleno úložiště dotazů, ukládá data v oknech agregace 15 minut, až 500 různých dotazů na každé okno. Pro konfiguraci parametrů úložiště dotazů jsou k dispozici následující možnosti.

| **Parametr** | **Popis** | **Výchozí** | **Rozsah**|
|---|---|---|---|
| pg_qs pg_qs.query_capture_mode | Nastaví, které příkazy jsou sledovány. | žádné | žádné, nahoře, vše |
| pg_qs pg_qs.max_query_text_length | Nastaví maximální délku dotazu, kterou lze uložit. Delší dotazy budou zkráceny. | 6000 | 100 – 10 000 |
| pg_qs pg_qs.retention_period_in_days | Nastaví dobu uchování. | 7 | 1 - 30 |
| pg_qs pg_qs.track_utility | Nastaví, jestli se mají sledovat příkazy nástrojů. | on | zapnuto, vypnuto |

Použijte [Azure Portal](howto-configure-server-parameters-using-portal.md) k získání nebo nastavení jiné hodnoty pro parametr.

## <a name="views-and-functions"></a>Zobrazení a funkce
Umožňuje zobrazit a spravovat úložiště dotazů pomocí následujících zobrazení a funkcí. Kdokoli v PostgreSQL veřejné roli může pomocí těchto zobrazení zobrazit data v úložišti dotazů. Tato zobrazení jsou k dispozici pouze v databázi **azure_sys** .
Dotazy jsou normalizovány tím, že si po odebrání literálů a konstant vyhledají jejich strukturu. Pokud jsou dva dotazy stejné s výjimkou hodnot literálů, budou mít stejný queryId.
### <a name="query_storeqs_view"></a>query_store query_store.qs_view
Toto zobrazení vrátí všechna data v úložišti dotazů. Pro každé jedinečné ID databáze, ID uživatele a ID dotazu je k dispozici jeden řádek. 

|**Název**   |**Typ** | **Reference**  | **Popis**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID z runtime_stats_entries tabulky|
|user_id    |identifikátor    |pg_authid. OID  |Identifikátor OID uživatele, který příkaz provedl|
|db_id  |identifikátor    |pg_database. OID    |Identifikátor objektu databáze, ve kterém byl příkaz proveden|
|query_id   |bigint  || Vnitřní kód hash vypočítaný z stromu analýzy příkazu|
|query_sql_text |Varchar (10000)  || Text zástupce příkazu Různé dotazy se stejnou strukturou jsou clusterované dohromady; Tento text je text pro první z dotazů v clusteru.|
|plan_id    |bigint |   |ID plánu, který odpovídá tomuto dotazu, zatím není k dispozici|
|start_time |časové razítko  ||  Dotazy jsou agregované podle časových intervalů – ve výchozím nastavení je časový rozsah intervalu 15 minut. Toto je čas spuštění odpovídající časovému intervalu pro tuto položku.|
|end_time   |časové razítko  ||  Čas ukončení odpovídající časovému intervalu pro tuto položku.|
|volání  |bigint  || Počet provedení dotazu|
|total_time |Dvojitá přesnost   ||  Celková doba provádění dotazu v milisekundách|
|min_time   |Dvojitá přesnost   ||  Minimální doba provádění dotazu v milisekundách|
|max_time   |Dvojitá přesnost   ||  Maximální doba provádění dotazu v milisekundách|
|mean_time  |Dvojitá přesnost   ||  Průměrná doba provádění dotazu (v milisekundách)|
|stddev_time|   Dvojitá přesnost    ||  Směrodatná odchylka doby provádění dotazu (v milisekundách) |
|tabulky   |bigint ||  Celkový počet řádků načtených nebo ovlivněných příkazem|
|shared_blks_hit|   bigint  ||  Celkový počet přístupů do mezipaměti sdílených bloků pomocí příkazu|
|shared_blks_read|  bigint  ||  Celkový počet sdílených bloků přečtených příkazem|
|shared_blks_dirtied|   bigint   || Celkový počet sdílených bloků změněných příkazem |
|shared_blks_written|   bigint  ||  Celkový počet sdílených bloků zapsaných příkazem|
|local_blks_hit|    bigint ||   Celkový počet přístupů do místní blokové mezipaměti příkazem|
|local_blks_read|   bigint   || Celkový počet místních bloků čtených příkazem|
|local_blks_dirtied|    bigint  ||  Celkový počet místních bloků změněných příkazem|
|local_blks_written|    bigint  ||  Celkový počet místních bloků zapsaných příkazem|
|temp_blks_read |bigint  || Celkový počet dočasných bloků čtených příkazem|
|temp_blks_written| bigint   || Celkový počet dočasných bloků zapsaných příkazem|
|blk_read_time  |Dvojitá přesnost    || Celková doba, po kterou příkaz strávil bloky čtení, v milisekundách (Pokud je povolená track_io_timing, jinak nula)|
|blk_write_time |Dvojitá přesnost    || Celková doba, po kterou příkaz strávil zápis bloků, v milisekundách (Pokud je povolená track_io_timing, jinak nula)|
    
### <a name="query_storequery_texts_view"></a>query_store query_store.query_texts_view
Toto zobrazení vrátí textová data dotazu v úložišti dotazů. Pro každý query_text je k dispozici jeden řádek.

| **Název** | **Typ** | **Popis** |
|--|--|--|
| query_text_id | bigint | ID pro query_textsovou tabulku |
| query_sql_text | Varchar (10000) | Text zástupce příkazu Různé dotazy se stejnou strukturou jsou clusterované dohromady; Tento text je text pro první z dotazů v clusteru. |

### <a name="functions"></a>Functions
`qs_reset` zahodí všechny statistiky shromážděné zatím v úložišti dotazů. Tuto funkci může spustit jenom role správce serveru.

`staging_data_reset` zahodí všechny statistiky shromážděné v paměti úložištěm dotazů (tj. data v paměti, která ještě nebyla vyprázdněna do databáze). Tuto funkci může spustit jenom role správce serveru.

## <a name="limitations-and-known-issues"></a>Omezení a známé problémy
- Pokud má server PostgreSQL parametr default_transaction_read_only zapnutý, úložiště dotazů nebude zachytávat žádná data.
## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [scénářích, ve kterých může být úložiště dotazů obzvlášť užitečné](concepts-query-store-scenarios.md).
- Přečtěte si další informace o [osvědčených postupech pro používání úložiště dotazů](concepts-query-store-best-practices.md).
