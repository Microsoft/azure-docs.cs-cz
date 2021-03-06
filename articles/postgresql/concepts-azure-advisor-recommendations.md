---
title: Azure Advisor pro PostgreSQL
description: Přečtěte si o Azure Advisor doporučení pro PostgreSQL.
author: alau-ms
ms.author: alau
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: a44f808daf32a1175035005067dfbb5edd664ef6
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310049"
---
# <a name="azure-advisor-for-postgresql"></a>Azure Advisor pro PostgreSQL
Přečtěte si, jak se Azure Advisor aplikuje na Azure Database for PostgreSQL a Získejte odpovědi na běžné otázky.
## <a name="what-is-azure-advisor-for-postgresql"></a>Co je Azure Advisor pro PostgreSQL?
Systém Azure Advisor používá telemetrii k vystavování doporučení výkonu a spolehlivosti pro vaši databázi PostgreSQL. Doporučení poradce jsou rozdělená mezi naše nabídky databáze PostgreSQL:
* Azure Database for PostgreSQL – Jeden server
* Azure Database for PostgreSQL – flexibilní Server
* Azure Database for PostgreSQL – Hyperscale (Citus)

Některá doporučení jsou společná pro více nabídek produktů, zatímco další doporučení jsou založená na optimalizaci pro konkrétní produkt.
## <a name="where-can-i-view-my-recommendations"></a>Kde se můžu podívat na moje doporučení?
Doporučení jsou k dispozici na navigačním panelu **Přehled** v Azure Portal. Náhled se zobrazí jako oznámení banneru a podrobnosti si můžete prohlédnout v části **oznámení** , která se nachází přímo pod grafy využití prostředků.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Snímek obrazovky Azure Portal zobrazující doporučení Azure Advisor":::

## <a name="recommendation-types"></a>Typy doporučení
Azure Database for PostgreSQL určit prioritu následujících typů doporučení:
* **Výkon**: pro zlepšení rychlosti PostgreSQL serveru. To zahrnuje využití procesoru, tlak v paměti, sdružování připojení, využití disku a parametry serveru specifické pro daný produkt. Další informace najdete v tématu [doporučení k výkonu služby Advisor](../advisor/advisor-performance-recommendations.md).
* **Spolehlivost**: zajištění a zlepšení kontinuity důležitých podnikových databází. To zahrnuje omezení úložiště, omezení připojení a doporučení pro distribuci dat ve škálování na více míst. Další informace najdete v tématu [doporučení pro spolehlivost Advisoru](../advisor/advisor-high-availability-recommendations.md).
* **Náklady**: pro optimalizaci a snížení celkové útraty Azure. To zahrnuje doporučení pro správné velikosti serveru. Další informace najdete v tématu [doporučení pro náklady na poradce](../advisor/advisor-cost-recommendations.md).

## <a name="understanding-your-recommendations"></a>Princip doporučení
* **Denní plán**: pro databáze Azure PostgreSQL zkontrolujeme telemetrie serveru a doporučení pro vydávání denních plánů. Pokud provedete změnu konfigurace serveru, existující doporučení zůstanou viditelná až do opětovného prověření telemetrie v následujícím dni. 
* **Historie výkonu**: některá z našich doporučení jsou založená na historii výkonu. Tato doporučení se zobrazí až po dokončení provozu serveru se stejnou konfigurací 7 dní. Díky tomu můžeme v rámci trvalého časového období detekovat vzory těžkého využití (například vysoké aktivity procesoru nebo velký objem připojení). Pokud zřizujete nový server nebo změníte novou konfiguraci vCore, budou tato doporučení dočasně pozastavena. Tím zabráníte tomu, aby se starší telemetrie spouštěla doporučení na nově konfigurovaném serveru. To ale také znamená, že doporučení založená na historii výkonu nemusí být identifikována okamžitě.

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu [přehled Azure Advisor](../advisor/advisor-overview.md).
