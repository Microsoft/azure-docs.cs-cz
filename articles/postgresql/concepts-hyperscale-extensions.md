---
title: Rozšíření – Citus (Velká měřítko) – Azure Database for PostgreSQL
description: V této části najdete popis možnosti rozšíření funkcí databáze pomocí rozšíření v Azure Database for PostgreSQL-Citus (škálování).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 4f7a961b04290bd17657949877c0b81bc281da50
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975546"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Rozšíření PostgreSQL v Azure Database for PostgreSQL – škálovatelné (Citus)

PostgreSQL poskytuje možnost rozšíření funkcí databáze pomocí rozšíření. Rozšíření umožňují sdružování více souvisejících objektů SQL dohromady v jednom balíčku, který se dá načíst nebo odebrat z databáze jediným příkazem. Po načtení do databáze nástroje můžou rozšíření fungovat jako integrované funkce. Další informace o rozšířeních PostgreSQL najdete v tématu věnovaném [sbalení objektů souvisejících s rozšířením](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Použití rozšíření PostgreSQL

Aby bylo možné použít rozšíření PostgreSQL, musí být v databázi nainstalovány. Pro instalaci konkrétního rozšíření spusťte pomocí příkazu [vytvořit rozšíření](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) z nástroje psql, aby se zabalené objekty načetly do vaší databáze.

Azure Database for PostgreSQL – Citus () aktuálně podporuje podmnožinu přípon klíčů, jak je uvedeno zde. Jiná rozšíření než ta, která jsou uvedena, nejsou podporována. Pomocí Azure Database for PostgreSQL nemůžete vytvořit vlastní rozšíření.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Rozšíření podporovaná nástrojem Azure Database for PostgreSQL

V následujících tabulkách jsou uvedeny standardní rozšíření PostgreSQL, která jsou aktuálně podporována nástrojem Azure Database for PostgreSQL. Tyto informace jsou také k dispozici po spuštění `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Datové typy – rozšíření

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Poskytuje znakový typ řetězce bez rozlišení velkých a malých písmen. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Poskytuje datový typ pro multidimenzionální datové krychle. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Poskytuje datový typ pro ukládání sad párů klíč-hodnota. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Poskytuje typy dat pro mezinárodní standardy číslování produktů. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Údržba Large Object. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Poskytuje datový typ pro hierarchické struktury podobné stromu. |
> | [SEG –](https://www.postgresql.org/docs/current/seg.html) | Datový typ reprezentující segmenty čar nebo intervaly s plovoucí desetinnou čárkou. |
> | [Nejlepší](https://github.com/citusdata/postgresql-topn/) | Zadejte pro JSONB Top-n. |

### <a name="full-text-search-extensions"></a>Rozšíření fulltextového vyhledávání

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [DICT –\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Poskytuje šablonu slovníku pro hledání textu pro celá čísla. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Šablona slovníku pro hledání textu pro zpracování rozšířeného synonyma |
> | [odakcent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Vyhledávací slovník textu, který z lexemes odebere zvýraznění (znaménka diakritiky). |

### <a name="functions-extensions"></a>Rozšíření funkcí

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funkce pro pole AutoIncrement. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Poskytuje způsob, jak vypočítat Skvělé vzdálenosti na povrchu země. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Poskytuje několik funkcí k určení podobností a vzdálenosti mezi řetězci. |
> | [Vložit\_uživatelské jméno](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funkce pro sledování, kdo změnil tabulku |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Celočíselný agregátor a enumerátor (zastaralé). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Poskytuje funkce a operátory pro manipulaci s poli typu Integer bez hodnoty null. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funkce pro sledování času poslední změny. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Poskytuje kryptografické funkce. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Spravuje dělené tabulky podle času nebo ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Poskytuje funkce a operátory pro určení podobnosti alfanumerického textu na základě odpovídajícího formátu Hiragana. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funkce pro implementaci referenční integrity (zastaralé). |
> | Analýza\_relací | Funkce pro dotazování na pole hstore |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Poskytuje funkce, které pracují s celými tabulkami, včetně křížového. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Aktivována oznámení o změně. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funkce pro implementaci času na cestách |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Generuje univerzálně jedinečné identifikátory (UUID). |

### <a name="hyperscale-extensions"></a>Rozšíření s škálovatelným škálováním

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Distribuovaná databáze Citus |
> | horizontálních oddílů\_– Nástroj pro vyrovnávání zatížení | Bezpečně vyvážit data ve skupině serverů v případě přidávání nebo odebírání uzlů. |

### <a name="index-types-extensions"></a>Rozšíření typů indexu

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Bloom metoda přístupu – index založený na souboru signatury. |
> | [BTREE\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Poskytuje ukázkové třídy operátorů GIN, které implementují chování typu B-Tree pro určité typy dat. |
> | [BTREE\_registr](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Poskytuje třídy operátoru indexového indexu, které implementují B-Tree. |

### <a name="language-extensions"></a>Jazyková rozšíření

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PgSQLový jazyk PL/spustitelný. |

### <a name="miscellaneous-extensions"></a>Různá rozšíření

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funkce správy pro PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funkce pro ověření integrity vztahu |
> | [soubor\_FDW](https://www.postgresql.org/docs/current/file-fdw.html) | Obálka cizích dat pro přístup plochých souborů |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Zkontrolujte obsah stránek databáze na nízké úrovni. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Poskytuje způsob, jak zkoumat, co se děje ve sdílené mezipaměti vyrovnávací paměti v reálném čase. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Plánovač úloh pro PostgreSQL. |
> | [PG\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Projděte si mapu volného místa (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Poskytuje způsob, jak načíst data relace do mezipaměti vyrovnávací paměti. |
> | [PG\_stat – příkazy\_](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Poskytuje způsob, jak sledovat statistiku provádění všech příkazů SQL provedených serverem. Informace o tomto rozšíření najdete v části "pg_stat_statements". |
> | [\_viditelnost stránky](https://www.postgresql.org/docs/current/pgvisibility.html) | Projděte si informace o viditelnosti mapy viditelnosti (VM) a na úrovni stránky. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Poskytuje způsob zobrazení informací o uzamykání na úrovni řádků. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Poskytuje způsob zobrazení statistik na úrovni řazené kolekce členů. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Obálka cizích dat slouží k přístupu k datům uloženým na externích PostgreSQL serverech. Informace o tomto rozšíření najdete v části "dblink a postgres_fdw".|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informace o certifikátech SSL. |
> | [TSM\_řádky\_systému](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE – metoda, která přijímá počet řádků jako limit. |
> | [TSM\_systémový čas\_](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE – metoda, která akceptuje dobu v milisekundách jako limit. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Poskytuje způsob vytváření hypotetických indexů, které nenákladují na procesor ani na disk. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Modul, který podporuje připojení k ostatním databázím PostgreSQL v rámci relace databáze. Informace o tomto rozšíření najdete v části "dblink a postgres_fdw". |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Dotazování XPath a transformace XSLT. |


### <a name="postgis-extensions"></a>Rozšíření PostGIS

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), PostGIS\_topologie, PostGIS\_tygr\_al Code, PostGIS\_sfcgal | Prostorové a geografické objekty pro PostgreSQL. |
> | Adresa\_standardizace, adresa\_normalizační\_data\_USA | Slouží k analýze adresy na prvky prvku. Slouží k podpoře kroku pro normalizaci adres geografického kódování. |
> | postgis\_sfcgal | PostGIS funkce SFCGAL. |
> | postgis\_tiger\_geocoder | PostGIS tygr, INCODE a Reverse INCODE. |
> | topologie\_PostGIS | Prostorové typy a funkce topologie PostGIS. |


## <a name="pg_stat_statements"></a>pg_stat_statements
[Rozšíření pg\_stat\_Statements](https://www.postgresql.org/docs/current/pgstatstatements.html) se v každém Azure Database for PostgreSQL serveru předem načítají a poskytuje vám prostředky pro sledování statistik provádění příkazů SQL.

Nastavení `pg_stat_statements.track` řídí, které příkazy jsou počítány rozšířením. Ve výchozím nastavení je `top`, což znamená, že jsou sledovány všechny příkazy vystavené přímo klienty. Tyto dvě úrovně sledování jsou `none` a `all`. Toto nastavení se dá nakonfigurovat jako parametr serveru prostřednictvím [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) nebo rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Existují kompromisy mezi informacemi o provádění dotazů, které pg_stat_statements poskytuje, a vlivem na výkon serveru při zaznamenání jednotlivých příkazů SQL. Pokud nepoužíváte rozšíření pg_stat_statements, doporučujeme nastavit `pg_stat_statements.track` na `none`. Některé služby monitorování třetích stran se můžou spoléhat na pg_stat_statements pro poskytování přehledů výkonu dotazů, takže ověřte, jestli se jedná o případ nebo ne.

## <a name="dblink-and-postgres_fdw"></a>dblink a postgres_fdw
Pomocí dblink a postgres_fdw se můžete připojit z jednoho serveru PostgreSQL k druhému nebo do jiné databáze na stejném serveru. Přijímající server musí v bráně firewall umožňovat připojení z odesílajícího serveru. Pokud chcete použít tato rozšíření pro připojení mezi Azure Database for PostgreSQL servery, nastavte možnost **Povolení přístupu ke službám Azure** na zapnuto. Toto nastavení musíte zapnout také v případě, že chcete použít rozšíření pro návrat ke stejnému serveru. Nastavení **Povolení přístupu ke službám Azure** najdete na stránce Azure Portal pro server Postgres v části **zabezpečení připojení**. Povolení **přístupu ke službám Azure** na seznamu povolených všech IP adres Azure.

V současné době se odchozí připojení z Azure Database for PostgreSQL nepodporují, s výjimkou připojení k jiným serverům Azure Database for PostgreSQL.
