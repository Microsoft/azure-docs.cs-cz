---
title: Přidání existujícího předplatného Azure do tenanta – Azure AD
description: Pokyny k přidání existujícího předplatného Azure do tenanta Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/01/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperfq4
ms.collection: M365-identity-device-management
ms.openlocfilehash: 347232d452b5d309da7cdb7c8fe34f0e41abde00
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87025334"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Přiřazení nebo přidání předplatného Azure do tenanta Azure Active Directory

Předplatné Azure má vztah důvěryhodnosti s Azure Active Directory (Azure AD). Předplatné důvěřuje službě Azure AD za účelem ověřování uživatelů, služeb a zařízení.

Několik předplatných může důvěřovat stejnému adresáři služby Azure AD. Každé předplatné může důvěřovat jenom jedinému adresáři.

Pokud platnost předplatného vyprší, ztratíte přístup ke všem ostatním prostředkům přidruženým k předplatnému. Adresář Azure AD však zůstává v Azure. Můžete přidružit a spravovat adresář pomocí jiného předplatného Azure.

Všichni uživatelé mají jeden *Domovský* adresář pro ověřování. Uživatelé můžou být taky hosty v jiných adresářích. Pro každého uživatele ve službě Azure AD vidíte jak domovský adresář, tak i hosty.

> [!Important]
> Když přiřadíte předplatné k jinému adresáři, uživatelé, kteří mají role přiřazené pomocí [řízení přístupu na základě role (RBAC)](../../role-based-access-control/role-assignments-portal.md) , ztratí přístup. Klasičtí správci předplatných, včetně správců služeb a spolusprávců, také ztratí přístup.
>
> Z předplatného se také odstraní přiřazení zásad, jakmile se předplatné přidruží k jinému adresáři.
>
> Když přesunete cluster Azure Kubernetes Service (AKS) do jiného předplatného nebo přesunete předplatné vlastnícího clusterů na nového tenanta, způsobí to, že cluster ztratí funkčnost z důvodu ztráty přiřazení rolí a práv instančního objektu. Další informace o AKS najdete v tématu [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Než začnete

Než budete moct přidružit nebo přidat svoje předplatné, proveďte následující úlohy:

- Projděte si následující seznam změn, ke kterým dojde po přidružení nebo přidání předplatného a o tom, jak to může být ovlivněno:

  - Uživatelé, kterým byly přiřazené role pomocí RBAC, ztratí přístup.
  - Správce služeb a spolusprávci ztratí přístup
  - Pokud máte nějaké trezory klíčů, nebudou k dispozici a budete je muset po přidružení opravit.
  - Pokud máte nějaké spravované identity pro prostředky, jako je Virtual Machines nebo Logic Apps, musíte je po přidružení znovu povolit nebo vytvořit znovu.
  - Pokud máte registrovanou Azure Stack, budete ji muset po přidružení znovu zaregistrovat.

- Přihlaste se pomocí účtu, který:

  - Má přiřazení role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) k předplatnému. Informace o tom, jak přiřadit roli vlastníka, najdete v tématu [Správa přístupu k prostředkům Azure pomocí RBAC a Azure Portal](../../role-based-access-control/role-assignments-portal.md).
  - Existuje v aktuálním adresáři i v novém adresáři. Aktuální adresář je přidružený k předplatnému. Přiřadíte nový adresář k předplatnému. Další informace o získání přístupu k jinému adresáři najdete v tématu [přidání Azure Active Directory uživatelů spolupráce B2B v Azure Portal](../b2b/add-users-administrator.md).

- Ujistěte se, že nepoužíváte předplatné Cloud Service Providers (CSP) pro Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), interní předplatné Microsoft (MS-AZR-0015P) nebo předplatné Microsoft Imagine (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Přidružení předplatného k adresáři<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Pokud chcete přidružit existující předplatné k adresáři služby Azure AD, postupujte takto:

1. Přihlaste se a vyberte předplatné, které chcete použít, ze [stránky předplatná v Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Vyberte **změnit adresář**.

    ![Stránka předplatné se zvýrazněnou možností změnit adresář](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Zkontrolujte všechna zobrazená upozornění a pak vyberte **změnit**.

    ![Změňte stránku adresáře, kde se zobrazí adresář, který se má změnit.](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Po změně adresáře pro předplatné se zobrazí zpráva o úspěchu.

  1. Vyberte **přepínač adresáře** na stránce odběr a přejděte k novému adresáři. 

      ![Stránka přepínač adresáře s ukázkovými informacemi](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

      Aby se všechno zobrazovalo správně, může trvat několik hodin. Pokud se zdá, že trvá příliš dlouho, podívejte se na **globální filtr předplatného**. Ujistěte se, že přesunuté předplatné není skryté. Možná se budete muset odhlásit z Azure Portal a znovu se přihlásit a zobrazit nový adresář.

Změna adresáře předplatného je operace na úrovni služby, takže nemá vliv na vlastnictví fakturace předplatného. Správce účtu může stále změnit správce služby z [centra účtů](https://account.azure.com/subscriptions). Pokud chcete odstranit původní adresář, musíte přenést vlastnictví fakturace předplatného na nového správce účtu. Další informace o přenosu vlastnictví fakturace najdete v tématu [převod vlastnictví předplatného Azure na jiný účet](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Kroky po přidružení

Až přiřadíte předplatné k jinému adresáři, možná budete muset provést následující úlohy pro obnovení operací:

- Pokud máte nějaké trezory klíčů, musíte změnit ID tenanta trezoru klíčů. Další informace najdete v tématu [Změna ID tenanta trezoru klíčů po přesunu předplatného](../../key-vault/general/move-subscription.md).

- Pokud jste pro prostředky použili spravované identity přiřazené systémem, musíte tyto identity znovu povolit. Pokud jste použili spravované identity přiřazené uživatelem, musíte tyto identity znovu vytvořit. Po opětovném povolení nebo opětovném vytvoření spravovaných identit musíte znovu zřídit oprávnění přiřazená těmto identitám. Další informace najdete v tématu [co jsou spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).

- Pokud jste zaregistrovali Azure Stack pomocí tohoto předplatného, musíte se znovu zaregistrovat. Další informace najdete v tématu [registrace Azure Stack s Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Další kroky

- Pokud chcete vytvořit nového tenanta Azure AD, přečtěte si téma [rychlý Start: vytvoření nového tenanta v Azure Active Directory](active-directory-access-create-new-tenant.md).

- Další informace o tom, jak Microsoft Azure řídí přístup k prostředkům, najdete v tématu [role správců klasického předplatného, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Další informace o tom, jak přiřadit role v Azure AD, najdete v tématu [přiřazení rolí správců a uživatelů bez oprávnění správce k Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
