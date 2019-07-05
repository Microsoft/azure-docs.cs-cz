---
title: Doporučení k výkonu ve službě Azure Database pro MariaDB
description: Tento článek popisuje funkce výkonu doporučení ve službě Azure Database pro MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a2f9b7597022822272692d20976e1da654b9d524
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462058"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Doporučení k výkonu ve službě Azure Database pro MariaDB

**Platí pro:** Azure Database pro MariaDB 10.2

> [!NOTE]
> Doporučení k výkonu je ve verzi preview.

Doporučení k výkonu funkce analyzuje vaše databáze pro vytvoření přizpůsobené návrhy pro zlepšení výkonu. K vytvoření doporučení, analýza zjistí různé vlastnosti databáze, včetně schémat. Povolit [Query Store](concepts-query-store.md) na serveru plně využívat funkci doporučení k výkonu. Pokud schéma výkonu je vypnuto, zapnutí Query Store umožňuje performance_schema a podmnožinu výkonu schématu nástroje potřebné pro funkci. Po implementaci jakékoli výkonu doporučení, měli byste otestovat výkon a vyhodnotit její dopad tyto změny.

## <a name="permissions"></a>Oprávnění

Pro spuštění funkce analýzy za použití funkce Doporučení k výkonu potřebujete oprávnění **vlastníka** nebo **přispěvatele**.

## <a name="performance-recommendations"></a>Doporučení k výkonu

Funkce [Doporučení k výkonu](concepts-performance-recommendations.md) analyzuje úlohy na serveru, aby identifikoval indexy a případně zlepšil výkon.

Otevřít **doporučení k výkonu** z **inteligentní výkonu** části řádku nabídek na stránce portálu Azure pro váš server MariaDB.

![Úvodní stránka Doporučení k výkonu](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Vyberte **analyzovat** a zvolte databázi, která začne analýza. V závislosti na velikosti pracovní zátěže analýza může trvat několik minut. Po dokončení analýzy se zobrazí oznámení na portálu. Analýza provádí podrobné zkoumání vaší databáze. Doporučujeme že provádět analýzy obdobích mimo špičku.

**Doporučení** okna se zobrazí seznam doporučení, pokud některá a ID související dotaz, který vygeneroval toto doporučení. ID dotazu, můžete použít [mysql.query_store](concepts-query-store.md#mysqlquery_store) zobrazení získat další informace o dotazu.

![Nová stránka doporučení výkonu](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Doporučení se automaticky nepoužijí. Na jednotlivá doporučení použít, zkopírujte text dotazu a spusťte ze svého klienta podle výběru. Nezapomeňte otestovat a monitorování pro vyhodnocení doporučení.

## <a name="recommendation-types"></a>Doporučení typy

V současné době pouze *Create Index* doporučení jsou podporovány.

### <a name="create-index-recommendations"></a>Vytvoření doporučení indexu

*Vytvořit Index* doporučení navrhnout novou indexy pro urychlení nejčastěji spuštění nebo časově náročné dotazů v úloze. Tento typ doporučení vyžaduje [Query Store](concepts-query-store.md) povolit. Query Store shromažďuje informace o dotazu a poskytuje podrobné dotazů modulu runtime a četnost statistické údaje, které využívá analýzy, aby doporučení.

## <a name="next-steps"></a>Další postup

- Další informace o [monitorování a optimalizace](concepts-monitoring.md) ve službě Azure Database pro MariaDB.