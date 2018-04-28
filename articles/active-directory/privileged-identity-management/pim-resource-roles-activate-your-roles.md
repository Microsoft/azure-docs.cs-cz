---
title: Aktivace role pro prostředků Azure pomocí Privileged Identity managementu | Microsoft Docs
description: Popisuje postup aktivace služby role v PIM.
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
ms.openlocfilehash: a985e67cc566cc45b3ee6b8dc98e91a8f34abd1b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>Aktivace role pro prostředků Azure pomocí Privileged Identity Management
Privileged Identity Management (PIM) přináší nové prostředí ve aktivace role pro prostředky Azure. Členy role vhodné můžete naplánovat aktivace pro budoucí datum a čas. Také můžete vybrat konkrétní aktivace trvání v rámci maximální (nakonfiguroval správce). Další informace najdete v tématu [jak aktivovat nebo deaktivovat role v Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Aktivace role
Vyhledejte **Moje role** část v levém podokně. Vyberte **aktivovat** pro roli, kterou chcete aktivovat.

![Karta "Vhodné role" v podokně "Role".](media/azure-pim-resource-rbac/rbac-roles.png)

Z **aktivací** nabídky, zadejte počáteční datum a čas při aktivaci role. Volitelně můžete zkrátit dobu trvání aktivace (dobu, po který role je aktivní) a v případě potřeby zadejte odůvodnění. Pak vyberte **aktivovat**.

Pokud se nemění počáteční datum a čas, se role aktivuje v sekundách. V **Moje role** podokně zpráva hlavičky ukazuje, že role je ve frontě pro aktivaci. Vyberte tlačítko Aktualizovat zrušte tuto zprávu.

!["Role" podokně s zpráva hlavičky a oznámení o čeká na schválení](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Pokud je naplánováno aktivace budoucí datum a čas, nevyřízené žádosti se zobrazí na **nevyřízené žádosti o** kartě v levém podokně. Pokud aktivaci role už nepotřebujete, můžete zrušit žádost výběrem **zrušit** tlačítko.

![Seznam čekající na vyřízení požadavků s tlačítka tlačítko Storno.](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>Použít právě dostatečně postupy správy

Osvědčené postupy právě dostatečně správy (JEA) pomocí vaše přiřazení role prostředků je jednoduchý PIM pro prostředky Azure. Uživatelé a členy skupiny s přiřazením v předplatná Azure nebo skupiny prostředků můžete aktivovat jejich stávající přiřazení role v menší oboru. 

Na stránce hledání najít podřízené prostředek, který potřebujete spravovat.

![Výběr prostředku](media/azure-pim-resource-rbac/azure-resources-02.png)

Vyberte **Moje role** v levém podokně a vyberte příslušnou roli aktivovat. Typ přiřazení je **zděděné** protože role se přiřadila na skupinu prostředků, ale u předplatného.

![Seznam přiřazení rolí způsobilé, se zvýrazněným typem přiřazení](media/azure-pim-resource-rbac/my-roles-02.png)
