---
title: Privileged Identity Management pro prostředky Azure - aktivace rolí | Microsoft Docs
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
ms.openlocfilehash: 3e5456e7a632639cb82d7ba2b2e073938b1798ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---activate"></a>Správa privilegovaných identit - role prostředků – aktivace
Aktivace role pro prostředky Azure zavádí nové prostředí, které umožňuje členům role vhodné naplánovat aktivace pro budoucí datum a čas a vyberte dobu trvání konkrétní aktivace v rámci maximální (nakonfiguroval správce). Další informace o [aktivace role Azure AD zde](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Aktivace role
Přejděte do části Moje role na levém navigačním panelu. Klikněte na "Aktivovat" role, kterou chcete aktivovat do.
![](media/azure-pim-resource-rbac/rbac-roles.png)

V nabídce aktivace zadejte požadované počáteční datum a čas, při aktivaci role. Volitelně můžete zkrátit dobu trvání aktivace (dobu role je aktivní) a zadejte odůvodnění, v případě potřeby; Klikněte na možnost aktivovat.

Pokud není změnit počáteční datum a čas, aktivuje se roli během několika sekund. Uvidíte, že role zařazených do fronty pro aktivační banner zpráva na stránce Moje role. Klikněte na tlačítko Aktualizovat zrušte tuto zprávu.

![](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Pokud se aktivace je naplánováno budoucí datum a čas, nevyřízené žádosti se zobrazí na kartě žádosti čekající na vyřízení levé navigační nabídce. V případě, že se už nevyžaduje aktivaci role, může uživatel zrušte žádost kliknutím na tlačítko Storno na pravé straně stránky.

![](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="just-enough-administration"></a>Akorát správy

Pomocí vaše přiřazení role prostředků akorát osvědčené postupy správy (JEA) je jednoduchý PIM pro prostředky Azure. Uživatelé a členy skupiny s přiřazením v Azure nebo skupiny prostředků můžete aktivovat jejich stávající přiřazení role v menší oboru. 

Na stránce hledání najít podřízené prostředek, který potřebujete spravovat.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Z nabídky na levém navigačním vyberete Moje role a vyberte příslušnou roli aktivovat. Všimněte si typ přiřazení zděděna, vzhledem k tomu, že role se přiřadila na předplatné, nikoli na skupinu prostředků, jak je uvedeno níže.

![](media/azure-pim-resource-rbac/my-roles-02.png)
