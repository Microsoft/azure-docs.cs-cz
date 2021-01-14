---
title: Úložiště dotazů – Azure Database for MySQL
description: Přečtěte si o funkci úložiště dotazů v Azure Database for MySQL, která vám pomůžou sledovat výkon v průběhu času.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: 82482b260233994672e603c16fe8cf919c92337f
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201021"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Monitorování výkonu Azure Database for MySQL s využitím úložiště dotazů

**Platí pro:** Azure Database for MySQL 5,7, 8,0

Funkce úložiště dotazů v Azure Database for MySQL poskytuje způsob, jak sledovat výkon dotazů v průběhu času. Úložiště dotazů zjednodušuje řešení potíží s výkonem tím, že vám pomůže rychle najít nejdelší běžící a většinu dotazů náročných na prostředky. Úložiště dotazů automaticky zachycuje historii dotazů a statistik za běhu a zachovává je pro vaši kontrolu. Odděluje data podle časových oken, takže můžete vidět vzory využití databáze. Data pro všechny uživatele, databáze a dotazy se ukládají do databáze schématu **MySQL** v instanci Azure Database for MySQL.

## <a name="common-scenarios-for-using-query-store"></a>Běžné scénáře použití úložiště dotazů

Úložiště dotazů je možné použít v několika scénářích, včetně následujících:

- Zjišťování vrácených dotazů
- Určení počtu spuštění dotazu v daném časovém intervalu
- Porovnání průměrné doby provádění dotazu napříč časovými okny pro zobrazení velkých rozdílů

## <a name="enabling-query-store"></a>Povoluje se úložiště dotazů.

Úložiště dotazů je funkce výslovného souhlasu, takže není ve výchozím nastavení na serveru aktivní. Úložiště dotazů je globálně povolené nebo zakázané pro všechny databáze na daném serveru a nedá se zapnout nebo vypnout pro každou databázi.

### <a name="enable-query-store-using-the-azure-portal"></a>Povolit úložiště dotazů pomocí Azure Portal

1. Přihlaste se k Azure Portal a vyberte Azure Database for MySQL server.
1. V části **Nastavení** v nabídce vyberte **parametry serveru** .
1. Vyhledejte parametr query_store_capture_mode.
1. Nastavte hodnotu vše a **uložte**.

Postup při povolování statistik čekání v úložišti dotazů:

1. Vyhledejte parametr query_store_wait_sampling_capture_mode.
1. Nastavte hodnotu vše a **uložte**.

Umožňuje trvat až 20 minut, než se první dávka dat uloží v databázi MySQL.

## <a name="information-in-query-store"></a>Informace v úložišti dotazů

Úložiště dotazů má dvě úložiště:

- Běhové úložiště statistiky pro uchování informací o statistice provádění dotazů.
- Úložiště statistiky čekání na trvalé informace o statistice čekání.

Za účelem minimalizace využití místa jsou statistiky spouštění za běhu v úložišti statistiky za běhu agregované přes pevný a konfigurovatelný časový interval. Informace v těchto úložištích jsou viditelné při dotazování zobrazení úložiště dotazů.

Následující dotaz vrátí informace o dotazech v úložišti dotazů:

```sql
SELECT * FROM mysql.query_store;
```

Nebo tento dotaz pro statistiku čekání:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Hledání dotazů čekání

> [!NOTE]
> V hodinách úlohy špičky by se nemělo povolit statistiku čekání nebo je u citlivých úloh zapnutá možnost neomezeně. <br>Pro úlohy, které běží s vysokým využitím procesoru nebo na serverech konfigurovaných s nižším virtuální jádra, buďte při povolování statistik čekání opatrní. Neměl by být zapnutý po neomezenou dobu.

Typy událostí čekání spojují různé události čekání do sad podle podobnosti. Úložiště dotazů poskytuje typ události čekání, název konkrétní události čekání a dotaz na něj. Možnost korelovat tyto informace o čekání pomocí statistiky za běhu dotazů znamená, že můžete získat hlubší přehled o tom, co přispívá k charakteristikám výkonu dotazů.

Tady je několik příkladů, jak můžete získat další přehledy o svých úlohách pomocí statistik čekání v úložišti dotazů:

| **Příležitostný** | **Akce** |
|---|---|
|Čekání na vysoký zámek | Ověřte texty dotazů pro ovlivněné dotazy a Identifikujte cílové entity. Hledání v úložišti dotazů pro další dotazy upravující stejnou entitu, která se spouští často a/nebo mají vysokou dobu trvání. Po identifikaci těchto dotazů zvažte změnu aplikační logiky pro zlepšení souběžnosti nebo použijte méně omezující úroveň izolace. |
|Vysoká vyrovnávací paměť v/v – čeká | Vyhledá dotazy s vysokým počtem fyzických čtení v úložišti dotazů. Pokud se shodují s dotazy s vysokým počtem vstupně-výstupních operací, zvažte, jestli v základní entitě zadáte index a nebudete moct hledat místo kontrol. Tím by došlo k minimalizaci režie v/v dotazů. Podívejte se na **doporučení týkající se výkonu** vašeho serveru na portálu a zjistěte, jestli existují doporučení indexu pro tento server, který by tyto dotazy optimalizoval. |
|Vysoká paměťová čekání | Vyhledá v úložišti dotazů nejlepší dotazy náročné na paměť. Tyto dotazy jsou pravděpodobně zpožděny o další průběh ovlivněných dotazů. Podívejte se na **doporučení týkající se výkonu** vašeho serveru na portálu a zjistěte, jestli existují doporučení indexu, která by tyto dotazy optimalizoval. |

## <a name="configuration-options"></a>Možnosti konfigurace

Když je povoleno úložiště dotazů, ukládá data v oknech agregace 15 minut, až 500 různých dotazů na každé okno.

Pro konfiguraci parametrů úložiště dotazů jsou k dispozici následující možnosti.

| **Parametr** | **Popis** | **Výchozí** | **Rozsah** |
|---|---|---|---|
| query_store_capture_mode | Zapněte nebo vypněte funkci úložiště dotazů na základě hodnoty. Poznámka: Pokud je performance_schema VYPNUTá, zapnutí query_store_capture_mode zapnete performance_schema a podmnožinu nástrojů schématu výkonu, které jsou pro tuto funkci nutné. | ALL | ŽÁDNÉ, VŠE |
| query_store_capture_interval | Interval zachycení úložiště dotazů je v řádu minut. Umožňuje zadat interval, ve kterém jsou metriky dotazu agregovány. | 15 | 5 - 60 |
| query_store_capture_utility_queries | Zapnutí nebo vypnutí zaznamenání všech obslužných dotazů, které jsou spuštěny v systému. | NO | ANO, NE |
| query_store_retention_period_in_days | Časový interval ve dnech, po který se mají uchovávat data v úložišti dotazů | 7 | 1 - 30 |

Následující možnosti platí konkrétně pro čekání na statistiku.

| **Parametr** | **Popis** | **Výchozí** | **Rozsah** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Umožňuje zapnout nebo vypnout statistiku čekání. | NTATO | ŽÁDNÉ, VŠE |
| query_store_wait_sampling_frequency | Mění frekvenci příkazu Wait-vzorkování v sekundách. 5 až 300 sekund. | 30 | 5-300 |

> [!NOTE]
> V současné době **query_store_capture_mode** nahrazuje tuto konfiguraci, což znamená, že je nutné povolit obě **QUERY_STORE_CAPTURE_MODE** a **query_store_wait_sampling_capture_mode** všem, aby statistiky čekání fungovaly. Pokud je **query_store_capture_mode** vypnutý, vystaví se nevyřízená statistika, protože statistiky čekání využívají performance_schema povolené a query_text zachycené úložištěm dotazů.

K získání nebo nastavení jiné hodnoty pro parametr použijte [Azure Portal](howto-server-parameters.md) nebo [Azure CLI](howto-configure-server-parameters-using-cli.md) .

## <a name="views-and-functions"></a>Zobrazení a funkce

Umožňuje zobrazit a spravovat úložiště dotazů pomocí následujících zobrazení a funkcí. Kdokoli z [veřejné role vybrat oprávnění](howto-create-users.md#to-create-more-admin-users-in-azure-database-for-mysql) může tato zobrazení použít k zobrazení dat v úložišti dotazů. Tato zobrazení jsou k dispozici pouze v databázi **MySQL** .

Dotazy jsou normalizovány tím, že si po odebrání literálů a konstant vyhledají jejich strukturu. Pokud jsou dva dotazy stejné s výjimkou hodnot literálů, budou mít stejnou hodnotu hash.

### <a name="mysqlquery_store"></a>mysql.query_store

Toto zobrazení vrátí všechna data v úložišti dotazů. Pro každé jedinečné ID databáze, ID uživatele a ID dotazu je k dispozici jeden řádek.

| **Název** | **Datový typ** | **IS_NULLABLE** | **Popis** |
|---|---|---|---|
| `schema_name`| varchar (64) | NO | Název schématu |
| `query_id`| bigint (20) | NO| Jedinečné ID generované pro konkrétní dotaz, pokud se stejný dotaz spustí v jiném schématu, vygeneruje se nové ID. |
| `timestamp_id` | časové razítko| NO| Časové razítko, ve kterém je dotaz spuštěn. To je založené na konfiguraci query_store_interval|
| `query_digest_text`| longtext| NO| Normalizovaný text dotazu po odebrání všech literálů|
| `query_sample_text` | longtext| NO| První vzhled skutečného dotazu s literály|
| `query_digest_truncated` | bit| ANO| Určuje, zda byl text dotazu zkrácen. Pokud je dotaz delší než 1 KB, hodnota bude Ano.|
| `execution_count` | bigint (20)| NO| Počet spuštění dotazu pro toto ID časového razítka/během nakonfigurovaného časového intervalu|
| `warning_count` | bigint (20)| NO| Počet upozornění, které tento dotaz vygeneroval během interního|
| `error_count` | bigint (20)| NO| Počet chyb, které tento dotaz vygeneroval během intervalu|
| `sum_timer_wait` | double| ANO| Celková doba provádění tohoto dotazu během intervalu|
| `avg_timer_wait` | double| ANO| Průměrná doba provádění tohoto dotazu během intervalu|
| `min_timer_wait` | double| ANO| Minimální doba provádění tohoto dotazu|
| `max_timer_wait` | double| ANO| Maximální doba spuštění|
| `sum_lock_time` | bigint (20)| NO| Celková doba trvání všech zámků pro toto spuštění dotazu během tohoto časového intervalu|
| `sum_rows_affected` | bigint (20)| NO| Počet ovlivněných řádků|
| `sum_rows_sent` | bigint (20)| NO| Počet řádků odeslaných klientovi|
| `sum_rows_examined` | bigint (20)| NO| Počet testovaných řádků|
| `sum_select_full_join` | bigint (20)| NO| Počet úplných spojení|
| `sum_select_scan` | bigint (20)| NO| Počet kontrol vybraných pro výběr |
| `sum_sort_rows` | bigint (20)| NO| Počet seřazených řádků|
| `sum_no_index_used` | bigint (20)| NO| Počet pokusů, kolikrát dotaz nepoužil žádné indexy|
| `sum_no_good_index_used` | bigint (20)| NO| Počet pokusů, kolikrát prováděcí modul dotazu nepoužil žádné dobré indexy|
| `sum_created_tmp_tables` | bigint (20)| NO| Celkový počet vytvořených dočasných tabulek|
| `sum_created_tmp_disk_tables` | bigint (20)| NO| Celkový počet dočasných tabulek vytvořených na disku (generuje se I/O)|
| `first_seen` | časové razítko| NO| První výskyt dotazu (UTC) během okna agregace|
| `last_seen` | časové razítko| NO| Poslední výskyt dotazu (UTC) během tohoto okna agregace|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Toto zobrazení vrátí data událostí čekání v úložišti dotazů. Pro každé jedinečné ID databáze, ID uživatele, ID dotazu a událost je jeden řádek.

| **Název**| **Datový typ** | **IS_NULLABLE** | **Popis** |
|---|---|---|---|
| `interval_start` | časové razítko | NO| Začátek intervalu (přírůstek 15 minut)|
| `interval_end` | časové razítko | NO| Konec intervalu (přírůstek 15 minut)|
| `query_id` | bigint (20) | NO| Generované jedinečné ID u normalizovaného dotazu (z úložiště dotazů)|
| `query_digest_id` | varchar (32) | NO| Normalizovaný text dotazu po odebrání všech literálů (z úložiště dotazů) |
| `query_digest_text` | longtext | NO| První vzhled skutečného dotazu s literály (z úložiště dotazů) |
| `event_type` | varchar (32) | NO| Kategorie události čekání |
| `event_name` | varchar (128) | NO| Název události čekání |
| `count_star` | bigint (20) | NO| Počet událostí čekání vzorků vydaných během intervalu pro dotaz |
| `sum_timer_wait_ms` | double | NO| Celková doba čekání (v milisekundách) tohoto dotazu během intervalu |

### <a name="functions"></a>Funkce

| **Název**| **Popis** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Vymaže všechna data z úložiště dotazů před daným časovým razítkem. |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Vymaže všechna data události čekání před daným časovým razítkem. |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Vyprázdní doporučení, jejichž vypršení platnosti je před daným časovým razítkem. |

## <a name="limitations-and-known-issues"></a>Omezení a známé problémy

- Pokud má server MySQL parametr `default_transaction_read_only` na, nemůže úložiště dotazů zachytit data.
- Funkce úložiště dotazů se dá přerušit, pokud dojde k dlouhým dotazům v kódování Unicode ( \> = 6000 bajtů).
- Doba uchování pro statistiku čekání je 24 hodin.
- V případě, že statistika čekání používá ukázku pro zachycení zlomku událostí. Frekvence se dá upravit pomocí parametru `query_store_wait_sampling_frequency` .

## <a name="next-steps"></a>Další kroky

- Další informace o službě [Query Performance Insights](concepts-query-performance-insight.md)
