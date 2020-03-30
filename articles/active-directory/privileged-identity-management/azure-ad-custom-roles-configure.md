---
title: Konfigurace vlastní role Azure AD – správa privilegovaných identit (PIM)
description: Jak nakonfigurovat vlastní role Azure AD v privilegované správě identit (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f087ffe824a198c578f076857cca7fdf0f0a60c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498679"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Konfigurace vlastních rolí Azure AD ve správě privilegovaných identit

Správce privilegovaných rolí může změnit nastavení role, která se vztahují na uživatele při aktivaci přiřazení k vlastní roli a pro ostatní správce aplikací, kteří přiřazují vlastní role.

> [!NOTE]
> Vlastní role Azure AD nejsou integrované s předdefinovanými rolemi adresáře během předběžné verze. Jakmile je funkce obecně dostupná, správa rolí proběhne v integrovaném prostředí rolí. Pokud se zobrazí následující banner, tyto role by měly být spravovány [v předdefinované role zkušenosti](pim-how-to-activate-role.md) a tento článek se nevztahuje:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Otevřít nastavení role

Podle těchto kroků otevřete nastavení pro roli Azure AD.

1. Přihlaste se ke [správě privilegovaných identit](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) na webu Azure Portal pomocí uživatelského účtu, který je přiřazený roli správce privilegované role.
1. Vyberte **vlastní role Azure AD (preview).**

    ![Výběrem náhledu vlastních rolí Azure AD zobrazíte vyhotovení přiřazení rolí.](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Výběrem **možnosti Nastavení** otevřete stránku **Nastavení.** Vyberte roli pro nastavení, která chcete konfigurovat.
1. Vyberte **Upravit,** chcete-li otevřít stránku **Nastavení role.**

    ![Otevření vlastní role Azure AD pro úpravy nastavení](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Nastavení role

Existuje několik nastavení, které můžete nakonfigurovat.

### <a name="assignment-duration"></a>Doba trvání přiřazení

Při konfiguraci nastavení role můžete vybrat ze dvou možností doby trvání přiřazení pro každý typ přiřazení (způsobilý nebo aktivní). Tyto možnosti se stanou výchozí maximální dobou trvání, pokud je člen přiřazen k roli ve správě privilegovaných identit.

Můžete zvolit jednu z těchto *vhodných* možností doby trvání přiřazení.

- **Povolit trvalé způsobilé přiřazení**: Správci mohou přiřadit trvalé způsobilé členství.
- **Platnost způsobilého přiřazení vyprší po**: Správci mohou vyžadovat, aby všechna způsobilá přiřazení měla zadané počáteční a koncové datum.

Můžete také zvolit jednu z těchto *aktivních* možností doby trvání přiřazení:

- **Povolit trvalé aktivní přiřazení**: Správci mohou přiřadit trvalé aktivní členství.
- **Platnost aktivního přiřazení po**ukončení platnosti : Správci mohou vyžadovat, aby všechna aktivní přiřazení měla zadané počáteční a koncové datum.

### <a name="require-azure-multi-factor-authentication"></a>Vyžadování služby Azure Multi-Factor Authentication

Správa privilegovaných identit poskytuje volitelné vynucení vícefaktorového ověřování Azure pro dva odlišné scénáře.

- **Vyžadovat vícefaktorové ověřování při aktivním přiřazení**

  Pokud chcete přiřadit členovi pouze na krátkou dobu (například jeden den), může být příliš pomalé na to, aby bylo možné vyžadovat, aby přiřazené členy požadovaly aktivaci. V tomto scénáři správa privilegovaných identit nemůže vynutit vícefaktorové ověřování, když uživatel aktivuje přiřazení role, protože jsou již aktivní v roli od okamžiku, kdy jsou přiřazeny. Chcete-li zajistit, aby správce, který úkol plní, byl tím, kým se podle nich jedná, zaškrtněte políčko **Vyžadovat vícefaktorové ověřování u aktivního přiřazení.**

- **Vyžadovat vícefaktorové ověřování při aktivaci**

  Oprávněným uživatelům přiřazeným k roli můžete před aktivací vyžadovat, aby se oprávnění uživatelé přiřazeni k roli zaregistrovali do vícefaktorového ověřování Azure. Tento proces zajišťuje, že uživatel, který požaduje aktivaci, je tím, za koho se jedná s přiměřenou jistotou. Vynucení této možnosti chrání kritické role v situacích, kdy uživatelský účet mohl být ohrožen. Pokud chcete, aby oprávněný člen před aktivací spouštěl azure vícefaktorové ověřování, zaškrtněte políčko **Vyžadovat vícefaktorové ověřování při aktivaci.**

Další informace naleznete [v tématu Vícefaktorové ověřování a Správa privilegovaných identit](pim-how-to-require-mfa.md).

### <a name="activation-maximum-duration"></a>Maximální doba trvání aktivace

Pomocí jezdce **Maximální doba trvání aktivace** nastavte maximální dobu v hodinách, po kterou zůstane role aktivní, než vyprší její platnost. Tato hodnota může být od, 1 a 24 hodin.

### <a name="require-justification"></a>Vyžadovat odůvodnění

Můžete požadovat, aby členové zadali odůvodnění aktivního přiřazení nebo při aktivaci. Chcete-li vyžadovat zarovnání, zaškrtněte políčko **Vyžadovat zarovnání aktivního přiřazení** nebo **Vyžadovat zarovnání při aktivaci.**

### <a name="require-approval-to-activate"></a>Vyžadovat schválení aktivace

Pokud chcete k aktivaci role vyžadovat schválení, postupujte takto.

1. Zaškrtněte políčko **Vyžadovat schválení k aktivaci.**
1. Výběrem **možnosti Vybrat schvalovatele** otevřete seznam **Vybrat člena nebo skupinu.**

    ![Otevření vlastní role Azure AD pro úpravy nastavení](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Vyberte alespoň jednoho člena nebo skupinu a klepněte na tlačítko **Vybrat**. Je nutné vybrat alespoň jednoho schvalovatele. Neexistují žádní výchozí schvalovatelé. Vaše výběry se zobrazí v seznamu vybraných schvalovatelů.
1. Po zadání nastavení role vyberte **Aktualizovat** a uložte změny.

## <a name="next-steps"></a>Další kroky

- [Aktivace vlastní role Azure AD](azure-ad-custom-roles-activate.md)
- [Přiřazení vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Odebrání nebo aktualizace přiřazení vlastní role Azure AD](azure-ad-custom-roles-update-remove.md)
- [Definice rolí ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
