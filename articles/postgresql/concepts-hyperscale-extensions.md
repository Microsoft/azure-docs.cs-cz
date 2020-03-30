---
title: Rozšíření – hyperškálování (Citus) – databáze Azure pro PostgreSQL
description: Popisuje možnost rozšíření funkcí databáze pomocí rozšíření v Azure Database for PostgreSQL – Hyperscale (Citus).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 84b28096904db49f98c16601c5927928ad38743b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485399"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Rozšíření PostgreSQL v Azure Database for PostgreSQL – Hyperscale (Citus)

PostgreSQL poskytuje možnost rozšířit funkce databáze pomocí rozšíření. Rozšíření umožňují sdružování více souvisejících objektů SQL dohromady v jednom balíčku, který lze načíst nebo odebrat z databáze pomocí jediného příkazu. Po načtení do databáze mohou rozšíření fungovat jako integrované funkce. Další informace o rozšíření PostgreSQL naleznete [v tématu Balíček související objekty do rozšíření](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Použití rozšíření PostgreSQL

Před použitím musí být v databázi nainstalována rozšíření PostgreSQL. Chcete-li nainstalovat konkrétní rozšíření, spusťte příkaz [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) z nástroje psql a načtěte zabalené objekty do databáze.

Azure Database for PostgreSQL – Hyperscale (Citus) aktuálně podporuje podmnožinu rozšíření klíčů, jak je uvedeno zde. Jiná rozšíření než ta uvedená nejsou podporována. S Azure Database pro PostgreSQL nemůžete vytvořit vlastní rozšíření.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Rozšíření podporovaná databází Azure pro PostgreSQL

V následujících tabulkách jsou uvedena standardní rozšíření PostgreSQL, která jsou aktuálně podporovaná azure database pro PostgreSQL. Tyto informace jsou také `SELECT * FROM pg_available_extensions;`k dispozici spuštěním .

### <a name="data-types-extensions"></a>Rozšíření datových typů

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Poskytuje typ řetězce znaku bez rozlišování velkých a malých písmen. |
> | [Datové krychle](https://www.postgresql.org/docs/current/static/cube.html) | Poskytuje datový typ pro vícerozměrné krychle. |
> | [hobchod](https://www.postgresql.org/docs/current/static/hstore.html) | Poskytuje datový typ pro ukládání sad párů klíč-hodnota. |
> | [hll](https://github.com/citusdata/postgresql-hll) | Poskytuje datovou strukturu HyperLogLog. |
> | [není](https://www.postgresql.org/docs/current/static/isn.html) | Poskytuje datové typy pro mezinárodní standardy číslování produktů. |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | Údržba velkých objektů. |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Poskytuje datový typ pro hierarchické stromové struktury. |
> | [Seg](https://www.postgresql.org/docs/current/seg.html) | Datový typ pro reprezentaci úsečkových segmentů nebo intervalů s plovoucí desetinnou čárou. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Typ pro top-n JSONB. |

### <a name="full-text-search-extensions"></a>Rozšíření fulltextového vyhledávání

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** |
> |---|---|
> | [dikt\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Poskytuje šablonu slovníku pro vyhledávání textu pro celá čísla. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Šablona slovníku pro vyhledávání textu pro rozšířené zpracování synonym. |
> | [odkcent](https://www.postgresql.org/docs/current/static/unaccent.html) | Slovník pro vyhledávání textu, který odstraňuje diakritiku (diakritiku) z lexémů. |

### <a name="functions-extensions"></a>Rozšíření funkcí

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funkce pro automatické pokrnění polí. |
> | [zemní vzdálenost](https://www.postgresql.org/docs/current/static/earthdistance.html) | Poskytuje prostředky pro výpočet velmi kruhových vzdáleností na povrchu Země. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Poskytuje několik funkcí k určení podobností a vzdálenosti mezi řetězci. |
> | [vložit\_uživatelské jméno](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funkce pro sledování, kdo změnil tabulku. |
> | [netagg](https://www.postgresql.org/docs/current/intagg.html) | Agregátor celého čísla a čítač výčtu (zastaralý). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Poskytuje funkce a operátory pro manipulaci s poli bez nula celá čísla. |
> | [moddatečas](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funkce pro sledování času poslední změny. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Poskytuje kryptografické funkce. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Spravuje dělené tabulky podle času nebo ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Poskytuje funkce a operátory pro určení podobnosti alfanumerického textu na základě porovnávání trigramů. |
> | [rafinace](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funkce pro implementaci referenční integrity (zastaralé). |
> | analýza\_relace | Funkce pro dotazování hstore pole. |
> | [stolní func](https://www.postgresql.org/docs/current/static/tablefunc.html) | Poskytuje funkce, které manipulují s celými tabulkami, včetně křížové tabulky. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Aktivovaná oznámení o změně. |
> | [cestování časem](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funkce pro implementaci cestování v čase. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Generuje univerzálně jedinečné identifikátory (UUID). |

### <a name="hyperscale-extensions"></a>Rozšíření hyperškálování

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** |
> |---|---|
> | [citus (Něm.)](https://github.com/citusdata/citus) | Citus distribuované databáze. |
> | vyvažovač střepů\_ | V případě přidání nebo odebrání uzlu bezpečně vyvažte data ve skupině serverů. |

### <a name="index-types-extensions"></a>Rozšíření typů indexů

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** |
> |---|---|
> | [Bloom](https://www.postgresql.org/docs/current/bloom.html) | Metoda přístupu k květu - index založený na souboru podpisu. |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Poskytuje ukázkové třídy operátoru GIN, které implementují chování podobné b-stromu pro určité datové typy. |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Poskytuje třídy operátoru indexu GiST, které implementují B-strom. |

### <a name="language-extensions"></a>Jazyková rozšíření

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL loadable procedurální jazyk. |

### <a name="miscellaneous-extensions"></a>Různá rozšíření

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Administrativní funkce pro PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funkce pro ověření integrity vztahu. |
> | [soubor\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Obálka cizích dat pro plochý přístup k souborům. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Zkontrolujte obsah stránek databáze na nízké úrovni. |
> | [pg\_vyrovnávací paměť](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Poskytuje prostředky pro zkoumání toho, co se děje ve sdílené mezipaměti vyrovnávací paměti v reálném čase. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Plánovač úloh pro PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Prohlédněte si mapu volného místa (FSM). |
> | [pg\_předhře](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Poskytuje způsob, jak načíst data vztahu do mezipaměti vyrovnávací paměti. |
> | [pg\_\_stat příkazy](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Poskytuje prostředky pro sledování statistiky provádění všech příkazů SQL prováděných serverem. Informace o tomto rozšíření naleznete v části "pg_stat_statements". |
> | [pg\_viditelnost](https://www.postgresql.org/docs/current/pgvisibility.html) | Zkontrolujte mapu viditelnosti (VM) a informace o viditelnosti na úrovni stránky. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Poskytuje prostředky pro zobrazení informací o uzamčení na úrovni řádku. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Poskytuje prostředky pro zobrazení statistiky úrovně n-tice. |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Obálka cizích dat používaná pro přístup k datům uloženým na externích serverech PostgreSQL. Informace o tomto rozšíření naleznete v části "dblink a postgres_fdw".|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informace o certifikátech SSL. |
> | [řádky\_systému\_TSM](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TableSAMPLE metoda, která přijímá počet řádků jako limit. |
> | [tsm\_\_systémový čas](https://www.postgresql.org/docs/current/tsm-system-time.html) | TableSAMPLE metoda, která přijímá čas v milisekundách jako limit. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Poskytuje prostředky pro vytváření hypotetických indexů, které nestojí procesor nebo disk. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Modul, který podporuje připojení k jiným databázím PostgreSQL z relace databáze. Informace o tomto rozšíření naleznete v části "dblink a postgres_fdw". |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath dotazování a XSLT. |


### <a name="postgis-extensions"></a>Rozšíření PostGIS

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** |
> |---|---|
> | [PostGIS](https://www.postgis.net/),\_postgis topologie,\_\_postgis tiger geocoder, postgis\_sfcgal | Prostorové a geografické objekty pro PostgreSQL. |
> | adresa\_standardizer,\_adresa\_\_standardizer data nás | Slouží k analýzě adresy do základních prvků. Slouží k podpoře kroku normalizace geokódování adres. |
> | postgis\_sfcgal | PostGIS SFCGAL. |
> | postgis\_\_tygří geokodér | PostGIS tygří geokodér a reverzní geokodér. |
> | postgis\_topologie | PostGIS topologie prostorové typy a funkce. |


## <a name="pg_stat_statements"></a>pg_stat_statements
[Rozšíření\_příkazů\_pg stat](https://www.postgresql.org/docs/current/pgstatstatements.html) je předinstalováno na každé databázi Azure pro postgreSQL server, aby vám poskytlo prostředky sledování statistik provádění příkazů SQL.

Nastavení `pg_stat_statements.track` určuje, jaké příkazy jsou počítány rozšíření. Výchozí hodnota `top`je , což znamená, že jsou sledovány všechny příkazy vydané přímo klienty. Další dvě úrovně `none` sledování `all`jsou a . Toto nastavení lze konfigurovat jako parametr serveru prostřednictvím [portálu Azure nebo](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Existuje kompromis mezi informaceo o spuštění dotazu pg_stat_statements poskytuje a vliv na výkon serveru, protože protokoluje každý příkaz SQL. Pokud rozšíření pg_stat_statements aktivně nepoužíváte, doporučujeme nastavit `pg_stat_statements.track` program `none`. Některé služby monitorování třetích stran mohou spoléhat na pg_stat_statements při poskytování přehledů o výkonu dotazů, proto potvrďte, zda se jedná o případ pro vás nebo ne.

## <a name="dblink-and-postgres_fdw"></a>dblink a postgres_fdw
Pomocí dblinka a postgres_fdw se můžete připojit z jednoho postgreSQL serveru k jinému nebo k jiné databázi na stejném serveru. Přijímající server musí povolit připojení z odesílajícího serveru prostřednictvím brány firewall. Chcete-li tato rozšíření použít k připojení mezi servery Azure Database for PostgreSQL, nastavte **povolit přístup ke službám Azure** na ZAPNUTO. Toto nastavení je také nutné zapnout, pokud chcete použít rozšíření pro opakování zpět na stejný server. Nastavení **Povolit přístup ke službám Azure** najdete na stránce portálu Azure pro server Postgres v části **Zabezpečení připojení**. Soustružení **Povolit přístup ke službám Azure** on whitelists všechny IP adresy Azure.

V současné době odchozí připojení z Databáze Azure pro PostgreSQL nejsou podporovány, s výjimkou připojení k jiným serverům Azure Database for PostgreSQL.
