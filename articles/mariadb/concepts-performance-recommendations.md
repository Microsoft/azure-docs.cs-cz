---
title: Doporučení pro výkon – Azure Database for MariaDB
description: Tento článek popisuje funkci doporučení výkonu v Azure Database for MariaDB
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: a20b8a43c8955e1afea7a7157e3e73425fb0d806
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664312"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Doporučení k výkonu ve službě Azure Database for MariaDB

**Platí pro:** Azure Database for MariaDB 10,2

Funkce doporučení pro výkon analyzuje vaše databáze a vytváří přizpůsobené návrhy pro zlepšení výkonu. Při vytváření doporučení analyzuje tato analýza různé charakteristiky databáze, včetně schématu. Povolením [úložiště dotazů](concepts-query-store.md) na serveru můžete plně využít funkci doporučení pro výkon. Pokud je schéma výkonu VYPNUTé, zapnutí úložiště dotazů umožňuje performance_schema a podmnožinu nástrojů schématu výkonu vyžadovaných pro danou funkci. Po implementaci jakéhokoli doporučení výkonu byste měli testovat výkon a vyhodnotit dopad těchto změn.

## <a name="permissions"></a>Oprávnění

Pro spuštění funkce analýzy za použití funkce Doporučení k výkonu potřebujete oprávnění **vlastníka** nebo **přispěvatele**.

## <a name="performance-recommendations"></a>Doporučení k výkonu

Funkce [Doporučení k výkonu](concepts-performance-recommendations.md) analyzuje úlohy na serveru, aby identifikoval indexy a případně zlepšil výkon.

Otevřete **doporučení pro výkon** z části **inteligentní výkon** na panelu nabídek na stránce Azure Portal pro server MariaDB.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="Úvodní stránka Doporučení k výkonu":::

Vyberte možnost **analyzovat** a zvolte databázi, která bude začínat analýzou. V závislosti na vašich úlohách může trvat několik minut, než se dokončí analýza. Po dokončení analýzy se zobrazí oznámení na portálu. Analýza provede důkladné přezkoumání vaší databáze. Doporučujeme, abyste provedli analýzu v době mimo špičku.

V okně **doporučení** se zobrazí seznam doporučení, pokud byla nalezena nějaká a související ID dotazu, které vygenerovalo toto doporučení. Pomocí ID dotazu můžete získat další informace o dotazu pomocí zobrazení [MySQL.query_store](concepts-query-store.md#mysqlquery_store) .

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Nová stránka s doporučeními pro výkon":::

Doporučení se nepoužívají automaticky. Pokud chcete doporučení použít, zkopírujte text dotazu a spusťte ho z vašeho klienta podle vlastního výběru. Nezapomeňte otestovat a monitorovat, abyste vyhodnotili doporučení.

## <a name="recommendation-types"></a>Typy doporučení

### <a name="index-recommendations"></a>Doporučení indexu

Doporučení *vytvořit index* návrhy nových indexů vám umožní zrychlit nejčastěji spouštěné nebo časově náročné dotazy v zatížení. Tento typ doporučení vyžaduje, aby [úložiště dotazů](concepts-query-store.md) bylo povolené. Úložiště dotazů shromažďuje informace o dotazech a poskytuje podrobné běhové dotazy a statistické údaje o četnosti, které analýza používá, aby provedla doporučení.

### <a name="query-recommendations"></a>Doporučení pro dotazy

Doporučení pro dotazy navrhují optimalizace a přepisují dotazy v úlohách. Díky identifikaci MariaDB dotazů proti antipatternům a jejich opravám syntakticky lze zlepšit výkon časově náročných dotazů. Tento typ doporučení vyžaduje, aby úložiště dotazů bylo povolené. Úložiště dotazů shromažďuje informace o dotazech a poskytuje podrobné běhové dotazy a statistické údaje o četnosti, které analýza používá, aby provedla doporučení.
## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [monitorování a ladění](concepts-monitoring.md) v Azure Database for MariaDB.