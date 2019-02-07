---
title: Optimalizace kolekce Statistika dotazu na serveru Azure Database for PostgreSQL
description: Tento článek popisuje, jak můžete optimalizovat kolekce Statistika dotazu na serveru Azure Database for PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 428a22f79008130448bef65f14322d6880b1b367
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814363"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql-server"></a>Optimalizace kolekce statistik dotazů na serveru Azure Database for PostgreSQL 
Tento článek popisuje, jak optimalizovat kolekce statistik dotazů na serveru Azure Database for PostgreSQL.

## <a name="use-pgstatsstatements"></a>Použití pg_stats_statements
**Pg_stat_statements** je rozšíření PostgreSQL, který je povolen ve výchozím nastavení ve službě Azure Database for PostgreSQL. Toto rozšíření poskytuje prostředky ke sledování provádění statistiky pro všechny příkazy SQL Server spuštěn. Tento modul zavěšení do každého spuštění dotazu a obsahuje netriviální nákladů na výkon. Povolení **pg_stat_statements** vynutí dotazování zápis textu do souborů na disku.

Pokud budete mít jedinečné dotazy s textem dlouhého dotazu nebo není aktivně monitorovat **pg_stat_statements**, zakažte **pg_stat_statements** pro zajištění nejlepšího výkonu. Uděláte to tak, změňte nastavení na `pg_stat_statements.track = NONE`.

Až 50 procent zkrátil jste viděli některé úlohy zákazníků při **pg_stat_statements** je zakázaná. Výměnou za to, které provedete při zakázání pg_stat_statements je nemožnost řešení problémů s výkonem.

Chcete-li nastavit `pg_stat_statements.track = NONE`:

- Na webu Azure Portal, přejděte na [správy prostředků PostgreSQL stránky a vyberte okno parametry serveru](howto-configure-server-parameters-using-portal.md).

  ![Okno parametr serveru PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Použití [rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md) az postgres server configuration nastavena na `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>Použití Query Store 
[Query Store](concepts-query-store.md) funkcí ve službě Azure Database pro PostgreSQL poskytují účinnější způsob sledování statistiky dotazů. Doporučujeme, abyste tuto funkci jako alternativu k použití *pg_stats_statements*. 

## <a name="next-steps"></a>Další postup
Zvažte nastavení `pg_stat_statements.track = NONE` v [webu Azure portal](howto-configure-server-parameters-using-portal.md) nebo s použitím [rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md).

Další informace naleznete v tématu: 
- [Scénáře použití úložiště dotazů](concepts-query-store-scenarios.md) 
- [Query Store osvědčené postupy](concepts-query-store-best-practices.md) 
