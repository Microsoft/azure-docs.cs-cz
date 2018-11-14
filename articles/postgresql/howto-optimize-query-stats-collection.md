---
title: Optimalizace kolekce statistiky dotazů ve službě Azure Database pro kolekce statistiky dotazů server PostgreSQL
description: Tento článek popisuje, jak můžete optimalizovat kolekce Statistika dotazu na serveru Azure Database for PostgreSQL.
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8b8d1a5a16a948953838f51edca091121dc14df1
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628958"
---
# <a name="optimize-query-statistics-collection-in-azure-database-for-postgresql-server"></a>Optimalizace kolekce statistik dotazů ve službě Azure Database pro PostgreSQL server 
Tento článek popisuje, k optimalizaci dotazu kolekce statistik v serveru Azure Database for PostgreSQL.

## <a name="using-pgstatsstatements"></a>Pomocí pg_stats_statements
**Pg_stat_statements** je rozšíření PostgreSQL, který je povolen ve výchozím nastavení ve službě Azure Database for PostgreSQL. Toto rozšíření poskytuje prostředky ke sledování statistiky provádění příkazů SQL Server spuštěn. Ale tento modul zavěšení do každého spuštění dotazu a obsahuje netriviální nákladů na výkon. Povolení **pg_stat_statements** vynutí dotazování zápis textu do souborů na disku.

Pro zákazníky, kteří mají jedinečné dotazy s textem dlouhého dotazu nebo nejsou aktivní sledování **pg_stat_statements**, doporučujeme zakázat **pg_stat_statements** nejlepšího výkonu dosáhnete tak, že nastavíte `pg_stat_statements.track = NONE`.

U některých úloh zákazníků jsme viděli až 50 procent zkrátil zakázáním **pg_stat_statements**. Kompromis, který provede zakázáním pg_stat_statements ale neschopnost řešení problémů s výkonem.

Chcete-li nastavit `pg_stat_statements.track = NONE`:

- Na webu Azure Portal, přejděte [PostgreSQL správy prostředků stránky a vyberte okno parametry serveru](howto-configure-server-parameters-using-portal.md).

![Okno parametr serveru PostgreSQL](.\media\howto-optimize-query-stats-collection\pg_stats_statements_portal.png)

- Pomocí [rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md), az postgres server configuration set `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="using-query-store"></a>Pomocí dotazu Store 
[Query Store](concepts-query-store.md) funkcí ve službě Azure Database for PostgreSQL poskytuje další metodu výkonné ke sledování statistiky dotazů a jako alternativu k použití se doporučuje *pg_stats_statements*. 

## <a name="next-steps"></a>Další postup
Zvažte nastavení `pg_stat_statements.track = NONE` v [webu Azure Portal](howto-configure-server-parameters-using-portal.md) nebo pomocí [rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md).

Najdete v článku [scénáře použití Query Store](concepts-query-store-scenarios.md) a [osvědčené postupy Query Store](concepts-query-store-best-practices.md) Další informace. 
