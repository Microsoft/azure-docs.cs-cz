---
title: Aktivace vlastní role Azure AD – správa privilegovaných identit (PIM)
description: Jak aktivovat vlastní roli Azure AD pro přiřazení privilegované správy identit (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
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
ms.openlocfilehash: cbd60d1311bd84adb303a0d329ab4e42f4d61525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498734"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Aktivace vlastní role Azure AD ve správě privilegovaných identit

Správa privilegovaných identit ve službě Azure Active Directory (Azure AD) teď podporuje přiřazení just-in-time a časově vázané na vlastní role vytvořené pro správu aplikací v prostředí správy správy identit a přístupu. Další informace o vytváření vlastních rolí pro delegování správy aplikací ve službě Azure AD najdete [v tématu Vlastní role správce ve službě Azure Active Directory (preview).](../users-groups-roles/roles-custom-overview.md)

> [!NOTE]
> Vlastní role Azure AD nejsou integrované s předdefinovanými rolemi adresáře během předběžné verze. Jakmile je funkce obecně dostupná, správa rolí proběhne v integrovaném prostředí rolí. Pokud se zobrazí následující banner, tyto role by měly být spravovány [v předdefinované role zkušenosti](pim-how-to-activate-role.md) a tento článek se nevztahuje:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="activate-a-role"></a>Aktivace role

Když potřebujete aktivovat vlastní roli Azure AD, požádejte o aktivaci výběrem možnosti navigace moje role v privilegované správě identit.

1. Přihlaste se [k portálu Azure](https://portal.azure.com).
1. Otevřete [správu privilegovaných identit](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)Azure AD .

1. Výběrem **vlastních rolí Azure AD** zobrazíte seznam způsobilých přiřazení vlastních rolí Azure AD.

   ![Zobrazení seznamu způsobilých přiřazení vlastních rolí Azure AD](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
>  Před přiřazením role je nutné vytvořit nebo nakonfigurovat roli. Další informace týkající se konfigurace vlastních rolí AAD naleznete v tématu [zde] (https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-ad-custom-roles-configure)

1. Na stránce **vlastní role Azure AD (Preview)** najděte přiřazení, které potřebujete.
1. Výběrem **možnosti Aktivovat roli** otevřete stránku **Aktivovat.**
1. Pokud vaše role vyžaduje vícefaktorové ověřování, vyberte **před pokračováním možnost Ověřit identitu**. Je nutné ověřit pouze jednou za relaci.
1. Vyberte **Ověřit mou identitu** a podle pokynů zajistěte další ověření zabezpečení.
1. Chcete-li zadat vlastní obor aplikace, otevřete podokno filtru výběrem **možnosti Obor.** Měli byste požádat o přístup k roli v minimálním rozsahu potřebné. Pokud je přiřazení v oboru aplikace, můžete aktivovat pouze v tomto oboru.

   ![Přiřazení oboru prostředků Azure AD k přiřazení role](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. V případě potřeby zadejte vlastní čas zahájení aktivace. Při použití je člen role aktivován v určený čas.
1. Do pole **Důvod** zadejte důvod žádosti o aktivaci. Ty mohou být požadovány nebo ne v nastavení role.
1. Vyberte **Aktivovat**.

Pokud role nevyžaduje schválení, aktivuje se podle vašeho nastavení a přidá se do seznamu aktivních rolí. Pokud chcete použít aktivovanou roli, začněte kroky v [části Přiřazení vlastní role Azure AD v privileged identity management](azure-ad-custom-roles-assign.md).

Pokud role vyžaduje schválení k aktivaci, obdržíte oznámení Azure s informací, že žádost čeká na schválení.

## <a name="next-steps"></a>Další kroky

- [Přiřazení vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Odebrání nebo aktualizace přiřazení vlastní role Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurace přiřazení vlastní role Azure AD](azure-ad-custom-roles-configure.md)
- [Definice rolí ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
