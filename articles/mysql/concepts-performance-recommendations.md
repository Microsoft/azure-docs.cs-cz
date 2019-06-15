---
title: Doporučení k výkonu ve službě Azure Database for MySQL
description: Tento článek popisuje funkce výkonu doporučení ve službě Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 569ef6e9f91fdd728c5d230e2a6c46a7b01e5a62
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078817"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Doporučení k výkonu ve službě Azure Database for MySQL

**Platí pro:** Azure Database for MySQL 5.7

> [!NOTE]
> Doporučení k výkonu je ve verzi preview. Podpora pro doporučení k výkonu na webu Azure Portal se nasazuje a ještě možná není k dispozici ve vaší oblasti.

Doporučení k výkonu funkce analyzuje vaše databáze pro vytvoření přizpůsobené návrhy pro zlepšení výkonu. K vytvoření doporučení, analýza zjistí různé vlastnosti databáze, včetně schémat. Povolit [Query Store](concepts-query-store.md) na serveru plně využívat funkci doporučení k výkonu. Pokud schéma výkonu je vypnuto, zapnutí Query Store umožňuje performance_schema a podmnožinu výkonu schématu nástroje potřebné pro funkci. Po implementaci jakékoli výkonu doporučení, měli byste otestovat výkon a vyhodnotit její dopad tyto změny.

## <a name="permissions"></a>Oprávnění

Pro spuštění funkce analýzy za použití funkce Doporučení k výkonu potřebujete oprávnění **vlastníka** nebo **přispěvatele**.

## <a name="performance-recommendations"></a>Doporučení k výkonu

Funkce [Doporučení k výkonu](concepts-performance-recommendations.md) analyzuje úlohy na serveru, aby identifikoval indexy a případně zlepšil výkon.

Otevřít **doporučení k výkonu** z **inteligentní výkonu** části řádku nabídek na stránce portálu Azure pro váš server MySQL.

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
- Další informace o [monitorování a optimalizace](concepts-monitoring.md) ve službě Azure Database for MySQL.