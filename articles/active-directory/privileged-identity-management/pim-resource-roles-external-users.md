---
title: Zvát hosty a přiřadit role prostředků Azure v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak pozvat externím uživatelům typu Host a přiřadit role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 03/13/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68c5e5e2ed0d3ec767a239439476a98bac73bcb4
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576875"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>Pozvat uživatele typu Host a přiřadit role prostředků Azure v PIM

Azure Active Directory (Azure AD) business-to-business (B2B) je sada funkcí v rámci Azure AD, která umožňuje organizacím spolupracovat s externím uživatelům typu Host (hostů) a pomocí libovolného účtu dodavateli. Když zkombinujete B2B s Azure AD Privileged Identity Management (PIM), můžete nadále platí vaše požadavky na dodržování předpisů a zásad správného řízení pro hosty. Například můžete použít tyto funkce PIM pro úlohy Azure identity s hostů:

- Přiřazení přístupu ke konkrétním prostředkům Azure
- Povolení přístupu just-in-time
- Zadejte dobu trvání a koncové datum přiřazení
- Vyžadovat vícefaktorové ověřování na aktivní přiřazení nebo aktivaci
- Provádění kontroly přístupu
- Využívat výstrahy a protokolů auditu

Tento článek popisuje, jak pozvat hosta pro vaši organizaci a spravovat jejich přístup k prostředkům Azure pomocí Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Pokud by zvát hosty?

Tady je několik ukázkových scénářů při můžou zvát hosty do vaší organizace:

- Povolit externí nezávislém dodavatele, který má pouze e-mailový účet pro přístup k prostředkům Azure pro projekt.
- Povolte externí partnera ve velké organizaci, který se používá pro přístup k aplikaci výdajů v místním Active Directory Federation Services.
- Povolit techniky podpory není ve vaší organizaci (třeba podporu Microsoftu) dočasně přístup k prostředku Azure pro řešení potíží.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Jak spolupráce pomocí B2B hosté práce?

Při použití spolupráce B2B ve službě můžete pozvat externího uživatele pro vaši organizaci jako Host. Host se zdá být ve vaší organizaci, ale Host nemá žádné přihlašovací údaje s ním spojená. Vždy, když hosta má k ověření, musí být ověřena v jejich domovskou organizaci a ne ve vaší organizaci. To znamená, že pokud Host už má přístup k jejich domovskou organizaci, jsou také ztratit přístup k vaší organizaci. Například pokud Host opustí organizaci, jsou automaticky ztratíte přístup ke všechny prostředky, které jste sdíleli s nimi ve službě Azure AD aniž byste museli něco dělat. Další informace o B2B najdete v tématu [novinky přístupu uživatelů typu Host v Azure Active Directory s B2B?](../b2b/what-is-b2b.md).

![B2B a hosta](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Zkontrolujte nastavení spolupráci hosta

Pokud chcete mít jistotu, že můžou zvát hosty do vaší organizaci, měli byste zkontrolovat nastavení spolupráci hosta.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Klikněte na tlačítko **Azure Active Directory** > **uživatelská nastavení**.

1. Klikněte na tlačítko **spravovat nastavení externí spolupráce**.

    ![Nastavení externí spolupráce](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Ujistěte se, že **správci a uživatelé v roli odesílatele pozvánky hostů můžou posílat pozvánky** přepínač nastavený na **Ano**.

## <a name="invite-a-guest-and-assign-a-role"></a>Pozvat hosta a přiřazení role

Použití PIM, můžete pozvat hosta a převést je na oprávněné pro roli prostředků Azure stejně jako uživatel členem.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako uživatel, který je členem skupiny [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) nebo [Správce uživatelů](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) role.

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **prostředky Azure**.

1. Použití **filtr prostředků** filtrovat seznam spravovaných prostředků.

1. Klikněte na prostředek, který chcete spravovat, například prostředku, skupiny prostředků, předplatné nebo skupinu pro správu.

    Obor byste měli nastavit na stačí jaké hosta.

1. V části Správa, klikněte na tlačítko **role** zobrazíte seznam rolí pro prostředky Azure.

    ![Role prostředků Azure](./media/pim-resource-roles-external-users/resources-roles.png)

1. Klikněte na tlačítko minimální roli, kterou bude uživatel.

    ![Vybraná role](./media/pim-resource-roles-external-users/selected-role.png)

1. Klikněte na stránce role **přidat člena** a otevřete tak podokno nové přiřazení.

1. Klikněte na tlačítko **vyberte člena nebo skupiny**.

    ![Vyberte člena nebo skupinu.](./media/pim-resource-roles-external-users/select-member-group.png)

1. Pozvat hosta, klikněte na tlačítko **pozvat**.

    ![Pozvat hosta](./media/pim-resource-roles-external-users/invite-guest.png)

1. Po výběru hosta, klikněte na tlačítko **pozvat**.

    Host přidaly jako vybraného členu.

1. V **vyberte člena nebo skupiny** podokně klikněte na tlačítko **vyberte**.

1. V **nastavení členství** podokně, vyberte typ přiřazení a dobu trvání.

    ![Nastavení členství](./media/pim-resource-roles-external-users/membership-settings.png)

1. K dokončení přiřazení, klikněte na tlačítko **provádí** a potom **přidat**.

    Přiřazení role hosta se zobrazí v seznamu rolí.

    ![Přiřazení role pro hosta](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Aktivace role v roli hosta

Jako externí uživatel potřebujete nejdřív přijmout pozvánku pro vaši organizaci Azure AD a pravděpodobně aktivaci vaší role.

1. Otevřete e-mailu s vaši pozvánku. E-mailu bude vypadat nějak takto.

    ![E-mailová pozvánka](./media/pim-resource-roles-external-users/email-invite.png)

1. Klikněte na tlačítko **Začínáme** odkaz v e-mailu.

1. Po kontrole oprávnění, klikněte na tlačítko **přijmout**.

    ![Zkontrolovat oprávnění](./media/pim-resource-roles-external-users/invite-accept.png)

1. Může se zobrazit výzva k přijměte podmínky použití a určete, jestli chcete zachovat přihlášení.

    Otevře se na webu Azure portal. Pokud jste právě oprávněné pro roli, nebudete mít přístup k prostředkům.

1. Chcete-li aktivovat svou roli, otevřete e-mailu s vaší aktivovat roli odkaz. E-mailu bude vypadat nějak takto.

    ![E-mailová pozvánka](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Klikněte na tlačítko **aktivovat roli** otevřete oprávněnou roli v PIM.

    ![Moje role – oprávněné](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. V části Akce klikněte na tlačítko **aktivovat** odkaz.

    V závislosti na nastavení role budete muset zadat některé informace o aktivaci role.

1. Po zadání nastavení pro roli, klikněte na tlačítko **aktivovat** aktivaci této role.

    ![Aktivovat roli](./media/pim-resource-roles-external-users/activate-role.png)

    Pokud správce musí schválit vaše žádost, byste měli mít přístup k uvedeným prostředkům.

## <a name="view-activity-for-a-guest"></a>Zobrazit aktivitu pro hosta

Stejně jako uživatel členem můžete zobrazit protokoly auditu ke sledování činnosti hosty.

1. Jako správce otevřete PIM a vyberte prostředek, který se sdílí.

1. Klikněte na tlačítko **audit prostředků** pro zobrazení aktivity pro daný prostředek. Následuje příklad aktivity pro určitou skupinu prostředků.

    ![Audit prostředků](./media/pim-resource-roles-external-users/audit-resource.png)

1. Chcete-li zobrazit aktivity hosta, klikněte na tlačítko **Azure Active Directory** > **uživatelé** > název typu Host.

1. Klikněte na tlačítko **protokoly auditu** chcete zobrazit protokoly auditu pro organizaci. V případě potřeby můžete zadat filtry.

    ![organizace auditu](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Další postup

- [Přiřazení rolí správce Azure AD v PIM](pim-how-to-add-role-to-user.md)
- [Co je přístupu uživatelů typu Host v Azure Active Directory s B2B?](../b2b/what-is-b2b.md)
