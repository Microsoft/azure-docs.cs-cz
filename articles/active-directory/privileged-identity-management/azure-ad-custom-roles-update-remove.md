---
title: Aktualizace nebo odebrání vlastní role Azure AD – Privileged Identity Management (PIM)
description: Jak aktualizovat nebo odebrat přiřazení vlastní role Azure AD Privileged Identity Management (PIM)
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77499108"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Aktualizace nebo odebrání přiřazené vlastní role Azure AD v Privileged Identity Management

V tomto článku se dozvíte, jak pomocí Privileged Identity Management (PIM) aktualizovat nebo odebrat přiřazení za běhu a časově vázaný čas na vlastní role vytvořené pro správu aplikací v prostředí pro správu služby Azure Active Directory (Azure AD). 

- Další informace o vytváření vlastních rolí pro delegování správy aplikací ve službě Azure AD najdete v tématu [vlastní role správců v Azure Active Directory (Preview)](../users-groups-roles/roles-custom-overview.md). 
- Pokud jste zatím Privileged Identity Management ještě nepoužili, získáte další informace na adrese Začínáme [s používáním Privileged Identity Management](pim-getting-started.md).

> [!NOTE]
> Vlastní role Azure AD nejsou integrovány s předdefinovanými rolemi adresáře ve verzi Preview. Jakmile je funkce všeobecně dostupná, bude se Správa rolí konat v prostředí integrovaných rolí. Pokud se zobrazí následující nápis, tyto role by se měly spravovat [v prostředí integrovaných rolí](pim-how-to-add-role-to-user.md) a tento článek neplatí:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>Aktualizace nebo odebrání přiřazení

Pomocí těchto kroků aktualizujete nebo odeberete existující přiřazení vlastní role.

1. Přihlaste se k [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) v Azure Portal pomocí uživatelského účtu, který je přiřazený k roli správce privilegované role.
1. Vyberte **vlastní role Azure AD (Preview)**.

    ![Výběrem vlastní role Azure AD ve verzi Preview zobrazíte oprávněná přiřazení rolí.](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Vyberte **role** , abyste viděli seznam **přiřazení** vlastních rolí pro aplikace Azure AD.

    ![Vybrat role zobrazit seznam oprávněných přiřazení rolí](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Vyberte roli, kterou chcete aktualizovat nebo odebrat.
1. Vyhledejte přiřazení role na kartách **opravňující role** nebo **aktivní role** .
1. Pokud chcete aktualizovat nebo odebrat přiřazení role, vyberte **aktualizovat** nebo **Odebrat** .

    ![V přiřazení oprávněné role vyberte Odebrat nebo aktualizovat.](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Další kroky

- [Aktivace vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Přiřazení vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Konfigurace přiřazení vlastní role Azure AD](azure-ad-custom-roles-configure.md)
