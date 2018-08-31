---
title: Vyhledejte prostředky Azure ke správě v PIM | Dokumentace Microsoftu
description: Zjistěte, jak zjistit prostředky Azure pro správu v Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: b5d48b3f854afaa79574e0ec13cff91f60396ac6
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190654"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Vyhledejte prostředky Azure ke správě v PIM

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

- [Konfigurace nastavení role prostředků Azure v PIM](pim-resource-roles-configure-role-settings.md)
- [Přiřazení role prostředků Azure v PIM](pim-resource-roles-assign-roles.md)
