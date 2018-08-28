---
title: Zjišťovat a spravovat prostředky Azure s použitím Privileged Identity Management | Dokumentace Microsoftu
description: Popisuje, jak chránit prostředky Azure pomocí PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: f4b6fafe40fe00492bac7bd6c28268321d4516db
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43104350"
---
# <a name="discover-and-manage-azure-resources-by-using-privileged-identity-management"></a>Zjišťovat a spravovat prostředky Azure s použitím Privileged Identity Management

Zjistěte, jak zjišťovat a spravovat prostředky Azure, když použijete v Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Tyto informace mohou být užitečné pro organizace, které už používají k ochraně prostředků správce PIM a vlastníkům předplatného, kteří chtějí zabezpečit prostředky v produkčním prostředí.

Když poprvé nastavujete PIM pro prostředky Azure, budete muset zjistit a vyberte prostředky, které chcete chránit pomocí PIM. Neexistuje žádné omezení počtu prostředků, které můžete spravovat pomocí služby PIM. Však doporučujeme začít s prostředky těch nejdůležitějších (produkce).

> [!NOTE]
> Je možné pouze vyhledejte a vyberte správu skupiny nebo předplatného prostředky spravovat pomocí PIM. Když spravujete skupinu pro správu nebo předplatného v PIM, můžete také spravovat její podřízené prostředky.

## <a name="discover-resources"></a>Zjistit prostředky

Na webu Azure Portal, přejděte **Privileged Identity Management** podokně. V nabídce vlevo v **SPRAVOVAT** vyberte **prostředky Azure**.

!["Privileged Identity Management – prostředky Azure" podokno](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Pokud je toto vaše první přihlášení pomocí PIM pro prostředky Azure, nejprve spusťte funkci zjišťování k vyhledání prostředků ke správě. V **zjistit prostředky** podokně, vyberte **zjistit prostředky** tlačítko a spuštění zjišťování.

![V podokně "Zjistit prostředky"](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Pokud jiný prostředek nebo adresáře správce ve vaší organizaci pomocí PIM už spravuje prostředek Azure, nebo pokud máte přiřazení oprávněné role pro určitý prostředek, zobrazení seznamu zobrazuje zprávu **vyhledejte prostředky nebo aktivujte přiřazení oprávněné role pokračujte**. 

![Tlačítko "Zjistit prostředky" v "Privileged Identity Manager - prostředků Azure" podokno](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Když vyberete **zjistit prostředky** tlačítko, ať už z hlavní nabídky nebo uprostřed v podokně se zobrazí seznam předplatných, které můžete spravovat. Předplatná, která je zvýrazněná jsou již chráněny pomocí PIM.

> [!NOTE]
> Abyste zabránili odebírá se nastavení PIM, po přihlášení k odběru nastavení spravovaných jiný správce prostředků, nemůže být předplatné nespravované.

!["Prostředky Azure – zjišťování" podokno](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

V **prostředků** sloupce, umístěte ukazatel myši nad předplatné, které chcete chránit pomocí služby PIM. Zaškrtněte políčko nalevo od názvu prostředků. Současně můžete vybrat několik předplatných.

![Seznam prostředků v "prostředky Azure – zjišťování" podokno](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Chcete-li zahájit proces registrace, v horní nabídce vyberte **prostředky spravovat**.

![Tlačítko "Spravovat prostředky" v "prostředky Azure – zjišťování" podokno](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

Vybrané prostředky se teď spravují pomocí PIM. Zavřít zjišťování obrazovky v pravém horním rohu vyberte **X**. Začněte správu nastavení PIM a přiřazování členů, v nabídce v horní části **Privileged Identity Management – prostředky Azure** podokně, vyberte **aktualizovat** tlačítko.

![Tlačítko "Obnovit" v horní nabídce "Privileged Identity Management – prostředky Azure" podokno](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role](pim-resource-roles-configure-role-settings.md)
- [Přiřazení rolí v PIM](pim-resource-roles-assign-roles.md)
