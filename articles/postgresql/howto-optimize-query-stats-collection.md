---
title: Optimalizace shromažďování statistik dotazů – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete na Azure Database for PostgreSQL jednom serveru optimalizovat kolekci statistik dotazů.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: bc731f6f6a5a60bce0851bf8fe5874f7149f3899
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90901453"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Optimalizace shromažďování statistických údajů dotazu na Azure Database for PostgreSQL jednom serveru
Tento článek popisuje, jak optimalizovat kolekci statistik dotazů na Azure Database for PostgreSQL serveru.

## <a name="use-pg_stats_statements"></a>Použít pg_stats_statements
**Pg_stat_statements** je rozšíření PostgreSQL, které je ve výchozím nastavení povolené v Azure Database for PostgreSQL. Rozšíření poskytuje způsob, jak sledovat statistiku spouštění všech příkazů SQL spuštěných serverem. Tento modul se do každého spuštění dotazu připojí a přináší netriviální náklady na výkon. Povolení **pg_stat_statements** vynutí zápisy textu dotazu do souborů na disku.

Pokud máte jedinečné dotazy s dlouhým textem dotazu nebo aktivně nesledujete **pg_stat_statements**, zakažte **pg_stat_statements** pro nejlepší výkon. Provedete to tak, že změníte nastavení na `pg_stat_statements.track = NONE` .

V případě, že je **pg_stat_statements** zakázaná, některé úlohy od zákazníků viděli až 50% zlepšení výkonu. Kompromisy, které uděláte při zakázání pg_stat_statements, jsou neschopnost řešit problémy s výkonem.

Nastavit `pg_stat_statements.track = NONE` :

- V Azure Portal otevřete [stránku Správa prostředků PostgreSQL a vyberte okno parametry serveru](howto-configure-server-parameters-using-portal.md).

  :::image type="content" source="./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png" alt-text="Okno parametrů serveru PostgreSQL":::

- Pomocí [Azure CLI](howto-configure-server-parameters-using-cli.md) AZ Postgres Server Configuration nastavte na `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE` .

## <a name="use-the-query-store"></a>Použití úložiště dotazů 
Funkce [úložiště dotazů](concepts-query-store.md) v Azure Database for PostgreSQL poskytuje efektivnější způsob, jak sledovat statistiku dotazů. Tuto funkci doporučujeme jako alternativu k používání *pg_stats_statements*. 

## <a name="next-steps"></a>Další kroky
Zvažte nastavení `pg_stat_statements.track = NONE` v [Azure Portal](howto-configure-server-parameters-using-portal.md) nebo pomocí [Azure CLI](howto-configure-server-parameters-using-cli.md).

Další informace naleznete v tématu: 
- [Scénáře použití úložiště dotazů](concepts-query-store-scenarios.md) 
- [Osvědčené postupy pro úložiště dotazů](concepts-query-store-best-practices.md) 
