---
title: Přidání existujícího předplatného Azure do tenanta – Azure Active Directory | Microsoft Docs
description: Pokyny k přidání existujícího předplatného Azure do tenanta Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6bce05b99c14fb464cd1a17587d9cf254909d87
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473388"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Přidružte nebo přidejte předplatné Azure ke svému tenantovi Azure Active Directory.

Předplatné Azure má vztah důvěryhodnosti s Azure Active Directory (Azure AD), což znamená, že předplatné důvěřuje službě Azure AD za účelem ověřování uživatelů, služeb a zařízení. Několik předplatných může důvěřovat stejnému adresáři služby Azure AD, ale každý odběr může důvěřovat jenom jedinému adresáři.

Pokud platnost předplatného vyprší, ztratíte přístup ke všem ostatním prostředkům přidruženým k předplatnému. Adresář služby Azure AD zůstane v Azure, takže můžete tento adresář přidružit a spravovat pomocí jiného předplatného Azure.

Všichni uživatelé mají jeden *Domovský* adresář pro ověřování. Uživatelé ale můžou být taky hosty v jiných adresářích. Pro každého uživatele ve službě Azure AD vidíte jak domovský adresář, tak i hosty.

> [!Important]
> Když přiřadíte předplatné k jinému adresáři, uživatelé, kteří mají role přiřazené pomocí [řízení přístupu na základě role (RBAC)](../../role-based-access-control/role-assignments-portal.md) , ztratí přístup. Správci s klasickým předplatným (Správci služeb a spolusprávci) ztratí přístup i vy. 
>
> Přiřazení zásad se také odstraňují z předplatného, když je předplatné přidružené k jinému adresáři.
> 
> Kromě toho je přesunutí clusteru Azure Kubernetes Service (AKS) na jiné předplatné nebo přesunutí předplatného pro clustery do nového tenanta způsobeno tím, že cluster ztratí funkčnost z důvodu ztráty přiřazení rolí a práv instančních objektů. Další informace o AKS najdete v tématu [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Než začnete

Než budete moct přidružit nebo přidat svoje předplatné, musíte provést následující úlohy:

1. Přečtěte si následující seznam změn a o tom, jak to může být ovlivněno:

    - Uživatelé, kterým byly přiřazené role pomocí RBAC, ztratí přístup.
    - Správce služeb a spolusprávci ztratí přístup
    - Pokud máte nějaké trezory klíčů, nebudou k dispozici a budete je muset po přidružení opravit.
    - Pokud máte nějaké spravované identity pro prostředky, jako je Virtual Machines nebo Logic Apps, budete je muset po přidružení znovu povolit nebo vytvořit znovu.
    - Pokud máte registrovanou Azure Stack, budete ji muset po přidružení znovu zaregistrovat.

1. Přihlaste se pomocí účtu, který:
    - Má přiřazení role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) k předplatnému. Informace o tom, jak přiřadit roli vlastníka, najdete v tématu [Správa přístupu k prostředkům Azure pomocí RBAC a Azure Portal](../../role-based-access-control/role-assignments-portal.md).
    - Existuje v aktuálním adresáři, který je přidružený k předplatnému, a v novém adresáři, ke kterému chcete přidružit předplatné. Další informace o získání přístupu k jinému adresáři najdete v tématu [jak Azure Active Directory správci přidat uživatele spolupráce B2B?](../b2b/add-users-administrator.md).

1. Ujistěte se, že nepoužíváte předplatné Cloud Service Providers (CSP) pro Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), interní předplatné Microsoft (MS-AZR-0015P) nebo předplatné Microsoft Imagine (MS-AZR-0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Přiřazení existujícího předplatného do adresáře služby Azure AD

1. Přihlaste se a vyberte předplatné, které chcete použít, ze [stránky předplatná v Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Vyberte **změnit adresář**.

    ![Stránka předplatné se zvýrazněnou možností změnit adresář](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Zkontrolujte všechna zobrazená upozornění a pak vyberte **změnit**.

    ![Změňte stránku adresáře, kde se zobrazí adresář, který se má změnit.](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Pro odběr se změnil adresář a zobrazí se zpráva o úspěchu.

    ![Zpráva o úspěchu změny adresáře](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. Pro přechod do nového adresáře použijte **přepínač adresáře** . Aby se všechno zobrazovalo správně, může trvat několik hodin. Pokud se zdá, že trvá moc dlouho, zkontrolujte **globální filtr předplatného** pro přesunuté předplatné, abyste se ujistili, že není skrytý. Možná se budete muset odhlásit z Azure Portal a znovu se přihlásit, abyste mohli zobrazit nový adresář. 

    ![Stránka přepínač adresáře s ukázkovými informacemi](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Změna adresáře předplatného je operace na úrovni služby, takže nemá vliv na vlastnictví fakturace předplatného. Správce účtu může stále změnit správce služby z [centra účtů](https://account.azure.com/subscriptions). Pokud chcete odstranit původní adresář, musíte přenést vlastnictví fakturace předplatného na nového správce účtu. Další informace o přenosu vlastnictví fakturace najdete v tématu [převod vlastnictví předplatného Azure na jiný účet](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Kroky po přidružení
Až přiřadíte předplatné k jinému adresáři, může docházet k dalším krokům, které musíte provést, aby se operace obnovila.

1. Pokud máte nějaké trezory klíčů, musíte změnit ID tenanta trezoru klíčů. Další informace najdete v tématu [Změna ID tenanta trezoru klíčů po přesunu předplatného](../../key-vault/key-vault-subscription-move-fix.md).

2. Pokud jste pro prostředky používali spravované identity přiřazené systémem, musíte je znovu povolit. Pokud jste používali spravované identity přiřazené uživatelem, musíte je znovu vytvořit. Po opětovném povolení nebo opětovném vytvoření spravovaných identit musíte znovu zřídit oprávnění přiřazená těmto identitám. Další informace najdete v tématu [co jsou spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).

3. Pokud jste zaregistrovali Azure Stack pomocí tohoto předplatného, musíte se znovu zaregistrovat. Další informace najdete v tématu [registrace Azure Stack s Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Další kroky

- Pokud chcete vytvořit nového tenanta Azure AD, přečtěte si téma [přístup Azure Active Directory k vytvoření nového tenanta](active-directory-access-create-new-tenant.md) .

- Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Další informace o tom, jak přiřadit role v Azure AD, najdete v tématu [přiřazení rolí adresáře uživatelům pomocí Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
