---
title: Doporučení pro výkon – Azure Database for MySQL
description: Tento článek popisuje funkci doporučení výkonu v Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 6f41863f45bdc90cb9fe589ba0a5011dea84a67c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84485216"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Doporučení k výkonu ve službě Azure Database for MySQL

**Platí pro:** Azure Database for MySQL 5,7, 8,0

Funkce doporučení pro výkon analyzuje vaše databáze a vytváří přizpůsobené návrhy pro zlepšení výkonu. Při vytváření doporučení analyzuje tato analýza různé charakteristiky databáze, včetně schématu. Povolením [úložiště dotazů](concepts-query-store.md) na serveru můžete plně využít funkci doporučení pro výkon. Pokud je schéma výkonu VYPNUTé, zapnutí úložiště dotazů umožňuje performance_schema a podmnožinu nástrojů schématu výkonu vyžadovaných pro danou funkci. Po implementaci jakéhokoli doporučení výkonu byste měli testovat výkon a vyhodnotit dopad těchto změn.

## <a name="permissions"></a>Oprávnění

Pro spuštění funkce analýzy za použití funkce Doporučení k výkonu potřebujete oprávnění **vlastníka** nebo **přispěvatele**.

## <a name="performance-recommendations"></a>Doporučení k výkonu

Funkce [Doporučení k výkonu](concepts-performance-recommendations.md) analyzuje úlohy na serveru, aby identifikoval indexy a případně zlepšil výkon.

Otevřete **doporučení k výkonu** z části **inteligentní výkon** na panelu nabídek na stránce Azure Portal pro server MySQL.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="Úvodní stránka Doporučení k výkonu":::

Vyberte možnost **analyzovat** a zvolte databázi, která bude začínat analýzou. V závislosti na vašich úlohách může trvat několik minut, než se dokončí analýza. Po dokončení analýzy se zobrazí oznámení na portálu. Analýza provede důkladné přezkoumání vaší databáze. Doporučujeme, abyste provedli analýzu v době mimo špičku.

V okně **doporučení** se zobrazí seznam doporučení, pokud byla nalezena nějaká a související ID dotazu, které vygenerovalo toto doporučení. Pomocí ID dotazu můžete získat další informace o dotazu pomocí zobrazení [MySQL.query_store](concepts-query-store.md#mysqlquery_store) .

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Úvodní stránka Doporučení k výkonu":::

Doporučení se nepoužívají automaticky. Pokud chcete doporučení použít, zkopírujte text dotazu a spusťte ho z vašeho klienta podle vlastního výběru. Nezapomeňte otestovat a monitorovat, abyste vyhodnotili doporučení.

## <a name="recommendation-types"></a>Typy doporučení

### <a name="index-recommendations"></a>Doporučení indexu

Doporučení *vytvořit index* návrhy nových indexů vám umožní zrychlit nejčastěji spouštěné nebo časově náročné dotazy v zatížení. Tento typ doporučení vyžaduje, aby [úložiště dotazů](concepts-query-store.md) bylo povolené. Úložiště dotazů shromažďuje informace o dotazech a poskytuje podrobné běhové dotazy a statistické údaje o četnosti, které analýza používá, aby provedla doporučení.

### <a name="query-recommendations"></a>Doporučení pro dotazy

Doporučení pro dotazy navrhují optimalizace a přepisují dotazy v úlohách. Díky identifikaci dotazů MySQL anti-Patterns a jejich opravám syntakticky můžete zlepšit výkon časově náročných dotazů. Tento typ doporučení vyžaduje, aby úložiště dotazů bylo povolené. Úložiště dotazů shromažďuje informace o dotazech a poskytuje podrobné běhové dotazy a statistické údaje o četnosti, které analýza používá, aby provedla doporučení.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [monitorování a ladění](concepts-monitoring.md) v Azure Database for MySQL.