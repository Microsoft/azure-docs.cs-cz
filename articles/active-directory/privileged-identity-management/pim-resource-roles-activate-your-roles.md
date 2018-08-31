---
title: Aktivovat Moje role prostředků Azure v PIM | Dokumentace Microsoftu
description: Zjistěte, jak aktivovat role prostředků Azure v Azure AD Privileged Identity Management (PIM).
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
ms.date: 08/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 234c1d71f0ec17d15a4dd589e3db92fd9bf68df2
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189485"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Aktivovat Moje role prostředků Azure v PIM
Privileged Identity Management (PIM) zavádí nové prostředí ve aktivace role prostředků Azure. Oprávněné role členy můžete naplánovat aktivace pro budoucí datum a čas. Může také vybrat dobu trvání konkrétní aktivace v rámci maximální (ve Správci nakonfigurované je). Další informace najdete v tématu [postup aktivace nebo deaktivace role v Azure AD Privileged Identity Management](pim-how-to-activate-role.md).

## <a name="activate-a-role"></a>Aktivovat roli
Přejděte **Moje role** části v levém podokně. Vyberte **aktivovat** pro roli, kterou chcete aktivovat.

![Na kartu "Oprávněných rolí" "role".](media/azure-pim-resource-rbac/rbac-roles.png)

Z **aktivací** nabídku, zadejte počáteční datum a čas aktivaci této role. Volitelně můžete snížit dobu trvání aktivace (délka, kterou roli je aktivní) a v případě potřeby zadejte odůvodnění. Vyberte **aktivovat**.

Je-li počáteční datum a čas se nezmění, role je aktivovat v řádu sekund. V **Moje role** podokně zpráva hlavičky ukazuje, že role je ve frontě pro aktivaci. Vyberte tlačítko pro aktualizaci zrušte tuto zprávu.

!["Role" podokno s hlavičky zpráv a oznámení o čekajících na schválení](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Pokud je aktivace naplánováno pro budoucí datum a čas, čekající žádost se zobrazí na **žádosti čekající na vyřízení** kartu v levém podokně. Pokud aktivace role se už nevyžaduje, můžete tak, že vyberete zrušit požadavek **zrušit** tlačítko.

![Seznam čekajících žádostí pomocí tlačítek pro tlačítko Storno.](media/azure-pim-resource-rbac/rbac-activate-pending.png)

## <a name="use-a-role-immediately-after-activation"></a>Použijte roli okamžitě po aktivaci

Z důvodu ukládání do mezipaměti, počet aktivací nedojde okamžitě na webu Azure Portal bez její aktualizaci. Pokud potřebujete omezit možnost zpoždění až po dokončení aktivace rolí, můžete použít **přístupu k aplikacím** stránky na portálu. Nové přiřazení role aplikace k němu přistupovat z této stránky vyhledat okamžitě.

1. Otevřete Azure AD Privileged Identity Management.

1. Klikněte na tlačítko **přístupu k aplikacím** stránky.

    ![Přístup k aplikaci PIM – snímek obrazovky](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Klikněte na tlačítko **prostředky Azure** znovu otevřít na portálu na **všechny prostředky** stránky.

    Po kliknutí na tento odkaz můžete vynutit aktualizaci a je vyhledat nové přiřazení role prostředků Azure.

## <a name="apply-just-enough-administration-practices"></a>Použít postupy Just Enough Administration

Přiřazení role prostředků pomocí osvědčených postupů Just Enough Administration (JEA) je jednoduchý s PIM pro prostředky Azure. Uživatelé a skupiny Členové s přiřazením předplatného Azure nebo skupiny prostředků můžete aktivovat jejich stávající přiřazení rolí v nižší oboru. 

Ze stránky hledání najdete podřízený prostředek, který je potřeba spravovat.

![Výběr prostředku](media/azure-pim-resource-rbac/azure-resources-02.png)

Vyberte **Moje role** v levém podokně a vyberte vhodnou roli aktivovat. Typ přiřazení je **zděděné** protože byla role přiřazená na předplatné, nikoli na skupinu prostředků.

![Seznam přiřazení oprávněné role se zvýrazněným typem přiřazení](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Další postup

- [Aktivovat Moje role adresáře Azure AD v PIM](pim-how-to-activate-role.md)