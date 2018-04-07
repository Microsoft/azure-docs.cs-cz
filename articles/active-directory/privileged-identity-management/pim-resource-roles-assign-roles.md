---
title: Privileged Identity Management pro prostředky Azure - přiřazení role | Microsoft Docs
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
ms.openlocfilehash: 9a9046afe2ee1e578333ff9d29f6fb21e95a0f22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---assign"></a>Správa privilegovaných identit - role prostředků - přiřadit

## <a name="assign-roles"></a>Přiřazení rolí

Pokud chcete přiřadit k roli uživatele nebo skupinu, vyberte roli (Pokud je zobrazení rolí), 

![](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

nebo klikněte na tlačítko Přidat z panelu akcí (Pokud na zobrazení členů).

![](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


-Li přidat uživatele nebo skupinu z karty členy, musíte: 

1. Abyste mohli vybrat uživatele nebo skupinu, vyberte v nabídce Přidat roli.

![](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Vyberte uživatele nebo skupiny z adresáře.

3. Typ odpovídající přiřazení vyberte z rozevírací nabídky. 

    - **Právě v čas přiřazení:** poskytuje členy uživatele nebo skupinu oprávněné, ale není trvalý přístup k roli v zadaném období času nebo po neomezenou dobu (Pokud je nakonfigurováno v nastavení role). 
    - **Přímé přiřazení:** nevyžaduje žádný uživatel nebo skupina členy aktivovat přiřazení role (označované jako trvalý přístup). Společnost Microsoft doporučuje pomocí přímé přiřazení pro krátkodobé použití, například na volání posuny nebo citlivé aktivity čas, kdy přístup nebude muset po dokončení úlohy.

Zaškrtávací políčko pod rozevíracím typ přiřazení umožňuje zadat, pokud má být trvalé přiřazení (vhodné trvale aktivovat pouze v čas přiřazení nebo trvale active pro přímé přiřazení).

![](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

>[!NOTE]
>Zaškrtávací políčko může být unmodifiable, pokud jiný správce má zadanou dobu trvání maximální přiřazení pro každý typ přiřazení v nastavení role.

 K určení doby trvání konkrétní přiřazení, zrušte výběr zaškrtnutím políčka a upravit spuštění nebo ukončení pole data a času.

![](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Správa přiřazení rolí

Správci mohou spravovat přiřazení rolí výběrem role nebo členy z levé navigaci. Výběr rolí umožňuje správcům obor jejich úlohy správy pro určité role, zatímco členy zobrazí všechny uživatele a skupiny přiřazení rolí pro prostředek.

![](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Pokud máte role čekající na vyřízení aktivace, banner oznámení se zobrazí v horní části stránky, při zobrazení členství.


## <a name="modify-existing-assignments"></a>Upravte existující přiřazení

Pokud chcete upravit existující přiřazení ze zobrazení podrobností uživatele nebo skupiny, vyberte z panelu Akce v horní části stránky změnit nastavení. Změňte typ přiřazení na právě v přiřazení čas nebo přímé přiřazení.

![](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
