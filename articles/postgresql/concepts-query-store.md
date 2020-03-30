---
title: Úložiště dotazů – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje funkci Úložiště dotazů v Azure Database for PostgreSQL – Jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: ccc503e6718ee8f516920cfbea3ad86e7ed81d84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768261"
---
# <a name="monitor-performance-with-the-query-store"></a>Sledování výkonu pomocí úložiště dotazů

**Platí pro:** Databáze Azure pro PostgreSQL – verze s jedním serverem 9.6, 10, 11

Funkce Úložiště dotazů v Azure Database for PostgreSQL poskytuje způsob, jak sledovat výkon dotazů v čase. Úložiště dotazů zjednodušuje řešení potíží s výkonem tím, že vám pomůže rychle najít nejdéle spuštěné a nejvíce náročné dotazy. Úložiště dotazů automaticky zachytí historii dotazů a statistik za běhu a zachová je pro vaši kontrolu. Odděluje data podle časových oken, takže můžete zobrazit vzory využití databáze. Data pro všechny uživatele, databáze a dotazy se ukládají v databázi s názvem **azure_sys** v instanci Azure Database for PostgreSQL.

> [!IMPORTANT]
> Neupravujte **azure_sys** databázi ani její schémata. Tím zabráníte správnému fungování úložiště dotazů a souvisejících funkcí výkonu.

## <a name="enabling-query-store"></a>Povolení úložiště dotazů
Úložiště dotazů je funkce pro přihlášení, takže není ve výchozím nastavení na serveru aktivní. Úložiště je povoleno nebo zakázáno globálně pro všechny databáze na daném serveru a nelze zapnout nebo vypnout pro každou databázi.

### <a name="enable-query-store-using-the-azure-portal"></a>Povolení úložiště dotazů pomocí portálu Azure
1. Přihlaste se na portál Azure a vyberte azure databázi pro postgreSQL server.
2. V nabídce **nastavení** vyberte **Parametry serveru.**
3. Vyhledejte `pg_qs.query_capture_mode` parametr.
4. Nastavte hodnotu `TOP` na **hodnotu**a Uložit .

Povolení statistiky čekání v úložišti dotazů: 
1. Vyhledejte `pgms_wait_sampling.query_capture_mode` parametr.
1. Nastavte hodnotu `ALL` na **hodnotu**a Uložit .


Případně můžete nastavit tyto parametry pomocí rozhraní příkazového řádku Azure.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Umožňuje až 20 minut první dávky dat pro uchování v databázi azure_sys.

## <a name="information-in-query-store"></a>Informace v úložišti dotazů
Úložiště dotazů má dva obchody:
- Úložiště statistik za běhu pro uchování informací statistiky spuštění dotazu.
- Čekání statistiky úložiště pro trvalé čekání statistiky informací.

Mezi běžné scénáře použití úložiště dotazů patří:
- Určení počtu spuštění dotazu v daném časovém okně
- Porovnání průměrné doby provádění dotazu v časových oknech za účelem zobrazení velkých rozdílových hodnot
- Identifikace nejdelších spuštěných dotazů za posledních X hodin
- Identifikace nejvyšších n dotazů, které čekají na prostředky
- Principy povahy čekání pro konkrétní dotaz

Aby se minimalizovalo využití místa, statistiky spuštění za běhu v úložišti statistik běhu jsou agregovány přes pevné, konfigurovatelné časové okno. Informace v těchto úložištích jsou viditelné dotazováním na zobrazení úložiště dotazů.

## <a name="access-query-store-information"></a>Informace o úložišti dotazů aplikace Access Query Store

Data úložiště dotazů jsou uložena v databázi azure_sys na serveru Postgres. 

Následující dotaz vrací informace o dotazech v úložišti dotazů:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Nebo tento dotaz pro čekání statistiky:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

Můžete také vyzařovat data úložiště dotazů do [protokolů monitorování Azure](../azure-monitor/log-query/log-query-overview.md) pro analýzy a výstrahy, centra událostí pro streamování a Azure Storage pro archivaci. Kategorie protokolu ke konfiguraci jsou **QueryStoreRuntimeStatistics** a **QueryStoreWaitStatistics**. Další informace o nastavení najdete v článku [nastavení azure monitoru.](../azure-monitor/platform/diagnostic-settings.md)


## <a name="finding-wait-queries"></a>Hledání dotazů na čekání
Wait typy událostí kombinovat různé události čekání do bloků podle podobnosti. Úložiště dotazů poskytuje typ události čekání, konkrétní název události čekání a dotaz. Možnost korelovat tyto informace čekání se statistikou za běhu dotazu znamená, že můžete získat hlubší pochopení toho, co přispívá k výkonu charakteristikdotazu.

Tady je několik příkladů, jak můžete získat další přehled o vaší pracovní zátěži pomocí statistiky čekání v úložišti dotazů:

| **Pozorování** | **Akce** |
|---|---|
|High Lock čeká | Zkontrolujte texty dotazů pro ovlivněné dotazy a identifikujte cílové entity. Vyhledejte v úložišti dotazů další dotazy, které upravují stejnou entitu, která se provádí často a/nebo má vysokou dobu trvání. Po identifikaci těchto dotazů zvažte změnu aplikační logiky ke zlepšení souběžnosti nebo použijte méně omezující úroveň izolace.|
| Vysoká vyrovnávací paměť IO čeká | Najděte dotazy s vysokým počtem fyzických čtení v úložišti dotazů. Pokud odpovídají dotazy s vysoké vi čeká, zvažte zavedení indexu na podkladové entity, aby se snaží místo prohledává. To by minimalizovalo vsazení vi režie dotazů. Zkontrolujte **doporučení výkonu** pro váš server na portálu a zjistěte, zda existují doporučení indexu pro tento server, který by optimalizoval dotazy.|
| Vysoká paměť čeká | Najděte nejvyšší paměti náročné dotazy v úložišti dotazů. Tyto dotazy pravděpodobně zpožďují další průběh ovlivněných dotazů. Zkontrolujte **doporučení výkonu** pro váš server na portálu a zjistěte, zda existují doporučení indexu, které by tyto dotazy optimalizovaly.|

## <a name="configuration-options"></a>Možnosti konfigurace
Pokud je povoleno úložiště dotazů, ukládá data v 15minutových oknech agregace, až 500 různých dotazů na okno. 

Pro konfiguraci parametrů úložiště dotazů jsou k dispozici následující možnosti.

| **Parametr** | **Popis** | **Výchozí** | **Rozsah**|
|---|---|---|---|
| pg_qs.query_capture_mode | Nastaví, které příkazy jsou sledovány. | Žádná | žádné, nahoru, všechny |
| pg_qs.max_query_text_length | Nastaví maximální délku dotazu, kterou lze uložit. Delší dotazy budou zkráceny. | 6000 | 100 - 10K |
| pg_qs.retention_period_in_days | Nastaví dobu uchování. | 7 | 1 - 30 |
| pg_qs.track_utility | Nastaví, zda jsou sledovány příkazy nástroje | on | zapnuto, vypnuto |

Následující možnosti platí konkrétně čekat statistiky.

| **Parametr** | **Popis** | **Výchozí** | **Rozsah**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Nastaví, které příkazy jsou sledovány pro čekání statistiky. | Žádná | žádné, všechny|
| Pgms_wait_sampling.history_period | Nastavte frekvenci v milisekundách, na které jsou vzorkovány události čekání. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** nahrazuje **pgms_wait_sampling.query_capture_mode**. Pokud je pg_qs.query_capture_mode ŽÁDNÝ, nemá nastavení pgms_wait_sampling.query_capture_mode žádný vliv.


Pomocí [portálu Azure nebo](howto-configure-server-parameters-using-portal.md) [rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md) získáte nebo nastavíte jinou hodnotu parametru.

## <a name="views-and-functions"></a>Pohledy a funkce
Zobrazení a správa úložiště dotazů pomocí následujících zobrazení a funkcí. Kdokoli ve veřejné roli PostgreSQL může tato zobrazení použít k zobrazení dat v úložišti dotazů. Tato zobrazení jsou k dispozici pouze v **databázi azure_sys.**

Dotazy jsou normalizovány při pohledu na jejich strukturu po odebrání literály a konstanty. Pokud dva dotazy jsou identické s výjimkou literál hodnoty, budou mít stejnou hodnotu hash.

### <a name="query_storeqs_view"></a>query_store.qs_view
Toto zobrazení vrátí všechna data v úložišti dotazů. Pro každé odlišné ID databáze, ID uživatele a ID dotazu existuje jeden řádek. 

|**Název**   |**Typ** | **Odkazy**  | **Popis**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID z tabulky runtime_stats_entries|
|user_id    |Oid    |pg_authid.oid  |OID uživatele, který provedl příkaz|
|Db_id  |Oid    |pg_database.oid    |OID databáze, ve které byl příkaz proveden|
|query_id   |bigint  || Interní kód hash vypočítaný ze stromu analýzy příkazu|
|query_sql_text |Varchar (10000)  || Text reprezentativního prohlášení. Různé dotazy se stejnou strukturou jsou seskupeny dohromady; tento text je text pro první z dotazů v clusteru.|
|plan_id    |bigint |   |ID plánu odpovídajícího tomuto dotazu, zatím není k dispozici|
|start_time |časové razítko  ||  Dotazy jsou agregovány podle časových bloků – časové rozpětí bloku je ve výchozím nastavení 15 minut. Toto je čas zahájení odpovídající časovému intervalu pro tuto položku.|
|end_time   |časové razítko  ||  Čas ukončení odpovídající časovému intervalu pro tuto položku.|
|Volání  |bigint  || Počet spuštění dotazu|
|total_time |dvojitá přesnost   ||  Celkový čas spuštění dotazu v milisekundách|
|min_time   |dvojitá přesnost   ||  Minimální doba spuštění dotazu v milisekundách|
|max_time   |dvojitá přesnost   ||  Maximální doba provádění dotazu v milisekundách|
|mean_time  |dvojitá přesnost   ||  Průměrná doba spuštění dotazu v milisekundách|
|stddev_time|   dvojitá přesnost    ||  Směrodatná odchylka doby spuštění dotazu v milisekundách |
|Řádky   |bigint ||  Celkový počet řádků načtených nebo ovlivněných příkazem|
|shared_blks_hit|   bigint  ||  Celkový počet přístupů do mezipaměti sdílených bloků podle příkazu|
|shared_blks_read|  bigint  ||  Celkový počet sdílených bloků přečtených příkazem|
|shared_blks_dirtied|   bigint   || Celkový počet sdílených bloků znečištěných prohlášením |
|shared_blks_written|   bigint  ||  Celkový počet sdílených bloků zapsaných příkazem|
|local_blks_hit|    bigint ||   Celkový počet místních přístupů do mezipaměti bloku podle příkazu|
|local_blks_read|   bigint   || Celkový počet místních bloků přečtených příkazem|
|local_blks_dirtied|    bigint  ||  Celkový počet místních bloků znečištěných prohlášením|
|local_blks_written|    bigint  ||  Celkový počet místních bloků zapsaných prohlášením|
|temp_blks_read |bigint  || Celkový počet dočasných bloků přečtených příkazem|
|temp_blks_written| bigint   || Celkový počet dočasných bloků zapsaných příkazem|
|blk_read_time  |dvojitá přesnost    || Celkový čas, který příkaz strávil čtením bloků v milisekundách (pokud je povoleno track_io_timing, jinak nula)|
|blk_write_time |dvojitá přesnost    || Celkový čas, který příkaz strávil psaním bloků v milisekundách (pokud je povoleno track_io_timing, jinak nula)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Toto zobrazení vrátí textová data dotazu v úložišti dotazů. Pro každý odlišný query_text je jeden řádek.

|**Název**|  **Typ**|   **Popis**|
|---|---|---|
|query_text_id  |bigint     |ID tabulky query_texts|
|query_sql_text |Varchar (10000)     |Text reprezentativního prohlášení. Různé dotazy se stejnou strukturou jsou seskupeny dohromady; tento text je text pro první z dotazů v clusteru.|

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
Toto zobrazení vrátí data událostí čekání v úložišti dotazů. Existuje jeden řádek pro každý odlišný ID databáze, ID uživatele, ID dotazu a událost.

|**Název**|  **Typ**|   **Odkazy**| **Popis**|
|---|---|---|---|
|user_id    |Oid    |pg_authid.oid  |OID uživatele, který provedl příkaz|
|Db_id  |Oid    |pg_database.oid    |OID databáze, ve které byl příkaz proveden|
|query_id   |bigint     ||Interní kód hash vypočítaný ze stromu analýzy příkazu|
|Event_type |text       ||Typ události, na kterou back-end čeká|
|event  |text       ||Název události čekání, pokud back-end aktuálně čeká|
|Volání  |Integer        ||Počet zachycených stejných událostí|


### <a name="functions"></a>Funkce
Query_store.qs_reset() vrátí neplatné

`qs_reset` zahodí všechny statistiky, které dosud shromáždilo úložiště dotazů. Tuto funkci může spustit pouze role správce serveru.

Query_store.staging_data_reset() vrátí neplatné

`staging_data_reset` zahodí všechny statistiky shromážděné v paměti úložištěm dotazů (to znamená data v paměti, která ještě nebyla vyprázdněna do databáze). Tuto funkci může spustit pouze role správce serveru.

## <a name="limitations-and-known-issues"></a>Omezení a známé problémy
- Pokud postgresql server má parametr default_transaction_read_only zapnuto, úložiště dotazů nemůže zachytit data.
- Funkce úložiště dotazů může být přerušena, pokud narazí na dlouhé dotazy Unicode (>= 6000 bajtů).
- Čtení replik replik [replikaci](concepts-read-replicas.md) data úložiště dotazů z hlavního serveru. To znamená, že úložiště dotazů repliky pro čtení neposkytuje statistiky o dotazech spuštěných v replice pro čtení.


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [scénářích, kde může být úložiště dotazů obzvláště užitečné](concepts-query-store-scenarios.md).
- Přečtěte si další informace o [doporučených postupech používání úložiště dotazů](concepts-query-store-best-practices.md).
