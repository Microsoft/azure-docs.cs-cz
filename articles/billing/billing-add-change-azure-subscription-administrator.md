---
title: Přidat nebo změnit správce předplatného Azure | Dokumentace Microsoftu
description: Popisuje, jak přidat nebo změnit správce předplatného Azure pomocí řízení přístupu na základě role (RBAC).
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 000315a2d751a05d3e401ee1bb9f593c6e321194
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922898"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Přidat nebo změnit správce předplatného Azure

Pokud chcete spravovat přístup k prostředkům Azure, potřebujete mít odpovídající roli správce. Azure má autorizaci systém volá řízení přístupu na základě role (RBAC) s několik předdefinovaných rolí, které můžete vybrat z. Můžete přiřadit tyto role v různých oborech, jako je například skupiny pro správu, předplatné nebo skupinu prostředků.

Microsoft doporučuje při správě přístupu k prostředkům pomocí RBAC. Nicméně pokud stále používáte Klasický model nasazení a správu klasických prostředků pomocí [modulu prostředí PowerShell služby správy Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure), budete muset použít klasického správce. 

> [!TIP]
> Pokud používáte pouze na webu Azure portal ke správě klasických prostředků, nebudete muset použít klasického správce.

Další informace najdete v tématu [Azure Resource Manageru a klasického nasazení](../azure-resource-manager/resource-manager-deployment-model.md) a [správci předplatného Azure classic](../role-based-access-control/classic-administrators.md).

Tento článek popisuje, jak přidat nebo změnit roli správce u uživatele pomocí RBAC v oboru předplatného.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Přiřadit uživatele jako správce předplatného

Chcete-li uživatel správcem předplatného Azure, přiřaďte jim [vlastníka](../role-based-access-control/built-in-roles.md#owner) rolí (RBAC role) v oboru předplatného. Role vlastníka poskytuje úplný přístup uživatelů ke všem prostředkům v rámci předplatného, včetně práva na delegovat přístup ostatním uživatelům. Tyto kroky jsou stejné jako ostatní přiřazení role.

1. Na webu Azure Portal, otevřete [předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Klikněte na předplatné, ve které chcete udělit přístup.

1. Klikněte na tlačítko **řízení přístupu (IAM)** .

1. Klikněte na tlačítko **přiřazení rolí** kartu zobrazíte všechna přiřazení rolí pro toto předplatné.

    ![Snímek obrazovky zobrazující přiřazení rolí](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Klikněte na tlačítko **přidat** > **přidat přiřazení role** otevřít **přidat přiřazení role** podokně.

    Pokud nemáte oprávnění k přiřazování rolí, možnost se deaktivuje.

1. V **Role** rozevíracího seznamu, vyberte **vlastníka** role.

1. V **vyberte** seznamu, vyberte uživatele. Pokud nevidíte uživatele v seznamu, můžete zadat **vyberte** pole Hledat v adresáři zobrazovaná jména a e-mailové adresy.

    ![Snímek obrazovky zobrazující vybrané role vlastníka](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Klikněte na tlačítko **Uložit** přiřazení role.

    Po chvíli se má uživatel přiřazenou roli vlastník v oboru předplatného.

## <a name="next-steps"></a>Další postup

* [Co je řízení přístupu na základě role (RBAC)?](../role-based-access-control/overview.md)
* [Vysvětlení různých rolí v Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Postup: Přiřazení nebo přidání předplatného Azure do služby Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Oprávnění role správce v Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktujte podporu

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
