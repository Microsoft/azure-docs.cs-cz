---
title: Přidání nebo změna správců předplatného Azure | Microsoft Docs
description: Popisuje, jak přidat nebo změnit správce předplatného Azure s využitím řízení přístupu na základě role (RBAC).
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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "64922898"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Přidání nebo změna správců předplatného Azure

Pokud chcete spravovat přístup k prostředkům Azure, potřebujete mít odpovídající roli správce. Azure má systém autorizace označovaný jako řízení přístupu na základě role (RBAC) s několika předdefinovanými rolemi, které můžete použít. Tyto role můžete přiřadit v různých oborech, například v oboru skupiny pro správu, předplatného nebo skupiny prostředků.

Microsoft doporučuje spravovat přístup k prostředkům pomocí řízení přístupu na základě role (RBAC). Pokud však stále používáte model nasazení Classic a ke správě klasických prostředků využíváte [modul PowerShellu pro správu služeb Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure), budete muset použít klasického správce. 

> [!TIP]
> Pokud ke správě klasických prostředků využíváte pouze Azure Portal, nebudete muset použít klasického správce.

Další informace najdete v tématech [Modely nasazení Azure Resource Manager a Classic](../azure-resource-manager/resource-manager-deployment-model.md) a [Klasičtí správci předplatných Azure](../role-based-access-control/classic-administrators.md).

Tento článek popisuje, jak pro uživatele přidat nebo změnit roli správce s využitím řízení přístupu na základě role (RBAC) v oboru předplatného.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Přiřazení uživatele jako správce předplatného

Pokud chcete z uživatele udělat správce předplatného Azure, přiřaďte mu roli [Vlastník](../role-based-access-control/built-in-roles.md#owner) (role RBAC) v oboru předplatného. Role Vlastník poskytuje uživateli úplný přístup ke všem prostředkům v předplatném, včetně práva delegovat přístup na ostatní. Tento postup je stejný jako u jakéhokoli jiného přiřazení role.

1. Na webu Azure Portal otevřete okno [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Klikněte na předplatné, ke kterému chcete udělit přístup.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Kliknutím na kartu **Přiřazení rolí** zobrazte všechna přiřazení rolí pro toto předplatné.

    ![Snímek obrazovky ukazující přiřazení rolí](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Kliknutím na **Přidat** > **Přidat přiřazení role** otevřete podokno **Přidat přiřazení role**.

    Pokud nemáte oprávnění přiřazovat role, tato možnost bude neaktivní.

1. V rozevíracím seznamu **Role** vyberte roli **Vlastník**.

1. V seznamu **Vybrat** vyberte uživatele. Pokud se uživatel v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy a e-mailové adresy.

    ![Snímek obrazovky s vybranou rolí Vlastník](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Kliknutím na **Uložit** roli přiřaďte.

    Za chvíli se uživateli přiřadí role Vlastník v oboru předplatného.

## <a name="next-steps"></a>Další kroky

* [Co je řízení přístupu na základě role (RBAC)?](../role-based-access-control/overview.md)
* [Vysvětlení různých rolí v Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Postup: Přiřazení nebo přidání předplatného Azure do služby Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Oprávnění role správce v Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.
