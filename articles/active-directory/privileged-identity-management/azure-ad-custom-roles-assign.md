---
title: Přiřazení vlastní role Azure AD – Privileged Identity Management (PIM)
description: Postup přiřazení vlastní role Azure AD pro přiřazení Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75e004980ada23a616b57e3c7eb1afb241e09598
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371643"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Přiřazení vlastní role Azure AD v Privileged Identity Management

V tomto článku se dozvíte, jak pomocí Privileged Identity Management (PIM) vytvořit přiřazení za běhu a časově vázaný čas pro vlastní role vytvořené pro správu aplikací v prostředí pro správu Azure Active Directory (Azure AD).

- Další informace o vytváření vlastních rolí pro delegování správy aplikací ve službě Azure AD najdete v tématu [vlastní role správců v Azure Active Directory (Preview)](../roles/custom-overview.md).
- Pokud jste zatím Privileged Identity Management ještě nepoužili, získáte další informace na adrese Začínáme [s používáním Privileged Identity Management](pim-getting-started.md).
- Informace o tom, jak udělit přístup jinému správci ke správě Privileged Identity Management, najdete v tématu [udělení přístupu jiným správcům pro správu Privileged Identity Management](pim-how-to-give-access-to-pim.md).

> [!NOTE]
> Vlastní role Azure AD nejsou integrovány s předdefinovanými rolemi adresáře ve verzi Preview. Jakmile je funkce všeobecně dostupná, bude se Správa rolí konat v prostředí integrovaných rolí. Pokud se zobrazí následující nápis, tyto role by se měly spravovat [v prostředí integrovaných rolí](pim-how-to-activate-role.md) a tento článek neplatí:
>
> [![Vyberte Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="assign-a-role"></a>Přiřazení role

Privileged Identity Management může spravovat vlastní role, které můžete vytvořit v Azure Active Directory (Azure AD) Správa aplikací.  Následující kroky vytvoří opravňující přiřazení k roli vlastního adresáře.

1. Přihlaste se k [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) v Azure Portal pomocí uživatelského účtu, který je přiřazený k roli správce privilegované role.
1. Vyberte **vlastní role Azure AD (Preview)**.

    ![Výběrem vlastní role Azure AD ve verzi Preview zobrazíte oprávněná přiřazení rolí.](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Vyberte **role** , abyste viděli seznam vlastních rolí pro aplikace Azure AD.

    ![Vybrat role zobrazit seznam oprávněných přiřazení rolí](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Výběrem **Přidat člena** otevřete stránku přiřazení.
1. Pokud chcete omezit rozsah přiřazení role na jednu aplikaci, vyberte **Rozsah** pro určení rozsahu aplikace.

    ![omezení rozsahu přiřazení oprávněných rolí v Azure AD](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Vyberte **Vybrat roli** a otevřete seznam **Vybrat roli** .

    ![Vyberte oprávněnou roli, kterou chcete přiřadit uživateli.](./media/azure-ad-custom-roles-assign/select-role.png)

1. Vyberte roli, kterou chcete přiřadit, a potom klikněte na **Vybrat**. Otevře se seznam **Vybrat člena** .

    ![Vyberte uživatele, kterému přiřadíte roli.](./media/azure-ad-custom-roles-assign/select-member.png)

1. Vyberte uživatele, kterého chcete přiřadit roli, a pak klikněte na **Vybrat**. Otevře se seznam **nastavení členství** .

    ![Nastavte typ přiřazení role na způsobilé nebo aktivní.](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. Na stránce **nastavení členství** vyberte **oprávněné** nebo **aktivní**:

    - **Způsobilá** přiřazení vyžadují, aby uživatel přiřazený k roli provedl akci předtím, než může tuto roli použít. Akce můžou zahrnovat předání kontroly služby Multi-Factor Authentication, poskytnutí obchodního odůvodnění nebo žádosti o schválení od určených schvalovatelů.
    - **Aktivní** přiřazení nevyžadují, aby přiřazený uživatel prováděl jakoukoli akci pro použití této role. Aktivní uživatelé mají všechna oprávnění přiřazená k této roli.

1. Pokud je **trvalé** zaškrtávací políčko k dispozici a je k dispozici (v závislosti na nastavení role), můžete určit, zda je přiřazení trvalé. Zaškrtnutím políčka zajistěte, aby se přiřazení trvale způsobilo nebo trvale přiřadilo. Zrušte zaškrtnutí políčka a určete dobu trvání přiřazení.
1. Pokud chcete vytvořit nové přiřazení role, klikněte na **Uložit** a pak na **Přidat**. Zobrazí se oznámení o stavu procesu přiřazení.

Přiřazení role ověříte tak, že v otevřené roli vyberete **přiřazení**  >  **přiřadíte** a ověříte, že je přiřazení role správně identifikované jako oprávněné nebo aktivní.

 ![Zkontrolujte, jestli je přiřazení role zobrazené jako oprávněné nebo aktivní.](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Další kroky

- [Aktivace vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Odebrání nebo aktualizace přiřazení vlastní role Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurace přiřazení vlastní role Azure AD](azure-ad-custom-roles-configure.md)
- [Definice rolí v Azure AD](../roles/permissions-reference.md)
