---
title: Úložiště dotazů – databáze Azure pro MySQL
description: Přečtěte si o funkci Úložiště dotazů v Azure Database for MySQL, která vám pomůže sledovat výkon v průběhu času.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d138c2fb8ed667d5b3c961c9f567264fa40edaee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537036"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Monitorování azure databáze pro výkon MySQL pomocí úložiště dotazů

**Platí pro:** Databáze Azure pro MySQL 5.7

Funkce Úložiště dotazů v Azure Database for MySQL poskytuje způsob, jak sledovat výkon dotazů v čase. Úložiště dotazů zjednodušuje řešení potíží s výkonem tím, že vám pomůže rychle najít nejdéle spuštěné a nejvíce náročné dotazy. Úložiště dotazů automaticky zachytí historii dotazů a statistik za běhu a zachová je pro vaši kontrolu. Odděluje data podle časových oken, takže můžete zobrazit vzory využití databáze. Data pro všechny uživatele, databáze a dotazy jsou uložena v databázi **schématu mysql** v instanci Azure Database for MySQL.

## <a name="common-scenarios-for-using-query-store"></a>Běžné scénáře pro použití úložiště dotazů

Úložiště dotazů lze použít v řadě scénářů, včetně následujících:

- Zjišťování regresovaných dotazů
- Určení počtu spuštění dotazu v daném časovém okně
- Porovnání průměrné doby provádění dotazu v časových oknech za účelem zobrazení velkých rozdílových hodnot

## <a name="enabling-query-store"></a>Povolení úložiště dotazů

Úložiště dotazů je funkce pro přihlášení, takže není ve výchozím nastavení na serveru aktivní. Úložiště dotazů je povoleno nebo globálně zakázáno pro všechny databáze na daném serveru a nelze je zapnout nebo vypnout na databázi.

### <a name="enable-query-store-using-the-azure-portal"></a>Povolení úložiště dotazů pomocí portálu Azure

1. Přihlaste se na portál Azure a vyberte databázi Azure pro server MySQL.
1. V nabídce **nastavení** vyberte **Parametry serveru.**
1. Vyhledejte parametr query_store_capture_mode.
1. Nastavte hodnotu na VŠE a **Uložit**.

Povolení statistiky čekání v úložišti dotazů:

1. Vyhledejte parametr query_store_wait_sampling_capture_mode.
1. Nastavte hodnotu na VŠE a **Uložit**.

Povolit až 20 minut pro první dávka dat zachovat v databázi mysql.

## <a name="information-in-query-store"></a>Informace v úložišti dotazů

Úložiště dotazů má dva obchody:

- Úložiště statistik y za běhu pro uchování informací statistiky spuštění dotazu.
- Čekání statistiky úložiště pro trvalé čekání statistiky informací.

Aby se minimalizovalo využití místa, statistiky spuštění za běhu v úložišti statistik y runtime jsou agregovány přes pevné, konfigurovatelné časové okno. Informace v těchto úložištích jsou viditelné dotazováním na zobrazení úložiště dotazů.

Následující dotaz vrací informace o dotazech v úložišti dotazů:

```sql
SELECT * FROM mysql.query_store;
```

Nebo tento dotaz pro statistiky čekání:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Hledání dotazů na čekání

> [!NOTE]
> Statistiky čekání by neměly být povoleny během pracovního vytížení ve špičce nebo by neměly být zapnuty neomezeně dlouho pro citlivé úlohy. <br>U úloh spuštěných s vysokým využitím procesoru nebo na serverech nakonfigurovaných s nižšími virtuálními jádry buďte opatrní při povolování statistik čekání. Neměl by být zapnutý donekonečna. 

Wait typy událostí kombinovat různé události čekání do bloků podle podobnosti. Úložiště dotazů poskytuje typ události čekání, konkrétní název události čekání a dotaz. Možnost korelovat tyto informace čekání se statistikou za běhu dotazu znamená, že můžete získat hlubší pochopení toho, co přispívá k výkonu charakteristikdotazu.

Tady je několik příkladů, jak můžete získat další přehled o vaší pracovní zátěži pomocí statistiky čekání v úložišti dotazů:

| **Pozorování** | **Akce** |
|---|---|
|High Lock čeká | Zkontrolujte texty dotazů pro ovlivněné dotazy a identifikujte cílové entity. Vyhledejte v úložišti dotazů další dotazy, které upravují stejnou entitu, která se provádí často a/nebo má vysokou dobu trvání. Po identifikaci těchto dotazů zvažte změnu aplikační logiky ke zlepšení souběžnosti nebo použijte méně omezující úroveň izolace. |
|Vysoká vyrovnávací paměť IO čeká | Najděte dotazy s vysokým počtem fyzických čtení v úložišti dotazů. Pokud odpovídají dotazy s vysoké vi čeká, zvažte zavedení indexu na podkladové entity, chcete-li provést hledá místo prohledává. To by minimalizovalo vsazení vi režie dotazů. Zkontrolujte **doporučení výkonu** pro váš server na portálu a zjistěte, zda existují doporučení indexu pro tento server, který by optimalizoval dotazy. |
|Vysoká paměť čeká | Najděte nejvyšší paměti náročné dotazy v úložišti dotazů. Tyto dotazy pravděpodobně zpožďují další průběh ovlivněných dotazů. Zkontrolujte **doporučení výkonu** pro váš server na portálu a zjistěte, zda existují doporučení indexu, které by tyto dotazy optimalizovaly.|

## <a name="configuration-options"></a>Možnosti konfigurace

Pokud je povoleno úložiště dotazů, ukládá data v 15minutových oknech agregace, až 500 různých dotazů na okno.

Pro konfiguraci parametrů úložiště dotazů jsou k dispozici následující možnosti.

| **Parametr** | **Popis** | **Výchozí** | **Rozsah** |
|---|---|---|---|
| query_store_capture_mode | Zapnutí/vypnutí funkce úložiště dotazů zapnuto/vypnuto na základě hodnoty. Poznámka: Pokud je performance_schema vypnutý, zapnutí query_store_capture_mode zapne performance_schema a podmnožinu nástrojů schématu výkonu požadovaných pro tuto funkci. | VŠE | ŽÁDNÉ, VŠECHNY |
| query_store_capture_interval | Interval zachycení úložiště dotazů v minutách. Umožňuje zadat interval, ve kterém jsou agregovány metriky dotazu. | 15 | 5 - 60 |
| query_store_capture_utility_queries | Zapnutí nebo vypnutí zachytit všechny dotazy nástroje, který je spuštěn v systému. | NO | ANO, NE |
| query_store_retention_period_in_days | Časové okno ve dnech pro uchování dat v úložišti dotazů. | 7 | 1 - 30 |

Následující možnosti platí konkrétně čekat statistiky.

| **Parametr** | **Popis** | **Výchozí** | **Rozsah** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Umožňuje zapnutí / vypnutí statistiky čekání. | Žádný | ŽÁDNÉ, VŠECHNY |
| query_store_wait_sampling_frequency | Změní frekvenci vyčkávacích odběrů vzorků v sekundách. 5 až 300 sekund. | 30 | 5-300 |

> [!NOTE]
> V současné době **query_store_capture_mode** nahrazuje tuto konfiguraci, což znamená, **jak query_store_capture_mode,** tak **query_store_wait_sampling_capture_mode** musí být povoleny všem pro čekání statistiky do práce. Pokud **je query_store_capture_mode** vypnuto, statistiky čekání jsou také vypnuty, protože statistika čekání využívá performance_schema povolena a query_text zachycené úložištěm dotazů.

Pomocí [portálu Azure nebo](howto-server-parameters.md)  [rozhraní příkazového](howto-configure-server-parameters-using-cli.md) řádku Azure získáte nebo nastavíte jinou hodnotu parametru.

## <a name="views-and-functions"></a>Pohledy a funkce

Zobrazení a správa úložiště dotazů pomocí následujících zobrazení a funkcí. Kdokoli ve [veřejné roli select privilege](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql) může tato zobrazení zobrazit v úložišti dotazů pomocí těchto zobrazení. Tato zobrazení jsou k dispozici pouze v databázi **mysql.**

Dotazy jsou normalizovány při pohledu na jejich strukturu po odebrání literály a konstanty. Pokud dva dotazy jsou identické s výjimkou literál hodnoty, budou mít stejnou hodnotu hash.

### <a name="mysqlquery_store"></a>mysql.query_store

Toto zobrazení vrátí všechna data v úložišti dotazů. Pro každé odlišné ID databáze, ID uživatele a ID dotazu existuje jeden řádek.

| **Název** | **Typ dat** | **IS_NULLABLE** | **Popis** |
|---|---|---|---|
| `schema_name`| Varchar(64) | NO | Název schématu |
| `query_id`| bigint(20) | NO| Jedinečné ID generované pro konkrétní dotaz, pokud se stejný dotaz spustí v jiném schématu, bude vygenerováno nové ID |
| `timestamp_id` | časové razítko| NO| Časové razítko, ve kterém je dotaz spuštěn. To je založeno na konfiguraci query_store_interval|
| `query_digest_text`| dlouhý text| NO| Normalizovaný text dotazu po odebrání všech literál|
| `query_sample_text` | dlouhý text| NO| První výskyt skutečného dotazu s literály|
| `query_digest_truncated` | bitové| ANO| Zda byl zkrácen text dotazu. Hodnota bude Ano, pokud je dotaz delší než 1 KB|
| `execution_count` | bigint(20)| NO| Počet spuštění dotazu pro toto ID časového razítka / během nakonfigurovaného intervalového období|
| `warning_count` | bigint(20)| NO| Počet upozornění, která tento dotaz generoval během interního|
| `error_count` | bigint(20)| NO| Počet chyb, které tento dotaz generoval během intervalu|
| `sum_timer_wait` | double| ANO| Celková doba spuštění tohoto dotazu během intervalu|
| `avg_timer_wait` | double| ANO| Průměrná doba spuštění tohoto dotazu během intervalu|
| `min_timer_wait` | double| ANO| Minimální doba spuštění tohoto dotazu|
| `max_timer_wait` | double| ANO| Maximální doba provádění|
| `sum_lock_time` | bigint(20)| NO| Celková doba strávená pro všechny zámky pro toto spuštění dotazu během tohoto časového období|
| `sum_rows_affected` | bigint(20)| NO| Počet ovlivněných řádků|
| `sum_rows_sent` | bigint(20)| NO| Počet řádků odeslaných klientovi|
| `sum_rows_examined` | bigint(20)| NO| Počet zkoumaných řádků|
| `sum_select_full_join` | bigint(20)| NO| Počet úplných spojení|
| `sum_select_scan` | bigint(20)| NO| Počet vybraných skenů |
| `sum_sort_rows` | bigint(20)| NO| Počet seřazených řádků|
| `sum_no_index_used` | bigint(20)| NO| Počet, kdy dotaz nepoužil žádné indexy|
| `sum_no_good_index_used` | bigint(20)| NO| Počet, kolikrát modul provádění dotazů nepoužil žádné dobré indexy|
| `sum_created_tmp_tables` | bigint(20)| NO| Celkový počet vytvořených dočasných tabulek|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Celkový počet dočasných tabulek vytvořených na disku (generuje vstupně-va)|
| `first_seen` | časové razítko| NO| První výskyt (UTC) dotazu během okna agregace|
| `last_seen` | časové razítko| NO| Poslední výskyt (UTC) dotazu během tohoto okna agregace|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Toto zobrazení vrátí data událostí čekání v úložišti dotazů. Existuje jeden řádek pro každý odlišný ID databáze, ID uživatele, ID dotazu a událost.

| **Název**| **Typ dat** | **IS_NULLABLE** | **Popis** |
|---|---|---|---|
| `interval_start` | časové razítko | NO| Začátek intervalu (15minutový přírůstek)|
| `interval_end` | časové razítko | NO| Konec intervalu (15minutový přírůstek)|
| `query_id` | bigint(20) | NO| Generované jedinečné ID na normalizovaném dotazu (z úložiště dotazů)|
| `query_digest_id` | Varchar(32) | NO| Normalizovaný text dotazu po odebrání všech literál (z úložiště dotazů) |
| `query_digest_text` | dlouhý text | NO| První vzhled skutečného dotazu s literály (z úložiště dotazů) |
| `event_type` | Varchar(32) | NO| Kategorie události čekání |
| `event_name` | Varchar(128) | NO| Název události čekání |
| `count_star` | bigint(20) | NO| Počet událostí čekání odebraných během intervalu pro dotaz |
| `sum_timer_wait_ms` | double | NO| Celková čekací doba (v milisekundách) tohoto dotazu během intervalu |

### <a name="functions"></a>Funkce

| **Název**| **Popis** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Vyčistí všechna data úložiště dotazů před daným časovým razítkem. |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Vyčistí všechna data události čekání před daným časovým razítkem. |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Vyčistí doporučení, jejichž vypršení je před daným časovým razítkem |

## <a name="limitations-and-known-issues"></a>Omezení a známé problémy

- Pokud má server MySQL `default_transaction_read_only` parametr zapnutý, úložiště dotazů nemůže zachytit data.
- Funkce úložiště dotazů může být přerušena, pokud\>narazí na dlouhé dotazy Unicode ( = 6000 bajtů).
- Doba uchovávání statistiky čekání je 24 hodin.
- Wait statistiky používá vzorek zachytit zlomek událostí. Frekvenci lze upravit pomocí `query_store_wait_sampling_frequency`parametru .

## <a name="next-steps"></a>Další kroky

- Další informace o [přehledech výkonu dotazů](concepts-query-performance-insight.md)
