---
title: Aktualizace nebo odebrání vlastní role Azure AD – správa privilegovaných identit (PIM)
description: Jak aktualizovat nebo odebrat vlastní přiřazení role Azure AD Privilegovaná správa identit (PIM)
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
ms.openlocfilehash: ad5747be47e250cf9c623cc40d21d12c91ee16f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499108"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Aktualizace nebo odebrání přiřazené vlastní role Azure AD ve správě privilegovaných identit

Tento článek vám řekne, jak pomocí správy privilegovaných identit (PIM) aktualizovat nebo odebrat přiřazení just-in-time a časově vázané na vlastní role vytvořené pro správu aplikací v prostředí správy Služby Azure Active Directory (Azure AD). 

- Další informace o vytváření vlastních rolí pro delegování správy aplikací ve službě Azure AD najdete [v tématu Vlastní role správce ve službě Azure Active Directory (preview).](../users-groups-roles/roles-custom-overview.md) 
- Pokud jste dosud nepoužili správu privilegovaných identit, získejte další informace na [začátku pomocí správy privilegovaných identit](pim-getting-started.md).

> [!NOTE]
> Vlastní role Azure AD nejsou integrované s předdefinovanými rolemi adresáře během předběžné verze. Jakmile je funkce obecně dostupná, správa rolí proběhne v integrovaném prostředí rolí. Pokud se zobrazí následující banner, tyto role by měly být spravovány [v předdefinované role zkušenosti](pim-how-to-add-role-to-user.md) a tento článek se nevztahuje:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>Aktualizace nebo odebrání přiřazení

Podle těchto kroků aktualizujte nebo odeberte existující vlastní přiřazení role.

1. Přihlaste se ke [správě privilegovaných identit](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) na webu Azure Portal pomocí uživatelského účtu, který je přiřazený roli správce privilegované role.
1. Vyberte **vlastní role Azure AD (preview).**

    ![Výběrem náhledu vlastních rolí Azure AD zobrazíte vyhotovení přiřazení rolí.](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Výběrem **možnosti Role** zobrazíte seznam **přiřazení** vlastních rolí pro aplikace Azure AD.

    ![Vybrat role, zobrazit seznam způsobilých přiřazení rolí.](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Vyberte roli, kterou chcete aktualizovat nebo odebrat.
1. Najděte přiřazení role na kartách **Způsobilé role** nebo **Aktivní role.**
1. Chcete-li aktualizovat nebo odebrat, vyberte **aktualizovat** nebo **odebrat** přiřazení role.

    ![Vybrat odebrat nebo aktualizovat ve způsobilém přiřazení role](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Další kroky

- [Aktivace vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Přiřazení vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Konfigurace přiřazení vlastní role Azure AD](azure-ad-custom-roles-configure.md)
