---
title: Přiřazení rolí prostředků Azure hostům v PIM – Azure AD | Microsoft Docs
description: Naučte se pozvat externí uživatele typu Host a přiřadit role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 763441d023e4a707c6a1edc09abfb6d8e5525723
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88782732"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Pozvání uživatelů typu Host a přiřazení rolí prostředků Azure v Privileged Identity Management

Azure Active Directory (Azure AD) uživatelé typu Host jsou součástí možností spolupráce B2B (Business-to-Business) v rámci služby Azure AD, abyste mohli spravovat externí uživatele a dodavatele typu hosta jako hosty ve službě Azure AD. Když kombinujete spolupráci B2B s Azure AD Privileged Identity Management (PIM), můžete požadavky na dodržování předpisů a zásady správného řízení pro hosty roztáhnout. Můžete například použít tyto funkce Privileged Identity Management pro úlohy Azure identity s hosty:

- Přiřazení přístupu ke konkrétním prostředkům Azure
- Povolit přístup za běhu
- Zadejte dobu trvání přiřazení a koncové datum.
- Vyžadovat vícefaktorové ověřování při aktivním přiřazení nebo aktivaci
- Provést kontroly přístupu
- Využití výstrah a protokolů auditu

Tento článek popisuje, jak pozvat hosta do vaší organizace a spravovat jejich přístup k prostředkům Azure pomocí Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Kdy byste měli pozvat hosty?

Tady je několik příkladů, kdy je možné pozvat hosty do vaší organizace:

- Umožněte externímu nezávislému dodavateli, který má jenom e-mailový účet pro přístup k prostředkům Azure pro projekt.
- Umožněte externímu partnerovi ve velké organizaci, která pro přístup k aplikaci výdajů používá místní Active Directory Federation Services (AD FS).
- Umožněte pracovníkům podpory, kteří nejsou ve vaší organizaci (například podpora Microsoftu), dočasný přístup k vašemu prostředku Azure, abyste mohli řešit problémy.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Jak funguje spolupráce pomocí programu B2B pro hosty?

Pokud používáte spolupráci B2B, můžete jako hosta pozvat externího uživatele do vaší organizace. Host se dá spravovat jako uživatel ve vaší organizaci, ale Host musí být ověřený v jejich domovské organizaci, a ne ve vaší organizaci Azure AD. To znamená, že pokud už Host nebude mít přístup k své domovské organizaci, ztratí přístup i k vaší organizaci. Pokud například Host opustí svou organizaci, automaticky ztratí přístup k prostředkům, které s nimi sdílíte ve službě Azure AD, aniž byste museli nic dělat. Další informace o spolupráci B2B najdete v tématu [co je přístup uživatelů typu Host v Azure Active Directory B2B?](../external-identities/what-is-b2b.md).

![Diagram znázorňující, jak je uživatel typu Host ověřený v domovském adresáři](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Kontrolovat nastavení spolupráce hostů

Abyste se ujistili, že budete moci pozvat hosty do vaší organizace, měli byste zkontrolovat nastavení spolupráce hostů.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).

1. Vyberte **Azure Active Directory**  >  **uživatelská nastavení**.

1. Vyberte možnost **spravovat externí nastavení spolupráce**.

    ![Stránka nastavení externí spolupráce se zobrazením oprávnění, pozvánky a nastavení omezení spolupráce](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Zajistěte, aby **Správci a uživatelé v roli pozvat pozvánky hosta mohli pozvaní** přepínač nastaven na **hodnotu Ano**.

## <a name="invite-a-guest-and-assign-a-role"></a>Pozvání hosta a přiřazení role

Pomocí Privileged Identity Management můžete pozvat hosta a nastavit je jako opravňující pro roli prostředků Azure.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem, který je členem role [správce privilegované role](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) nebo [Správce uživatelů](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) .

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **prostředky Azure**.

1. Pomocí **filtru prostředků** vyfiltrujte seznam spravovaných prostředků.

1. Vyberte prostředek, který chcete spravovat, například prostředek, skupinu prostředků, předplatné nebo skupinu pro správu.

    Rozsah byste měli nastavit jenom na to, co host potřebuje.

1. V části Spravovat vyberte **role** , abyste viděli seznam rolí pro prostředky Azure.

    ![Seznam rolí prostředků Azure zobrazuje počet uživatelů, kteří jsou aktivní a mají nárok](./media/pim-resource-roles-external-users/resources-roles.png)

1. Vyberte minimální roli, kterou bude uživatel potřebovat.

    ![Vybraná stránka role se seznamem aktuálních členů této role](./media/pim-resource-roles-external-users/selected-role.png)

1. Na stránce Role vyberte **Přidat člena** a otevřete tak podokno nové přiřazení.

1. Klikněte na **Vybrat člena nebo skupinu**.

    ![Nové přiřazení – vyberte člena nebo podokno skupin a seznam uživatelů a skupin spolu s možností pozvat](./media/pim-resource-roles-external-users/select-member-group.png)

1. Pokud chcete pozvat hosta, klikněte na **pozvat**.

    ![Pozvání stránky hosta s poli pro zadání e-mailové adresy a zadání osobní zprávy](./media/pim-resource-roles-external-users/invite-guest.png)

1. Po výběru hosta klikněte na **pozvat**.

    Host by měl být přidán jako vybraný člen.

1. V podokně **Vybrat člena nebo skupinu** klikněte na **Vybrat**.

1. V podokně **nastavení členství** vyberte typ přiřazení a dobu trvání.

    ![Nové přiřazení – stránka nastavení členství s možnostmi pro určení typu přiřazení, počátečního data a koncového data](./media/pim-resource-roles-external-users/membership-settings.png)

1. Chcete-li dokončit přiřazení, vyberte **Hotovo** a pak **Přidat**.

    V seznamu rolí se zobrazí přiřazení role hosta.

    ![Stránka role výpis hosta jako způsobilý](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Aktivovat roli jako hosta

Pokud jste externím uživatelem, musíte přijmout pozvánku jako hosta v organizaci Azure AD a případně aktivovat přiřazení role.

1. Otevřete e-mail pomocí pozvánky. E-mail bude vypadat podobně jako v následujícím příkladu.

    ![Odeslat pozvánku e-mailem s názvem adresáře, osobní zprávou a odkazem Začínáme](./media/pim-resource-roles-external-users/email-invite.png)

1. V e-mailu **vyberte odkaz Začínáme** .

1. Po kontrole oprávnění klikněte na **přijmout**.

    ![Stránka pro kontrolu oprávnění v prohlížeči se seznamem oprávnění, která má organizace chtít zkontrolovat](./media/pim-resource-roles-external-users/invite-accept.png)

1. Může se zobrazit výzva, abyste přijali podmínky použití a určili, zda chcete zůstat přihlášeni. Pokud máte v Azure Portal *nárok* na roli, zatím nebudete mít přístup k prostředkům.

1. Pokud chcete aktivovat přiřazení role, otevřete e-mail s odkazem pro aktivaci role. E-mail bude vypadat podobně jako v následujícím příkladu.

    ![E-mail s oznámením, že u role s odkazem aktivovat roli máte nárok](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Výběrem **aktivovat roli** otevřete své oprávněné role v Privileged Identity Management.

    ![Stránka Moje role v Privileged Identity Management obsahuje seznam vašich oprávněných rolí](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. V části Akce vyberte odkaz **aktivovat** .

    V závislosti na nastavení role budete muset zadat nějaké informace pro aktivaci role.

1. Po zadání nastavení pro roli aktivujte roli kliknutím na **aktivovat** .

    ![Aktivovat rozsah a možnosti výpisu stránky a zadat čas spuštění, dobu trvání a důvod](./media/pim-resource-roles-external-users/activate-role.png)

    Pokud správce není nutný ke schválení vaší žádosti, měli byste mít přístup k určeným prostředkům.

## <a name="view-activity-for-a-guest"></a>Zobrazit aktivitu pro hosta

Můžete zobrazit protokoly auditu a sledovat, co dělají hosté.

1. Jako správce otevřete Privileged Identity Management a vyberte prostředek, který se sdílí.

1. Výběrem možnosti **audit prostředků** zobrazíte aktivitu pro daný prostředek. Níže vidíte příklad aktivity pro skupinu prostředků.

    ![Prostředky Azure – stránka auditu prostředků výpis doby, žadatele a akce](./media/pim-resource-roles-external-users/audit-resource.png)

1. Pokud chcete zobrazit aktivitu pro hosta, vyberte **Azure Active Directory**  >  **Users**  >  *jméno hosta*uživatele.

1. Pokud chcete zobrazit protokoly auditu pro organizaci, vyberte **protokoly auditu** . V případě potřeby můžete zadat filtry.

    ![Protokoly auditu adresáře výpis data, cíle, zahájení a aktivity](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí správce Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Co je přístup uživatelů typu Host ve spolupráci B2B Azure AD?](../external-identities/what-is-b2b.md)