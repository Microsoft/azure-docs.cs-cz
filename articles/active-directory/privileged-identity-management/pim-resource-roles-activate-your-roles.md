---
title: Aktivovat role prostředků Azure s použitím Privileged Identity Management | Dokumentace Microsoftu
description: Popisuje, jak aktivovat role v PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: d2f61f1ebdd473a24115c7774801f5b7694f224f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443201"
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>Aktivovat role prostředků Azure s použitím Privileged Identity Management
Privileged Identity Management (PIM) zavádí nové prostředí ve aktivace role prostředků Azure. Oprávněné role členy můžete naplánovat aktivace pro budoucí datum a čas. Může také vybrat dobu trvání konkrétní aktivace v rámci maximální (ve Správci nakonfigurované je). Další informace najdete v tématu [postup aktivace nebo deaktivace role v Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Aktivace role
Přejděte **Moje role** části v levém podokně. Vyberte **aktivovat** pro roli, kterou chcete aktivovat.

![Na kartu "Oprávněných rolí" "role".](media/azure-pim-resource-rbac/rbac-roles.png)

Z **aktivací** nabídku, zadejte počáteční datum a čas aktivaci této role. Volitelně můžete snížit dobu trvání aktivace (délka, kterou roli je aktivní) a v případě potřeby zadejte odůvodnění. Vyberte **aktivovat**.

Je-li počáteční datum a čas se nezmění, role je aktivovat v řádu sekund. V **Moje role** podokně zpráva hlavičky ukazuje, že role je ve frontě pro aktivaci. Vyberte tlačítko pro aktualizaci zrušte tuto zprávu.

!["Role" podokno s hlavičky zpráv a oznámení o čekajících na schválení](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Pokud je aktivace naplánováno pro budoucí datum a čas, čekající žádost se zobrazí na **žádosti čekající na vyřízení** kartu v levém podokně. Pokud aktivace role se už nevyžaduje, můžete tak, že vyberete zrušit požadavek **zrušit** tlačítko.

![Seznam čekajících žádostí pomocí tlačítek pro tlačítko Storno.](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>Použít postupy Just Enough Administration

Přiřazení role prostředků pomocí osvědčených postupů Just Enough Administration (JEA) je jednoduchý s PIM pro prostředky Azure. Uživatelé a skupiny Členové s přiřazením předplatného Azure nebo skupiny prostředků můžete aktivovat jejich stávající přiřazení rolí v nižší oboru. 

Ze stránky hledání najdete podřízený prostředek, který je potřeba spravovat.

![Výběr prostředku](media/azure-pim-resource-rbac/azure-resources-02.png)

Vyberte **Moje role** v levém podokně a vyberte vhodnou roli aktivovat. Typ přiřazení je **zděděné** protože byla role přiřazená na předplatné, nikoli na skupinu prostředků.

![Seznam přiřazení oprávněné role se zvýrazněným typem přiřazení](media/azure-pim-resource-rbac/my-roles-02.png)
