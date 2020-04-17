---
title: Přidání existujícího předplatného Azure do svého tenanta – Azure AD
description: Pokyny, jak přidat existující předplatné Azure do klienta Azure Active Directory.
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
ms.openlocfilehash: 104bf51fb03d88ab0e5efd25ebebb0e3060bc264
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457922"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Přiřazení nebo přidání předplatného Azure do tenanta Azure Active Directory

Předplatné Azure má vztah důvěryhodnosti s Azure Active Directory (Azure AD). Předplatné důvěřuje Azure AD k ověření uživatelů, služeb a zařízení.

Více předplatných můžete důvěřovat stejnému adresáři Azure AD. Každé předplatné může důvěřovat pouze jednomu adresáři.

Pokud platnost vašeho předplatného vyprší, ztratíte přístup ke všem ostatním prostředkům přidruženým k předplatnému. Adresář Azure AD však zůstane v Azure. Adresář můžete přidružit a spravovat pomocí jiného předplatného Azure.

Všichni uživatelé mají jeden *domovský* adresář pro ověřování. Vaši uživatelé mohou být také hosty v jiných adresářích. Můžete zobrazit domovní i host adresáře pro každého uživatele ve službě Azure AD.

> [!Important]
> Při přidružení předplatného k jinému adresáři ztratí uživatelé, kteří mají role přiřazené pomocí [řízení přístupu na základě rolí (RBAC),](../../role-based-access-control/role-assignments-portal.md) přístup. Klasičtí správci předplatných, včetně správců služeb a spolusprávců, také ztratí přístup.
>
> Z předplatného se také odstraní přiřazení zásad, jakmile se předplatné přidruží k jinému adresáři.
>
> Přesunutí clusteru služby Azure Kubernetes Service (AKS) do jiného předplatného nebo přesunutí předplatného vlastnícího clusteru do nového klienta způsobí, že cluster ztratí funkce kvůli ztrátě přiřazení rolí a práv hlavního mocnina služby. Další informace o AKS najdete [v tématu Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Než začnete

Než budete moci předplatné přidružit nebo přidat, proveďte následující úkoly:

- Projděte si následující seznam změn a způsob, jakým by vás mohly ovlivnit:

  - Uživatelé, kterým byly přiřazeny role pomocí RBAC ztratí přístup
  - Správce služeb a spolusprávci ztratí přístup
  - Pokud máte nějaké trezory klíčů, budou nepřístupné a budete je muset opravit po přidružení.
  - Pokud máte nějaké spravované identity pro prostředky, jako jsou virtuální počítače nebo logic apps, musíte je znovu povolit nebo znovu vytvořit po přidružení
  - Pokud máte registrovaný Azure Stack, budete ho muset po přidružení znovu zaregistrovat.

- Přihlaste se pomocí účtu, který:

  - Má přiřazení role [vlastníka](../../role-based-access-control/built-in-roles.md#owner) pro předplatné. Informace o tom, jak přiřadit roli vlastníka, [najdete v tématu Správa přístupu k prostředkům Azure pomocí RBAC a portálu Azure](../../role-based-access-control/role-assignments-portal.md).
  - Existuje v aktuálním adresáři i v novém adresáři. Aktuální adresář je přidružen k předplatnému. Nový adresář přidružíte k předplatnému. Další informace o získání přístupu k jinému adresáři najdete v tématu [Přidání uživatelů spolupráce služby Azure Active Directory B2B na webu Azure Portal](../b2b/add-users-administrator.md).

- Ujistěte se, že nepoužíváte předplatné Azure Cloud Service Providers (CSP) (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), interní předplatné Microsoftu (MS-AZR-0015P) nebo předplatné Microsoft Imagine (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Přidružení předplatného k adresáři<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Pokud chcete k adresáři Azure AD přidružit existující předplatné, postupujte takto:

1. Přihlaste se a vyberte předplatné, které chcete použít, na [stránce Předplatná na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Vyberte **Možnost Změnit adresář**.

    ![Stránka Odběry se zvýrazněnou možností Změnit adresář](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Zkontrolujte všechna upozornění, která se zobrazí, a pak vyberte **Změnit**.

    ![Změna stránky adresáře se zobrazením adresáře, na který se má změnit](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Adresář se změní pro odběr a zobrazí se zpráva o úspěchu.

    ![Zpráva o úspěchu změny adresáře](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Pomocí **adresáře Přepnout** přejděte do nového adresáře. Může trvat několik hodin, než se všechno správně objeví. Pokud se zdá, že to trvá příliš dlouho, zkontrolujte **filtr globální odběr**. Ujistěte se, že přesunuté předplatné není skryté. Možná se budete muset odhlásit z webu Azure Portal a znovu se přihlásit, abyste viděli nový adresář.

![Stránka přepínače adresářů s ukázkovými informacemi](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Změna adresáře předplatného je operace na úrovni služby, takže nemá vliv na vlastnictví fakturace předplatného. Správce účtu může stále měnit správce služeb z [Centra účtů](https://account.azure.com/subscriptions). Chcete-li odstranit původní adresář, je nutné převést vlastnictví fakturace předplatného na nového správce účtu. Další informace o převodu vlastnictví fakturace najdete v [tématu Převod vlastnictví předplatného Azure na jiný účet](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Kroky po přidružení

Po přidružení předplatného k jinému adresáři může být nutné provést následující úkoly, abyste obnovili operace:

- Pokud máte nějaké trezory klíčů, musíte změnit ID klienta trezoru klíčů. Další informace naleznete [v tématu Změna ID klienta trezoru klíčů po přesunutí předplatného](../../key-vault/general/subscription-move-fix.md).

- Pokud jste pro prostředky použili spravované identity přiřazené systémem, je nutné tyto identity znovu povolit. Pokud jste použili spravované identity přiřazené uživatelem, musíte tyto identity znovu vytvořit. Po opětovném povolení nebo opětovném vytvoření spravovaných identit je nutné znovu nastolit oprávnění přiřazená těmto identitám. Další informace najdete v tématu [Co je spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).

- Pokud jste zaregistrovali Azure Stack pomocí tohoto předplatného, musíte znovu zaregistrovat. Další informace najdete [v tématu Registrace Azure Stack u Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Další kroky

- Pokud chcete vytvořit nového klienta Azure AD, [přečtěte si pár témat: Vytvoření nového klienta ve službě Azure Active Directory](active-directory-access-create-new-tenant.md).

- Další informace o tom, jak Microsoft Azure řídí přístup k prostředkům, najdete [v tématu Klasické role správce předplatného, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Další informace o tom, jak přiřadit role ve službě Azure AD, najdete v [tématu Přiřazení rolí správce a role bez oprávnění správce uživatelům pomocí služby Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
