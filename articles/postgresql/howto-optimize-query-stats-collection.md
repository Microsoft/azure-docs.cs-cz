---
title: Optimalizace kolekce statistik dotazů – Databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete optimalizovat shromažďování statistik dotazů v databázi Azure pro PostgreSQL – jeden server
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f467f01118470eb51f7decf3bd6457917c566723
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770165"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Optimalizace shromažďování statistik dotazů v databázi Azure pro PostgreSQL – jeden server
Tento článek popisuje, jak optimalizovat shromažďování statistik dotazů na serveru Azure Database for PostgreSQL.

## <a name="use-pg_stats_statements"></a>Použití pg_stats_statements
**Pg_stat_statements** je rozšíření PostgreSQL, které je ve výchozím nastavení povolené v Azure Database for PostgreSQL. Rozšíření poskytuje prostředky ke sledování statistiky provádění pro všechny příkazy SQL prováděné serverem. Tento modul zavěsí do každého spuštění dotazu a je dodáván s netriviální náklady na výkon. Povolení **pg_stat_statements** vynutí zápisy textu dotazu do souborů na disku.

Pokud máte jedinečné dotazy s dlouhým textem dotazu nebo nesledujete aktivně **pg_stat_statements**, zakažte **pg_stat_statements** pro dosažení nejlepšího výkonu. Chcete-li tak učinit, `pg_stat_statements.track = NONE`změňte nastavení na .

Některé úlohy zákazníků zaznamenaly až 50% zlepšení výkonu, když je **zakázáno pg_stat_statements.** Kompromis, který provedete při zakázání pg_stat_statements je neschopnost řešit problémy s výkonem.

Nastavení `pg_stat_statements.track = NONE`:

- Na webu Azure Portal přejděte na [stránku Správy prostředků PostgreSQL a vyberte okno parametrů serveru](howto-configure-server-parameters-using-portal.md).

  ![Okno parametru serveru PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Použijte [konfiguraci](howto-configure-server-parameters-using-cli.md) az postgres serveru `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`Azure CLI nastavenou na .

## <a name="use-the-query-store"></a>Použití úložiště dotazů 
Funkce [Úložiště dotazů](concepts-query-store.md) v Azure Database for PostgreSQL poskytuje efektivnější metodu sledování statistik dotazů. Tuto funkci doporučujeme jako alternativu k použití *pg_stats_statements*. 

## <a name="next-steps"></a>Další kroky
Zvažte `pg_stat_statements.track = NONE` nastavení na [webu Azure Portal](howto-configure-server-parameters-using-portal.md) nebo pomocí [příkazového příkazového příkazu k webu Azure](howto-configure-server-parameters-using-cli.md).

Další informace naleznete v tématu: 
- [Scénáře použití úložiště dotazů](concepts-query-store-scenarios.md) 
- [Osvědčené postupy pro úložiště dotazů](concepts-query-store-best-practices.md) 
