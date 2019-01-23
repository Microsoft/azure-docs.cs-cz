---
title: Použití rozšíření PostgreSQL v Azure Database for PostgreSQL
description: Popisuje možnosti k rozšíření funkčnosti vaší databáze pomocí rozšíření ve službě Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/22/2019
ms.openlocfilehash: 6c6fec968efdd85eaf6249459f8e1a0384f2ea11
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462177"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Rozšíření PostgreSQL v Azure Database for PostgreSQL
PostgreSQL poskytuje schopnost rozšiřovat funkce vaší databáze pomocí rozšíření. Rozšíření umožňují sdružování více souvisejících objektů SQL společně v jednom balíčku, který může načíst nebo odstranit z databáze pomocí jediného příkazu. Po načtení v databázi, můžou rozšíření fungovat stejně jako integrované funkce. Další informace o rozšíření PostgreSQL, naleznete v tématu [balení souvisejících objektů do rozšíření](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Jak používat rozšíření PostgreSQL
Rozšíření PostgreSQL musí být nainstalován ve vaší databázi, abyste mohli používat. Pokud chcete nainstalovat konkrétní rozšíření, spusťte [vytvořit rozšíření](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) příkaz nástroj psql načíst objekty zabalené do databáze.

Azure Database for PostgreSQL aktuálně podporuje jenom podmnožinu klíče rozšíření, jak je uvedeno níže. Rozšíření nad rámec uvedené nejsou podporovány. Nelze vytvořit vlastní rozšíření s využitím Azure Database pro služba PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Rozšíření podporovány službou Azure Database for PostgreSQL
Následující tabulky uvádí standardní PostgreSQL rozšíření, které jsou aktuálně podporovány službou Azure Database for PostgreSQL. Tyto informace jsou k dispozici také spuštěním `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Rozšíření typů dat

> [!div class="mx-tableFixed"]
| **Rozšíření** | **Popis** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Poskytuje datový typ pro automatické šifrovaná hesla. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Poskytuje typ řetězce znaků na velká a malá písmena. |
| [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Poskytuje datový typ pro multidimenzionální datové krychle. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Poskytuje datový typ pro ukládání sad párů klíč/hodnota. |
| [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Poskytuje datové typy pro mezinárodní produktu číslování. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Poskytuje datový typ pro hierarchické stromové struktury jako struktury. |

### <a name="functions-extensions"></a>Funkce rozšíření

> [!div class="mx-tableFixed"]
| **Rozšíření** | **Popis** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Poskytuje prostředky k výpočtu kruh velké vzdálenosti na zemském povrchu. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Poskytuje několik funkcí k určení vzdálenost mezi řetězci a podobnosti. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Poskytuje funkce a operátory pro práci s null bez pole celých čísel. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Poskytuje kryptografické funkce. |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Spravuje dělené tabulky podle času nebo ID. |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Poskytuje funkce a operátory pro určení podobnost na základě porovnání trigram alfanumerické text. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Poskytuje funkce, které pracují s celé tabulky, včetně křížového. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Generuje univerzálně jedinečné identifikátory (UUID). |

### <a name="full-text-search-extensions"></a>Fulltextové vyhledávání rozšíření

> [!div class="mx-tableFixed"]
| **Rozšíření** | **Popis** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Poskytuje šablony textu vyhledávání slovníku pro celá čísla. |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Slovník vyhledávání textu, který zruší lexemes zvýraznění (značky diakritických znamének). |

### <a name="index-types-extensions"></a>Rozšíření index typy

> [!div class="mx-tableFixed"]
| **Rozšíření** | **Popis** |
|---|---|
| [taková\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Poskytuje ukázkové GIN – operátor třídy, které implementují B-stromu jako chování u určitých datových typů. |
| [taková\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Poskytuje GiST index operátor třídy, které implementují B-stromu. |

### <a name="language-extensions"></a>Jazyková rozšíření

> [!div class="mx-tableFixed"]
| **Rozšíření** | **Popis** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL zaveditelný procedurální jazyk. |
| [plv8](https://plv8.github.io/) | Rozšíření jazyka Javascript pro PostgreSQL, který lze použít pro uložené procedury, triggery, atd. |

### <a name="miscellaneous-extensions"></a>Ostatní rozšíření

> [!div class="mx-tableFixed"]
| **Rozšíření** | **Popis** |
|---|---|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Poskytuje prostředky k posouzení, co se děje v mezipaměti sdílené vyrovnávací paměti v reálném čase. |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Poskytuje způsob, jak načíst data relace do mezipaměti vyrovnávací paměti. |
| [PG\_stat\_příkazy](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Poskytuje prostředky pro sledování statistiky provádění příkazů SQL Server spuštěn. (Viz níže pro poznámky na toto rozšíření). |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Poskytuje prostředky k zobrazení informací na úrovni řádků zamykání. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Poskytuje prostředky k zobrazení Statistika na úrovni řazené kolekce členů. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Obálka cizí dat používá pro přístup k datům uloženým v externí servery PostgreSQL. (Viz níže pro poznámky na toto rozšíření).|
| [hypopg](https://hypopg.readthedocs.io/en/latest/) | Poskytuje způsob vytváření hypotetické indexy, které není nákladů CPU nebo disk. |
| [dblink](https://www.postgresql.org/docs/current/dblink.html) | Modul, který podporuje připojení k jiné databáze PostgreSQL z v rámci relace databáze. (Viz níže pro poznámky na toto rozšíření). |


### <a name="postgis-extensions"></a>PostGIS rozšíření

> [!div class="mx-tableFixed"]
| **Rozšíření** | **Popis** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Prostorová a geografické objekty pro PostgreSQL. |
| Adresa\_standardizer, adresa\_standardizer\_data\_USA | Použít k analýze adresu do rozložení na základní prvky. Použít pro podporu geokódování adresu normalizace kroku. |
| [pgrouting](https://pgrouting.org/) | Rozšiřuje PostGIS / geoprostorové databáze PostgreSQL k poskytování geoprostorového směrování funkce. |


### <a name="using-pgstatstatements"></a>Pomocí pg_stat_statements
[Pg\_stat\_příkazy rozšíření](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) se předem na každý server Azure Database for PostgreSQL poskytnout způsob sledování statistiky provádění příkazů SQL.
Nastavení `pg_stat_statements.track`, který určuje, jaké příkazy se počítají podle přípony, výchozí hodnota je `top`, to znamená všechny příkazy vydané klienti jsou sledovány. Jsou dvě další sledování úrovně `none` a `all`. Toto nastavení se dá konfigurovat jako parametr serveru prostřednictvím [webu Azure portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Je kompromis mezi informace o spuštění dotazu poskytuje pg_stat_statements a dopad na výkon serveru jako protokoly každý příkaz jazyka SQL. Pokud nepoužíváte aktivně pg_stat_statements rozšíření, doporučujeme vám, že jste nastavili `pg_stat_statements.track` k `none`. Všimněte si, že některé třetích stran sledování služeb může záviset na pg_stat_statements poskytovat informace o výkonu dotazů, proto zkontrolujte, jestli to není v tomto případě nebo ne.

### <a name="using-dblink-and-postgresfdw"></a>Pomocí dblink a postgres_fdw
dblink a postgres_fdw vám umožní připojit z jednoho serveru PostgreSQL do jiné nebo do jiné databáze na stejném serveru. Přijímající server je potřeba povolit připojení ze serveru pro odesílání prostřednictvím brány firewall. Při použití těchto rozšíření k připojení mezi – Azure Database for postgresql – servery, to můžete udělat tak, že nastavíte "Povolit přístup ke službám Azure" na ON. Pokud chcete, abyste použili rozšíření se ve smyčce zpět na stejný server je také potřeba. Nastavení "Povolit přístup ke službám Azure" najdete na stránce webu Azure portal pro Postgres server v části zabezpečení připojení. Zapnutí "Povolit přístup ke službám Azure" na seznamů povolených všechny IP adresy Azure.


## <a name="next-steps"></a>Další postup
Pokud se rozšíření, které byste chtěli používat, dejte nám vědět. Hlasovat pro požadavky na existující nebo vytvořte novou zpětnou vazbu a požadavků v našich [fóru pro zpětnou vazbu zákazníků](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
