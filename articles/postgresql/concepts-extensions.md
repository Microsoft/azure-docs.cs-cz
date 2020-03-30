---
title: Rozšíření – databáze Azure pro PostgreSQL – jeden server
description: Informace o dostupných rozšířeních Postgres v Azure Database for PostgreSQL – single server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a12738f5de783c8a34718b8d9cb4bbf54f230589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201267"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Rozšíření PostgreSQL v Azure Database for PostgreSQL – jeden server
PostgreSQL poskytuje možnost rozšířit funkce databáze pomocí rozšíření. Rozšíření sdružují více souvisejících objektů SQL do jednoho balíčku, který lze načíst nebo odebrat z databáze pomocí jediného příkazu. Po načtení do databáze rozšíření fungovat jako vestavěné funkce.

## <a name="how-to-use-postgresql-extensions"></a>Jak používat rozšíření PostgreSQL
Před použitím musí být v databázi nainstalována rozšíření PostgreSQL. Chcete-li nainstalovat konkrétní rozšíření, spusťte příkaz [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) z nástroje psql a načtěte zabalené objekty do databáze.

Azure Database for PostgreSQL podporuje podmnožinu rozšíření klíčů, jak je uvedeno níže. Tyto informace jsou také `SELECT * FROM pg_available_extensions;`k dispozici spuštěním . Rozšíření nad rámec uvedených nejsou podporovány. V Azure Database pro PostgreSQL nemůžete vytvořit vlastní rozšíření.

## <a name="postgres-11-extensions"></a>Postgres 11 rozšíření

Následující rozšíření jsou k dispozici v Azure Database pro postgreSQL servery, které mají Postgres verze 11. 

> [!div class="mx-tableFixed"]
> | **Linka**| **Verze rozšíření** | **Popis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Slouží k analýzě adresy do základních prvků. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Příklad datové sady Dataset standardizeru V USA|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | podpora indexování běžných datových typů v GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1,5             | podpora indexování běžných datových typů v GiST|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1,5             | datový typ pro řetězce znaků bez rozpoznávání velkých a malých písmen|
> |[Datové krychle](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | datový typ pro vícerozměrné krychle|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | připojení k dalším databázím PostgreSQL z databáze|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | šablona slovníku pro vyhledávání textu pro celá čísla|
> |[zemní vzdálenost](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | vypočítat velké kruhové vzdálenosti na povrchu Země|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | určit podobnosti a vzdálenost mezi řetězci|
> |[hobchod](https://www.postgresql.org/docs/11/hstore.html)                       | 1,5             | datový typ pro ukládání sad (klíč, hodnota) párů|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Hypotetické indexy pro PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | funkce, operátory a podpora indexu pro 1D pole celočísel|
> |[není](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | datové typy pro mezinárodní standardy číslování produktů|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | datový typ pro hierarchické stromové struktury|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkce a operátory, které emulují podmnožinu funkcí a balíčků z komerčních RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | poskytuje funkce auditování|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | kryptografické funkce|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting Rozšíření|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | zobrazit informace o uzamčení na úrovni řádků|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1,5             | zobrazit statistiky na úrovni řazené kolekce členů|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | zkontrolujte sdílenou vyrovnávací paměťovou vyrovnávací paměť|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Rozšíření pro správu dělených tabulek podle času nebo ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | předteplá data vztahu|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | sledovat statistiku provádění všech provedených příkazů SQL|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | měření podobnosti textu a vyhledávání indexů na základě trigramů|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Procedurální jazyk PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (v8) důvěryhodný procedurální jazyk|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | Geometrie, geografie a rastrových prostorových typů a funkcí postGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL funkce|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tygří geokodér a reverzní geokodér|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS topologie prostorové typy a funkce|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | obálka cizích dat pro vzdálené servery PostgreSQL|
> |[stolní func](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funkce, které manipulují s celými tabulkami, včetně křížových|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2             | Umožňuje škálovatelné břitové destičky a složité dotazy pro data časových řad.|
> |[odkcent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | slovník pro vyhledávání textu, který odstraňuje zvýraznění|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | generovat univerzálně jedinečné identifikátory (UUID)|

## <a name="postgres-10-extensions"></a>Postgres 10 rozšíření 

Následující rozšíření jsou k dispozici v Azure Database pro postgreSQL servery, které mají Postgres verze 10.

> [!div class="mx-tableFixed"]
> | **Linka**| **Verze rozšíření** | **Popis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Slouží k analýzě adresy do základních prvků. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Příklad datové sady Dataset standardizeru V USA|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | podpora indexování běžných datových typů v GIN|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1,5             | podpora indexování běžných datových typů v GiST|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | datový typ pro automaticky šifrovaná hesla|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | datový typ pro řetězce znaků bez rozpoznávání velkých a malých písmen|
> |[Datové krychle](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | datový typ pro vícerozměrné krychle|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | připojení k dalším databázím PostgreSQL z databáze|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | šablona slovníku pro vyhledávání textu pro celá čísla|
> |[zemní vzdálenost](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | vypočítat velké kruhové vzdálenosti na povrchu Země|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | určit podobnosti a vzdálenost mezi řetězci|
> |[hobchod](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | datový typ pro ukládání sad (klíč, hodnota) párů|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypotetické indexy pro PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | funkce, operátory a podpora indexu pro 1D pole celočísel|
> |[není](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | datové typy pro mezinárodní standardy číslování produktů|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | datový typ pro hierarchické stromové struktury|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkce a operátory, které emulují podmnožinu funkcí a balíčků z komerčních RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | poskytuje funkce auditování|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | kryptografické funkce|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting Rozšíření|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | zobrazit informace o uzamčení na úrovni řádků|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1,5             | zobrazit statistiky na úrovni řazené kolekce členů|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | zkontrolujte sdílenou vyrovnávací paměťovou vyrovnávací paměť|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozšíření pro správu dělených tabulek podle času nebo ID|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | předteplá data vztahu|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | sledovat statistiku provádění všech provedených příkazů SQL|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | měření podobnosti textu a vyhledávání indexů na základě trigramů|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | Procedurální jazyk PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) důvěryhodný procedurální jazyk|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | Geometrie, geografie a rastrových prostorových typů a funkcí postGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL funkce|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS tygří geokodér a reverzní geokodér|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | PostGIS topologie prostorové typy a funkce|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | obálka cizích dat pro vzdálené servery PostgreSQL|
> |[stolní func](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | funkce, které manipulují s celými tabulkami, včetně křížových|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Umožňuje škálovatelné břitové destičky a složité dotazy pro data časových řad.|
> |[odkcent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | slovník pro vyhledávání textu, který odstraňuje zvýraznění|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | generovat univerzálně jedinečné identifikátory (UUID)|

## <a name="postgres-96-extensions"></a>Rozšíření Postgres 9.6 

Následující rozšíření jsou k dispozici v Azure Database pro postgreSQL servery, které mají Postgres verze 9.6.

> [!div class="mx-tableFixed"]
> | **Linka**| **Verze rozšíření** | **Popis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Slouží k analýzě adresy do základních prvků. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Příklad datové sady Dataset standardizeru V USA|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | podpora indexování běžných datových typů v GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | podpora indexování běžných datových typů v GiST|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | datový typ pro automaticky šifrovaná hesla|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | datový typ pro řetězce znaků bez rozpoznávání velkých a malých písmen|
> |[Datové krychle](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | datový typ pro vícerozměrné krychle|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | připojení k dalším databázím PostgreSQL z databáze|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | šablona slovníku pro vyhledávání textu pro celá čísla|
> |[zemní vzdálenost](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | vypočítat velké kruhové vzdálenosti na povrchu Země|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | určit podobnosti a vzdálenost mezi řetězci|
> |[hobchod](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | datový typ pro ukládání sad (klíč, hodnota) párů|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypotetické indexy pro PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | funkce, operátory a podpora indexu pro 1D pole celočísel|
> |[není](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | datové typy pro mezinárodní standardy číslování produktů|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | datový typ pro hierarchické stromové struktury|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkce a operátory, které emulují podmnožinu funkcí a balíčků z komerčních RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | poskytuje funkce auditování|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | kryptografické funkce|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting Rozšíření|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | zobrazit informace o uzamčení na úrovni řádků|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | zobrazit statistiky na úrovni řazené kolekce členů|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | zkontrolujte sdílenou vyrovnávací paměťovou vyrovnávací paměť|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozšíření pro správu dělených tabulek podle času nebo ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | předteplá data vztahu|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | sledovat statistiku provádění všech provedených příkazů SQL|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | měření podobnosti textu a vyhledávání indexů na základě trigramů|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | Procedurální jazyk PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) důvěryhodný procedurální jazyk|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | Geometrie, geografie a rastrových prostorových typů a funkcí postGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL funkce|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS tygří geokodér a reverzní geokodér|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | PostGIS topologie prostorové typy a funkce|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | obálka cizích dat pro vzdálené servery PostgreSQL|
> |[stolní func](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | funkce, které manipulují s celými tabulkami, včetně křížových|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Umožňuje škálovatelné břitové destičky a složité dotazy pro data časových řad.|
> |[odkcent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | slovník pro vyhledávání textu, který odstraňuje zvýraznění|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | generovat univerzálně jedinečné identifikátory (UUID)|

## <a name="postgres-95-extensions"></a>Rozšíření Postgres 9.5 

Následující rozšíření jsou k dispozici v Azure Database pro postgreSQL servery, které mají Postgres verze 9.5.

> [!div class="mx-tableFixed"]
> | **Linka**| **Verze rozšíření** | **Popis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Slouží k analýzě adresy do základních prvků. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Příklad datové sady Dataset standardizeru V USA|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | podpora indexování běžných datových typů v GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | podpora indexování běžných datových typů v GiST|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | datový typ pro automaticky šifrovaná hesla|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | datový typ pro řetězce znaků bez rozpoznávání velkých a malých písmen|
> |[Datové krychle](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | datový typ pro vícerozměrné krychle|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | připojení k dalším databázím PostgreSQL z databáze|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | šablona slovníku pro vyhledávání textu pro celá čísla|
> |[zemní vzdálenost](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | vypočítat velké kruhové vzdálenosti na povrchu Země|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | určit podobnosti a vzdálenost mezi řetězci|
> |[hobchod](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | datový typ pro ukládání sad (klíč, hodnota) párů|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypotetické indexy pro PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | funkce, operátory a podpora indexu pro 1D pole celočísel|
> |[není](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | datové typy pro mezinárodní standardy číslování produktů|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | datový typ pro hierarchické stromové struktury|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkce a operátory, které emulují podmnožinu funkcí a balíčků z komerčních RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | poskytuje funkce auditování|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | kryptografické funkce|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting Rozšíření|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | zobrazit informace o uzamčení na úrovni řádků|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | zobrazit statistiky na úrovni řazené kolekce členů|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | zkontrolujte sdílenou vyrovnávací paměťovou vyrovnávací paměť|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozšíření pro správu dělených tabulek podle času nebo ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | předteplá data vztahu|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | sledovat statistiku provádění všech provedených příkazů SQL|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | měření podobnosti textu a vyhledávání indexů na základě trigramů|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | Procedurální jazyk PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | Geometrie, geografie a rastrových prostorových typů a funkcí postGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL funkce|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS tygří geokodér a reverzní geokodér|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | PostGIS topologie prostorové typy a funkce|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | obálka cizích dat pro vzdálené servery PostgreSQL|
> |[stolní func](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | funkce, které manipulují s celými tabulkami, včetně křížových|
> |[odkcent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | slovník pro vyhledávání textu, který odstraňuje zvýraznění|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | generovat univerzálně jedinečné identifikátory (UUID)|


## <a name="pg_stat_statements"></a>pg_stat_statements
[Rozšíření pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) je předinstalováno na každé databázi Azure pro postgreSQL server, aby vám poskytlo způsob sledování statistik y provádění příkazů SQL.
Nastavení `pg_stat_statements.track`, které řídí, jaké příkazy jsou počítány rozšířením, výchozí `top`hodnoty , což znamená, že jsou sledovány všechny příkazy vydané přímo klienty. Další dvě úrovně `none` sledování `all`jsou a . Toto nastavení lze konfigurovat jako parametr serveru prostřednictvím [portálu Azure nebo](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Existuje kompromis mezi informace o spuštění dotazu pg_stat_statements poskytuje a dopad na výkon serveru, protože protokoluje každý příkaz SQL. Pokud rozšíření pg_stat_statements aktivně nepoužíváte, doporučujeme nastavit `pg_stat_statements.track` `none`program . Všimněte si, že některé služby monitorování třetích stran mohou spoléhat na pg_stat_statements poskytovat přehledy o výkonu dotazu, proto zkontrolujte, zda se jedná o případ pro vás, nebo ne.

## <a name="dblink-and-postgres_fdw"></a>dblink a postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) a [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) umožňují připojení z jednoho PostgreSQL serveru k jinému nebo k jiné databázi na stejném serveru. Přijímající server musí povolit připojení z odesílajícího serveru prostřednictvím brány firewall. Při použití těchto rozšíření pro připojení mezi Azure Database pro servery PostgreSQL, to lze provést nastavením "Povolit přístup ke službám Azure" na ON. To je také potřeba, pokud chcete použít rozšíření smyčky zpět na stejný server. Nastavení "Povolit přístup ke službám Azure" najdete na stránce portálu Azure pro server Postgres v části Zabezpečení připojení. Zapnutí "Povolit přístup ke službám Azure" umístí všechny IP adresy Azure na seznam povolených.

V současné době odchozí připojení z Databáze Azure pro PostgreSQL nejsou podporovány, s výjimkou připojení k jiným serverům Azure Database for PostgreSQL.

## <a name="uuid"></a>Uuid
Pokud plánujete použít `uuid_generate_v4()` z [rozšíření uuid-ossp](https://www.postgresql.org/docs/current/uuid-ossp.html), `gen_random_uuid()` zvažte porovnání s z [rozšíření pgcrypto](https://www.postgresql.org/docs/current/pgcrypto.html) pro výhody výkonu.

## <a name="pgaudit"></a>pgAudit
[Rozšíření pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) poskytuje protokolování session a auditu objektů. Chcete-li se dozvědět, jak používat toto rozšíření v Azure Database pro PostgreSQL, navštivte [článek koncepty auditování](concepts-audit.md). 

## <a name="pg_prewarm"></a>pg_prewarm
Rozšíření pg_prewarm načte relační data do mezipaměti. Předběžné vysušující mezipaměti znamená, že vaše dotazy mají lepší dobu odezvy při prvním spuštění po restartování. V Postgres 10 a nižší, předehřívání se provádí ručně pomocí [funkce předvídání](https://www.postgresql.org/docs/10/pgprewarm.html).

V Postgres 11 a vyšší, můžete nakonfigurovat prewarming se stane [automaticky](https://www.postgresql.org/docs/current/pgprewarm.html). Je třeba zahrnout pg_prewarm `shared_preload_libraries` do seznamu parametrů a restartovat server, chcete-li použít změnu. Parametry lze nastavit z [portálu Azure](howto-configure-server-parameters-using-portal.md), [CLI](howto-configure-server-parameters-using-cli.md), REST API nebo ARM šablony. 

## <a name="timescaledb"></a>Časová databáze
TimescaleDB je databáze časových řad, která je zabalena jako rozšíření pro PostgreSQL. TimescaleDB poskytuje časově orientované analytické funkce, optimalizace a škálování Postgres pro úlohy časových řad.

[Další informace o TimescaleDB](https://docs.timescale.com/latest), registrované ochranné známce společnosti [Timescale, Inc.](https://www.timescale.com/). Azure Database for PostgreSQL poskytuje open source verzi Timescale. Chcete-li zjistit, které funkce časové osy jsou v této verzi k dispozici, naleznete [v porovnání produktů časové osy](https://www.timescale.com/products/).

### <a name="installing-timescaledb"></a>Instalace timescaleDB
Chcete-li nainstalovat TimescaleDB, musíte jej zahrnout do sdílených knihovnách přednačtení serveru. Změna `shared_preload_libraries` parametru Postgres vyžaduje **restartování serveru,** aby se projevilo. Parametry můžete změnit pomocí [portálu Azure nebo](howto-configure-server-parameters-using-portal.md) [rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md).

Použití [portálu Azure](https://portal.azure.com/):

1. Vyberte svůj server Azure Database for PostgreSQL.

2. Na postranním panelu vyberte **Parametry serveru**.

3. Vyhledejte `shared_preload_libraries` parametr.

4. Vyberte **možnost TimescaleDB**.

5. Vyberte **Uložit,** chcete-li zachovat změny. Jakmile je změna uložena, dostanete oznámení. 

6. Po oznámení **restartujte** server, abyste tyto změny použili. Informace o restartování serveru najdete v [tématu Restartování databáze Azure pro server PostgreSQL](howto-restart-server-portal.md).


Nyní můžete povolit TimescaleDB v databázi Postgres. Připojte se k databázi a vyjezte následující příkaz:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Pokud se zobrazí chyba, potvrďte, že jste po uložení shared_preload_libraries [restartovali server.](howto-restart-server-portal.md) 

Nyní můžete vytvořit hypertabulku TimescaleDB [od začátku](https://docs.timescale.com/getting-started/creating-hypertables) nebo migrovat [existující data časových řad v PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).

### <a name="restoring-a-timescale-database"></a>Obnovení databáze časové osy
Chcete-li obnovit databázi časové osy pomocí pg_dump a pg_restore, `timescaledb_pre_restore()` je `timescaledb_post restore()`třeba spustit dva pomocné postupy v cílové databázi: a .

Nejprve připravte cílovou databázi:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

Nyní můžete spustit pg_dump v původní databázi a pak pg_restore. Po obnovení nezapomeňte spustit následující příkaz v obnovené databázi:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Další kroky
Pokud nevidíte rozšíření, které chcete použít, dejte nám vědět. Hlasujte pro stávající žádosti nebo vytvořte nové žádosti o zpětnou vazbu v našem [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
