---
title: Doporučení k výkonu ve službě Azure Database for PostgreSQL
description: Tento článek popisuje doporučení ohledně výkonu, které jeden můžete získat ve službě Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 46a4e69ecb08276e12ccc197de2d3ad838628b78
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378597"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Doporučení k výkonu ve službě Azure Database for PostgreSQL

**Platí pro:** Azure Database for PostgreSQL 9.6 a 10

> [!IMPORTANT]
> Doporučení k výkonu je ve verzi Public Preview.

Doporučení k výkonu funkcí Určuje horní indexy, které je možné vytvořit v Azure Database for PostgreSQL server ke zlepšení výkonu. K vytvoření doporučení indexu, tato funkce vezme v úvahu různé vlastnosti databáze, včetně jeho schématu a úloh podle Query Store. Po implementaci jakékoli doporučení výkon, by měl zákazníkům testování výkonu vyhodnotit její dopad těchto změn. 

## <a name="permissions"></a>Oprávnění
Pro spuštění funkce analýzy za použití funkce Doporučení k výkonu potřebujete oprávnění **vlastníka** nebo **přispěvatele**.

## <a name="performance-recommendations"></a>Doporučení k výkonu
Funkce [Doporučení k výkonu](concepts-performance-recommendations.md) analyzuje úlohy na serveru, aby identifikoval indexy a případně zlepšil výkon.

Otevřete **Doporučení k výkonu** z části nabídky **Podpora a řešení potíží** na stránce portálu Azure pro váš server PostgreSQL.

![Úvodní stránka Doporučení k výkonu](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

Vyberte **Analyzovat** a zvolte databázi. Tím se spustí analýza. V závislosti na velikosti pracovní zátěže to může trvat několik minut. Po dokončení analýzy se zobrazí oznámení na portálu.

Okno **Doporučení k výkonu** zobrazí seznam doporučení, jestliže byla nějaká nalezena. Doporučení zobrazí informace o příslušné **databázi**, **tabulce**, **sloupci** a **velikosti indexu**.

![Nová stránka doporučení výkonu](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Chcete-li implementovat doporučení, zkopírujte text dotazu a spusťte z klienta svého výběru.

## <a name="next-steps"></a>Další postup
- Další informace o [sledování a ladění ](concepts-monitoring.md) ve službě Azure Database for PostgreSQL.

