---
title: Přiřazení rolí prostředků Azure hostům v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak pozvat externí uživatele typu Host a přiřadit role prostředků Azure v azure ad privilegované správy identit (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2efcf77d65fa2f9e203ed805cd7d78b9802ee3aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021940"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Pozvání uživatelů typu Host a přiřazení rolí prostředků Azure ve službě Správa privilegovaných identit

Uživatelé typu Host Azure Active Directory (Azure AD) jsou součástí možností spolupráce mezi podniky (B2B) v rámci Azure AD, takže můžete spravovat externí uživatele typu Host a dodavatele jako hosty ve službě Azure AD. Když zkombinujete spolupráci B2B se správou privilegovaných identit (PIM) Azure AD, můžete rozšířit požadavky na dodržování předpisů a zásad správného řízení na hosty. Tyto funkce správy privilegovaných identit můžete například použít pro úlohy identity Azure s hosty:

- Přiřazení přístupu ke konkrétním prostředkům Azure
- Povolení přístupu za čase
- Určení doby trvání a koncového data přiřazení
- Vyžadovat vícefaktorové ověřování při aktivním přiřazení nebo aktivaci
- Provádění kontrol přístupu
- Využití výstrah a protokolů auditu

Tento článek popisuje, jak pozvat hosta do vaší organizace a spravovat jejich přístup k prostředkům Azure pomocí správy privilegovaných identit.

## <a name="when-would-you-invite-guests"></a>Kdy byste pozvali hosty?

Zde je několik příkladů, kdy můžete pozvat hosty do vaší organizace:

- Povolte externímu samostatně výdělečně činnému dodavateli, který má jenom e-mailový účet pro přístup k prostředkům Azure pro projekt.
- Povolte externímu partnerovi ve velké organizaci, která používá místní službu Active Directory Federation Services pro přístup k aplikaci výdajů.
- Povolte technikům podpory, kteří nejsou ve vaší organizaci (například podpora Microsoftu), dočasně přistupovat k vašemu prostředku Azure k řešení problémů.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Jak funguje spolupráce s b2B hosty?

Při použití spolupráce B2B, můžete pozvat externího uživatele do vaší organizace jako hosta. Host a host a mů e být spravován jako uživatel ve vaší organizaci, ale host musí být ověřen ve své domovské organizaci a ne ve vaší organizaci Azure AD. To znamená, že pokud host již nemá přístup ke své domácí organizaci, ztratí také přístup k vaší organizaci. Například pokud host opustí svou organizaci, automaticky ztratí přístup k prostředkům, které jste s nimi sdíleli ve službě Azure AD, aniž byste museli nic dělat. Další informace o spolupráci B2B najdete v tématu [Co je přístup uživatele typu Host ve službě Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![Diagram znázorňující ověřování uživatele typu Host v domovském adresáři](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Kontrola nastavení spolupráce s hosty

Chcete-li se ujistit, že můžete pozvat hosty do vaší organizace, měli byste zkontrolovat nastavení spolupráce hostů.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Vyberte**nastavení uživatele** **služby Azure Active Directory** > .

1. Vyberte **Spravovat nastavení externí spolupráce**.

    ![Stránka nastavení externí spolupráce zobrazující nastavení oprávnění, pozvání a omezení spolupráce](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Ujistěte se, že **správci a uživatelé v roli pozvacího hosta mohou pozvat** přepínač je nastavena na **Ano**.

## <a name="invite-a-guest-and-assign-a-role"></a>Pozvání hosta a přiřazení role

Pomocí správy privilegovaných identit můžete pozvat hosta a učinit ho způsobilým pro roli prostředku Azure.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) s uživatelem, který je členem role [Správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) nebo Správce [uživatelů.](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **prostředky Azure**.

1. Pomocí **filtru Zdroje** můžete filtrovat seznam spravovaných prostředků.

1. Vyberte prostředek, který chcete spravovat, například prostředek, skupinu prostředků, předplatné nebo skupinu pro správu.

    Obor byste měli nastavit pouze na to, co host potřebuje.

1. V části Správa vyberte **Role,** chcete-li zobrazit seznam rolí pro prostředky Azure.

    ![Seznam rolí prostředků Azure zobrazující počet aktivních a způsobilých uživatelů](./media/pim-resource-roles-external-users/resources-roles.png)

1. Vyberte minimální roli, kterou bude uživatel potřebovat.

    ![Vybraná stránka role se seznamem aktuálních členů této role](./media/pim-resource-roles-external-users/selected-role.png)

1. Na stránce role vyberte **Přidat člena,** chcete-li otevřít podokno Nové přiřazení.

1. Klepněte **na tlačítko Vybrat člena nebo skupinu**.

    ![Nové přiřazení – Výběr podokna členů nebo skupin se seznamem uživatelů a skupin spolu s možností Pozvat](./media/pim-resource-roles-external-users/select-member-group.png)

1. Pokud chcete pozvat hosta, klikněte na **Pozvat**.

    ![Pozvání stránky hosta s poli k zadání e-mailové adresy a zadání osobní zprávy](./media/pim-resource-roles-external-users/invite-guest.png)

1. Po výběru hosta klepněte na **tlačítko Pozvat**.

    Host by měl být přidán jako vybraný člen.

1. V **podokně Vybrat člena nebo skupinu** klepněte na **tlačítko Vybrat**.

1. V podokně **Nastavení členství** vyberte typ přiřazení a dobu trvání.

    ![Nové přiřazení – stránka Nastavení členství s možnostmi určení typu přiřazení, počátečního data a koncového data](./media/pim-resource-roles-external-users/membership-settings.png)

1. Chcete-li úkol dokončit, vyberte **Hotovo** a potom **přidat**.

    Přiřazení role hosta se zobrazí v seznamu rolí.

    ![Stránka role, ve které je host uveden jako způsobilý](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Aktivovat roli hosta

Pokud jste externí uživatel, musíte přijmout pozvání, aby se host v organizaci Azure AD a případně aktivovat přiřazení role.

1. Otevřete e-mail s pozvánkou. E-mail bude vypadat podobně jako následující.

    ![E-mailová pozvánka s názvem adresáře, osobní zprávou a odkazem Začínáme](./media/pim-resource-roles-external-users/email-invite.png)

1. V e-mailu vyberte odkaz **Začínáme.**

1. Po kontrole oprávnění klepněte na tlačítko **Přijmout**.

    ![Kontrola stránky oprávnění v prohlížeči se seznamem oprávnění, která by organizace chtěla zkontrolovat](./media/pim-resource-roles-external-users/invite-accept.png)

1. Můžete být požádáni o přijetí podmínek použití a určení, zda chcete zůstat přihlášeni. Pokud máte na webu Azure Portal *nárok* na roli, ještě nebudete mít přístup k prostředkům.

1. Chcete-li aktivovat přiřazení role, otevřete e-mail s odkazem na aktivovanou roli. E-mail bude vypadat podobně jako následující.

    ![E-mail s upozorněním, že máte nárok na roli s odkazem aktivovat roli](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Výběrem **možnosti Aktivovat roli** otevřete své způsobilé role ve správě privilegovaných identit.

    ![Stránka Moje role ve správě privilegovaných identit se seznamem vašich způsobilých rolí](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. V části Akce vyberte odkaz **Aktivovat.**

    V závislosti na nastavení role budete muset zadat některé informace pro aktivaci role.

1. Jakmile zadáte nastavení role, aktivujte roli klepnutím na **tlačítko Aktivovat.**

    ![Aktivace oboru výpisu stránky a možností určení času zahájení, doby trvání a důvodu](./media/pim-resource-roles-external-users/activate-role.png)

    Pokud správce není povinen schválit vaši žádost, měli byste mít přístup k určeným prostředkům.

## <a name="view-activity-for-a-guest"></a>Zobrazit aktivitu pro hosta

Můžete zobrazit protokoly auditu a sledovat, co hosté dělají.

1. Jako správce otevřete správu privilegovaných identit a vyberte prostředek, který byl sdílen.

1. Výběrem **možnosti Audit zdrojů** zobrazíte aktivitu pro tento zdroj. Následující text ukazuje příklad aktivity pro skupinu prostředků.

    ![Prostředky Azure – stránka auditu prostředků se seznamem času, žadatele a akce](./media/pim-resource-roles-external-users/audit-resource.png)

1. Chcete-li zobrazit aktivitu hosta, vyberte*název hosta* **Služby Azure Active Directory** > **Users** > .

1. Chcete-li zobrazit protokoly auditu pro organizaci, vyberte **protokoly auditu.** V případě potřeby můžete zadat filtry.

    ![Protokoly auditu adresáře se seznamem dat, cíle, iniciovaných a aktivit](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí správce Azure AD ve správě privilegovaných identit](pim-how-to-add-role-to-user.md)
- [Co je přístup uživatele typu Host ve spolupráci azure ad b2b?](../b2b/what-is-b2b.md)
