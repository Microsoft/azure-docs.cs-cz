---
title: Přidání existujícího předplatného Azure do svého tenanta – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny k přidání existujícího předplatného Azure do svého tenanta Azure Active Directory.
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
ms.openlocfilehash: fb4fa92d8b3c174cdf9b3695f8564cc11c1ad291
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851744"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Přiřazení nebo přidání předplatného Azure do svého tenanta Azure Active Directory

Předplatné Azure má vztah důvěryhodnosti s Azure Active Directory (Azure AD), což znamená, že předplatné důvěřuje službě Azure AD za účelem ověřování uživatelů, služeb a zařízení. Několik předplatných může důvěřovat stejnému adresáři Azure AD, ale každé předplatné může důvěřovat pouze jeden adresář.

Pokud vaše předplatné vyprší, ztratíte přístup k všechny další prostředky spojené s předplatným. Adresář Azure AD ale zůstává v Azure, umožňuje přiřadit a spravovat adresář pomocí jiného předplatného Azure.

Všichni uživatelé mají jeden *Domovský* adresář pro ověřování. Uživatelé však může být také hosty v dalších adresářích. Zobrazí se domácích a hosta adresář pro každého uživatele ve službě Azure AD.

> [!Important]
> Když přiřadíte předplatné k jinému adresáři, uživatelé, kteří mají role přiřazené pomocí [řízení přístupu na základě role (RBAC)](../../role-based-access-control/role-assignments-portal.md) , ztratí přístup. Správci s klasickým předplatným (Správci služeb a spolusprávci) ztratí přístup i vy.
> 
> Kromě toho je přesunutí clusteru Azure Kubernetes Service (AKS) na jiné předplatné nebo přesunutí předplatného pro clustery do nového tenanta způsobeno tím, že cluster ztratí funkčnost z důvodu ztráty přiřazení rolí a práv instančních objektů. Další informace o AKS najdete v tématu [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Před zahájením

Předtím, než můžete přiřadit nebo přidejte svoje předplatné, je třeba provést následující úkoly:

1. Přečtěte si následující seznam změn a o tom, jak to může být ovlivněno:

    - Uživatelé, kterým byly přiřazené role pomocí RBAC, ztratí přístup.
    - Správce služeb a spolusprávci ztratí přístup
    - Pokud máte nějaké trezory klíčů, nebudou k dispozici a budete je muset po přidružení opravit.
    - Pokud máte nějaké spravované identity pro prostředky, jako je Virtual Machines nebo Logic Apps, budete je muset po přidružení znovu povolit nebo vytvořit znovu.
    - Pokud máte registrovanou Azure Stack, budete ji muset po přidružení znovu zaregistrovat.

1. Přihlaste se pomocí účtu, který:
    - Má přiřazení role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) k předplatnému. Informace o tom, jak přiřadit roli vlastníka, najdete v tématu [Správa přístupu k prostředkům Azure pomocí RBAC a Azure Portal](../../role-based-access-control/role-assignments-portal.md).
    - Existuje v aktuálním adresáři, který je spojen s předplatným a v novém adresáři, který je ve které chcete přidružit předplatné do budoucna. Další informace o tom, jak přístup k jinému adresáři, najdete v části [jak správci služby Azure Active Directory přidat uživatele spolupráce B2B?](../b2b/add-users-administrator.md).

1. Ujistěte se, že nepoužíváte předplatného služby Azure Service poskytovatele CSP (Cloud) (MS-AZR - 0145P, MS - AZR - 0146P, MS - AZR - 159P), předplatné Microsoft Internal (MS-AZR - 0015P) nebo předplatné Microsoft Imagine (MS-AZR - 0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Přiřazení existujícího předplatného do adresáře služby Azure AD

1. Přihlaste se a vyberte předplatné, které chcete použít [stránce předplatná na webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Vyberte **změnit adresář**.

    ![Stránka předplatných se zvýrazněnou možností změnit adresář](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Zkontrolujte všechna upozornění, která se zobrazí a pak vyberte **změnu**.

    ![Změnit na stránku adresáře s adresářem změnit na](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Adresář se změní pro předplatné a se zobrazí zpráva o úspěchu.

    ![Zpráva o úspěchu změny adresáře](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. Pro přechod do nového adresáře použijte **přepínač adresáře** . Aby se všechno zobrazovalo správně, může trvat několik hodin. Pokud se zdá, že trvá moc dlouho, zkontrolujte **globální filtr** předplatného pro přesunuté předplatné, abyste se ujistili, že není skrytý. Možná se budete muset odhlásit z Azure Portal a znovu se přihlásit, abyste mohli zobrazit nový adresář. 

    ![Stránka přepínač adresáře s ukázkovými informacemi](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Změna adresáře předplatného je operace úroveň služby, takže to nijak neovlivní vlastnictví fakturace předplatného. Správce účtu může stále změnit správce služby z [centra pro účty](https://account.azure.com/subscriptions). Pokud chcete odstranit původní adresář, musíte přenést vlastnictví pro nového správce účtu. fakturace předplatného Další informace o tom, jak převést vlastnictví fakturace, najdete v tématu [Převod vlastnictví předplatného Azure na jiný účet](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Kroky po přidružení
Až přiřadíte předplatné k jinému adresáři, může docházet k dalším krokům, které musíte provést, aby se operace obnovila.

1. Pokud máte nějaké trezory klíčů, musíte změnit ID tenanta trezoru klíčů. Další informace najdete v tématu [Změna ID tenanta trezoru klíčů po přesunu](../../key-vault/key-vault-subscription-move-fix.md)předplatného.

2. Pokud jste pro prostředky používali spravované identity přiřazené systémem, musíte je znovu povolit. Pokud jste používali spravované identity přiřazené uživatelem, musíte je znovu vytvořit. Po opětovném povolení nebo opětovném vytvoření spravovaných identit musíte znovu zřídit oprávnění přiřazená těmto identitám. Další informace najdete v tématu [co jsou spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).

3. Pokud jste zaregistrovali Azure Stack pomocí tohoto předplatného, musíte se znovu zaregistrovat. Další informace najdete v tématu [registrace Azure Stack s Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Další postup

- K vytvoření nové služby Azure AD tenanta, přečtěte si téma [přístupu Azure Active Directory a vytvořit nového tenanta](active-directory-access-create-new-tenant.md)

- Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Další informace o tom, jak přiřadit role ve službě Azure AD najdete v tématu [přiřazení role adresáře pro uživatele se službou Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
