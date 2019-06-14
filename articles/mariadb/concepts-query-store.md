---
title: Query Store ve službě Azure Database pro MariaDB
description: Tento článek popisuje funkci Query Store ve službě Azure Database pro MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 9016fa159e868f649901928cdf2dca2f08725e77
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079389"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>Monitorování – Azure Database pro MariaDB výkon s Query Store

**Platí pro:**  Azure Database pro MariaDB 10.2

> [!NOTE]
> Query Store je ve verzi preview. Podpora pro Store dotaz na webu Azure Portal se nasazuje a ještě možná není k dispozici ve vaší oblasti.

Funkce Query Store ve službě Azure Database pro Mariadb poskytuje způsob, jak sledovat výkon dotazu v čase. Store zjednodušuje tím, že vám pomáhá rychle problémů s výkonem dotazů najít dotazy nejdéle probíhající a nejvíce náročné. Query Store automaticky zaznamenává historii dotazů a statistické údaje a uchovávají je po dobu zkontrolovali. Rozděluje data podle časových oken, kde můžete zobrazit způsobů využití databází. Data pro všechny uživatele, databází a dotazů se ukládají v **mysql** schématu databáze ve službě Azure Database pro MariaDB instanci.

## <a name="common-scenarios-for-using-query-store"></a>Obvyklé scénáře pro použití Query Store

Úložiště dotazů je možné v řadě scénářů, včetně následujících:

- Zjišťování nižším dotazy
- Určení, kolikrát dotaz byl proveden v rámci daného časového intervalu
- Porovnání Průměrná doba provádění dotazu napříč zobrazíte velké rozdíly časových oken
- Identifikuje nejdelšího spouštění dotazů v minulosti X hodin
- Určení horních N dotazy, které čekají na prostředky
- Pochopení povahy čekání dotazu
- Principy trendy pro prostředek čeká a tam, kde existuje kolize prostředků

## <a name="enabling-query-store"></a>Povolení Query Store

Query Store je funkce opt-in, proto není ve výchozím nastavení na server aktivní. Úložiště dotazů je povolit nebo zakázat globálně pro všechny databáze na daném serveru a není možné zapnout nebo vypnout na databázi.

### <a name="enable-query-store-using-the-azure-portal"></a>Povolit Query Store pomocí webu Azure portal

1. Přihlaste se k webu Azure portal a vyberte váš server Azure Database for MariaDB.
1. Vyberte **parametry serveru** v **nastavení** části nabídky.
1. Vyhledejte parametr query_store_capture_mode.
1. Nastavte hodnotu na vše a **Uložit**.

Pokud chcete povolit čekání statistické údaje ve vašich Query Store:

1. Vyhledejte parametr query_store_wait_sampling_capture_mode.
1. Nastavte hodnotu na vše a **Uložit**.

Povolit až 20 minut, než se pro první dávku dat pro uchování v databázi mysql.

## <a name="information-in-query-store"></a>Informace v Query Store

Query Store má dvě úložiště:

- Běhové statistiky úložiště pro trvalé statistické informace pro spuštění dotazu.
- Statistiky čekání úložiště pro trvalé počkejte statistické informace.

K minimalizaci využití místa, běhové statistiky provádění v úložišti statistiku modulu runtime shrnou za pevnou, konfigurovatelné časové období. Informace v těchto úložišť jsou viditelné pomocí dotazu na zobrazení úložiště dotazů.

Následující dotaz vrátí informace o dotazech v Query Store:

```sql
SELECT * FROM mysql.query_store;
```

Nebo tento dotaz pro čekání statistiky:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Zjištění čekání dotazy

Typy událostí čekání kombinovat různé čekání události do intervalů odpovědným. Query Store poskytuje typ události čekání, název události konkrétní čekání a dotyčný dotazu. Schopnost korelovat tyto informace čekání s modulem runtime dotazu statistiky znamená, že můžete získat lepší představu o co přispívá k dotazu výkonové charakteristiky.

Tady jsou některé příklady, jak můžete získat podrobnější informace o vaší úlohy pomocí statistiky čekání v Query Store:

| **Zjišťování** | **Akce** |
|---|---|
|Vysoká čeká na zámek | Kontrola textů dotazů pro ovlivněné dotazy a určení cílové entity. Hledat další dotazy úpravy stejnou entitu, která je často proveden v Query Store a/nebo mají vysoké dobu. Poté, co identifikujete tyto dotazy, zvažte změnu aplikační logiku a zvýšit souběžnost, nebo použít méně omezující úroveň izolace. |
|Počká, vysoké vstupně-výstupních operací vyrovnávací paměti | Najdete dotazy s velký počet fyzických čtení v Query Store. Pokud se shodují dotazů s vysokým vstupně-výstupní operace čeká, zvažte Představujeme indexu na základní entity, provedete vyhledá namísto kontroly. To by minimalizovat nároky na vstupně-výstupních operací dotazů. Zkontrolujte, **doporučení k výkonu** pro váš server na portálu a podívejte se, pokud jsou doporučení indexu pro tento server, který by optimalizace dotazů. |
|Vysoký poměr paměti čeká | Najdete dotazy v Query Store využívající hlavní paměti. Tyto dotazy jsou pravděpodobně zpoždění další průběh ovlivněné dotazy. Zkontrolujte, **doporučení k výkonu** pro váš server na portálu a podívejte se, pokud jsou doporučení indexu, které by optimalizací těchto dotazů.|

## <a name="configuration-options"></a>Možnosti konfigurace

Pokud je povolena Query Store uloží data v systému windows agregace 15 minut až 500 různých dotazů na okno.

Tyto možnosti jsou k dispozici pro konfiguraci Query Store parametry.

| **Parametr** | **Popis** | **Výchozí** | **rozsah** |
|---|---|---|---|
| query_store_capture_mode | Zapněte funkci úložiště dotazů zapnout nebo vypnout podle hodnoty. Poznámka: Pokud performance_schema je VYPNUTÝ, zapnutí query_store_capture_mode se zapnou performance_schema a podmnožinu výkonu schématu nástroje potřebné pro tuto funkci. | VŠECHNY | NONE, VŠE |
| query_store_capture_interval | Úložiště dotazů zachycení interval v minutách. Umožňuje zadat interval, ve kterém se agregují dotaz metriky | 15 | 5 - 60 |
| query_store_capture_utility_queries | Zapnutí zapnuto nebo vypnuto zachytit všechny dotazy nástroje, které se provádí v systému. | NO | ANO, NE |
| query_store_retention_period_in_days | Časový interval ve dnech, pokud chcete zachovat data v úložišti dotazů. | 7 | 1 - 30 |

Tyto možnosti platí konkrétně pro počkejte statistiky.

| **Parametr** | **Popis** | **Výchozí** | **rozsah** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Umožňuje zapínání zapnuto / vypnuto statistiky čekání. | NONE | NONE, VŠE |
| query_store_wait_sampling_frequency | Alters frekvence vzorkování čekání v sekundách. 5 do 300 sekund. | 30 | 5-300 |

> [!NOTE]
> Aktuálně **query_store_capture_mode** nahrazuje tuto konfiguraci, to znamená i **query_store_capture_mode** a **query_store_wait_sampling_capture_mode** potřeba povolit pro všechny statistiky čekání pracovat. Pokud **query_store_capture_mode** je vypnutý, pak statistiky čekání je vypnutý a protože statistiky čekání využívá performance_schema povolená a požadovaný dotaz nezachytává úložiště dotazů.

Použití [webu Azure portal](howto-server-parameters.md) získat nebo nastavit jinou hodnotu pro parametr.

## <a name="views-and-functions"></a>Zobrazení a funkce

Umožňuje zobrazit a spravovat Query Store pomocí následujících zobrazení a funkce. Kdokoli ve [vyberte oprávnění veřejné role](howto-create-users.md#create-additional-admin-users) pomocí těchto zobrazení zobrazit data v dotazu Store. Tato zobrazení jsou dostupné jen **mysql** databáze.

Dotazy jsou normalizovány podle jejich strukturu po odebrání literály a konstanty. Pokud dva dotazy jsou stejné s výjimkou hodnoty literálu, mají stejnou hodnotu hash.

### <a name="mysqlquerystore"></a>mysql.query_store

Toto zobrazení vrátí všechna data v dotazu Store. Existuje jeden řádek pro každý jedinečných databáze, ID, ID uživatele a ID dotazu.

| **Název** | **Datový typ** | **IS_NULLABLE** | **Popis** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Název schématu |
| `query_id`| bigint(20) | NO| Vygeneruje jedinečné ID generované pro konkrétní dotaz, pokud se stejný dotaz je vyhodnocen v jiné schéma, nové ID |
| `timestamp_id` | timestamp| NO| Časové razítko spuštění dotazu. To je založené na konfiguraci query_store_interval|
| `query_digest_text`| longtext| NO| Text dotazu normalizované po odebrání všech literálech|
| `query_sample_text` | longtext| NO| První výskyt samotný dotaz s literály|
| `query_digest_truncated` | bit| ANO| Určuje, zda byl zkrácen text dotazu. Hodnota bude Ano, pokud dotaz je delší než 1 kB?|
| `execution_count` | bigint(20)| NO| Počet pokusů, které máte dotaz provést pro toto ID časové razítko / období nakonfigurovaný interval|
| `warning_count` | bigint(20)| NO| Počet upozornění na tento dotaz vygenerovaných během vnitřní|
| `error_count` | bigint(20)| NO| Počet chyb tento dotaz generovaných během intervalu|
| `sum_timer_wait` | double| ANO| Celková doba spuštění tohoto dotazu během intervalu|
| `avg_timer_wait` | double| ANO| Průměrná doba provedení tohoto dotazu během intervalu|
| `min_timer_wait` | double| ANO| Minimální doba spuštění pro tento dotaz|
| `max_timer_wait` | double| ANO| Maximální doba spuštění|
| `sum_lock_time` | bigint(20)| NO| Celkové množství času stráveného pro všech zámků pro toto spuštění dotazu během tohoto časového intervalu|
| `sum_rows_affected` | bigint(20)| NO| Počet ovlivněných řádků|
| `sum_rows_sent` | bigint(20)| NO| Počet řádků, které jsou odeslány do klienta|
| `sum_rows_examined` | bigint(20)| NO| Počet řádků, které jsou zkoumány|
| `sum_select_full_join` | bigint(20)| NO| Počet úplné spojení|
| `sum_select_scan` | bigint(20)| NO| Počet vyberte kontroly |
| `sum_sort_rows` | bigint(20)| NO| Počet řádků, řazení|
| `sum_no_index_used` | bigint(20)| NO| Počet pokusů, kdy dotaz nepoužívá žádné indexy|
| `sum_no_good_index_used` | bigint(20)| NO| Počet pokusů, pokud prováděcí modul dotazu nepoužívá žádné vhodné indexy|
| `sum_created_tmp_tables` | bigint(20)| NO| Celkový počet dočasné tabulky vytvořené|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Celkový počet dočasné tabulky vytvořené na disku (generuje vstupně-výstupních operací)|
| `first_seen` | timestamp| NO| První výskyt (UTC) dotazu během časového období agregace|
| `last_seen` | timestamp| NO| Poslední výskyt (UTC) dotazu během tohoto časového období agregace|

### <a name="mysqlquerystorewaitstats"></a>mysql.query_store_wait_stats

Toto zobrazení, že se vrátí čekání dat události v Query Store. Existuje jeden řádek pro každý jedinečných databázi s ID, ID uživatele, ID dotazu a události.

| **Název**| **Datový typ** | **IS_NULLABLE** | **Popis** |
|---|---|---|---|
| `interval_start` | timestamp | NO| Začátek intervalu (krok 15 minut)|
| `interval_end` | timestamp | NO| Konec intervalu (krok 15 minut)|
| `query_id` | bigint(20) | NO| Jedinečné ID generované na normalizované dotaz (z úložiště dotazů)|
| `query_digest_id` | varchar(32) | NO| Text dotazu normalizované po odebrání všech literálech (z úložiště dotazů) |
| `query_digest_text` | longtext | NO| První výskyt samotný dotaz s literály (z úložiště dotazů) |
| `event_type` | varchar(32) | NO| Kategorie události čekání |
| `event_name` | varchar(128) | NO| Název události čekání |
| `count_star` | bigint(20) | NO| Počet odebraných během intervalu pro dotaz události čekání |
| `sum_timer_wait_ms` | double | NO| Celkový čas čekání (v milisekundách) tohoto dotazu během intervalu |

### <a name="functions"></a>Funkce

| **Název**| **Popis** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Vymaže všechny dotazu úložiště dat před dané časové razítko |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Vymazat všechny počkejte data událostí před dané časové razítko |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Vymaže doporučení, jehož vypršení platnosti předchází dané časové razítko |

## <a name="limitations-and-known-issues"></a>Omezení a známé problémy

- Pokud má parametr MariaDB server `default_transaction_read_only` , Query Store nejde zachytit data.
- Query Store funkce můžete přerušit, pokud nalezne dlouhé dotazy kódování Unicode (\>= 6000 bajtů).
- Doba uchování pro čekání statistiky je 24 hodin.
- Statistiky čekání využívá zachytávání. ti ukázka vzorky událostí. Frekvenci lze změnit pomocí parametru `query_store_wait_sampling_frequency`.

## <a name="next-steps"></a>Další postup

- Další informace o [informace o výkonu dotazů](concepts-query-performance-insight.md)
