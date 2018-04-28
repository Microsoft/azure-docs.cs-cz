---
title: Přiřazení role pro prostředků Azure pomocí Privileged Identity managementu | Microsoft Docs
description: Popisuje, jak přiřadit role v PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 501f063992d2f5c7769a5c9059b346aa2b5c2bb4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>Přiřazení role pro prostředků Azure pomocí Privileged Identity Management

## <a name="assign-roles"></a>Přiřazení rolí

Když zobrazujete přiřadit uživatele nebo skupiny k roli **role** podokně, vyberte roli a potom vyberte **přidat uživatele**. 

!["Role" podokně s tlačítko "Přidat uživatele"](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

Můžete také vybrat **přidat uživatele** z **členy** podokně.

!["Členy" podokně s tlačítko "Přidat uživatele"](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Pokud chcete přidat uživatele nebo skupiny z **členy** podokně, budete muset: 

1. Vyberte roli z **vyberte roli** podokně, abyste mohli vybrat uživatele nebo skupinu.

   !["Vyberte roli" podokno](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Vyberte uživatele nebo skupiny z adresáře.

3. Typ odpovídající přiřazení vyberte z rozevírací nabídky: 

   - **Právě v čase**: poskytuje členy uživatele nebo skupinu oprávněné, ale není trvalý přístup do role v zadaném období nebo po neomezenou dobu (Pokud je nakonfigurováno v nastavení role). 
   - **Přímé**: uživatel nebo skupina členy aktivovat přiřazení role (označované jako trvalý přístup) nevyžaduje. Doporučujeme používat přímé přiřazení ke krátkodobému použití, kde přístup nebude muset po dokončení úlohy. Příklady jsou posuny na volání a dobou aktivity.

4. Pokud má být přiřazení trvalé (trvale vhodné pro přiřazení za běhu, nebo pro přímé přiřazení trvale aktivní), zaškrtněte níže políčko **typ přiřazení** pole.

   ![Podokno "Členství nastavení" pole "Typ přiřazení" a odpovídající zaškrtávací políčko](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >Zaškrtávací políčko může být unmodifiable, pokud jiný správce má zadanou dobu trvání maximální přiřazení pro každý typ přiřazení v nastavení role.

   K určení doby trvání konkrétní přiřazení, zrušte zaškrtnutí políčka a změnit počáteční nebo koncové datum a čas polí.

   !["Nastavení členství" podokně s polí pro počáteční datum, čas zahájení, koncové datum a čas ukončení](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Správa přiřazení rolí

Správci mohou spravovat přiřazení rolí tak, že vyberete buď **role** nebo **členy** v levém podokně. Výběr **role** umožňuje správcům určit obor jejich úlohy správy pro určité role. Výběr **členy** zobrazí všechny uživatele a skupiny přiřazení rolí pro prostředek.

![Podokno "Role"](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![Podokno "Členy"](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Pokud máte role čekající na vyřízení aktivace, banner oznámení se zobrazí v horní části podokna, když zobrazujete členství.


## <a name="modify-existing-assignments"></a>Upravte existující přiřazení

Chcete-li upravit existující přiřazení ze zobrazení podrobností uživatele nebo skupiny, vyberte **změnit nastavení** z panelu akcí. Změnit typ přiřazení na **těsně v čase** nebo **přímé**.

![Podokno "Podrobnosti uživatele" pomocí tlačítka "Změnu nastavení"](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
