---
title: Rozšíření – Azure Database for PostgreSQL – flexibilní Server
description: Další informace o dostupných rozšířeních Postgres v Azure Database for PostgreSQL-flexibilním serveru
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 1126c218f8e80b7d89183746890a3fae1357d29d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935688"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>Rozšíření PostgreSQL v Azure Database for PostgreSQL – flexibilní Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

PostgreSQL poskytuje možnost rozšíření funkcí databáze pomocí rozšíření. Rozšíření seskupují více souvisejících objektů SQL společně v jednom balíčku, který se dá načíst nebo odebrat z databáze pomocí příkazu. Po načtení do databáze rozšíření fungují jako integrované funkce.

## <a name="how-to-use-postgresql-extensions"></a>Jak používat rozšíření PostgreSQL
Aby bylo možné použít rozšíření PostgreSQL, musí být v databázi nainstalovány. Pro instalaci konkrétního rozšíření spusťte příkaz [Create Extension](https://www.postgresql.org/docs/current/sql-createextension.html) . Tento příkaz načte zabalený objekt do vaší databáze.

Azure Database for PostgreSQL podporuje podmnožinu rozšíření klíčů, jak je uvedeno níže. Tyto informace jsou také k dispozici v systému `SHOW azure.extensions;` . Rozšíření, která nejsou uvedená v tomto dokumentu, se na Azure Database for PostgreSQL flexibilním serveru nepodporují. V Azure Database for PostgreSQL nemůžete vytvořit ani načíst vlastní rozšíření.


## <a name="postgres-12-extensions"></a>Postgres 12 rozšíření

Následující rozšíření jsou k dispozici v Azure Database for PostgreSQL-flexibilní servery, které mají Postgres verze 12. 

> [!div class="mx-tableFixed"]
> | **Linka**| **Verze rozšíření** | **Popis** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1,2             | funkce pro ověření integrity vztahu|
> |[bloom](https://www.postgresql.org/docs/12/bloom.html)                    | 1,0             | Metoda přístupu k Bloom – index založený na souboru signatury|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | Podpora indexování běžných typů DataTypes v GIN|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | Podpora indexování běžných typů DataTypes v registru|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | datový typ pro řetězce znaků bez rozlišení velkých a malých písmen|
> |[datov](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | datový typ pro multidimenzionální datové krychle|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1,2             | připojení k jiným databázím PostgreSQL v rámci databáze|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1,0             | Šablona slovníku pro hledání textu pro celá čísla|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1,0             | Šablona slovníku pro hledání textu pro rozšířené zpracování synonym|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1,1             | Výpočet vzdáleností skvělého kruhu na povrchu země|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1,1             | určení podobností a vzdálenosti mezi řetězci|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | datový typ pro ukládání sad párů (klíč, hodnota)|
> |[deznačka](https://www.postgresql.org/docs/12/intagg.html)                     | 1,1             | Agregátor celých čísel a enumerátor. Zastaralé|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1,2             | funkce, operátory a podpora indexu pro jednorozměrná pole s celými čísly|
> |[není](https://www.postgresql.org/docs/12/isn.html)                          | 1,2             | datové typy pro mezinárodní standardy číslování produktů|
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1,1             | datový typ pro hierarchické struktury podobné stromu|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1,7             | Kontrola obsahu databázových stránek na nízké úrovni|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | Prověřte sdílenou mezipaměť vyrovnávací paměti|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1,2             | Projděte si mapu volného místa (FSM).|
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1,2             | data předteplého vztahu|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1,7             | sledovat statistiku spuštění všech provedených příkazů SQL|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | měření podobnosti textu a hledání v indexu na základě trigrams|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1,2             | Projděte si informace o viditelnosti (VM) a viditelnost na úrovni stránky.|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | poskytuje funkce auditování|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | kryptografické funkce|
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1,2             | Zobrazit informace o uzamykání na úrovni řádků|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | Zobrazit statistiky na úrovni řazené kolekce členů|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1,0             | Procedurální jazyk PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 3.0.0           | PostGIS geometrii, geografické a rastrové typy a funkce|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1,0             | obálka s cizími daty pro vzdálené servery PostgreSQL|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1,2             | informace o certifikátech SSL|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1,0             |  TABLESAMPLE – metoda, která přijímá počet řádků jako limit|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1,0             |  TABLESAMPLE – metoda, která akceptuje dobu v milisekundách jako limit|
> |[odakcent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1,1             | slovník hledání textu, který odebere zvýraznění|
> |[UUID – OSSP](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1,1             | generování univerzálně jedinečných identifikátorů (UUID)|

## <a name="postgres-11-extensions"></a>Rozšíření Postgres 11

Následující rozšíření jsou k dispozici v Azure Database for PostgreSQL-flexibilní servery, které mají Postgres verze 11. 

> [!div class="mx-tableFixed"]
> | **Linka**| **Verze rozšíření** | **Popis** |
> |---|---|---|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1,1             | funkce pro ověření integrity vztahu|
> |[bloom](https://www.postgresql.org/docs/11/bloom.html)                    | 1,0             | Metoda přístupu k Bloom – index založený na souboru signatury|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | Podpora indexování běžných typů DataTypes v GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | Podpora indexování běžných typů DataTypes v registru|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | datový typ pro řetězce znaků bez rozlišení velkých a malých písmen|
> |[datov](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | datový typ pro multidimenzionální datové krychle|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1,2             | připojení k jiným databázím PostgreSQL v rámci databáze|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1,0             | Šablona slovníku pro hledání textu pro celá čísla|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1,0             | Šablona slovníku pro hledání textu pro rozšířené zpracování synonym|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1,1             | Výpočet vzdáleností skvělého kruhu na povrchu země|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1,1             | určení podobností a vzdálenosti mezi řetězci|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | datový typ pro ukládání sad párů (klíč, hodnota)|
> |[deznačka](https://www.postgresql.org/docs/11/intagg.html)                     | 1,1             | Agregátor celých čísel a enumerátor. Zastaralé|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1,2             | funkce, operátory a podpora indexu pro jednorozměrná pole s celými čísly|
> |[není](https://www.postgresql.org/docs/11/isn.html)                          | 1,2             | datové typy pro mezinárodní standardy číslování produktů|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1,1             | datový typ pro hierarchické struktury podobné stromu|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1,7             | Kontrola obsahu databázových stránek na nízké úrovni|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | Prověřte sdílenou mezipaměť vyrovnávací paměti|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1,2             | Projděte si mapu volného místa (FSM).|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1,2             | data předteplého vztahu|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | sledovat statistiku spuštění všech provedených příkazů SQL|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | měření podobnosti textu a hledání v indexu na základě trigrams|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1,2             | Projděte si informace o viditelnosti (VM) a viditelnost na úrovni stránky.|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | poskytuje funkce auditování|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | kryptografické funkce|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1,2             | Zobrazit informace o uzamykání na úrovni řádků|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | Zobrazit statistiky na úrovni řazené kolekce členů|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1,0             | Procedurální jazyk PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometrii, geografické a rastrové typy a funkce|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1,0             | obálka s cizími daty pro vzdálené servery PostgreSQL|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1,2             | informace o certifikátech SSL|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1,0             | funkce, které pracují s celými tabulkami včetně křížového|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1,0             |  TABLESAMPLE – metoda, která přijímá počet řádků jako limit|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1,0             |  TABLESAMPLE – metoda, která akceptuje dobu v milisekundách jako limit|
> |[odakcent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1,1             | slovník hledání textu, který odebere zvýraznění|
> |[UUID – OSSP](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1,1             | generování univerzálně jedinečných identifikátorů (UUID)|


## <a name="pg_prewarm"></a>pg_prewarm

Rozšíření pg_prewarm načte relační data do mezipaměti. Předzpracováním vašich mezipamětí znamená, že vaše dotazy mají po restartování lepší dobu odezvy při jejich prvním spuštění. Funkce Automatické předběžné zahřívání není v současnosti k dispozici v Azure Database for PostgreSQL-flexibilním serveru.


## <a name="next-steps"></a>Další kroky

Pokud nevidíte rozšíření, které byste chtěli použít, dejte nám prosím jistotu. Hlasujte pro existující žádosti nebo vytvořte nové žádosti o zpětnou vazbu na našem [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).