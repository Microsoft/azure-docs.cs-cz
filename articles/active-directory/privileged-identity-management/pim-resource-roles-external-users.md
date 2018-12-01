---
title: Pozvání externího uživatele a přiřadit role prostředků Azure v PIM | Dokumentace Microsoftu
description: Zjistěte, jak pozvat externího uživatele a přiřadit role prostředků Azure v Azure AD Privileged Identity Management (PIM).
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
ms.date: 11/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 561fccd3e0baee481937954cc65601576a4fd60f
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730116"
---
# <a name="invite-external-users-and-assign-azure-resource-roles-in-pim"></a>Pozvání externího uživatele a přiřadit role prostředků Azure v PIM

Azure Active Directory (Azure AD) business-to-business (B2B) je sada funkcí v rámci Azure AD, která umožňuje organizacím ke spolupráci s externími uživateli a pomocí libovolného účtu dodavateli. Když zkombinujete B2B se sadou Azure AD Privileged Identity Management (PIM), můžete nadále platí vaše požadavky na dodržování předpisů a zásad správného řízení pro externí uživatele. Například můžete použít tyto funkce PIM pro prostředky Azure s externími uživateli:

- Přiřazení přístupu ke konkrétním prostředkům Azure
- Povolení přístupu just-in-time
- Zadejte dobu trvání a koncové datum přiřazení
- Vyžadovat vícefaktorové ověřování na aktivní přiřazení nebo aktivaci
- Provádění kontroly přístupu
- Využívat výstrahy a protokolů auditu

Tento článek popisuje, jak pozvat externího uživatele do adresáře a spravovat jejich přístup k prostředkům Azure pomocí PIM.

## <a name="when-would-you-invite-external-users"></a>Pokud by pozvat externími uživateli?

Tady je několik ukázkových scénářů při může pozvat externího uživatele do adresáře:

- Povolit externí nezávislém dodavatele, který má pouze e-mailový účet pro přístup k prostředkům Azure pro projekt.
- Povolte externí partnera ve velké organizaci, který se používá pro přístup k aplikaci výdajů v místním Active Directory Federation Services.
- Povolit techniky podpory není ve vaší organizaci (třeba podporu Microsoftu) dočasně přístup k prostředku Azure pro řešení potíží.

## <a name="how-does-external-collaboration-using-b2b-work"></a>Jak funguje externí spolupráce B2B práce?

Při použití B2B můžete pozvat externího uživatele do adresáře. Externí uživatel se zobrazí ve vašem adresáři, ale uživatel nemá žádné přihlašovací údaje s ním spojená. Vždy, když má externího uživatele k ověření, musí být ověřena v jejich domovský adresář a není adresář. To znamená, že pokud externí uživatel už má přístup k jejich domovský adresář, aby automaticky nepřišla o přístup k adresáři. Například v případě externí uživatel společnost opustí organizaci, jsou automaticky ztratíte přístup ke všechny prostředky, které jste sdíleli s nimi ve vašem adresáři aniž byste je museli cokoli dělat. Další informace o B2B najdete v tématu [novinky přístupu uživatelů typu Host v Azure Active Directory s B2B?](../b2b/what-is-b2b.md).

![B2B a externí uživatele](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-external-collaboration-settings"></a>Zkontrolujte nastavení externí spolupráce

Pokud chcete mít jistotu, že může pozvat externího uživatele do adresáře, měli byste zkontrolovat nastavení externí spolupráce.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Klikněte na tlačítko **Azure Active Directory** > **uživatelská nastavení**.

1. Klikněte na tlačítko **spravovat nastavení externí spolupráce**.

    ![Nastavení externí spolupráce](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Ujistěte se, že **správci a uživatelé v roli odesílatele pozvánky hostů můžou posílat pozvánky** přepínač nastavený na **Ano**.

## <a name="invite-an-external-user-and-assign-a-role"></a>Pozvání externího uživatele a přiřazení role

Použití PIM, můžete pozvání externího uživatele a převést je na oprávněné pro roli prostředků Azure stejně jako uživatel členem.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako uživatel, který je členem skupiny [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) nebo [správce uživatelských účtů](../users-groups-roles/directory-assign-admin-roles.md#user-account-administrator) role.

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **prostředky Azure**.

1. Použití **filtr prostředků** filtrovat seznam spravovaných prostředků.

1. Klikněte na prostředek, který chcete spravovat, například prostředku, skupiny prostředků, předplatné nebo skupinu pro správu.

    Obor byste měli nastavit na pouze to, co externí uživatel potřebuje.

1. V části Správa, klikněte na tlačítko **role** zobrazíte seznam rolí pro prostředky Azure.

    ![Role prostředků Azure](./media/pim-resource-roles-external-users/resources-roles.png)

1. Klikněte na tlačítko minimální roli, kterou bude uživatel.

    ![Vybraná role](./media/pim-resource-roles-external-users/selected-role.png)

1. Klikněte na stránce role **přidat člena** a otevřete tak podokno nové přiřazení.

1. Klikněte na tlačítko **vyberte člena nebo skupiny**.

    ![Vyberte člena nebo skupinu.](./media/pim-resource-roles-external-users/select-member-group.png)

1. Pozvání externího uživatele, klikněte na tlačítko **pozvat**.

    ![Pozvat hosta](./media/pim-resource-roles-external-users/invite-guest.png)

1. Po zadání externího uživatele, klikněte na tlačítko **pozvat**.

    Externí uživatel by měl přidán jako vybraného členu.

1. V okně vyberte podokno člena nebo skupinu, klikněte na tlačítko **vyberte**.

1. V podokně nastavení členství vyberte typ přiřazení a dobu trvání.

    ![Nastavení členství](./media/pim-resource-roles-external-users/membership-settings.png)

1. K dokončení přiřazení, klikněte na tlačítko **provádí** a potom **přidat**.

    Přiřazení externího uživatele role se zobrazí v seznamu rolí.

    ![Přiřazení role pro externí uživatele](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-an-external-user"></a>Aktivace role jako externí uživatel

Jako externího uživatele musíte nejprve přijměte toto pozvání do adresáře Azure AD a pravděpodobně aktivovat svou roli.

1. Otevřete e-mailu s vaší pozvání do adresáře nebylo. E-mailu bude vypadat nějak takto.

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

## <a name="view-activity-for-an-external-user"></a>Zobrazit aktivitu pro externí uživatele

Stejně jako uživatel členem můžete zobrazit protokoly auditu ke sledování činnosti externích uživatelů.

1. Jako správce otevřete PIM a vyberte prostředek, který se sdílí.

1. Klikněte na tlačítko **audit prostředků** pro zobrazení aktivity pro daný prostředek. Následuje příklad aktivity pro určitou skupinu prostředků.

    ![Audit prostředků](./media/pim-resource-roles-external-users/audit-resource.png)

1. Chcete-li zobrazit aktivitu pro externí uživatele, klikněte na tlačítko **Azure Active Directory** > **uživatelé** > externího uživatele.

1. Klikněte na tlačítko **protokoly auditu** zobrazíte její protokol auditu adresáře. V případě potřeby můžete zadat filtry.

    ![Audit adresáře](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Další postup

- [Přiřazení role adresáře Azure AD v PIM](pim-how-to-add-role-to-user.md)
- [Co je přístupu uživatelů typu Host v Azure Active Directory s B2B?](../b2b/what-is-b2b.md)
