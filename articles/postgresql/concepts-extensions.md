---
title: Rozšíření – Azure Database for PostgreSQL – jeden server
description: Přečtěte si o dostupných rozšířeních Postgres v Azure Database for PostgreSQL-Single server
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: de395bed885804445146b01745edf5e93426cb13
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606313"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Rozšíření serveru PostgreSQL v Azure Database for PostgreSQL – jeden server
PostgreSQL poskytuje možnost rozšíření funkcí databáze pomocí rozšíření. Rozšíření je balíček, který vznikne sloučením několika souvisejících objektů SQL. Tento balíček můžete jedním příkazem do databáze načíst nebo ho z ní odebrat. Po načtení do databáze rozšíření fungují jako integrované funkce.

## <a name="how-to-use-postgresql-extensions"></a>Jak používat rozšíření PostgreSQL
Aby bylo možné použít rozšíření PostgreSQL, musí být v databázi nainstalovány. Pokud chcete určité rozšíření nainstalovat, spusťte nástrojem psql příkaz [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html), který do databáze načte zabalené objekty.

Azure Database for PostgreSQL podporuje podmnožinu rozšíření klíčů, jak je uvedeno níže. Tyto informace jsou také k dispozici v systému `SELECT * FROM pg_available_extensions;` . Rozšíření nad rámec těch, která jsou uvedena, nejsou podporována. V Azure Database for PostgreSQL nemůžete vytvořit vlastní rozšíření.

## <a name="postgres-11-extensions"></a>Rozšíření Postgres 11

V Azure Database for PostgreSQLch serverech jsou k dispozici následující rozšíření, která mají Postgres verze 11. 

> [!div class="mx-tableFixed"]
> | **Linka**| **Verze rozšíření** | **Popis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Slouží k analýze adresy na prvky prvku. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Příklad adresy pro normalizační sadu US|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | Podpora indexování běžných typů DataTypes v GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | Podpora indexování běžných typů DataTypes v registru|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | datový typ pro řetězce znaků bez rozlišení velkých a malých písmen|
> |[datov](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | datový typ pro multidimenzionální datové krychle|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | připojení k jiným databázím PostgreSQL v rámci databáze|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | Šablona slovníku pro hledání textu pro celá čísla|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | Výpočet vzdáleností skvělého kruhu na povrchu země|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | určení podobností a vzdálenosti mezi řetězci|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | datový typ pro ukládání sad párů (klíč, hodnota)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Hypotetické indexy pro PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | funkce, operátory a podpora indexu pro jednorozměrná pole s celými čísly|
> |[není](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | datové typy pro mezinárodní standardy číslování produktů|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | datový typ pro hierarchické struktury podobné stromu|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkce a operátory, které emuluje podmnožinu funkcí a balíčků z komerčních RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | poskytuje funkce auditování|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | kryptografické funkce|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | Rozšíření pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | Zobrazit informace o uzamykání na úrovni řádků|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Zobrazit statistiky na úrovni řazené kolekce členů|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | Prověřte sdílenou mezipaměť vyrovnávací paměti|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Rozšíření pro správu dělených tabulek podle času nebo ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | data předteplého vztahu|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | sledovat statistiku spuštění všech provedených příkazů SQL|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | měření podobnosti textu a hledání v indexu na základě trigrams|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Procedurální jazyk PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | Důvěryhodný procedurální jazyk PL/JavaScript (V8)|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometrii, geografické a rastrové typy a funkce|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL – funkce|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tygr, a reverzní INCODE|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Prostorové typy a funkce topologie PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | obálka s cizími daty pro vzdálené servery PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funkce, které pracují s celými tabulkami včetně křížového|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2, 1.7.4             | Umožňuje škálovatelné vkládání a složité dotazy pro data časových řad.|
> |[odakcent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | slovník hledání textu, který odebere zvýraznění|
> |[UUID – OSSP](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | generování univerzálně jedinečných identifikátorů (UUID)|

## <a name="postgres-10-extensions"></a>Rozšíření Postgres 10 

V Azure Database for PostgreSQLch serverech jsou k dispozici následující rozšíření, která mají Postgres verze 10.

> [!div class="mx-tableFixed"]
> | **Linka**| **Verze rozšíření** | **Popis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Slouží k analýze adresy na prvky prvku. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Příklad adresy pro normalizační sadu US|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | Podpora indexování běžných typů DataTypes v GIN|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | Podpora indexování běžných typů DataTypes v registru|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | datový typ pro automaticky zašifrovaná hesla|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | datový typ pro řetězce znaků bez rozlišení velkých a malých písmen|
> |[datov](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | datový typ pro multidimenzionální datové krychle|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | připojení k jiným databázím PostgreSQL v rámci databáze|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | Šablona slovníku pro hledání textu pro celá čísla|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | Výpočet vzdáleností skvělého kruhu na povrchu země|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | určení podobností a vzdálenosti mezi řetězci|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | datový typ pro ukládání sad párů (klíč, hodnota)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypotetické indexy pro PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | funkce, operátory a podpora indexu pro jednorozměrná pole s celými čísly|
> |[není](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | datové typy pro mezinárodní standardy číslování produktů|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | datový typ pro hierarchické struktury podobné stromu|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkce a operátory, které emuluje podmnožinu funkcí a balíčků z komerčních RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | poskytuje funkce auditování|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | kryptografické funkce|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | Rozšíření pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | Zobrazit informace o uzamykání na úrovni řádků|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | Zobrazit statistiky na úrovni řazené kolekce členů|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | Prověřte sdílenou mezipaměť vyrovnávací paměti|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozšíření pro správu dělených tabulek podle času nebo ID|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | data předteplého vztahu|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | sledovat statistiku spuštění všech provedených příkazů SQL|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | měření podobnosti textu a hledání v indexu na základě trigrams|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | Procedurální jazyk PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | Důvěryhodný procedurální jazyk PL/JavaScript (V8)|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | PostGIS geometrii, geografické a rastrové typy a funkce|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL – funkce|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS tygr, a reverzní INCODE|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | Prostorové typy a funkce topologie PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | obálka s cizími daty pro vzdálené servery PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | funkce, které pracují s celými tabulkami včetně křížového|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1, 1.7.4             | Umožňuje škálovatelné vkládání a složité dotazy pro data časových řad.|
> |[odakcent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | slovník hledání textu, který odebere zvýraznění|
> |[UUID – OSSP](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | generování univerzálně jedinečných identifikátorů (UUID)|

## <a name="postgres-96-extensions"></a>Rozšíření Postgres 9,6 

V Azure Database for PostgreSQLch serverech jsou k dispozici následující rozšíření, která mají Postgres verze 9,6.

> [!div class="mx-tableFixed"]
> | **Linka**| **Verze rozšíření** | **Popis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Slouží k analýze adresy na prvky prvku. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Příklad adresy pro normalizační sadu US|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | Podpora indexování běžných typů DataTypes v GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | Podpora indexování běžných typů DataTypes v registru|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | datový typ pro automaticky zašifrovaná hesla|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | datový typ pro řetězce znaků bez rozlišení velkých a malých písmen|
> |[datov](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | datový typ pro multidimenzionální datové krychle|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | připojení k jiným databázím PostgreSQL v rámci databáze|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | Šablona slovníku pro hledání textu pro celá čísla|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | Výpočet vzdáleností skvělého kruhu na povrchu země|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | určení podobností a vzdálenosti mezi řetězci|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | datový typ pro ukládání sad párů (klíč, hodnota)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypotetické indexy pro PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | funkce, operátory a podpora indexu pro jednorozměrná pole s celými čísly|
> |[není](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | datové typy pro mezinárodní standardy číslování produktů|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | datový typ pro hierarchické struktury podobné stromu|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkce a operátory, které emuluje podmnožinu funkcí a balíčků z komerčních RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | poskytuje funkce auditování|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | kryptografické funkce|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | Rozšíření pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | Zobrazit informace o uzamykání na úrovni řádků|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | Zobrazit statistiky na úrovni řazené kolekce členů|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | Prověřte sdílenou mezipaměť vyrovnávací paměti|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozšíření pro správu dělených tabulek podle času nebo ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | data předteplého vztahu|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | sledovat statistiku spuštění všech provedených příkazů SQL|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | měření podobnosti textu a hledání v indexu na základě trigrams|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | Procedurální jazyk PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | Důvěryhodný procedurální jazyk PL/JavaScript (V8)|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | PostGIS geometrii, geografické a rastrové typy a funkce|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL – funkce|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS tygr, a reverzní INCODE|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | Prostorové typy a funkce topologie PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | obálka s cizími daty pro vzdálené servery PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | funkce, které pracují s celými tabulkami včetně křížového|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1, 1.7.4             | Umožňuje škálovatelné vkládání a složité dotazy pro data časových řad.|
> |[odakcent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | slovník hledání textu, který odebere zvýraznění|
> |[UUID – OSSP](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | generování univerzálně jedinečných identifikátorů (UUID)|

## <a name="postgres-95-extensions"></a>Rozšíření Postgres 9,5

>[!NOTE]
> PostgreSQL verze 9,5 byla vyřazena.

V Azure Database for PostgreSQLch serverech jsou k dispozici následující rozšíření, která mají Postgres verze 9,5.

> [!div class="mx-tableFixed"]
> | **Linka**| **Verze rozšíření** | **Popis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Slouží k analýze adresy na prvky prvku. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Příklad adresy pro normalizační sadu US|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | Podpora indexování běžných typů DataTypes v GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | Podpora indexování běžných typů DataTypes v registru|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | datový typ pro automaticky zašifrovaná hesla|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | datový typ pro řetězce znaků bez rozlišení velkých a malých písmen|
> |[datov](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | datový typ pro multidimenzionální datové krychle|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | připojení k jiným databázím PostgreSQL v rámci databáze|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | Šablona slovníku pro hledání textu pro celá čísla|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | Výpočet vzdáleností skvělého kruhu na povrchu země|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | určení podobností a vzdálenosti mezi řetězci|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | datový typ pro ukládání sad párů (klíč, hodnota)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypotetické indexy pro PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | funkce, operátory a podpora indexu pro jednorozměrná pole s celými čísly|
> |[není](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | datové typy pro mezinárodní standardy číslování produktů|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | datový typ pro hierarchické struktury podobné stromu|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkce a operátory, které emuluje podmnožinu funkcí a balíčků z komerčních RDBMS|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | poskytuje funkce auditování|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | kryptografické funkce|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | Rozšíření pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | Zobrazit informace o uzamykání na úrovni řádků|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | Zobrazit statistiky na úrovni řazené kolekce členů|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | Prověřte sdílenou mezipaměť vyrovnávací paměti|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozšíření pro správu dělených tabulek podle času nebo ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | data předteplého vztahu|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | sledovat statistiku spuštění všech provedených příkazů SQL|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | měření podobnosti textu a hledání v indexu na základě trigrams|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | Procedurální jazyk PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | PostGIS geometrii, geografické a rastrové typy a funkce|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL – funkce|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS tygr, a reverzní INCODE|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | Prostorové typy a funkce topologie PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | obálka s cizími daty pro vzdálené servery PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | funkce, které pracují s celými tabulkami včetně křížového|
> |[odakcent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | slovník hledání textu, který odebere zvýraznění|
> |[UUID – OSSP](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | generování univerzálně jedinečných identifikátorů (UUID)|


## <a name="pg_stat_statements"></a>pg_stat_statements
[Rozšíření pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) je předem načteno na každém serveru Azure Database for PostgreSQL a poskytuje tak prostředky pro sledování statistik provádění příkazů SQL.
Nastavení `pg_stat_statements.track` , které řídí, které příkazy jsou počítány rozšířením, výchozí hodnota `top` , což znamená, že jsou sledovány všechny příkazy vydané přímo klienty. Tyto dvě úrovně sledování jsou `none` a `all` . Toto nastavení se dá nakonfigurovat jako parametr serveru prostřednictvím [Azure Portal](./howto-configure-server-parameters-using-portal.md) nebo rozhraní příkazového [řádku Azure CLI](./howto-configure-server-parameters-using-cli.md).

Mezi informacemi o spuštění dotazu pg_stat_statements poskytuje kompromis a dopad na výkon serveru při zaznamenání jednotlivých příkazů SQL. Pokud nepoužíváte rozšíření pg_stat_statements, doporučujeme, abyste nastavili `pg_stat_statements.track` na `none` . Všimněte si, že některé služby monitorování třetích stran můžou spoléhat na pg_stat_statements k poskytování přehledů o výkonu dotazů, takže ověřte, jestli se jedná o případ nebo ne.

## <a name="dblink-and-postgres_fdw"></a>dblink a postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) a [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) vám umožní připojit se z jednoho serveru PostgreSQL k druhému nebo do jiné databáze na stejném serveru. Přijímající server musí v bráně firewall umožňovat připojení z odesílajícího serveru. Pokud se tato rozšíření používají pro připojení mezi Azure Database for PostgreSQL servery, můžete to udělat nastavením možnosti "povolení přístupu ke službám Azure" na ZAPNUTo. To je také nutné v případě, že chcete použít rozšíření pro návrat ke stejnému serveru. Nastavení "povolení přístupu ke službám Azure" najdete na stránce Azure Portal pro server Postgres v části zabezpečení připojení. Zapnutím možnosti "povolení přístupu ke službám Azure" získáte všechny IP adresy Azure na seznamu povolených.

Odchozí připojení z Azure Database for PostgreSQL se v současné době nepodporují, s výjimkou připojení k ostatním Azure Database for PostgreSQLm serverům ve stejné oblasti.

## <a name="uuid"></a>uuid
Pokud plánujete použití `uuid_generate_v4()` z [rozšíření UUID-OSSP](https://www.postgresql.org/docs/current/uuid-ossp.html), zvažte porovnání s `gen_random_uuid()` [rozšířením pgcrypto](https://www.postgresql.org/docs/current/pgcrypto.html) pro výhody výkonu.

## <a name="pgaudit"></a>pgAudit
[Rozšíření pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) poskytuje protokolování auditování relací a objektů. Informace o tom, jak používat toto rozšíření v Azure Database for PostgreSQL, najdete v článku věnovaném [koncepcím auditu](concepts-audit.md). 

## <a name="pg_prewarm"></a>pg_prewarm
Rozšíření pg_prewarm načte relační data do mezipaměti. Předzpracováním vašich mezipamětí znamená, že vaše dotazy mají po restartování lepší dobu odezvy při jejich prvním spuštění. V Postgres 10 a níže se předtepla provádí ručně pomocí [předteplé funkce](https://www.postgresql.org/docs/10/pgprewarm.html).

V Postgres 11 a vyšších můžete nakonfigurovat, aby se předhřívání provádělo [automaticky](https://www.postgresql.org/docs/current/pgprewarm.html). Je potřeba zahrnout pg_prewarm do `shared_preload_libraries` seznamu parametrů a restartovat server, aby se změna projevila. Parametry lze nastavit v šabloně [Azure Portal](howto-configure-server-parameters-using-portal.md), [CLI](howto-configure-server-parameters-using-cli.md), REST API nebo ARM. 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB je databáze časových řad, která je zabalená jako přípona pro PostgreSQL. TimescaleDB poskytuje časově orientované analytické funkce, optimalizace a škáluje Postgres pro úlohy časových řad.

[Přečtěte si další informace o TimescaleDB](https://docs.timescale.com/latest), registrovaná ochranná známce s [časovou známkou, Inc.](https://www.timescale.com/) Azure Database for PostgreSQL poskytuje [edici Apache-2](https://www.timescale.com/legal/licenses)pro TimescaleDB.

### <a name="installing-timescaledb"></a>Instalace TimescaleDB
Chcete-li nainstalovat TimescaleDB, je třeba jej zahrnout do sdílených předem načtených knihoven serveru. Změna parametru Postgres vyžaduje, `shared_preload_libraries` aby se **restart serveru** projevil. Parametry můžete změnit pomocí [Azure Portal](howto-configure-server-parameters-using-portal.md) nebo rozhraní příkazového [řádku Azure CLI](howto-configure-server-parameters-using-cli.md).

Použití [Azure Portal](https://portal.azure.com/):

1. Vyberte svůj server Azure Database for PostgreSQL.

2. Na bočním panelu vyberte **parametry serveru**.

3. Vyhledejte `shared_preload_libraries` parametr.

4. Vyberte **TimescaleDB**.

5. Vyberte **Uložit** a zachovejte vaše změny. Jakmile se změna uloží, dostanete oznámení. 

6. Po oznámení **restartujte** Server, aby se změny projevily. Informace o tom, jak restartovat server, najdete v tématu [restart serveru Azure Database for PostgreSQL](howto-restart-server-portal.md).


V databázi Postgres teď můžete povolit TimescaleDB. Připojte se k databázi a vydejte následující příkaz:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Pokud se zobrazí chyba, potvrďte, že jste [restart serveru](howto-restart-server-portal.md) po uložení shared_preload_libraries. 

Nyní můžete vytvořit TimescaleDBou tabulku [zcela od začátku](https://docs.timescale.com/getting-started/creating-hypertables) nebo migrovat [existující data časových řad v PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).

### <a name="restoring-a-timescale-database"></a>Obnovení databáze časové osy
Chcete-li obnovit databázi časové osy pomocí pg_dump a pg_restore, je nutné spustit dva pomocné procedury v cílové databázi: `timescaledb_pre_restore()` a `timescaledb_post restore()` .

Nejprve Připravte cílovou databázi:

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
Pokud nevidíte rozšíření, které byste chtěli použít, dejte nám prosím jistotu. Hlasujte pro existující žádosti nebo vytvořte nové žádosti o zpětnou vazbu na našem [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).