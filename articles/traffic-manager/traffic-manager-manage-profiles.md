---
title: Správa profilů Azure Traffic Manageru | Dokumentace Microsoftu
description: Tento článek vám pomůže vytvořit, zakázat, povolit nebo odstranit profil Azure Traffic Manageru.
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: kumud
ms.openlocfilehash: e19f8f373fed8c7e9ab64f2fc9e34eba182af717
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29398052"
---
# <a name="manage-an-azure-traffic-manager-profile"></a>Správa profilu Azure Traffic Manageru

Profily Traffic Manageru využívají metody směrování provozu k řízení provozu do vašich cloudových služeb nebo na koncové body webů. Tento článek vysvětluje, jak tyto profily vytvořit a spravovat.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Profil Traffic Manageru můžete vytvořit pomocí webu Azure Portal. Po vytvoření profilu můžete nakonfigurovat koncové body, monitorování a další nastavení na webu Azure Portal. Traffic Manager podporuje až 200 koncových bodů na jeden profil. Většina scénářů použití však vyžaduje jen několik koncových bodů.

### <a name="to-create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com). Pokud ještě účet nemáte, můžete si zaregistrovat [zkušební verzi na měsíc zdarma](https://azure.microsoft.com/free/). 
2. Klikněte na **Vytvořit prostředek** > **Sítě** > **Profil služby Traffic Manager** > **Vytvořit**.
4. Část **Vytvořit profil služby Traffic Manager** vyplňte následovně:
    1. Do pole **Název** zadejte název profilu. Tento název musí být jedinečný v rámci zóny trafficmanager.net a ve výsledcích názvu DNS <name>, trafficmanager.net.
    2. Jakou **Metodu směrování** vyberte **Priorita**.
    3. V poli **Předplatné** vyberte předplatné, v rámci kterého chcete profil vytvořit.
    4. V části **Skupina prostředků** vytvořte novou skupinu prostředků, do které chcete profil umístit.
    5. V poli **Umístění skupiny prostředků** vyberte umístění skupiny prostředků. Toto nastavení se týká umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manageru, který se nasadí globálně.
    6. Klikněte na možnost **Vytvořit**.
    7. Po dokončení globálního nasazení profilu Traffic Manageru bude uveden jako jeden z prostředků v příslušné skupině prostředků.

## <a name="disable-enable-or-delete-a-profile"></a>Zakázání, povolení nebo odstranění profilu

Existující profil můžete zakázat, aby Traffic Manager neodkazoval požadavky uživatelů na nakonfigurované koncové body. Pokud profil Traffic Manageru zakážete, zůstane zachován beze změny včetně informací, které obsahuje, a je možné ho upravovat v rámci rozhraní Traffic Manageru.  Odkazování se obnoví, až profil znovu povolíte. Když vytvoříte profil Traffic Manageru na portálu Azure Portal, je automaticky povolen. Pokud se rozhodnete, že profil již nebude potřebný, můžete ho odstranit.

### <a name="to-disable-a-profile"></a>Zakázání profilu

1. Pokud používáte vlastní název domény, změňte si na internetovém serveru DNS záznam CNAME tak, aby už neodkazoval na váš profil Traffic Manageru.
2. Nasměrování provozu do koncových bodů prostřednictvím nastavení profilu Traffic Manageru se zastaví.
3. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com).
2. Na panelu hledání na portálu vyhledejte název **profilu služby Traffic Manager**, který chcete upravit, a pak na tento profil služby Traffic Manager klikněte v zobrazených výsledcích.
3. Klikněte na **Přehled** > **Zakázat**.
4. Potvrďte zakázání profilu služby Traffic Manager.

### <a name="to-enable-a-profile"></a>Povolení profilu

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com).
2. Na panelu hledání na portálu vyhledejte název **profilu služby Traffic Manager**, který chcete upravit, a pak na tento profil služby Traffic Manager klikněte v zobrazených výsledcích.
3. Klikněte na **Přehled** > **Povolit**.
1. Pokud používáte vlastní název domény, vytvořte si na internetovém serveru DNS záznam prostředku DNS, který bude odkazovat na název domény ve vašem profilu Traffic Manageru.
2. Provoz se směruje do příslušných koncových bodů.

### <a name="to-delete-a-profile"></a>Odstranění profilu

1. Ověřte, že záznam prostředku DNS na vašem serveru DNS pro internet již nepoužívá záznam prostředku CNAME, který odkazuje na název domény pro váš profil Traffic Manageru.
2. Na panelu hledání na portálu vyhledejte název **profilu služby Traffic Manager**, který chcete upravit, a pak na tento profil služby Traffic Manager klikněte v zobrazených výsledcích.
3. Klikněte na **Přehled** > **Odstranit**.
4. Potvrďte odstranění profilu služby Traffic Manager.

## <a name="next-steps"></a>Další kroky

* [Přidání koncového bodu](traffic-manager-endpoints.md)
* [Konfigurace metody prioritního směrování](traffic-manager-configure-priority-routing-method.md)
* [Konfigurace metody geografického směrování](traffic-manager-configure-geographic-routing-method.md) 
* [Konfigurace metody váženého směrování](traffic-manager-configure-weighted-routing-method.md)
* [Konfigurace metody směrování podle výkonu](traffic-manager-configure-performance-routing-method.md)
