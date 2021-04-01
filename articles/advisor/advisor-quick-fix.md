---
title: Rychlá oprava nápravy pro doporučení poradce
description: Provést hromadnou nápravu pomocí rychlé opravy v Advisoru
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: a9c86a7ae510d9657f64c71db2aa8c4e3e558f52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90968588"
---
# <a name="quick-fix-remediation-for-advisor"></a>Rychlá oprava nápravy pro poradce
**Rychlá oprava** umožňuje rychlejší a snazší způsob nápravy doporučení na více prostředků. Poskytuje možnost pro hromadné nápravy prostředků a pomáhá optimalizovat vaše předplatné rychleji díky nápravě ve velkém rozsahu pro vaše prostředky.
Tato funkce je k dispozici pouze pro určitá doporučení prostřednictvím Azure Portal.


## <a name="steps-to-use-quick-fix"></a>Postup použití příkazu "Rychlá oprava"

1. V seznamu doporučení s popisem **rychlé opravy** klikněte na doporučení.

   :::image type="content" source="./media/quick-fix-1.png" alt-text="{Snímek obrazovky Azure Advisor, který zobrazuje popisky rychlých oprav v doporučeních.}":::
   
   *Ceny v imagi jsou třeba jenom pro účely.*

2. Na stránce s podrobnostmi o doporučení uvidíte seznam prostředků, pro které máte toto doporučení. Vyberte všechny prostředky, které chcete opravit pro doporučení.

   :::image type="content" source="./media/quick-fix-2.png" alt-text="Snímek okna ovlivněných prostředků s vybranými položkami seznamu a tlačítkem pro rychlé opravy":::
   
   *Ceny v imagi jsou třeba jenom pro účely.*

3. Po výběru prostředků klikněte na tlačítko **Rychlá oprava** a proveďte hromadnou nápravu.

   > [!NOTE]
   > Některé z uvedených prostředků můžou být zakázané, protože nemáte příslušná oprávnění k jejich úpravám.
   
   > [!NOTE]
   > Pokud existují další dopady, kromě výhod uvedených v poradci, budete informováni o zkušenostech, které vám pomůžou při rozhodování o nápravě.
   
4. Zobrazí se oznámení o dokončení opravy. V případě, že existují prostředky, které nejsou opraveny a prostředky ve vybraném režimu v zobrazení seznamu prostředků, se zobrazí chyba.  


## <a name="next-steps"></a>Další kroky

Další informace o doporučeních služby Advisor najdete v těchto tématech:
* [Seznámení s Azure Advisorem](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Doporučení pro náklady na poradce](advisor-cost-recommendations.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
* [REST API Advisoru](/rest/api/advisor/)
