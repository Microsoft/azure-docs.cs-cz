---
title: Query Store ve službě Azure Database for PostgreSQL
description: Tento článek popisuje funkci Query Store ve službě Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 149840157c5e9bb47be70f669b2078585fe4b56c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953019"
---
# <a name="monitor-performance-with-the-query-store"></a>Sledování výkonu s Query Store

**Platí pro:** Azure Database for PostgreSQL 9.6 a 10

> [!IMPORTANT]
> Funkce Query Store je ve verzi Public Preview.


Funkce Query Store ve službě Azure Database for PostgreSQL poskytuje způsob, jak sledovat výkon dotazu v čase. Store zjednodušuje tím, že vám pomáhá rychle problémů s výkonem dotazů najít dotazy nejdéle probíhající a nejvíce náročné. Query Store automaticky zaznamenává historii dotazů a statistické údaje a uchovávají je po dobu zkontrolovali. Rozděluje data podle časových oken, kde můžete zobrazit způsobů využití databází. Data pro všechny uživatele, databází a dotazů se ukládají v databázi s názvem **azure_sys** ve službě Azure Database for postgresql – instance.

> [!IMPORTANT]
> Neprovádějte žádné změny **azure_sys** databáze nebo jeho schémata. To zabrání Query Store a výkonu funkce správně funguje.

## <a name="enabling-query-store"></a>Povolení Query Store
Query Store je funkce opt-in, proto není ve výchozím nastavení na server aktivní. Povolit nebo zakázat globálně pro všechny databáze na daném serveru úložišti a není možné zapnout nebo vypnout na databázi.

### <a name="enable-query-store-using-the-azure-portal"></a>Povolit Query Store pomocí webu Azure portal
1. Přihlaste se k webu Azure portal a vyberte váš server Azure Database for PostgreSQL.
2. Vyberte **parametry serveru** v **nastavení** části nabídky.
3. Hledat **pg_qs.query_capture_mode** parametru.
4. Aktualizujte hodnotu NONE nahoru a uložte.

Případně můžete nastavit tento parametr pomocí Azure CLI.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
```

Povolit pro první dávku dat pro uchování v databázi azure_sys až 20 minut.

## <a name="information-in-query-store"></a>Informace v Query Store
Query Store má dvě úložiště:
- Úložiště statistiku modulu runtime pro trvalé statistické informace pro spuštění dotazu.
- Statistiky čekání úložiště pro trvalé počkejte statistické informace.

Obvyklé scénáře pro použití Query Store patří:
- Určení, kolikrát dotaz byl proveden v rámci daného časového intervalu
- Porovnání Průměrná doba provádění dotazu napříč zobrazíte velké rozdíly časových oken
- Identifikuje nejdelšího spouštění dotazů v minulosti X hodin
- Určení horních N dotazy, které čekají na prostředky
- Pochopení povahy čekání pro konkrétní dotaz

K minimalizaci využití místa, běhové statistiky provádění v úložišti statistiky runtime shrnou za pevnou, konfigurovatelné časové období. Informace v těchto úložišť jsou viditelné pomocí dotazu na zobrazení úložiště dotazů.

Následující dotaz vrátí informace o dotazech v Query Store:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Nebo tento dotaz pro statistiky čekání:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Zjištění čekání dotazy
Typy událostí čekání kombinovat různé čekání události do intervalů odpovědným. Query Store poskytuje typ události čekání, název události konkrétní čekání a dotyčný dotazu. Schopnost korelovat tyto informace čekání s modulem runtime dotazu statistiky znamená, že můžete získat lepší představu o co přispívá k dotazu výkonové charakteristiky.

Tady jsou některé příklady, jak můžete získat podrobnější informace o vaší úlohy pomocí statistiky čekání v Query Store:

| **Zjišťování** | **Akce** |
|---|---|
|Vysoká čeká na zámek | Kontrola textů dotazů pro ovlivněné dotazy a určení cílové entity. Hledat další dotazy úpravy stejnou entitu, která je často proveden v Query Store a/nebo mají vysoké dobu. Poté, co identifikujete tyto dotazy, zvažte změnu aplikační logiku a zvýšit souběžnost, nebo použít méně omezující úroveň izolace.|
| Počká, vysoké vstupně-výstupních operací vyrovnávací paměti | Najdete dotazy s velký počet fyzických čtení v Query Store. Pokud se shodují dotazů s vysokým vstupně-výstupní operace čeká, zvažte Představujeme indexu na základní entitu, aby bylo možné provést vyhledá namísto kontroly. To by minimalizovat nároky na vstupně-výstupních operací dotazů. Zkontrolujte, **doporučení k výkonu** pro váš server na portálu a podívejte se, pokud jsou doporučení indexu pro tento server, který by optimalizace dotazů.|
| Vysoký poměr paměti čeká | Najdete dotazy v Query Store využívající hlavní paměti. Tyto dotazy jsou pravděpodobně zpoždění další průběh ovlivněné dotazy. Zkontrolujte, **doporučení k výkonu** pro váš server na portálu a podívejte se, pokud jsou doporučení indexu, které by optimalizací těchto dotazů.|

## <a name="configuration-options"></a>Možnosti konfigurace
Pokud je povolena Query Store uloží data v systému windows agregace 15 minut až 500 různých dotazů na okno. 

Tyto možnosti jsou k dispozici pro konfiguraci Query Store parametry.
| **Parametr** | **Popis** | **Výchozí** | **rozsah**|
|---|---|---|---|
| pg_qs.query_capture_mode | Nastaví, které příkazy jsou sledovány. | nahoru | NONE, top, vše |
| pg_qs.max_query_text_length | Nastaví maximální dotazu, který lze uložit. Dotazy na delší dobu se zkrátí. | 6000 | 100 – 10 TISÍC |
| pg_qs.retention_period_in_days | Nastaví dobu uchování. | 7 | 1 - 30 |
| pg_qs.track_utility | Nastaví, zda jsou sledovány příkazy nástroje | zapnuté | vypnutý |

Tyto možnosti platí konkrétně pro počkejte statistiky.
| **Parametr** | **Popis** | **Výchozí** | **rozsah**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Sady, které příkazy jsou sledována pro čekání statistiky. | žádný | NONE, vše|
| Pgms_wait_sampling.history_period | Nastavte četnost, v milisekundách, na které čekání jsou odebírána data události. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** nahrazuje **pgms_wait_sampling.query_capture_mode**. Pokud pg_qs.query_capture_mode je NONE, pgms_wait_sampling.query_capture_mode nastavení nemá žádný vliv.


Použití [webu Azure portal](howto-configure-server-parameters-using-portal.md) nebo [rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md) získat nebo nastavit jinou hodnotu pro parametr.

## <a name="views-and-functions"></a>Zobrazení a funkce
Umožňuje zobrazit a spravovat Query Store pomocí následujících zobrazení a funkce. Kdokoli ve veřejné role PostgreSQL k zobrazení dat v Query Store slouží tato zobrazení. Tato zobrazení jsou dostupné jen **azure_sys** databáze.

Dotazy jsou normalizovány podle jejich strukturu po odebrání literály a konstanty. Pokud dva dotazy jsou stejné s výjimkou hodnoty literálu, mají stejnou hodnotu hash.

### <a name="querystoreqsview"></a>query_store.qs_view
Toto zobrazení vrátí všechna data v dotazu Store. Existuje jeden řádek pro každý jedinečných databáze, ID, ID uživatele a ID dotazu. 

|**Název**   |**Typ** | **Odkazy**  | **Popis**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID z tabulky runtime_stats_entries|
|USER_ID    |identifikátor objektu    |pg_authid.OID  |Identifikátor objektu uživatele, který je proveden příkaz|
|%{db_id/  |identifikátor objektu    |pg_database.OID    |Identifikátor objektu databáze, ve kterém byl spuštěn příkaz|
|query_id   |bigint  || Interní hodnota hash, vypočítá ze strom analýzy – příkaz|
|query_sql_text |Varchar(10000)  || Text reprezentativní příkazu. Různé dotazy s stejné struktury jsou Clusterované společně; Tento text je text pro první dotazů v clusteru.|
|hodnotou plan_id    |bigint |   |ID plánu ještě odpovídající tento dotaz není k dispozici|
|start_time |časové razítko  ||  Dotazy jsou agregované podle časovým intervalům - časový rozsah interval je 15 minut, ve výchozím nastavení ale konfigurovatelné. Toto je počáteční čas odpovídající časovém intervalu pro tuto položku.|
|end_time   |časové razítko  ||  Koncový čas odpovídající časovém intervalu pro tuto položku.|
|volání  |bigint  || Počet, kolikrát dotaz proveden|
|TOTAL_TIME |dvojitou přesností   ||  Celkový počet dotazů doby spuštění, v milisekundách|
|min_time   |dvojitou přesností   ||  Doba spuštění minimální dotazu v milisekundách|
|max_time   |dvojitou přesností   ||  Doba provádění maximální dotazu v milisekundách|
|mean_time  |dvojitou přesností   ||  Střední čas spuštění dotazu, v milisekundách|
|stddev_time|   dvojitou přesností    ||  Směrodatná odchylka doby provádění dotazu, v milisekundách |
|řádky   |bigint ||  Celkový počet řádků načíst vliv na jeden nebo příkaz|
|shared_blks_hit|   bigint  ||  Celkový počet přístupů k mezipaměti bloku sdíleného příkazem|
|shared_blks_read|  bigint  ||  Celkový počet sdílených bloků čtení příkazem|
|shared_blks_dirtied|   bigint   || Celkový počet změněných příkazem sdílené bloků |
|shared_blks_written|   bigint  ||  Celkový počet sdílených bloků, které jsou napsané pomocí příkazu|
|local_blks_hit|    bigint ||   Celkový počet přístupů k mezipaměti místním blokovým příkazem|
|local_blks_read|   bigint   || Celkový počet místních bloků čtení příkazem|
|local_blks_dirtied|    bigint  ||  Celkový počet změněných příkaz místní bloků|
|local_blks_written|    bigint  ||  Celkový počet zapsaných příkaz místní bloků|
|temp_blks_read |bigint  || Celkový počet dočasné bloky čtení příkazem|
|temp_blks_written| bigint   || Celkový počet zapsaných pomocí příkazu temp bloků|
|blk_read_time  |dvojitou přesností    || Celkový čas strávený příkaz bloky čtení v milisekundách (Pokud track_io_timing je povoleno, jinak hodnotu)|
|blk_write_time |dvojitou přesností    || Celkový čas strávený příkaz psaní bloků v milisekundách (Pokud track_io_timing je povoleno, jinak hodnotu)|
    
### <a name="querystorequerytextsview"></a>query_store.query_texts_view
Toto zobrazení vrátí data text dotazu Query Store. Existuje jeden řádek pro každý jedinečných požadovaný dotaz.

|**Název**|  **Typ**|   **Popis**|
|---|---|---|
|query_text_id  |bigint     |ID pro tabulku query_texts|
|query_sql_text |Varchar(10000)     |Text reprezentativní příkazu. Různé dotazy s stejné struktury jsou Clusterované společně; Tento text je text pro první dotazů v clusteru.|

### <a name="querystorepgmswaitsamplingview"></a>query_store.pgms_wait_sampling_view
Toto zobrazení, že se vrátí čekání dat události v Query Store. Existuje jeden řádek pro každý jedinečných databázi s ID, ID uživatele, ID dotazu a události.

|**Název**|  **Typ**|   **Odkazy**| **Popis**|
|---|---|---|---|
|USER_ID    |identifikátor objektu    |pg_authid.OID  |Identifikátor objektu uživatele, který je proveden příkaz|
|%{db_id/  |identifikátor objektu    |pg_database.OID    |Identifikátor objektu databáze, ve kterém byl spuštěn příkaz|
|query_id   |bigint     ||Interní hodnota hash, vypočítá ze strom analýzy – příkaz|
|event_type |text       ||Typ události, pro které čeká na back-endu|
|událost  |text       ||Název události čekání, pokud aktuálně čekají na back-endu|
|volání  |Integer        ||Počet stejnou událost zachycena|


### <a name="functions"></a>Functions
Vrací hodnotu void Query_store.qs_reset()

`qs_reset` zahodí všechny statistiky zatím shromážděné Query Store. Tato funkce může provádět jenom role správce serveru.

Vrací hodnotu void Query_store.staging_data_reset()

`staging_data_reset` zahodí všechny statistiky Query Store (tedy data v paměti, která byla vyprázdněna nebyl dosud k databázi) shromážděné v paměti. Tato funkce může provádět jenom role správce serveru.


## <a name="next-steps"></a>Další postup
- Další informace o [scénáře, kde může být zvláště užitečné Query Store](concepts-query-store-scenarios.md).
- Další informace o [osvědčené postupy pro používání Query Store](concepts-query-store-best-practices.md).