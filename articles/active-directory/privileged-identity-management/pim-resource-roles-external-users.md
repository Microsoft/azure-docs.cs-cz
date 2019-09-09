---
title: Pozvání hostů a přiřazení rolí prostředků Azure v PIM-Azure Active Directory | Microsoft Docs
description: Naučte se pozvat externí uživatele typu Host a přiřadit role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3e01b58a2a2fc6f93ae5ccc15e200a0cea69a0c
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804225"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>Pozvání uživatelů typu Host a přiřazení rolí prostředků Azure v PIM

Azure Active Directory (Azure AD) Business-to-Business (B2B) je sada funkcí v rámci služby Azure AD, která organizacím umožňuje spolupracovat s externími uživateli typu Host (hostů) a dodavateli pomocí libovolného účtu. Když zkombinujete B2B s Azure AD Privileged Identity Management (PIM), můžete dál uplatňovat požadavky na dodržování předpisů a zásady správného řízení pro hosty. Můžete například použít tyto funkce PIM pro úlohy Azure identity s hosty:

- Přiřazení přístupu ke konkrétním prostředkům Azure
- Povolit přístup za běhu
- Zadejte dobu trvání přiřazení a koncové datum.
- Vyžadovat MFA při aktivním přiřazení nebo aktivaci
- Provést kontroly přístupu
- Využití výstrah a protokolů auditu

Tento článek popisuje, jak pozvat hosta do vaší organizace a spravovat jejich přístup k prostředkům Azure pomocí Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Kdy byste měli pozvat hosty?

Tady je několik ukázkových scénářů, kdy můžete pozvat hosty do vaší organizace:

- Umožněte externímu nezávislému dodavateli, který má jenom e-mailový účet pro přístup k prostředkům Azure pro projekt.
- Umožněte externímu partnerovi ve velké organizaci, která pro přístup k aplikaci výdajů používá místní Active Directory Federation Services (AD FS).
- Umožněte pracovníkům podpory, kteří nejsou ve vaší organizaci (například podpora Microsoftu), dočasný přístup k vašemu prostředku Azure, abyste mohli řešit problémy.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Jak funguje spolupráce pomocí programu B2B pro hosty?

Pokud používáte spolupráci B2B, můžete jako hosta pozvat externího uživatele do vaší organizace. Zdá se, že host je ve vaší organizaci, ale k hostovi nejsou přidružené žádné přihlašovací údaje. Pokaždé, když je nutné ověřit hosta, musí být ověřeni v jejich domovské organizaci, nikoli ve vaší organizaci. To znamená, že pokud už Host nebude mít přístup k své domovské organizaci, ztratí přístup i k vaší organizaci. Pokud například Host opustí svou organizaci, automaticky ztratí přístup k prostředkům, které s nimi sdílíte ve službě Azure AD, aniž byste museli nic dělat. Další informace o B2B najdete v tématu [co je přístup uživatelů typu Host v Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![Diagram znázorňující, jak se uživatel typu Host zobrazuje v adresáři, ale je ověřený v domovském adresáři](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Kontrolovat nastavení spolupráce hostů

Abyste se ujistili, že budete moci pozvat hosty do vaší organizace, měli byste zkontrolovat nastavení spolupráce hostů.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Klikněte na **Azure Active Directory** > **nastavení uživatele**.

1. Klikněte na **spravovat externí nastavení spolupráce**.

    ![Stránka nastavení externí spolupráce se zobrazením oprávnění, pozvánky a nastavení omezení spolupráce](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Ujistěte se, že **Správci a uživatelé v roli pozvání hosta můžou pozvat** na **hodnotu Ano**.

## <a name="invite-a-guest-and-assign-a-role"></a>Pozvání hosta a přiřazení role

Pomocí PIM můžete pozvat hosta a učinit jim nárok na roli prostředků Azure, jako je členský uživatel.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem, který je členem role [správce privilegované role](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) nebo [Správce uživatelů](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) .

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **prostředky Azure**.

1. Pomocí **filtru prostředků** vyfiltrujte seznam spravovaných prostředků.

1. Klikněte na prostředek, který chcete spravovat, například prostředek, skupinu prostředků, předplatné nebo skupinu pro správu.

    Rozsah byste měli nastavit jenom na to, co host potřebuje.

1. V části Spravovat klikněte na **role** . zobrazí se seznam rolí pro prostředky Azure.

    ![Seznam rolí prostředků Azure zobrazuje počet uživatelů, kteří jsou aktivní a mají nárok](./media/pim-resource-roles-external-users/resources-roles.png)

1. Klikněte na minimální roli, kterou bude uživatel potřebovat.

    ![Vybraná stránka role se seznamem aktuálních členů této role](./media/pim-resource-roles-external-users/selected-role.png)

1. Kliknutím na tlačítko **Přidat člena** na stránce role otevřete podokno nové přiřazení.

1. Klikněte na **Vybrat člena nebo skupinu**.

    ![Nové přiřazení – vyberte člena nebo podokno skupin a seznam uživatelů a skupin spolu s možností pozvat](./media/pim-resource-roles-external-users/select-member-group.png)

1. Pokud chcete pozvat hosta, klikněte na **pozvat**.

    ![Pozvání stránky hosta s poli pro zadání e-mailové adresy a zadání osobní zprávy](./media/pim-resource-roles-external-users/invite-guest.png)

1. Po výběru hosta klikněte na **pozvat**.

    Host by měl být přidán jako vybraný člen.

1. V podokně **Vybrat člena nebo skupinu** klikněte na **Vybrat**.

1. V podokně **nastavení členství** vyberte typ přiřazení a dobu trvání.

    ![Nové přiřazení – stránka nastavení členství s možnostmi pro určení typu přiřazení, počátečního data a koncového data](./media/pim-resource-roles-external-users/membership-settings.png)

1. Přiřazení dokončíte kliknutím na **Hotovo** a potom na **Přidat**.

    V seznamu rolí se zobrazí přiřazení role hosta.

    ![Stránka role výpis hosta jako způsobilý](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Aktivovat roli jako hosta

Jako externí uživatel musíte nejdřív přijmout pozvánku do vaší organizace Azure AD a případně tuto roli aktivovat.

1. Otevřete e-mail pomocí pozvánky. E-mail bude vypadat podobně jako v následujícím příkladu.

    ![Odeslat pozvánku e-mailem s názvem adresáře, osobní zprávou a odkazem Začínáme](./media/pim-resource-roles-external-users/email-invite.png)

1. Klikněte na **odkaz Začínáme v e** -mailu.

1. Po kontrole oprávnění klikněte na **přijmout**.

    ![Stránka pro kontrolu oprávnění v prohlížeči se seznamem oprávnění, která má organizace chtít zkontrolovat](./media/pim-resource-roles-external-users/invite-accept.png)

1. Může se zobrazit výzva, abyste přijali podmínky použití a určili, zda chcete zůstat přihlášeni.

    Otevře se Azure Portal. Pokud jste pro roli jenom oprávněni, nebudete mít přístup k prostředkům.

1. Chcete-li aktivovat svoji roli, otevřete odkaz e-mail s aktivací role. E-mail bude vypadat podobně jako v následujícím příkladu.

    ![E-mailová zpráva od PIM, která indikuje, že máte nárok na roli s odkazem pro aktivaci role](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Kliknutím na **aktivovat roli** otevřete své oprávněné role v PIM.

    ![Stránka Moje role v PIM se seznamem vašich oprávněných rolí](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. V části Akce klikněte na odkaz **aktivovat** .

    V závislosti na nastavení role budete muset zadat nějaké informace pro aktivaci role.

1. Po zadání nastavení pro roli aktivujte roli kliknutím na **aktivovat** .

    ![Aktivovat rozsah a možnosti výpisu stránky a zadat čas spuštění, dobu trvání a důvod](./media/pim-resource-roles-external-users/activate-role.png)

    Pokud správce není nutný ke schválení vaší žádosti, měli byste mít přístup k určeným prostředkům.

## <a name="view-activity-for-a-guest"></a>Zobrazit aktivitu pro hosta

Stejně jako uživatel s členem můžete zobrazit protokoly auditu a sledovat tak, co dělají hosté.

1. Jako správce otevřete PIM a vyberte prostředek, který se sdílí.

1. Kliknutím na **audit prostředků** zobrazíte aktivitu pro daný prostředek. Níže vidíte příklad aktivity pro skupinu prostředků.

    ![Prostředky Azure – stránka auditu prostředků výpis doby, žadatele a akce](./media/pim-resource-roles-external-users/audit-resource.png)

1. Chcete-li zobrazit aktivitu pro hosta, klikněte na **Azure Active Directory** > **Uživatelé** > název hosta.

1. Kliknutím na **protokoly auditu** zobrazíte protokoly auditu pro organizaci. V případě potřeby můžete zadat filtry.

    ![Protokoly auditu adresáře výpis data, cíle, zahájení a aktivity](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí správce Azure AD v PIM](pim-how-to-add-role-to-user.md)
- [Co je přístup uživatelů typu Host ve Azure Active Directory B2B?](../b2b/what-is-b2b.md)
