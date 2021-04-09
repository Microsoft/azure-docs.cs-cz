---
title: Přidání existujícího předplatného Azure do tenanta – Azure AD
description: Pokyny k přidání existujícího předplatného Azure do tenanta Azure Active Directory (Azure AD).
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ac9553660aace8242c81b41fa2cc9171d28219
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594627"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Přiřazení nebo přidání předplatného Azure do tenanta Azure Active Directory

Předplatné Azure má vztah důvěryhodnosti s Azure Active Directory (Azure AD). Předplatné důvěřuje službě Azure AD za účelem ověřování uživatelů, služeb a zařízení.

Několik předplatných může důvěřovat stejnému adresáři služby Azure AD. Každé předplatné může důvěřovat jenom jedinému adresáři.

Jedno nebo více předplatných Azure může vytvořit vztah důvěryhodnosti s instancí služby Azure Active Directory (Azure AD), aby bylo možné ověřovat a autorizovat objekty zabezpečení a zařízení proti službám Azure.  Po vypršení platnosti předplatného zůstane důvěryhodná instance služby Azure AD, ale objekty zabezpečení ztratí přístup k prostředkům Azure.

Když se uživatel zaregistruje do cloudové služby Microsoftu, vytvoří se nový tenant Azure AD a uživatel se stane členem role globálního správce. Pokud se ale vlastník předplatného připojí ke stávajícímu tenantovi, vlastník není přiřazený k roli globálního správce.

Všichni uživatelé mají jeden *Domovský* adresář pro ověřování. Uživatelé můžou být taky hosty v jiných adresářích. Pro každého uživatele ve službě Azure AD vidíte jak domovský adresář, tak i hosty.

> [!Important]
> Když přidružíte předplatné k jinému adresáři, uživatelé, kteří mají role přiřazené pomocí [řízení přístupu založeného na rolích Azure](../../role-based-access-control/role-assignments-portal.md) , ztratí přístup. Klasičtí správci předplatných, včetně správců služeb a spolusprávců, také ztratí přístup.
>
> Když přesunete cluster Azure Kubernetes Service (AKS) do jiného předplatného nebo přesunete předplatné vlastnícího clusterů na nového tenanta, způsobí to, že cluster ztratí funkčnost z důvodu ztráty přiřazení rolí a práv instančního objektu. Další informace o AKS najdete v tématu [Azure Kubernetes Service (AKS)](../../aks/index.yml).

## <a name="before-you-begin"></a>Než začnete

Než budete moct přidružit nebo přidat svoje předplatné, proveďte následující úlohy:

- Projděte si následující seznam změn, ke kterým dojde po přidružení nebo přidání předplatného a o tom, jak to může být ovlivněno:

  - Uživatelé s přiřazenými rolemi pomocí Azure RBAC ztratí přístup.
  - Správce služeb a Co-Administrators ztratí přístup.
  - Pokud máte nějaké trezory klíčů, nebudou k dispozici a budete je muset po přidružení opravit.
  - Pokud máte nějaké spravované identity pro prostředky, jako je Virtual Machines nebo Logic Apps, musíte je po přidružení znovu povolit nebo vytvořit znovu.
  - Pokud máte registrovanou Azure Stack, budete ji muset po přidružení znovu zaregistrovat.
  - Další informace najdete v tématu věnovaném [převodu předplatných Azure do jiného adresáře Azure AD](../../role-based-access-control/transfer-subscription.md).

- Přihlaste se pomocí účtu, který:

  - Má přiřazení role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) k předplatnému. Informace o tom, jak přiřadit roli vlastníka, najdete v tématu [přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md).
  - Existuje v aktuálním adresáři i v novém adresáři. Aktuální adresář je přidružený k předplatnému. Přiřadíte nový adresář k předplatnému. Další informace o získání přístupu k jinému adresáři najdete v tématu [přidání Azure Active Directory uživatelů spolupráce B2B v Azure Portal](../external-identities/add-users-administrator.md).

- Ujistěte se, že nepoužíváte předplatné služby Azure Cloud Service Providers (CSP) (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), interní předplatné od Microsoftu (MS-AZR-0015P) nebo předplatné Microsoft Azure for Students Starter (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Přidružení předplatného k adresáři<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Pokud chcete přidružit existující předplatné k adresáři služby Azure AD, postupujte takto:

1. Přihlaste se a vyberte předplatné, které chcete použít, ze [stránky předplatná v Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Vyberte **změnit adresář**.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png" alt-text="Snímek obrazovky zobrazující stránku odběrů s zvýrazněnou možností adresář změn":::

1. Zkontrolujte všechna zobrazená upozornění a pak vyberte **změnit**.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png" alt-text="Snímek obrazovky, na kterém se zobrazuje stránka pro změnu adresáře s ukázkovým adresářem a zvýrazněným tlačítkem pro změnu":::

   Po změně adresáře pro předplatné se zobrazí zpráva o úspěchu.

1. Vyberte **přepínač adresáře** na stránce odběr a přejděte k novému adresáři.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/directory-switcher.png" alt-text="Snímek obrazovky zobrazující stránku s přepínačem adresáře s ukázkovými informacemi":::

   Aby se všechno zobrazovalo správně, může trvat několik hodin. Pokud se zdá, že trvá příliš dlouho, podívejte se na **globální filtr předplatného**. Ujistěte se, že přesunuté předplatné není skryté. Možná se budete muset odhlásit z Azure Portal a znovu se přihlásit a zobrazit nový adresář.

Změna adresáře předplatného je operace na úrovni služby, takže nemá vliv na vlastnictví fakturace předplatného. Pokud chcete odstranit původní adresář, musíte přenést vlastnictví fakturace předplatného na nového správce účtu. Další informace o přenosu vlastnictví fakturace najdete v tématu [převod vlastnictví předplatného Azure na jiný účet](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Kroky po přidružení

Až přiřadíte předplatné k jinému adresáři, možná budete muset provést následující úlohy pro obnovení operací:

- Pokud máte nějaké trezory klíčů, musíte změnit ID tenanta trezoru klíčů. Další informace najdete v tématu [Změna ID tenanta trezoru klíčů po přesunu předplatného](../../key-vault/general/move-subscription.md).

- Pokud jste pro prostředky použili spravované identity přiřazené systémem, musíte tyto identity znovu povolit. Pokud jste použili spravované identity přiřazené uživatelem, musíte tyto identity znovu vytvořit. Po opětovném povolení nebo opětovném vytvoření spravovaných identit musíte znovu zřídit oprávnění přiřazená těmto identitám. Další informace najdete v tématu, které vysvětluje, [co jsou spravované identity pro prostředky Azure](../managed-identities-azure-resources/overview.md).

- Pokud jste zaregistrovali Azure Stack pomocí tohoto předplatného, musíte se znovu zaregistrovat. Další informace najdete v tématu [registrace centra Azure Stack s Azure](/azure-stack/operator/azure-stack-registration).

- Další informace najdete v tématu věnovaném [převodu předplatných Azure do jiného adresáře Azure AD](../../role-based-access-control/transfer-subscription.md).

## <a name="next-steps"></a>Další kroky

- Pokud chcete vytvořit nového tenanta Azure AD, přečtěte si téma [rychlý Start: vytvoření nového tenanta v Azure Active Directory](active-directory-access-create-new-tenant.md).

- Další informace o tom, jak Microsoft Azure řídí přístup k prostředkům, najdete v tématu [role správců klasického předplatného, role Azure a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Další informace o tom, jak přiřadit role v Azure AD, najdete v tématu [přiřazení rolí správců a uživatelů bez oprávnění správce k Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
