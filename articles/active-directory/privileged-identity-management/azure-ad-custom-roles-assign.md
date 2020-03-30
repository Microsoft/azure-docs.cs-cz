---
title: Přiřazení vlastní role Azure AD – správa privilegovaných identit (PIM)
description: Jak přiřadit vlastní roli Azure AD pro přiřazení privilegované správy identit (PIM)
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
ms.openlocfilehash: 0303d37ef5bbbf266feb5456b0bc224ce272ee13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499248"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Přiřazení vlastní role Azure AD ve správě privilegovaných identit

Tento článek vám řekne, jak pomocí správy privilegovaných identit (PIM) vytvořit přiřazení just-in-time a časově vázané na vlastní role vytvořené pro správu aplikací v prostředí správy Azure Active Directory (Azure AD).

- Další informace o vytváření vlastních rolí pro delegování správy aplikací ve službě Azure AD najdete [v tématu Vlastní role správce ve službě Azure Active Directory (preview).](../users-groups-roles/roles-custom-overview.md)
- Pokud jste dosud nepoužili správu privilegovaných identit, získejte další informace na [začátku pomocí správy privilegovaných identit](pim-getting-started.md).
- Informace o udělení přístupu jinému správci ke správě privilegovaných identit naleznete v [tématu Udělení přístupu ostatním správcům ke správě privilegovaných identit](pim-how-to-give-access-to-pim.md).

> [!NOTE]
> Vlastní role Azure AD nejsou integrované s předdefinovanými rolemi adresáře během předběžné verze. Jakmile je funkce obecně dostupná, správa rolí proběhne v integrovaném prostředí rolí. Pokud se zobrazí následující banner, tyto role by měly být spravovány [v předdefinované role zkušenosti](pim-how-to-activate-role.md) a tento článek se nevztahuje:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="assign-a-role"></a>Přiřazení role

Správa privilegovaných identit může spravovat vlastní role, které můžete vytvořit ve správě aplikací Azure Active Directory (Azure AD).  Následující kroky vytvoří způsobilé přiřazení k vlastní roli adresáře.

1. Přihlaste se ke [správě privilegovaných identit](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) na webu Azure Portal pomocí uživatelského účtu, který je přiřazený roli správce privilegované role.
1. Vyberte **vlastní role Azure AD (preview).**

    ![Výběrem náhledu vlastních rolí Azure AD zobrazíte vyhotovení přiřazení rolí.](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Výběrem **role** zobrazíte seznam vlastních rolí pro aplikace Azure AD.

    ![Vybrat role, zobrazit seznam způsobilých přiřazení rolí.](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Výběrem **možnosti Přidat člena** otevřete stránku přiřazení.
1. Chcete-li omezit rozsah přiřazení role na jednu aplikaci, vyberte **obor** a určete obor aplikace.

    ![omezení rozsahu přiřazení způsobilých rolí ve službě Azure AD](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Vyberte **Vybrat roli,** chcete-li otevřít seznam **Vybrat roli.**

    ![vyberte způsobilou roli, kterou chcete přiřadit uživateli](./media/azure-ad-custom-roles-assign/select-role.png)

1. Vyberte roli, kterou chcete přiřadit, a klepněte na tlačítko **Vybrat**. Otevře se seznam **Vybrat člena.**

    ![Vyberte uživatele, kterému roli přiřazujete](./media/azure-ad-custom-roles-assign/select-member.png)

1. Vyberte uživatele, kterého chcete roli přiřadit, a klepněte na tlačítko **Vybrat**. Otevře se seznam **Nastavení členství.**

    ![Nastavení typu přiřazení role na způsobilé nebo aktivní](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. Na stránce **Nastavení členství** vyberte **Způsobilé** nebo **Aktivní**:

    - **Způsobilá** přiřazení vyžadují, aby uživatel přiřazený k roli provedl akci předtím, než bude moci roli použít. Akce mohou zahrnovat absolvování vícefaktorové kontroly ověřování, poskytnutí obchodního odůvodnění nebo vyžádání schválení od určených schvalovatelů.
    - **Aktivní** přiřazení nevyžadují, aby přiřazený uživatel provedl žádnou akci k použití role. Aktivní uživatelé mají oprávnění přiřazená roli po celou dobu.

1. Pokud je k dispozici a je k dispozici **trvalé** políčko (v závislosti na nastavení role), můžete určit, zda je přiřazení trvalé. Zaškrtnutím políčka chcete-li, aby bylo přiřazení trvale způsobilé nebo trvale přiřazené. Chcete-li určit dobu trvání přiřazení, zrušte zaškrtnutí políčka.
1. Chcete-li vytvořit nové přiřazení role, klepněte na tlačítko **Uložit** a potom **na Přidat**. Zobrazí se oznámení o stavu procesu přiřazení.

Chcete-li ověřit přiřazení role, vyberte v otevřené roli **přiřazení** > **a** ověřte, zda je přiřazení role správně označeno jako způsobilé nebo aktivní.

 ![Zkontrolujte, zda je přiřazení role viditelné jako způsobilé nebo aktivní.](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Další kroky

- [Aktivace vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Odebrání nebo aktualizace přiřazení vlastní role Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurace přiřazení vlastní role Azure AD](azure-ad-custom-roles-configure.md)
- [Definice rolí ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
