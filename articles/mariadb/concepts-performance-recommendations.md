---
title: Doporučení pro výkon – databáze Azure pro MariaDB
description: Tento článek popisuje funkci doporučení výkonu v Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 838a4123bd5007f987f27674862409445967a2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528094"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Doporučení k výkonu ve službě Azure Database for MariaDB

**Platí pro:** Databáze Azure pro MariaDB 10.2

Funkce Doporučení pro zvýšení výkonu analyzuje databáze a vytváří přizpůsobené návrhy pro zlepšení výkonu. Chcete-li vytvořit doporučení, analýza se zabývá různými charakteristikami databáze včetně schématu. Povolte [úložiště dotazů](concepts-query-store.md) na serveru, abyste mohli plně využívat funkci Doporučení pro výkon. Pokud je schéma výkonu vypnuto, zapnutí úložiště dotazů umožňuje performance_schema a podmnožinu nástrojů schématu výkonu požadovaných pro tuto funkci. Po implementaci jakékoli doporučení výkonu, měli byste otestovat výkon vyhodnotit dopad těchto změn.

## <a name="permissions"></a>Oprávnění

Pro spuštění funkce analýzy za použití funkce Doporučení k výkonu potřebujete oprávnění **vlastníka** nebo **přispěvatele**.

## <a name="performance-recommendations"></a>Doporučení k výkonu

Funkce [Doporučení k výkonu](concepts-performance-recommendations.md) analyzuje úlohy na serveru, aby identifikoval indexy a případně zlepšil výkon.

Otevřete **doporučení výkonu** z části **Inteligentní výkon** na panelu nabídek na stránce portálu Azure pro váš server MariaDB.

![Úvodní stránka Doporučení k výkonu](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Vyberte **Analyzovat** a zvolte databázi, která zahájí analýzu. V závislosti na zatížení může dokončení analýzy trvat několik minut. Po dokončení analýzy se zobrazí oznámení na portálu. Analýza provádí hloubkové prozkoumání databáze. Doporučujeme provádět analýzy mimo špičku.

V okně **Doporučení** se zobrazí seznam doporučení, pokud byly nalezeny a související ID dotazu, který vygeneroval toto doporučení. Pomocí ID dotazu můžete pomocí zobrazení [mysql.query_store](concepts-query-store.md#mysqlquery_store) získat další informace o dotazu.

![Doporučení pro zvýšení výkonu nová stránka](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Doporučení nejsou automaticky použita. Chcete-li použít doporučení, zkopírujte text dotazu a spusťte jej z vybraného klienta. Nezapomeňte otestovat a sledovat vyhodnotit doporučení.

## <a name="recommendation-types"></a>Typy doporučení

V současné době jsou podporována pouze *vytvořit index* doporučení.

### <a name="create-index-recommendations"></a>Vytvořit doporučení rejstříku

*Vytvořit index* doporučení navrhnout nové indexy urychlit nejčastěji spuštěné nebo časově náročné dotazy v zatížení. Tento typ doporučení vyžaduje, aby bylo povoleno [úložiště dotazů.](concepts-query-store.md) Úložiště dotazů shromažďuje informace o dotazu a poskytuje podrobné statistiky běhu dotazu a frekvence, které analýza používá k doporučení.

## <a name="next-steps"></a>Další kroky

- Další informace o [monitorování a ladění](concepts-monitoring.md) v Azure Database pro MariaDB.