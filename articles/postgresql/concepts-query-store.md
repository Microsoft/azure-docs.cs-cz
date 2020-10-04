---
title: Úložiště dotazů – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje funkci úložiště dotazů na Azure Database for PostgreSQL jednom serveru.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: 7b6c8faafac34ada664ddfadebf8d71a16c73fa7
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710528"
---
# <a name="monitor-performance-with-the-query-store"></a>Monitorování výkonu pomocí úložiště dotazů

**Platí pro:** Azure Database for PostgreSQL – jeden server verze 9,6 a vyšší

Funkce úložiště dotazů v Azure Database for PostgreSQL poskytuje způsob, jak sledovat výkon dotazů v průběhu času. Úložiště dotazů zjednodušuje řešení potíží s výkonem tím, že vám pomůže rychle najít nejdelší běžící a většinu dotazů náročných na prostředky. Úložiště dotazů automaticky zachycuje historii dotazů a statistik za běhu a zachovává je pro vaši kontrolu. Odděluje data podle časových oken, takže můžete vidět vzory využití databáze. Data pro všechny uživatele, databáze a dotazy jsou uložena v databázi s názvem **azure_sys** v instanci Azure Database for PostgreSQL.

> [!IMPORTANT]
> Neupravujte **azure_sys** databázi ani její schémata. Tím se zabrání správnému fungování úložiště dotazů a souvisejících funkcí výkonu.

## <a name="enabling-query-store"></a>Povoluje se úložiště dotazů.
Úložiště dotazů je funkce výslovného souhlasu, takže není ve výchozím nastavení na serveru aktivní. Úložiště je povolené nebo zakázané globálně pro všechny databáze na daném serveru a nedá se zapnout nebo vypnout pro každou databázi.

### <a name="enable-query-store-using-the-azure-portal"></a>Povolit úložiště dotazů pomocí Azure Portal
1. Přihlaste se k Azure Portal a vyberte Azure Database for PostgreSQL Server.
2. V části **Nastavení** v nabídce vyberte **parametry serveru** .
3. Vyhledejte `pg_qs.query_capture_mode` parametr.
4. Nastavte hodnotu na `TOP` a **uložte**.

Postup při povolování statistik čekání v úložišti dotazů: 
1. Vyhledejte `pgms_wait_sampling.query_capture_mode` parametr.
1. Nastavte hodnotu na `ALL` a **uložte**.


Případně můžete tyto parametry nastavit pomocí Azure CLI.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Umožňuje až 20 minut první dávky dat pro uchování v databázi azure_sys.

## <a name="information-in-query-store"></a>Informace v úložišti dotazů
Úložiště dotazů má dvě úložiště:
- Úložiště statistik modulu runtime pro uchování informací o statistice provádění dotazů.
- Úložiště statistiky čekání na trvalé informace o statistice čekání.

Mezi běžné scénáře použití úložiště dotazů patří:
- Určení počtu spuštění dotazu v daném časovém intervalu
- Porovnání průměrné doby provádění dotazu napříč časovými okny pro zobrazení velkých rozdílů
- Identifikace nejdelších spuštěných dotazů za posledních X hodin
- Identifikace prvních N dotazů, které čekají na prostředky
- Principy čekací povahy pro konkrétní dotaz

Za účelem minimalizace využití místa jsou statistiky spuštění modulu runtime v úložišti statistik modulu runtime agregovány v pevně nastaveném časovém intervalu. Informace v těchto úložištích jsou viditelné při dotazování zobrazení úložiště dotazů.

## <a name="access-query-store-information"></a>Přístup k informacím o úložišti dotazů

Data úložiště dotazů se ukládají do databáze azure_sys na serveru Postgres. 

Následující dotaz vrátí informace o dotazech v úložišti dotazů:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Nebo tento dotaz pro statistiku čekání:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Hledání dotazů čekání
Typy událostí čekání spojují různé události čekání do sad podle podobnosti. Úložiště dotazů poskytuje typ události čekání, název konkrétní události čekání a dotaz na něj. Možnost korelovat tyto informace o čekání pomocí statistiky za běhu dotazů znamená, že můžete získat hlubší přehled o tom, co přispívá k charakteristikám výkonu dotazů.

Tady je několik příkladů, jak můžete získat další přehledy o svých úlohách pomocí statistik čekání v úložišti dotazů:

| **Příležitostný** | **Akce** |
|---|---|
|Čekání na vysoký zámek | Ověřte texty dotazů pro ovlivněné dotazy a Identifikujte cílové entity. Hledání v úložišti dotazů pro další dotazy upravující stejnou entitu, která se spouští často a/nebo mají vysokou dobu trvání. Po identifikaci těchto dotazů zvažte změnu aplikační logiky pro zlepšení souběžnosti nebo použijte méně omezující úroveň izolace.|
| Vysoká vyrovnávací paměť v/v – čeká | Vyhledá dotazy s vysokým počtem fyzických čtení v úložišti dotazů. Pokud se shodují s dotazy s vysokým počtem vstupně-výstupních operací, zvažte zavedení indexu v základní entitě, aby se místo kontrol hledaly. Tím by došlo k minimalizaci režie v/v dotazů. Podívejte se na **doporučení týkající se výkonu** vašeho serveru na portálu a zjistěte, jestli existují doporučení indexu pro tento server, který by tyto dotazy optimalizoval.|
| Vysoká paměťová čekání | Vyhledá v úložišti dotazů nejlepší dotazy náročné na paměť. Tyto dotazy jsou pravděpodobně zpožděny o další průběh ovlivněných dotazů. Podívejte se na **doporučení týkající se výkonu** vašeho serveru na portálu a zjistěte, jestli existují doporučení indexu, která by tyto dotazy optimalizoval.|

## <a name="configuration-options"></a>Možnosti konfigurace
Když je povoleno úložiště dotazů, ukládá data v oknech agregace 15 minut, až 500 různých dotazů na každé okno. 

Pro konfiguraci parametrů úložiště dotazů jsou k dispozici následující možnosti.

| **Parametr** | **Popis** | **Výchozí** | **Oblasti**|
|---|---|---|---|
| pg_qs pg_qs.query_capture_mode | Nastaví, které příkazy jsou sledovány. | žádné | žádné, nahoře, vše |
| pg_qs pg_qs.max_query_text_length | Nastaví maximální délku dotazu, kterou lze uložit. Delší dotazy budou zkráceny. | 6000 | 100 – 10 000 |
| pg_qs pg_qs.retention_period_in_days | Nastaví dobu uchování. | 7 | 1 - 30 |
| pg_qs pg_qs.track_utility | Nastaví, jestli se mají sledovat příkazy nástrojů. | on | zapnuto, vypnuto |

Následující možnosti platí konkrétně pro čekání na statistiku.

| **Parametr** | **Popis** | **Výchozí** | **Oblasti**|
|---|---|---|---|
| pgms_wait_sampling pgms_wait_sampling.query_capture_mode | Nastaví, které příkazy jsou sledovány pro statistiku čekání. | žádné | žádné, vše|
| Pgms_wait_sampling Pgms_wait_sampling.history_period | Nastavte četnost vzorkování událostí čekání v milisekundách. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs. query_capture_mode** nahrazuje **pgms_wait_sampling. query_capture_mode**. Pokud pg_qs. query_capture_mode je NONE, nastavení pgms_wait_sampling. query_capture_mode nemá žádný vliv.


K získání nebo nastavení jiné hodnoty pro parametr použijte [Azure Portal](howto-configure-server-parameters-using-portal.md) nebo [Azure CLI](howto-configure-server-parameters-using-cli.md) .

## <a name="views-and-functions"></a>Zobrazení a funkce
Umožňuje zobrazit a spravovat úložiště dotazů pomocí následujících zobrazení a funkcí. Kdokoli v PostgreSQL veřejné roli může pomocí těchto zobrazení zobrazit data v úložišti dotazů. Tato zobrazení jsou k dispozici pouze v databázi **azure_sys** .

Dotazy jsou normalizovány tím, že si po odebrání literálů a konstant vyhledají jejich strukturu. Pokud jsou dva dotazy stejné s výjimkou hodnot literálů, budou mít stejnou hodnotu hash.

### <a name="query_storeqs_view"></a>query_store query_store.qs_view
Toto zobrazení vrátí všechna data v úložišti dotazů. Pro každé jedinečné ID databáze, ID uživatele a ID dotazu je k dispozici jeden řádek. 

|**Název**   |**Typ** | **Odkazy**  | **Popis**|
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

|**Název**|  **Typ**|   **Popis**|
|---|---|---|
|query_text_id  |bigint     |ID pro query_textsovou tabulku|
|query_sql_text |Varchar (10000)     |Text zástupce příkazu Různé dotazy se stejnou strukturou jsou clusterované dohromady; Tento text je text pro první z dotazů v clusteru.|

### <a name="query_storepgms_wait_sampling_view"></a>query_store query_store.pgms_wait_sampling_view
Toto zobrazení vrátí data událostí čekání v úložišti dotazů. Pro každé jedinečné ID databáze, ID uživatele, ID dotazu a událost je jeden řádek.

|**Název**|  **Typ**|   **Odkazy**| **Popis**|
|---|---|---|---|
|user_id    |identifikátor    |pg_authid. OID  |Identifikátor OID uživatele, který příkaz provedl|
|db_id  |identifikátor    |pg_database. OID    |Identifikátor objektu databáze, ve kterém byl příkaz proveden|
|query_id   |bigint     ||Vnitřní kód hash vypočítaný z stromu analýzy příkazu|
|event_type |text       ||Typ události, pro kterou back-end čeká|
|event  |text       ||Název události čekání, pokud back-end momentálně čeká|
|volání  |Integer        ||Číslo stejné zachycené události|


### <a name="functions"></a>Funkce
Query_store Query_store.qs_reset () vrátí typ void.

`qs_reset` zahodí všechny statistiky shromážděné zatím v úložišti dotazů. Tuto funkci může spustit jenom role správce serveru.

Query_store Query_store.staging_data_reset () vrátí typ void.

`staging_data_reset` zahodí všechny statistiky shromážděné v paměti úložištěm dotazů (tj. data v paměti, která ještě nebyla vyprázdněna do databáze). Tuto funkci může spustit jenom role správce serveru.


## <a name="azure-monitor"></a>Azure Monitor
Azure Database for PostgreSQL je integrován s [nastavením diagnostiky Azure monitor](../azure-monitor/platform/diagnostic-settings.md). Nastavení diagnostiky umožňuje odeslat protokoly Postgres ve formátu JSON, abyste [Azure monitor protokoly](../azure-monitor/log-query/log-query-overview.md) pro analýzy a upozorňování, Event Hubs pro streamování a Azure Storage k archivaci.

>[!IMPORTANT]
> Tato diagnostická funkce pro je dostupná jenom v Pro obecné účely a paměťově optimalizované cenové úrovně.

### <a name="configure-diagnostic-settings"></a>Konfigurace nastavení diagnostiky
Nastavení diagnostiky pro server Postgres můžete povolit pomocí Azure Portal, CLI, REST API a PowerShellu. Kategorie protokolů, které se mají nakonfigurovat, jsou **QueryStoreRuntimeStatistics** a **QueryStoreWaitStatistics**. 

Postup povolení protokolů prostředku pomocí Azure Portal:

1. Na portálu přejděte v navigační nabídce serveru Postgres na nastavení diagnostiky.
2. Vyberte Přidat nastavení diagnostiky.
3. Pojmenujte toto nastavení.
4. Vyberte preferovaný koncový bod (účet úložiště, centrum událostí a Log Analytics).
5. Vyberte typy protokolů **QueryStoreRuntimeStatistics** a **QueryStoreWaitStatistics**.
6. Uložte nastavení.

Pokud chcete toto nastavení povolit pomocí PowerShellu, rozhraní příkazového řádku nebo REST API, přejděte na [článek nastavení diagnostiky](../azure-monitor/platform/diagnostic-settings.md).

### <a name="json-log-format"></a>Formát protokolu JSON
V následujících tabulkách jsou popsána pole pro dva typy protokolů. V závislosti na zvoleném výstupním koncovém bodu se můžou pole zahrnutá a pořadí, ve kterém se zobrazují, lišit.

#### <a name="querystoreruntimestatistics"></a>QueryStoreRuntimeStatistics
|**Pole** | **Popis** |
|---|---|
| TimeGenerated [UTC] | Časové razítko, kdy se protokol zaznamenal v UTC |
| ResourceId | Identifikátor URI prostředku Azure serveru Postgres |
| Kategorie | `QueryStoreRuntimeStatistics` |
| OperationName | `QueryStoreRuntimeStatisticsEvent` |
| LogicalServerName_s | Název serveru Postgres | 
| runtime_stats_entry_id_s | ID z runtime_stats_entries tabulky |
| user_id_s | Identifikátor OID uživatele, který příkaz provedl |
| db_id_s | Identifikátor objektu databáze, ve kterém byl příkaz proveden |
| query_id_s | Vnitřní kód hash vypočítaný z stromu analýzy příkazu |
| end_time_s | Čas ukončení odpovídající časovému intervalu pro tuto položku |
| calls_s | Počet provedení dotazu |
| total_time_s | Celková doba provádění dotazu v milisekundách |
| min_time_s | Minimální doba provádění dotazu v milisekundách |
| max_time_s | Maximální doba provádění dotazu v milisekundách |
| mean_time_s | Průměrná doba provádění dotazu (v milisekundách) |
| ResourceGroup | Skupina prostředků | 
| SubscriptionId | ID vašeho předplatného |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Prostředek | Název serveru Postgres |
| ResourceType | `Servers` | 


#### <a name="querystorewaitstatistics"></a>QueryStoreWaitStatistics
|**Pole** | **Popis** |
|---|---|
| TimeGenerated [UTC] | Časové razítko, kdy se protokol zaznamenal v UTC |
| ResourceId | Identifikátor URI prostředku Azure serveru Postgres |
| Kategorie | `QueryStoreWaitStatistics` |
| OperationName | `QueryStoreWaitEvent` |
| user_id_s | Identifikátor OID uživatele, který příkaz provedl |
| db_id_s | Identifikátor objektu databáze, ve kterém byl příkaz proveden |
| query_id_s | Vnitřní kód hash dotazu |
| calls_s | Číslo stejné zachycené události |
| event_type_s | Typ události, pro kterou back-end čeká |
| event_s | Název události čekání, pokud back-end aktuálně čeká |
| start_time_t | Čas spuštění události |
| end_time_s | Čas ukončení události | 
| LogicalServerName_s | Název serveru Postgres | 
| ResourceGroup | Skupina prostředků | 
| SubscriptionId | ID vašeho předplatného |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Prostředek | Název serveru Postgres |
| ResourceType | `Servers` | 

## <a name="limitations-and-known-issues"></a>Omezení a známé problémy
- Pokud má server PostgreSQL parametr default_transaction_read_only na, nemůže úložiště dotazů zachytit data.
- Funkce úložiště dotazů se dá přerušit, pokud dojde k dlouhým dotazům v kódování Unicode (>= 6000 bajtů).
- [Čtení replik](concepts-read-replicas.md) replikuje data úložiště dotazů z primárního serveru. To znamená, že úložiště dotazů repliky pro čtení neposkytuje statistiku o dotazech spuštěných v replice pro čtení.


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [scénářích, ve kterých může být úložiště dotazů obzvlášť užitečné](concepts-query-store-scenarios.md).
- Přečtěte si další informace o [osvědčených postupech pro používání úložiště dotazů](concepts-query-store-best-practices.md).
