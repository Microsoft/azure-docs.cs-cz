---
title: Optimalizace shromažďování statistik dotazů – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete na Azure Database for PostgreSQL jednom serveru optimalizovat kolekci statistik dotazů.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f467f01118470eb51f7decf3bd6457917c566723
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74770165"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Optimalizace shromažďování statistických údajů dotazu na Azure Database for PostgreSQL jednom serveru
Tento článek popisuje, jak optimalizovat kolekci statistik dotazů na Azure Database for PostgreSQL serveru.

## <a name="use-pg_stats_statements"></a>Použít pg_stats_statements
**Pg_stat_statements** je rozšíření PostgreSQL, které je ve výchozím nastavení povolené v Azure Database for PostgreSQL. Rozšíření poskytuje způsob, jak sledovat statistiku spouštění všech příkazů SQL spuštěných serverem. Tento modul se do každého spuštění dotazu připojí a přináší netriviální náklady na výkon. Povolení **pg_stat_statements** vynutí zápisy textu dotazu do souborů na disku.

Pokud máte jedinečné dotazy s dlouhým textem dotazu nebo aktivně nesledujete **pg_stat_statements**, zakažte **pg_stat_statements** pro nejlepší výkon. Provedete to tak, že změníte `pg_stat_statements.track = NONE`nastavení na.

V případě, že je **pg_stat_statements** zakázaná, některé úlohy od zákazníků viděli až 50% zlepšení výkonu. Kompromisy, které uděláte při zakázání pg_stat_statements, jsou neschopnost řešit problémy s výkonem.

Nastavit `pg_stat_statements.track = NONE`:

- V Azure Portal otevřete [stránku Správa prostředků PostgreSQL a vyberte okno parametry serveru](howto-configure-server-parameters-using-portal.md).

  ![Okno parametrů serveru PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Pomocí [Azure CLI](howto-configure-server-parameters-using-cli.md) AZ Postgres Server Configuration nastavte na `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>Použití úložiště dotazů 
Funkce [úložiště dotazů](concepts-query-store.md) v Azure Database for PostgreSQL poskytuje efektivnější způsob, jak sledovat statistiku dotazů. Tuto funkci doporučujeme jako alternativu k používání *pg_stats_statements*. 

## <a name="next-steps"></a>Další kroky
Zvažte nastavení `pg_stat_statements.track = NONE` v [Azure Portal](howto-configure-server-parameters-using-portal.md) nebo pomocí [Azure CLI](howto-configure-server-parameters-using-cli.md).

Další informace naleznete v tématu: 
- [Scénáře použití úložiště dotazů](concepts-query-store-scenarios.md) 
- [Osvědčené postupy pro úložiště dotazů](concepts-query-store-best-practices.md) 
