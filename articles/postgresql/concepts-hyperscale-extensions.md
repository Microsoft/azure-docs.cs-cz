---
title: Rozšíření PostgreSQL v Azure Database for PostgreSQL – velkokapacitní (Citus) (preview)
description: Popisuje možnosti k rozšíření funkčnosti vaší databáze pomocí rozšíření ve službě Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 4022c95bfda8cbdaed75876793bfbba4254a5c54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65410253"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---hyperscale-citus-preview"></a>Rozšíření PostgreSQL v Azure Database for PostgreSQL – velkokapacitní (Citus) (preview)

PostgreSQL poskytuje schopnost rozšiřovat funkce vaší databáze pomocí rozšíření. Rozšíření umožňují sdružování více souvisejících objektů SQL společně v jednom balíčku, který může načíst nebo odstranit z databáze pomocí jediného příkazu. Po načtení v databázi, můžou rozšíření fungovat stejně jako integrované funkce. Další informace o rozšíření PostgreSQL, naleznete v tématu [balení souvisejících objektů do rozšíření](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Jak používat rozšíření PostgreSQL

Rozšíření PostgreSQL musí být nainstalován ve vaší databázi, abyste mohli používat. Pokud chcete nainstalovat konkrétní rozšíření, spusťte [vytvořit rozšíření](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) příkaz nástroj psql načíst objekty zabalené do databáze.

Azure Database for PostgreSQL aktuálně podporuje jenom podmnožinu klíče rozšíření, jak je uvedeno níže. Rozšíření nad rámec uvedené nejsou podporovány. Nelze vytvořit vlastní rozšíření s využitím Azure Database pro služba PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Rozšíření podporovány službou Azure Database for PostgreSQL

Následující tabulky uvádí standardní PostgreSQL rozšíření, které jsou aktuálně podporovány službou Azure Database for PostgreSQL. Tyto informace jsou k dispozici také spuštěním `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Rozšíření typů dat

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Poskytuje typ řetězce znaků na velká a malá písmena. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Poskytuje datový typ pro multidimenzionální datové krychle. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Poskytuje datový typ pro ukládání sad párů klíč/hodnota. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Poskytuje datové typy pro mezinárodní produktu číslování. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Velký objekt údržby. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Poskytuje datový typ pro hierarchické stromové struktury jako struktury. |
> | [seg –](https://www.postgresql.org/docs/current/seg.html) | Datový typ představující segmenty čáry nebo intervaly s plovoucí desetinnou čárkou. |
> | [prvních n hodnot](https://github.com/citusdata/postgresql-topn/) | Typ n nejlepších JSONB. |

### <a name="functions-extensions"></a>Funkce rozšíření

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funkce pro pole s automatickými přírůstky. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Poskytuje prostředky k výpočtu kruh velké vzdálenosti na zemském povrchu. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Poskytuje několik funkcí k určení vzdálenost mezi řetězci a podobnosti. |
> | [Vložit\_uživatelské jméno](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funkce pro sledování, kdo ho změnil tabulku. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Agregátor celé číslo a enumerátor (zastaralé). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Poskytuje funkce a operátory pro práci s null bez pole celých čísel. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funkce pro sledování čas poslední změny. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Poskytuje kryptografické funkce. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Spravuje dělené tabulky podle času nebo ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Poskytuje funkce a operátory pro určení podobnost na základě porovnání trigram alfanumerické text. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funkce pro implementaci referenční integritu (zastaralé). |
> | relace\_analytics | Funkce pro dotazování hstore pole. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Poskytuje funkce, které pracují s celé tabulky, včetně křížového. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Aktivaci oznámení o změnách. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funkce pro implementaci doba trvání cesty. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Generuje univerzálně jedinečné identifikátory (UUID). |

### <a name="full-text-search-extensions"></a>Fulltextové vyhledávání rozšíření

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Poskytuje šablony textu vyhledávání slovníku pro celá čísla. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Textové vyhledávání slovníku šablony pro rozšířené synonymum zpracování. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Slovník vyhledávání textu, který zruší lexemes zvýraznění (značky diakritických znamének). |

### <a name="index-types-extensions"></a>Rozšíření index typy

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [Standard](https://www.postgresql.org/docs/current/bloom.html) | Metoda přístupu Standard - soubor s podpisem na základě indexu. |
> | [taková\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Poskytuje ukázkové GIN – operátor třídy, které implementují B-stromu jako chování u určitých datových typů. |
> | [taková\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Poskytuje GiST index operátor třídy, které implementují B-stromu. |

### <a name="language-extensions"></a>Jazyková rozšíření

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL zaveditelný procedurální jazyk. |

### <a name="hyperscale-extensions"></a>Velkokapacitní rozšíření

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus distribuovaná databáze. |
> | horizontální oddíl\_rebalancer | Bezpečně obnovit rovnováhu data ve skupině serveru v případě uzlu přidání nebo odebrání. |

### <a name="miscellaneous-extensions"></a>Ostatní rozšíření

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funkce správy pro PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funkce pro ověřování integrity vztah. |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Obálka cizí data pro přístup k souborům bez stromové struktury. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Kontrola obsahu stránky databáze na nízké úrovni. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Poskytuje prostředky k posouzení, co se děje v mezipaměti sdílené vyrovnávací paměti v reálném čase. |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Plánovač úloh pro PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Zkontrolujte volné místo map (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Poskytuje způsob, jak načíst data relace do mezipaměti vyrovnávací paměti. |
> | [PG\_stat\_příkazy](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Poskytuje prostředky pro sledování statistiky provádění příkazů SQL Server spuštěn. (Viz níže pro poznámky na toto rozšíření). |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | Podívejte se na viditelnost mapy (VM) a informace o viditelnost na úrovni stránky. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Poskytuje prostředky k zobrazení informací na úrovni řádků zamykání. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Poskytuje prostředky k zobrazení Statistika na úrovni řazené kolekce členů. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Obálka cizí dat používá pro přístup k datům uloženým v externí servery PostgreSQL. (Viz níže pro poznámky na toto rozšíření).|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informace o certifikátech SSL. |
> | [tsm\_systému\_řádků](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Klauzule TABLESAMPLE metodu, která přijímá počet řádků jako omezení. |
> | [tsm\_systému\_čas](https://www.postgresql.org/docs/current/tsm-system-time.html) | Klauzule TABLESAMPLE metodu, která přijímá čas v milisekundách jako omezení. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Poskytuje způsob vytváření hypotetické indexy, které není nákladů CPU nebo disk. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Modul, který podporuje připojení k jiné databáze PostgreSQL z v rámci relace databáze. (Viz níže pro poznámky na toto rozšíření). |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Dotazy XPath a XSLT. |


### <a name="postgis-extensions"></a>PostGIS rozšíření

> [!div class="mx-tableFixed"]
> | **Rozšíření** | **Popis** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Prostorová a geografické objekty pro PostgreSQL. |
> | Adresa\_standardizer, adresa\_standardizer\_data\_USA | Použít k analýze adresu do rozložení na základní prvky. Použít pro podporu geokódování adresu normalizace kroku. |
> | postgis\_sfcgal | PostGIS SFCGAL funkce. |
> | postgis\_tiger\_geocoder | PostGIS tiger geocoder a reverzní geocoder. |
> | postgis\_topologie | PostGIS topologie prostorové typy a funkce. |


## <a name="pgstatstatements"></a>pg_stat_statements
[Pg\_stat\_příkazy rozšíření](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) se předem na každý server Azure Database for PostgreSQL poskytnout způsob sledování statistiky provádění příkazů SQL.
Nastavení `pg_stat_statements.track`, který určuje, jaké příkazy se počítají podle přípony, výchozí hodnota je `top`, to znamená všechny příkazy vydané klienti jsou sledovány. Jsou dvě další sledování úrovně `none` a `all`. Toto nastavení se dá konfigurovat jako parametr serveru prostřednictvím [webu Azure portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Je kompromis mezi informace o spuštění dotazu poskytuje pg_stat_statements a dopad na výkon serveru jako protokoly každý příkaz jazyka SQL. Pokud nepoužíváte aktivně pg_stat_statements rozšíření, doporučujeme vám, že jste nastavili `pg_stat_statements.track` k `none`. Všimněte si, že některé třetích stran sledování služeb může záviset na pg_stat_statements poskytovat informace o výkonu dotazů, proto zkontrolujte, jestli to není v tomto případě nebo ne.

## <a name="dblink-and-postgresfdw"></a>dblink a postgres_fdw
dblink a postgres_fdw vám umožní připojit z jednoho serveru PostgreSQL do jiné nebo do jiné databáze na stejném serveru. Přijímající server je potřeba povolit připojení ze serveru pro odesílání prostřednictvím brány firewall. Při použití těchto rozšíření k připojení mezi – Azure Database for postgresql – servery, to můžete udělat tak, že nastavíte "Povolit přístup ke službám Azure" na ON. Pokud chcete, abyste použili rozšíření se ve smyčce zpět na stejný server je také potřeba. Nastavení "Povolit přístup ke službám Azure" najdete na stránce webu Azure portal pro Postgres server v části zabezpečení připojení. Zapnutí "Povolit přístup ke službám Azure" na seznamů povolených všechny IP adresy Azure.

V současné době odchozí připojení ze služby Azure Database for PostgreSQL nejsou podporovány, s výjimkou připojení k jiné – Azure Database for postgresql – servery.
