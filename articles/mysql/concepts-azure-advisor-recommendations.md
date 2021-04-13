---
title: Azure Advisor pro MySQL
description: Přečtěte si o Azure Advisor doporučení pro MySQL.
author: alau-ms
ms.author: alau
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4b3d750c930defbfcb7db6d4d67210e001e3ce8b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315414"
---
# <a name="azure-advisor-for-mysql"></a>Azure Advisor pro MySQL
Přečtěte si, jak se Azure Advisor aplikuje na Azure Database for MySQL a Získejte odpovědi na běžné otázky.
## <a name="what-is-azure-advisor-for-mysql"></a>Co je Azure Advisor pro MySQL?
Systém Azure Advisor používá telemetrii k vystavování doporučení výkonu a spolehlivosti pro vaši databázi MySQL. Doporučení Advisoru se rozdělí mezi naše nabídky databáze MySQL:
* Azure Database for MySQL – jeden server
* Azure Database for MySQL – flexibilní Server

Některá doporučení jsou společná pro více nabídek produktů, zatímco další doporučení jsou založená na optimalizaci pro konkrétní produkt.
## <a name="where-can-i-view-my-recommendations"></a>Kde se můžu podívat na moje doporučení?
Doporučení jsou k dispozici na navigačním panelu **Přehled** v Azure Portal. Náhled se zobrazí jako oznámení banneru a podrobnosti si můžete prohlédnout v části **oznámení** , která se nachází přímo pod grafy využití prostředků.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Snímek obrazovky Azure Portal zobrazující doporučení Azure Advisor":::

## <a name="recommendation-types"></a>Typy doporučení
Azure Database for MySQL určit prioritu následujících typů doporučení:
* **Výkon**: pro zlepšení rychlosti serveru MySQL. To zahrnuje využití procesoru, tlak v paměti, sdružování připojení, využití disku a parametry serveru specifické pro daný produkt. Další informace najdete v tématu [doporučení k výkonu služby Advisor](../advisor/advisor-performance-recommendations.md).
* **Spolehlivost**: zajištění a zlepšení kontinuity důležitých podnikových databází. To zahrnuje i omezení úložiště a omezení počtu připojení. Další informace najdete v tématu [doporučení pro spolehlivost Advisoru](../advisor/advisor-high-availability-recommendations.md).
* **Náklady**: pro optimalizaci a snížení celkové útraty Azure. To zahrnuje doporučení pro správné velikosti serveru. Další informace najdete v tématu [doporučení pro náklady na poradce](../advisor/advisor-cost-recommendations.md).

## <a name="understanding-your-recommendations"></a>Princip doporučení
* **Denní plán**: u databází Azure MySQL kontrolujeme telemetrie serveru a doporučení pro vydávání denních plánů. Pokud provedete změnu konfigurace serveru, existující doporučení zůstanou viditelná až do opětovného prověření telemetrie v následujícím dni. 
* **Historie výkonu**: některá z našich doporučení jsou založená na historii výkonu. Tato doporučení se zobrazí až po dokončení provozu serveru se stejnou konfigurací 7 dní. Díky tomu můžeme v rámci trvalého časového období detekovat vzory těžkého využití (například vysoké aktivity procesoru nebo velký objem připojení). Pokud zřizujete nový server nebo změníte novou konfiguraci vCore, budou tato doporučení dočasně pozastavena. Tím zabráníte tomu, aby se starší telemetrie spouštěla doporučení na nově konfigurovaném serveru. To ale také znamená, že doporučení založená na historii výkonu nemusí být identifikována okamžitě.

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu [přehled Azure Advisor](../advisor/advisor-overview.md).
