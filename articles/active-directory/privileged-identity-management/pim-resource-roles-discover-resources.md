---
title: Zjišťovat a spravovat prostředky Azure pomocí Privileged Identity managementu | Microsoft Docs
description: Popisuje, jak chránit prostředky Azure pomocí PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: 563c7f24ec4045b46d5bdcf0dc267dbdda2d5a5e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234467"
---
# <a name="discover-and-manage-azure-resources-by-using-privileged-identity-management"></a>Zjišťovat a spravovat prostředky Azure pomocí Privileged Identity Management

Zjistěte, jak chcete zjišťovat a spravovat prostředky Azure, když používáte v Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Tato informace může být užitečné pro organizace, které už používají PIM pro ochranu správce prostředků a vlastníkům předplatné, kteří se zaměřujete na zabezpečení produkční prostředků.

Pokud musíte nejdřív nastavit PIM pro prostředky Azure, budete muset zjišťovat a vyberte prostředky k ochraně PIM. Neexistuje žádné omezení počtu prostředků, které můžete spravovat pomocí PIM. Doporučujeme však počínaje vaše nejdůležitější prostředky (produkční).

> [!NOTE]
> Je možné provádět pouze vyhledejte a vyberte prostředky předplatného pro správu pomocí PIM. Když spravujete předplatné v PIM, můžete také spravovat podřízené prostředky v rámci předplatného.

## <a name="discover-resources"></a>Zjistit prostředky

V portálu Azure přejděte do **Privileged Identity Management** podokně. V nabídce vlevo v **SPRAVOVAT** vyberte **prostředky Azure**.

!["Privileged Identity Management - prostředky Azure" podokno](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Pokud je to poprvé pomocí PIM pro prostředky Azure, nejprve spuštěním zjišťování můžete najít prostředky spravovat. V **zjišťovat prostředky** podokně, vyberte **zjišťovat prostředky** tlačítko Spustit zjišťování prostředí.

![V podokně "Zjišťovat prostředky"](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Pokud jiný správce prostředků nebo adresář ve vaší organizaci už spravuje prostředek služby Azure pomocí PIM, nebo pokud máte přiřazení role vhodné pro prostředek, zobrazení seznamu zobrazí zprávu **zjistit prostředky, nebo aktivace přiřazení role vhodné pokračujte**. 

![Tlačítko "Zjišťovat prostředky" v "privilegované Identity správce - prostředky Azure" podokno](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Když vyberete **zjišťovat prostředky** tlačítko, ať už z hlavní nabídky nebo uprostřed podokně, zobrazí se seznam odběry, které můžete spravovat. Předplatná, která je zvýrazněná, jsou již chráněny pomocí PIM.

> [!NOTE]
> Abyste zabránili odebrání nastavení PIM po předplatné je nastaven na spravované jiný správce prostředků, nemůže být odběr nespravované.

!["Prostředky Azure – zjišťování" podokno](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

V **prostředků** sloupce, umístěte ukazatel myši nad předplatné, které chcete chránit pomocí PIM. Zaškrtněte políčko nalevo od názvu prostředku. Současně můžete vybrat více předplatných.

![Seznam prostředků v "prostředky Azure – zjišťování" podokno](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Chcete-li zahájit proces registrace v horní nabídce vyberte **prostředky spravovat**.

![Tlačítko "Správa prostředků" v "prostředky Azure – zjišťování" podokno](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

Vybrané prostředky se nyní spravují nástrojem PIM. Zavřete obrazovce zjišťování v pravém horním rohu vyberte **X**. Zahájíte správu PIM nastavení a přiřazení členy, v nabídce v horní části **Privileged Identity Management - prostředky Azure** podokně, vyberte **aktualizovat** tlačítko.

![Tlačítko "Aktualizovat" v horní nabídce "Privileged Identity Management - prostředky Azure" podokno](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role](pim-resource-roles-configure-role-settings.md)
- [Přiřazení role v PIM](pim-resource-roles-assign-roles.md)
