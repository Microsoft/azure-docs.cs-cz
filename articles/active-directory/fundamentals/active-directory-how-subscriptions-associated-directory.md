---
title: Přidání existujícího předplatného Azure do tenanta – Azure AD
description: Pokyny k přidání existujícího předplatného Azure do svého tenanta Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d2111e631dd43b5d86a0bd486642657a20cc3ba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422983"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Přiřazení nebo přidání předplatného Azure do svého tenanta Azure Active Directory

Předplatné Azure má vztah důvěryhodnosti s Azure Active Directory (Azure AD). Předplatné důvěřuje službě Azure AD za účelem ověřování uživatelů, služeb a zařízení.

Několik předplatných může důvěřovat stejnému adresáři služby Azure AD. Každé předplatné může důvěřovat jenom jedinému adresáři.

Pokud vaše předplatné vyprší, ztratíte přístup k všechny další prostředky spojené s předplatným. Adresář Azure AD však zůstává v Azure. Můžete přidružit a spravovat adresář pomocí jiného předplatného Azure.

Všichni uživatelé mají jeden *Domovský* adresář pro ověřování. Uživatelé můžou být taky hosty v jiných adresářích. Zobrazí se domácích a hosta adresář pro každého uživatele ve službě Azure AD.

> [!Important]
> Když přiřadíte předplatné k jinému adresáři, uživatelé, kteří mají role přiřazené pomocí [řízení přístupu na základě role (RBAC)](../../role-based-access-control/role-assignments-portal.md) , ztratí přístup. Správci s klasickým předplatným, včetně správců služeb a spolusprávců, ztratí i přístup.
>
> Přiřazení zásad se také odstraňují z předplatného, když je předplatné přidružené k jinému adresáři.
>
> Když přesunete cluster Azure Kubernetes Service (AKS) do jiného předplatného nebo přesunete předplatné vlastnícího clusterů na nového tenanta, způsobí to, že cluster ztratí funkčnost z důvodu ztráty přiřazení rolí a práv instančního objektu. Další informace o AKS najdete v tématu [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Než začnete

Než budete moct přidružit nebo přidat svoje předplatné, proveďte následující úlohy:

- Přečtěte si následující seznam změn a o tom, jak to může být ovlivněno:

  - Uživatelé, kterým byly přiřazené role pomocí RBAC, ztratí přístup.
  - Správce služeb a spolusprávci ztratí přístup
  - Pokud máte nějaké trezory klíčů, nebudou k dispozici a budete je muset po přidružení opravit.
  - Pokud máte nějaké spravované identity pro prostředky, jako je Virtual Machines nebo Logic Apps, musíte je po přidružení znovu povolit nebo vytvořit znovu.
  - Pokud máte registrovanou Azure Stack, budete ji muset po přidružení znovu zaregistrovat.

- Přihlaste se pomocí účtu, který:

  - Má přiřazení role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) k předplatnému. Informace o tom, jak přiřadit roli vlastníka, najdete v tématu [Správa přístupu k prostředkům Azure pomocí RBAC a Azure Portal](../../role-based-access-control/role-assignments-portal.md).
  - Existuje v aktuálním adresáři i v novém adresáři. Aktuální adresář je přidružený k předplatnému. Přiřadíte nový adresář k předplatnému. Další informace o získání přístupu k jinému adresáři najdete v tématu [přidání Azure Active Directory uživatelů spolupráce B2B v Azure Portal](../b2b/add-users-administrator.md).

- Ujistěte se, že nepoužíváte předplatného služby Azure Service poskytovatele CSP (Cloud) (MS-AZR - 0145P, MS - AZR - 0146P, MS - AZR - 159P), předplatné Microsoft Internal (MS-AZR - 0015P) nebo předplatné Microsoft Imagine (MS-AZR - 0144P).

## Přidružení předplatného k adresáři<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Pokud chcete přidružit existující předplatné k adresáři služby Azure AD, postupujte takto:

1. Přihlaste se a vyberte předplatné, které chcete použít [stránce předplatná na webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Vyberte **změnit adresář**.

    ![Stránka předplatných se zvýrazněnou možností změnit adresář](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Zkontrolujte všechna upozornění, která se zobrazí a pak vyberte **změnu**.

    ![Změnit na stránku adresáře s adresářem změnit na](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Adresář se změní pro předplatné a se zobrazí zpráva o úspěchu.

    ![Zpráva o úspěchu změny adresáře](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Pomocí **přepínače Adresář** přejděte do nového adresáře. Aby se všechno zobrazovalo správně, může trvat několik hodin. Pokud se zdá, že trvá příliš dlouho, podívejte se na **globální filtr předplatného**. Ujistěte se, že přesunuté předplatné není skryté. Možná se budete muset odhlásit z Azure Portal a znovu se přihlásit a zobrazit nový adresář.

![Stránka přepínač adresáře s ukázkovými informacemi](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Změna adresáře předplatného je operace úroveň služby, takže to nijak neovlivní vlastnictví fakturace předplatného. Správce účtu může stále změnit správce služby z [centra pro účty](https://account.azure.com/subscriptions). Pokud chcete odstranit původní adresář, musíte přenést vlastnictví fakturace předplatného na nového správce účtu. Další informace o přenosu vlastnictví fakturace najdete v tématu [převod vlastnictví předplatného Azure na jiný účet](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Kroky po přidružení

Až přiřadíte předplatné k jinému adresáři, možná budete muset provést následující úlohy pro obnovení operací:

- Pokud máte nějaké trezory klíčů, musíte změnit ID tenanta trezoru klíčů. Další informace najdete v tématu [Změna ID tenanta trezoru klíčů po přesunu předplatného](../../key-vault/key-vault-subscription-move-fix.md).

- Pokud jste pro prostředky použili spravované identity přiřazené systémem, musíte tyto identity znovu povolit. Pokud jste použili spravované identity přiřazené uživatelem, musíte tyto identity znovu vytvořit. Po opětovném povolení nebo opětovném vytvoření spravovaných identit musíte znovu zřídit oprávnění přiřazená těmto identitám. Další informace najdete v tématu [co je spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).

- Pokud jste zaregistrovali Azure Stack pomocí tohoto předplatného, musíte se znovu zaregistrovat. Další informace najdete v tématu [registrace Azure Stack s Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Další kroky

- Pokud chcete vytvořit nového tenanta Azure AD, přečtěte si téma [rychlý Start: vytvoření nového tenanta v Azure Active Directory](active-directory-access-create-new-tenant.md).

- Další informace o tom, jak Microsoft Azure řídí přístup k prostředkům, najdete v tématu [role správců klasického předplatného, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Další informace o tom, jak přiřadit role v Azure AD, najdete v tématu [přiřazení rolí správců a uživatelů bez oprávnění správce k Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
