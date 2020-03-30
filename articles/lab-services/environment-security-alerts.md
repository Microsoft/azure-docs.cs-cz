---
title: Výstrahy zabezpečení pro prostředí v azure devtest labs
description: Tento článek ukazuje, jak zobrazit výstrahy zabezpečení pro prostředí v DevTest Labs a provést příslušnou akci.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: fbac5a2fab91cdac8ebf626e324f12f209cfade5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588701"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Výstrahy zabezpečení pro prostředí v azure devtest labs
Jako uživatel testovacího prostředí teď můžete zobrazit výstrahy Azure Security Center pro vaše testovací prostředí. Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a řešení ochrany koncových bodů, aby se zjistily skutečné hrozby a snížil počet falešných poplachů. Seznam upřednostňovaných výstrah zabezpečení se zobrazí ve službě Security Center spolu s informacemi, které potřebujete k rychlému prozkoumání problému, a doporučeními týkajícími se řešení útoku. [Další informace o výstrahách zabezpečení v Azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Požadavky
V současné době můžete zobrazit výstrahy zabezpečení pouze pro platformu jako služby (PaaS) prostředí nasazené do testovacího prostředí. Chcete-li tuto funkci otestovat nebo použít, [nasaďte prostředí do testovacího prostředí](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Zobrazení výstrah zabezpečení pro prostředí

1. Na domovské stránce testovacího prostředí vyberte v levé nabídce **výstrahy zabezpečení.** Měli byste vidět počet výstrah zabezpečení (vysoká, střední a nízká). Další informace o [tom, jak jsou výstrahy klasifikovány](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Výstrahy zabezpečení - přehled](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Klikněte pravým tlačítkem myši na tři tečky (...) v posledním sloupci a vyberte **zobrazit výstrahy zabezpečení**. 

    ![Zobrazení výstrah zabezpečení](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Zobrazí se další podrobnosti o výstrahách a doporučeních poradců. Další informace o [správě a reakci na výstrahy zabezpečení v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

    ![Zobrazení výstrah zabezpečení](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Další kroky
Další informace o prostředích najdete v následujících článcích:

- [Vytváření prostředí s více virtuálními počítači a prostředků PaaS pomocí šablon Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurace a použití veřejných prostředí](devtest-lab-configure-use-public-environments.md)
