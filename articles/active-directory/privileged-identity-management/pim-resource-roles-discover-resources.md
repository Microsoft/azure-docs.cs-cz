---
title: Privileged Identity Management pro prostředky Azure - zjišťovat a spravovat prostředky Azure | Microsoft Docs
description: Popisuje, jak chránit prostředky Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 78650e47ec92aa144e4ccc8c57f309240bf31ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="discover-and-manage-azure-resources"></a>Zjišťovat a spravovat prostředky Azure

Pokud vaše organizace už používá Azure AD PIM k ochraně správci ve vašem adresáři nebo jste vlastník předplatného, vyhledávání k zabezpečení prostředků produkční jste na správném místě.

Když poprvé povolíte PIM pro prostředky Azure, musíte zjistit a vyberte prostředky k ochraně PIM. Neexistuje žádné omezení počtu prostředků, které můžete spravovat pomocí PIM, ale doporučujeme nejprve spustit s vaše nejdůležitější prostředky (produkční).

> [!Note]
> Pouze prostředky předplatného můžete vyhledávat a vybraných pro správu. Výběr ke správě předplatného v PIM umožňuje správu také všechny podřízené prostředky.

## <a name="discover-resources"></a>Zjišťovat prostředky

Přejděte do Azure AD PIM a vyberte prostředky Azure v části Správa levé navigační nabídce.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Pokud je to poprvé pomocí PIM pro prostředky Azure musíte pro spouštění funkce zjišťování můžete najít prostředky spravovat.
Klikněte na tlačítko "Zjišťovat prostředky" v centru obrazovky spustíte zjišťování prostředí.

![](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Pokud prostředek služby Azure s PIM už spravuje jiný správce prostředků nebo adresář ve vaší organizaci, nebo nemáte přiřazení role vhodné pro prostředek, zobrazení seznamu bude obsahovat zprávy: "zjistit prostředky, nebo aktivovat vhodné role přiřazení pokračujte". 

![](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Vyberete-li na tlačítko v panelu akcí nebo středu obrazovky zjistit prostředky, zobrazí se seznam dostupných pro správu předplatných. V tomto okamžiku zobrazí-li zvýrazněné odběry ji znamená, že jsou chráněné PIM.

> [!Note]
> Odebírá se nastavení PIM, aby jiný správce prostředků po předplatné se spravuje nemůže nespravované.

![](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

Podržte ukazatel nad předplatné, které chcete chránit pomocí PIM a vyberte pole zcela vlevo řádku. Současně můžete vybrat více předplatných.

![](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Registrace zahájíte proces kliknutím na tlačítko "Správa prostředků" na řádku v horní části obrazovky.

![](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

Vybrané prostředky se nyní spravují nástrojem PIM. Zavřete obrazovce zjišťování v pravém horním rohu stránky "X" a klikněte na tlačítko Aktualizovat na panelu v horní části obrazovky prostředky spravovat Azure zahájíte správu PIM nastavení a přiřazení členy.

![](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Další postup

[Konfigurace nastavení role](pim-resource-roles-configure-role-settings.md)

[Přiřazení role v PIM](pim-resource-roles-assign-roles.md)
