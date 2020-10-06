---
title: Přidání nebo změna správců předplatného Azure
description: Popisuje, jak přidat nebo změnit správce předplatného Azure s využitím řízení přístupu na základě role v Azure (Azure RBAC).
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 273f12765f60eb1a0d65037e30e33e58b16463bf
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372302"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Přidání nebo změna správců předplatného Azure


Pokud chcete spravovat přístup k prostředkům Azure, potřebujete mít odpovídající roli správce. Azure má systém autorizace označovaný jako [řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md) s několika předdefinovanými rolemi, které můžete použít. Tyto role můžete přiřadit v různých oborech, například v oboru skupiny pro správu, předplatného nebo skupiny prostředků. Ve výchozím nastavení může osoba, která vytváří nové předplatné Azure, přiřazovat ostatním uživatelům přístup pro správu k předplatnému.

Tento článek popisuje, jak pro uživatele přidat nebo změnit roli správce s využitím Azure RBAC v rozsahu předplatného.

Microsoft doporučuje spravovat přístup k prostředkům pomocí Azure RBAC. Pokud však stále používáte model nasazení Classic a ke správě klasických prostředků využíváte [modul PowerShellu pro správu služeb Azure](/powershell/module/servicemanagement/azure.service), budete muset použít klasického správce.

> [!TIP]
> Pokud ke správě klasických prostředků využíváte pouze Azure Portal, nebudete muset použít klasického správce.

Další informace najdete v tématech [Modely nasazení Azure Resource Manager a Classic](../../azure-resource-manager/management/deployment-models.md) a [Klasičtí správci předplatných Azure](../../role-based-access-control/classic-administrators.md).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>Přiřazení správce předplatného

Pokud chcete z uživatele udělat správce předplatného Azure, stávající správce mu přiřadí roli [Vlastník](../../role-based-access-control/built-in-roles.md#owner) (role Azure) v oboru předplatného. Role Vlastník poskytuje uživateli úplný přístup ke všem prostředkům v předplatném, včetně práva delegovat přístup na ostatní. Tento postup je stejný jako u jakéhokoli jiného přiřazení role.

Pokud si nejste jisti, kdo je správcem účtu předplatného, zjistíte to následujícím postupem.

1. Otevřete [stránku Předplatná na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte předplatné, které chcete zkontrolovat, a pak se podívejte pod **Nastavení**.
1. Vyberte **Vlastnosti**. Správce účtu předplatného se zobrazí v poli **Správce účtu**.

### <a name="to-assign-a-user-as-an-administrator"></a>Přiřazení uživatele jako správce

1. Přihlaste se k webu Azure Portal jako vlastník předplatného a otevřete [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Klikněte na předplatné, ke kterému chcete udělit přístup.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Kliknutím na kartu **Přiřazení rolí** zobrazte všechna přiřazení rolí pro toto předplatné.

    ![Snímek obrazovky ukazující přiřazení rolí](./media/add-change-subscription-administrator/role-assignments.png)

1. Kliknutím na **Přidat** > **Přidat přiřazení role** otevřete podokno **Přidat přiřazení role**.

    Pokud nemáte oprávnění přiřazovat role, tato možnost bude neaktivní.

1. V rozevíracím seznamu **Role** vyberte roli **Vlastník**.

1. V seznamu **Vybrat** vyberte uživatele. Pokud se uživatel v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy a e-mailové adresy.

    ![Snímek obrazovky s vybranou rolí Vlastník](./media/add-change-subscription-administrator/add-role.png)

1. Kliknutím na **Uložit** roli přiřaďte.

    Za chvíli se uživateli přiřadí role Vlastník v oboru předplatného.

## <a name="next-steps"></a>Další kroky

* [Co je řízení přístupu na základě role v Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
* [Vysvětlení různých rolí v Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Přiřazení nebo přidání předplatného Azure do tenanta Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Oprávnění role správce v Azure Active Directory](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.
