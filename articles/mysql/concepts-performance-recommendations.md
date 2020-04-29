---
title: Doporučení pro výkon – Azure Database for MySQL
description: Tento článek popisuje funkci doporučení výkonu v Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c7779d82ddd6e5fd1bf7fcd983937ea6c10dab1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537070"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Doporučení k výkonu ve službě Azure Database for MySQL

**Platí pro:** Azure Database for MySQL 5,7

Funkce doporučení pro výkon analyzuje vaše databáze a vytváří přizpůsobené návrhy pro zlepšení výkonu. Při vytváření doporučení analyzuje tato analýza různé charakteristiky databáze, včetně schématu. Povolením [úložiště dotazů](concepts-query-store.md) na serveru můžete plně využít funkci doporučení pro výkon. Pokud je schéma výkonu VYPNUTé, zapnutí úložiště dotazů umožňuje performance_schema a podmnožinu nástrojů schématu výkonu vyžadovaných pro danou funkci. Po implementaci jakéhokoli doporučení výkonu byste měli testovat výkon a vyhodnotit dopad těchto změn.

## <a name="permissions"></a>Oprávnění

Pro spuštění funkce analýzy za použití funkce Doporučení k výkonu potřebujete oprávnění **vlastníka** nebo **přispěvatele**.

## <a name="performance-recommendations"></a>Doporučení k výkonu

Funkce [Doporučení k výkonu](concepts-performance-recommendations.md) analyzuje úlohy na serveru, aby identifikoval indexy a případně zlepšil výkon.

Otevřete **doporučení k výkonu** z části **inteligentní výkon** na panelu nabídek na stránce Azure Portal pro server MySQL.

![Úvodní stránka Doporučení k výkonu](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Vyberte možnost **analyzovat** a zvolte databázi, která bude začínat analýzou. V závislosti na vašich úlohách může trvat několik minut, než se dokončí analýza. Po dokončení analýzy se zobrazí oznámení na portálu. Analýza provede důkladné přezkoumání vaší databáze. Doporučujeme, abyste provedli analýzu v době mimo špičku.

V okně **doporučení** se zobrazí seznam doporučení, pokud byla nalezena nějaká a související ID dotazu, které vygenerovalo toto doporučení. S ID dotazu můžete použít zobrazení [MySQL. query_store](concepts-query-store.md#mysqlquery_store) a získat další informace o dotazu.

![Nová stránka s doporučeními pro výkon](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Doporučení se nepoužívají automaticky. Pokud chcete doporučení použít, zkopírujte text dotazu a spusťte ho z vašeho klienta podle vlastního výběru. Nezapomeňte otestovat a monitorovat, abyste vyhodnotili doporučení.

## <a name="recommendation-types"></a>Typy doporučení

V současné době se podporují jenom doporučení *Create index* .

### <a name="create-index-recommendations"></a>Vytvořit doporučení indexu

Doporučení *vytvořit index* návrhy nových indexů vám umožní zrychlit nejčastěji spouštěné nebo časově náročné dotazy v zatížení. Tento typ doporučení vyžaduje, aby [úložiště dotazů](concepts-query-store.md) bylo povolené. Úložiště dotazů shromažďuje informace o dotazech a poskytuje podrobné běhové dotazy a statistické údaje o četnosti, které analýza používá, aby provedla doporučení.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [monitorování a ladění](concepts-monitoring.md) v Azure Database for MySQL.