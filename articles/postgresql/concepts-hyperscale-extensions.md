---
title: Rozšíření – Citus (Velká měřítko) – Azure Database for PostgreSQL
description: V této části najdete popis možnosti rozšíření funkcí databáze pomocí rozšíření v Azure Database for PostgreSQL-Citus (škálování).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 221d8b1d9fdd40a71bcfdeed57c02451e44052f2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012758"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Rozšíření PostgreSQL v Azure Database for PostgreSQL – škálovatelné (Citus)

PostgreSQL umožňuje rozšířit funkce databáze přidáním rozšíření. Rozšíření umožňují sdružování více souvisejících objektů SQL dohromady v jednom balíčku, který se dá načíst nebo odebrat z databáze jediným příkazem. Po načtení do databáze rozšíření fungují jako integrované funkce. Další informace o rozšířeních PostgreSQL najdete v tématu věnovaném [sbalení objektů souvisejících s rozšířením](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Použití rozšíření PostgreSQL

Aby bylo možné použít rozšíření PostgreSQL, musí být v databázi nainstalovány. Pro instalaci konkrétního rozšíření spusťte příkaz [Create Extension](https://www.postgresql.org/docs/current/static/sql-createextension.html) z nástroje psql, který načte zabalené objekty do vaší databáze.

Azure Database for PostgreSQL – Citus () aktuálně podporuje podmnožinu přípon klíčů, jak je uvedeno zde. Jiná rozšíření než ta, která jsou uvedena, nejsou podporována. Pomocí Azure Database for PostgreSQL nemůžete vytvořit vlastní rozšíření.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Rozšíření podporovaná nástrojem Azure Database for PostgreSQL

V následujících tabulkách jsou uvedeny standardní rozšíření PostgreSQL, která jsou aktuálně podporována nástrojem Azure Database for PostgreSQL. Tyto informace jsou také k dispozici v systému `SELECT * FROM pg_available_extensions;` .

Verze jednotlivých rozšíření nainstalovaných ve skupině serverů se někdy liší v závislosti na verzi PostgreSQL (11, 12 nebo 13). V tabulce jsou uvedeny verze rozšíření na verzi databáze.

### <a name="citus-extension"></a>Rozšíření Citus

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citus](https://github.com/citusdata/citus) | Distribuovaná databáze Citus | 9,5-1 | 9,5-1 | 10.0 – 2 |

### <a name="data-types-extensions"></a>Datové typy – rozšíření

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Poskytuje znakový typ řetězce bez rozlišení velkých a malých písmen. | 1.5 | 1.6 | 1.6 |
> | [datov](https://www.postgresql.org/docs/current/static/cube.html) | Poskytuje datový typ pro multidimenzionální datové krychle. | 1.4 | 1.4 | 1.4 |
> | [hll](https://github.com/citusdata/postgresql-hll) | Poskytuje strukturu dat HyperLogLog. | 2,14 | 2.15 | 2.15 |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Poskytuje datový typ pro ukládání sad párů klíč-hodnota. | 1.5 | 1.6 | 1.7 |
> | [není](https://www.postgresql.org/docs/current/static/isn.html) | Poskytuje typy dat pro mezinárodní standardy číslování produktů. | 1.2 | 1.2 | 1.2 |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | Údržba Large Object. | 1.1 | 1.1 | 1.1 |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Poskytuje datový typ pro hierarchické struktury podobné stromu. | 1.1 | 1,1 | 1,2 |
> | [SEG –](https://www.postgresql.org/docs/current/seg.html) | Datový typ reprezentující segmenty čar nebo intervaly s plovoucí desetinnou čárkou. | 1.3 | 1.3 | 1.3 |
> | [tdigest](https://github.com/tvondra/tdigest) | Datový typ pro akumulaci statistik na základě rozsahu, jako jsou quantiles a oříznutí. | 1.0 | 1.0 | 1.0 |
> | [Nejlepší](https://github.com/citusdata/postgresql-topn/) | Zadejte pro JSONB Top-n. | 2.2.2 | kládají | kládají |

### <a name="full-text-search-extensions"></a>Rozšíření fulltextového vyhledávání

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [DICT – \_ int](https://www.postgresql.org/docs/current/static/dict-int.html) | Poskytuje šablonu slovníku pro hledání textu pro celá čísla. | 1.0 | 1.0 | 1.0 |
> | [DICT – \_ xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Šablona slovníku pro hledání textu pro zpracování rozšířeného synonyma | 1.0 | 1.0 | 1.0 |
> | [odakcent](https://www.postgresql.org/docs/current/static/unaccent.html) | Vyhledávací slovník textu, který z lexemes odebere zvýraznění (znaménka diakritiky). | 1.1 | 1.1 | 1.1 |

### <a name="functions-extensions"></a>Rozšíření funkcí

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funkce pro pole AutoIncrement. | 1.0 | 1.0 | 1.0 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Poskytuje způsob, jak vypočítat Skvělé vzdálenosti na povrchu země. | 1.1 | 1.1 | 1.1 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Poskytuje několik funkcí k určení podobností a vzdálenosti mezi řetězci. | 1.1 | 1.1 | 1.1 |
> | [vložit \_ uživatelské jméno](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funkce pro sledování, kdo změnil tabulku | 1.0 | 1.0 | 1.0 |
> | [deznačka](https://www.postgresql.org/docs/current/intagg.html) | Celočíselný agregátor a enumerátor (zastaralé). | 1.1 | 1.1 | 1.1 |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Poskytuje funkce a operátory pro manipulaci s poli typu Integer bez hodnoty null. | 1.2 | 1.2 | 1.3 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funkce pro sledování času poslední změny. | 1.0 | 1.0 | 1.0 |
> | [\_část pg](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Spravuje dělené tabulky podle času nebo ID. | 4.1 | 4.4.1 | 4.4.1 |
> | [PG \_ trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Poskytuje funkce a operátory pro určení podobnosti alfanumerického textu na základě odpovídajícího formátu Hiragana. | 1.4 | 1.4 | 1.5 |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Poskytuje kryptografické funkce. | 1.3 | 1.3 | 1.3 |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funkce pro implementaci referenční integrity (zastaralé). | 1.0 | 1.0 | 1.0 |
> | \_Analýza relací | Funkce pro dotazování na pole hstore | | | |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Poskytuje funkce, které pracují s celými tabulkami, včetně křížového. | 1.0 | 1.0 | 1.0 |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Aktivována oznámení o změně. | 1.0 | 1.0 | 1.0 |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funkce pro implementaci času na cestách | 1.0 | | |
> | [UUID – OSSP](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Generuje univerzálně jedinečné identifikátory (UUID). | 1.1 | 1.1 | 1.1 |

### <a name="index-types-extensions"></a>Rozšíření typů indexu

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Bloom metoda přístupu – index založený na souboru signatury. | 1.0 | 1.0 | 1.0 |
> | [BTREE \_ gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Poskytuje ukázkové třídy operátorů GIN, které implementují chování typu B-Tree pro určité typy dat. | 1.3 | 1.3 | 1.3 |
> | [BTREE \_ registr](https://www.postgresql.org/docs/current/static/btree-gist.html) | Poskytuje třídy operátoru indexového indexu, které implementují B-Tree. | 1.5 | 1.5 | 1.5 |

### <a name="language-extensions"></a>Jazyková rozšíření

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PgSQLový jazyk PL/spustitelný. | 1.0 | 1.0 | 1.0 |

### <a name="miscellaneous-extensions"></a>Různá rozšíření

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funkce správy pro PostgreSQL. | 2.0 | 2.0 | 2.1 |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funkce pro ověření integrity vztahu | 1,1 | 1,2 | 1.2 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Modul, který podporuje připojení k ostatním databázím PostgreSQL v rámci relace databáze. Informace o tomto rozšíření najdete v části "dblink a postgres_fdw". | 1.2 | 1.2 | 1.2 |
> | [\_FDW souboru](https://www.postgresql.org/docs/current/file-fdw.html) | Obálka cizích dat pro přístup plochých souborů | 1.0 | 1.0 | 1.0 |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Zkontrolujte obsah stránek databáze na nízké úrovni. | 1.7 | 1.7 | 1.8 |
> | [PG \_ buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Poskytuje způsob, jak zkoumat, co se děje ve sdílené mezipaměti vyrovnávací paměti v reálném čase. | 1.3 | 1.3 | 1.3 |
> | [PG \_ cron](https://github.com/citusdata/pg_cron) | Plánovač úloh pro PostgreSQL. | 1.1 | 1.3 | 1.3 |
> | [PG \_ freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Projděte si mapu volného místa (FSM). | 1.2 | 1.2 | 1.2 |
> | [zahřívání na str \_](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Poskytuje způsob, jak načíst data relace do mezipaměti vyrovnávací paměti. | 1.2 | 1.2 | 1.2 |
> | [\_příkazy pg stat \_](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Poskytuje způsob, jak sledovat statistiku provádění všech příkazů SQL provedených serverem. Informace o tomto rozšíření najdete v části "pg_stat_statements". | 1.6 | 1.7 | 1.8 |
> | [\_viditelnost stránky](https://www.postgresql.org/docs/current/pgvisibility.html) | Projděte si informace o viditelnosti mapy viditelnosti (VM) a na úrovni stránky. | 1.2 | 1.2 | 1.2 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Poskytuje způsob zobrazení informací o uzamykání na úrovni řádků. | 1.2 | 1.2 | 1.2 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Poskytuje způsob zobrazení statistik na úrovni řazené kolekce členů. | 1.5 | 1.5 | 1.5 |
> | [Postgres \_ FDW](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Obálka cizích dat slouží k přístupu k datům uloženým na externích PostgreSQL serverech. Informace o tomto rozšíření najdete v části "dblink a postgres_fdw".| 1.0 | 1.0 | 1.0 |
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informace o certifikátech TLS/SSL. | 1.2 | 1.2 | 1.2 |
> | [TSM \_ systémové \_ řádky](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE – metoda, která přijímá počet řádků jako limit. | 1.0 | 1.0 | 1.0 |
> | [TSM \_ systémový \_ čas](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE – metoda, která akceptuje dobu v milisekundách jako limit. | 1.0 | 1.0 | 1.0 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Dotazování XPath a transformace XSLT. | 1.1 | 1.1 | 1.1 |


### <a name="postgis-extensions"></a>Rozšíření PostGIS

> [!div class="mx-tableFixed"]
> | **Linka** | **Popis** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [PostGIS](https://www.postgis.net/), PostGIS, \_ topologie PostGIS \_ tygr \_ , PostGIS \_ sfcgal | Prostorové a geografické objekty pro PostgreSQL. | 2.5.1 | 3.0.3 | 3.0.3 |
> | adresa pro \_ standardizaci adresování, adresové \_ \_ data \_ | Slouží k analýze adresy na prvky prvku. Slouží k podpoře kroku pro normalizaci adres geografického kódování. | 2.5.1 | 3.0.3 | 3.0.3 |
> | PostGIS \_ sfcgal | PostGIS funkce SFCGAL. | 2.5.1 | 3.0.3 | 3.0.3 |
> | PostGIS \_ tygr, \_ INCODE | PostGIS tygr, INCODE a Reverse INCODE. | 2.5.1 | 3.0.3 | 3.0.3 |
> | \_topologie PostGIS | Prostorové typy a funkce topologie PostGIS. | 2.5.1 | 3.0.3 | 3.0.3 |


## <a name="pg_stat_statements"></a>pg_stat_statements
[ \_ \_ Rozšíření příkazů pg stat](https://www.postgresql.org/docs/current/pgstatstatements.html) je předem načteno na každý Azure Database for PostgreSQL Server, který poskytuje prostředky pro sledování statistik provádění příkazů SQL.

Nastavení `pg_stat_statements.track` Určuje, které příkazy se započítávají pomocí rozšíření. Výchozí hodnota je `top` , což znamená, že jsou sledovány všechny příkazy vystavené přímo klienty. Tyto dvě úrovně sledování jsou `none` a `all` . Toto nastavení se dá nakonfigurovat jako parametr serveru prostřednictvím [Azure Portal](./howto-configure-server-parameters-using-portal.md) nebo rozhraní příkazového [řádku Azure CLI](./howto-configure-server-parameters-using-cli.md).

Existují kompromisy mezi informacemi o provádění dotazů, které pg_stat_statements poskytuje, a vlivem na výkon serveru při zaznamenání jednotlivých příkazů SQL. Pokud nepoužíváte rozšíření pg_stat_statements, doporučujeme, abyste nastavili `pg_stat_statements.track` na `none` . Některé služby monitorování třetích stran se můžou spoléhat na pg_stat_statements pro poskytování přehledů výkonu dotazů, takže ověřte, jestli se jedná o případ nebo ne.

## <a name="dblink-and-postgres_fdw"></a>dblink a postgres_fdw

Pomocí dblink a Postgres FDW se můžete \_ připojit z jednoho serveru PostgreSQL k druhému nebo do jiné databáze na stejném serveru.  Přijímající server musí v bráně firewall umožňovat připojení z odesílajícího serveru.  Pokud chcete tato rozšíření použít pro připojení mezi skupinami serverů Azure Database for PostgreSQL nebo Citus (), nastavte možnost **dovolit službám a prostředkům Azure přístup k této skupině serverů (nebo serveru)** na zapnuto.  Toto nastavení musíte zapnout také v případě, že chcete použít rozšíření pro návrat ke stejnému serveru.
Nastavení **Povolení služeb a prostředků Azure pro přístup k této skupině serverů** najdete na stránce Azure Portal pro skupinu serverů Citus () v části **síť**.  V současné době se odchozí připojení z Azure Database for PostgreSQL jednom serveru a Citus () nepodporují, s výjimkou připojení k ostatním serverům Azure Database for PostgreSQL a skupinám serverů Citus ().
