---
title: Doporučení k výkonu ve službě Azure Database for PostgreSQL
description: Tento článek popisuje doporučení ohledně výkonu, které jeden můžete získat ve službě Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 6de302dbcfa9d6d1d2b311f41b03d8e54aeb63f6
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395436"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Doporučení k výkonu ve službě Azure Database for PostgreSQL

**Platí pro:** Azure Database for PostgreSQL 9.6 a 10

> [!IMPORTANT]
> Doporučení k výkonu je ve verzi Public Preview v omezeném počtu oblastí.

Doporučení k výkonu funkcí Určuje horní indexy, které je možné vytvořit v Azure Database for PostgreSQL server ke zlepšení výkonu. K vytvoření doporučení indexu, tato funkce vezme v úvahu různé vlastnosti databáze, včetně jeho schématu a úloh podle Query Store. Po implementaci jakékoli doporučení výkon, by měl zákazníkům testování výkonu vyhodnotit její dopad těchto změn. 

## <a name="permissions"></a>Oprávnění
**Vlastník** nebo **Přispěvatel** oprávnění potřebná pro spuštění analýzy pomocí funkce doporučení k výkonu.

## <a name="performance-recommendations"></a>Doporučení k výkonu
[Doporučení k výkonu](concepts-performance-recommendations.md) funkce analyzuje úlohy napříč serveru k identifikaci indexy má potenciál zlepšit výkon.

Otevřít **doporučení k výkonu** z **podpora a řešení potíží** části řádku nabídek na stránce portálu Azure pro váš server PostgreSQL.

![Úvodní stránka doporučení k výkonu](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

Vyberte **analyzovat** a zvolte databázi. Tím se začne analýza. V závislosti na velikosti pracovní zátěže to může trvat několik minut. Po dokončení analýzy, budou se oznámení na portálu.

**Doporučení k výkonu** okna se zobrazí seznam doporučení, pokud některá. Doporučení se zobrazí informace o příslušné **databáze**, **tabulky**, **sloupec**, a **velikost indexu**.

![Nová stránka doporučení výkonu](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Implementace doporučení, zkopírujte text dotazu a spusťte ze svého klienta podle výběru.

## <a name="next-steps"></a>Další postup
- Další informace o [monitorování a optimalizace](concepts-monitoring.md) ve službě Azure Database for PostgreSQL.

