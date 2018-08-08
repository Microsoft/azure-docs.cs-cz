---
title: Přiřazení rolí pro prostředky Azure s použitím Privileged Identity Management | Dokumentace Microsoftu
description: Popisuje, jak přiřadit role v PIM.
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
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 52320fe160fc1df2997dd419b406fce24f43b786
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622972"
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>Přiřazení rolí pro prostředky Azure s použitím Privileged Identity Management

## <a name="assign-roles"></a>Přiřazení rolí

Přiřadit uživatele nebo skupiny k roli při zobrazení **role** podokně, vyberte roli a pak vyberte **přidat uživatele**. 

![Podokno "Role" tlačítko "Přidat uživatele"](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

Můžete také vybrat **přidat uživatele** z **členy** podokně.

!["Členy" podokno s tlačítko "Přidat uživatele"](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Pokud přidáváte uživatele nebo skupiny z **členy** podokně, budete muset: 

1. Zvolte roli z **vybrat roli** podokno, abyste mohli vybrat uživatele nebo skupinu.

   !["Vybrat roli" podokno](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Zvolte uživatele nebo skupiny z adresáře.

3. Z rozevírací nabídky vyberte typ odpovídající přiřazení: 

   - **Za běhu**: poskytuje členům uživatele nebo skupinu oprávnění, ale není trvalý přístup k roli během zadaného období nebo odkládat donekonečna (Pokud je nakonfigurovaný v nastavení role). 
   - **Přímé**: nevyžaduje, aby uživatel nebo skupina členy aktivovat přiřazení role (označuje se jako trvalý přístup). Doporučujeme používat přímého přiřazení pro krátkodobé použití, ve kterém nesmí být nutný přístup k po dokončení úkolu. Příklady se posune na volání a časově závislé aktivity.

4. Pokud má být přiřazení trvalé (trvalá způsobilost pro přiřazení v čase, nebo trvale aktivní pro přímého přiřazení), zaškrtněte políčko níže **typ přiřazení** pole.

   ![Podokno "Nastavení členství" s "přiřazení zadejte" a související zaškrtávacího políčka](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >Zaškrtávací políčko může neupravitelných, pokud jiný správce má zadanou přiřazení maximální doba trvání pro každý typ přiřazení v nastavení role.

   Chcete-li určit dobu trvání konkrétní přiřazení, zrušte zaškrtnutí políčka a upravit počáteční a koncové datum a čas polí.

   ![Podokno "Nastavení členství" s polí pro počáteční datum, čas zahájení, koncové datum a čas ukončení](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Správa přiřazení rolí

Správci můžou Spravovat přiřazení rolí tak, že vyberete buď **role** nebo **členy** v levém podokně. Výběr **role** umožňuje správcům k určení rozsahu jejich úlohy správy pro konkrétní role. Výběr **členy** zobrazí všechny uživatele a skupiny přiřazení rolí pro prostředek.

!["Role"](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

!["Členy" podokno](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Pokud máte roli čekající na aktivaci, zobrazí se banner s oznámením v horní části podokna při zobrazení členství.


## <a name="modify-existing-assignments"></a>Upravte existující přiřazení

Chcete-li upravit existující přiřazení ze zobrazení podrobností uživatele nebo skupiny, vyberte **změnit nastavení** z panelu akcí. Změnit typ přiřazení k **pouze v čase** nebo **přímé**.

![Podokno "Podrobnosti o uživateli" s "Nastavení" tlačítko](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
