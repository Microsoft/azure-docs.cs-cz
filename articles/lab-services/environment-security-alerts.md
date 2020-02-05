---
title: Výstrahy zabezpečení pro prostředí v Azure DevTest Labs
description: V tomto článku se dozvíte, jak zobrazit výstrahy zabezpečení pro prostředí v DevTest Labs a provést příslušnou akci.
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
ms.openlocfilehash: 4ca17bece33107de756eb221e14eaab851660a99
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992232"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Výstrahy zabezpečení pro prostředí v Azure DevTest Labs
V tomto článku se dozvíte, jak zobrazit výstrahy zabezpečení pro prostředí v Azure DevTest Labs. 

## <a name="prerequisites"></a>Požadavky
V současné době můžete zobrazit výstrahy zabezpečení pouze pro prostředí nasazená do testovacího prostředí. K otestování nebo použití této funkce nasaďte prostředí do testovacího prostředí. 

## <a name="view-security-alerts-for-an-environment"></a>Zobrazení výstrah zabezpečení pro prostředí

1. Na domovské stránce testovacího prostředí vyberte v nabídce vlevo možnost **výstrahy zabezpečení** . Měl by se zobrazit počet výstrah zabezpečení (vysoká, střední a nízká).

    ![Výstrahy zabezpečení – přehled](./media/environment-security-alerts/security-alerts-overview-page.png)
2. V posledním sloupci klikněte pravým tlačítkem na tři tečky (...) a vyberte **Zobrazit výstrahy zabezpečení**. 

    ![Zobrazení výstrah zabezpečení](./media/environment-security-alerts/view-security-alerts-menu.png)
3. Zobrazí se další podrobnosti o výstrahách a doporučeních pro poradce. 

    ![Zobrazení výstrah zabezpečení](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Další kroky
Další informace o prostředích najdete v následujících článcích:

- [Vytvoření prostředí s více virtuálními počítači a prostředků PaaS pomocí šablon Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurace a používání veřejných prostředí](devtest-lab-configure-use-public-environments.md)
