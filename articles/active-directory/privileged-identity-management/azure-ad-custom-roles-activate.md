---
title: Aktivace vlastní role Azure AD – Privileged Identity Management (PIM)
description: Postup aktivace vlastní role Azure AD pro přiřazení Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
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
ms.openlocfilehash: 6c0d98641f8e2040de8350b7dd0231c2e7c889c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371609"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Aktivace vlastní role Azure AD v Privileged Identity Management

Privileged Identity Management v Azure Active Directory (Azure AD) teď podporuje přiřazení za běhu a časově vázaný čas na vlastní role vytvořené pro správu aplikací v prostředí pro správu identit a přístupu. Další informace o vytváření vlastních rolí pro delegování správy aplikací ve službě Azure AD najdete v tématu [vlastní role správců v Azure Active Directory (Preview)](../roles/custom-overview.md).

> [!NOTE]
> Vlastní role Azure AD nejsou integrovány s předdefinovanými rolemi adresáře ve verzi Preview. Jakmile je funkce všeobecně dostupná, bude se Správa rolí konat v prostředí integrovaných rolí. Pokud se zobrazí následující nápis, tyto role by se měly spravovat [v prostředí integrovaných rolí](pim-how-to-activate-role.md) a tento článek neplatí:
>
> :::image type="content" source="media/pim-how-to-add-role-to-user/pim-new-version.png" alt-text="Vyberte Privileged Identity Management ve službě Azure AD." lightbox="media/pim-how-to-add-role-to-user/pim-new-version.png":::

## <a name="activate-a-role"></a>Aktivace role

Pokud potřebujete aktivovat vlastní roli Azure AD, vyžádejte si aktivaci tak, že v Privileged Identity Management vyberete možnost navigační role.

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
1. Otevřete [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)služby Azure AD.

1. Výběrem **vlastní role Azure AD** zobrazíte seznam oprávněných přiřazení vlastních rolí Azure AD.

   ![Zobrazit seznam oprávněných přiřazení vlastní role Azure AD](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
> Před přiřazením role musíte vytvořit nebo nakonfigurovat roli. Další informace o konfiguraci vlastních rolí AAD najdete v tématu [Konfigurace vlastních rolí Azure AD v Privileged Identity Management](azure-ad-custom-roles-configure.md).

1. Na stránce **vlastní role Azure AD (Preview)** Najděte potřebné přiřazení.
1. Kliknutím na **aktivovat roli** otevřete stránku **aktivovat** .
1. Pokud vaše role vyžaduje službu Multi-Factor Authentication, vyberte **před pokračováním ověřit identitu**. Pro každou relaci se vyžaduje ověřování jenom jednou.
1. Vyberte **ověřit moji identitu** a postupujte podle pokynů a poskytněte jakékoli další ověření zabezpečení.
1. Pokud chcete zadat vlastní obor aplikace, vyberte **Rozsah** a otevřete tak podokno filtru. Měli byste požádat o přístup k roli s minimálním požadovaným rozsahem. Pokud je vaše přiřazení v oboru aplikace, můžete aktivovat pouze v tomto oboru.

   ![Přiřazení oboru prostředků Azure AD k přiřazení role](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. V případě potřeby zadejte vlastní čas zahájení aktivace. Při použití se člen role aktivuje v zadaném čase.
1. Do pole **důvod** zadejte důvod žádosti o aktivaci. Tyto zásady je možné v nastavení role nastavit jako povinné nebo ne.
1. Vyberte **aktivovat**.

Pokud role nevyžaduje schválení, aktivuje se podle nastavení a přidá se do seznamu aktivních rolí. Pokud chcete použít aktivovanou roli, začněte postupem v části [přiřazení vlastní role Azure AD v Privileged Identity Management](azure-ad-custom-roles-assign.md).

Pokud role vyžaduje schválení, obdržíte oznámení Azure, které vás informuje, že žádost čeká na schválení.

## <a name="next-steps"></a>Další kroky

- [Přiřazení vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Odebrání nebo aktualizace přiřazení vlastní role Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurace přiřazení vlastní role Azure AD](azure-ad-custom-roles-configure.md)
- [Definice rolí v Azure AD](../roles/permissions-reference.md)
