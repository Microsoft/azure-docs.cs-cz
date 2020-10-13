---
title: Výstrahy zabezpečení pro prostředí v Azure DevTest Labs
description: V tomto článku se dozvíte, jak zobrazit výstrahy zabezpečení pro prostředí v DevTest Labs a provést příslušnou akci.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c9f7cf9fe7ab0e3f573470228ee1962aa92ccaef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91308686"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Výstrahy zabezpečení pro prostředí v Azure DevTest Labs
Jako uživatel testovacího prostředí teď můžete zobrazit Azure Security Center výstrahy pro testovací prostředí. Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a řešení ochrany koncových bodů, aby se zjistily skutečné hrozby a snížil počet falešných poplachů. Seznam upřednostňovaných výstrah zabezpečení se zobrazí ve službě Security Center spolu s informacemi, které potřebujete k rychlému prozkoumání problému, a doporučeními týkajícími se řešení útoku. [Přečtěte si další informace o výstrahách zabezpečení v Azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Požadavky
V současné době můžete zobrazit výstrahy zabezpečení pouze pro prostředí PaaS (Platform as a Service) nasazená v testovacím prostředí. K otestování nebo použití této funkce [Nasaďte prostředí do testovacího prostředí](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Zobrazení výstrah zabezpečení pro prostředí

1. Na domovské stránce testovacího prostředí vyberte v nabídce vlevo možnost **výstrahy zabezpečení** . Měl by se zobrazit počet výstrah zabezpečení (vysoká, střední a nízká). Přečtěte si další informace o [tom, jak jsou výstrahy klasifikované](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Výstrahy zabezpečení – přehled](./media/environment-security-alerts/security-alerts-overview-page.png)
2. V posledním sloupci klikněte pravým tlačítkem na tři tečky (...) a vyberte **Zobrazit výstrahy zabezpečení**. 

    ![Snímek obrazovky zobrazující stránku výstrahy zabezpečení s vybranou možnost Zobrazit výstrahy zabezpečení](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Zobrazí se další podrobnosti o výstrahách a doporučeních pro poradce. Přečtěte si další informace o [správě a reagování na výstrahy zabezpečení v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

    ![Zobrazení výstrah zabezpečení](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Další kroky
Další informace o prostředích najdete v následujících článcích:

- [Vytvoření prostředí s více virtuálními počítači a prostředků PaaS pomocí šablon Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurace a použití veřejných prostředí](devtest-lab-configure-use-public-environments.md)
