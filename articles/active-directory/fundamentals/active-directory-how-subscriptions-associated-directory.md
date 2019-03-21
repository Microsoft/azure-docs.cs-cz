---
title: Přidání existujícího předplatného Azure do svého tenanta – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny k přidání existujícího předplatného Azure do svého tenanta Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 262f46b3d0b61923352f3ff98b08cb5e4f6d5e75
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993903"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Přiřazení nebo přidání předplatného Azure do svého tenanta Azure Active Directory

Předplatné Azure má vztah důvěryhodnosti s Azure Active Directory (Azure AD), což znamená, že předplatné vztahy důvěryhodnosti služby Azure AD k ověřování uživatelů, služeb a zařízení. Několik předplatných může důvěřovat stejnému adresáři Azure AD, ale každé předplatné může důvěřovat pouze jeden adresář.

Pokud vaše předplatné vyprší, ztratíte přístup k všechny další prostředky spojené s předplatným. Adresář Azure AD ale zůstává v Azure, umožňuje přiřadit a spravovat adresář pomocí jiného předplatného Azure.

Všichni uživatelé mají jeden *domácí* adresáře pro ověřování. Uživatelé však může být také hosty v dalších adresářích. Zobrazí se domácích a hosta adresář pro každého uživatele ve službě Azure AD.

> [!Important]
> Když přidružíte předplatné na jiný adresář, uživatelé, kteří mají přiřazené, pomocí role [řízení přístupu na základě role (RBAC)](../../role-based-access-control/role-assignments-portal.md) tento přístup přijdou. Správci předplatného Classic (Správce služeb a spolupracujících správců) taky ztratíte přístup.

## <a name="before-you-begin"></a>Před zahájením

Předtím, než můžete přiřadit nebo přidejte svoje předplatné, je třeba provést následující úkoly:

1. Projděte si následující seznam změn a jak vám může to mít vliv:

    - Uživatelé, kteří mají přiřazený role pomocí RBAC tento přístup přijdou
    - Správce služeb a spolupracujících správců přijdete o přístup
    - Pokud máte jakékoli trezory klíčů budou nedostupné a budete mít a opravte je po přidružení
    - Pokud máte registrované služby Azure Stack, budete muset znovu zaregistrovat po přidružení

1. Přihlaste se pomocí účtu, který:
    - Má [vlastníka](../../role-based-access-control/built-in-roles.md#owner) přiřazení role pro předplatné. Informace o tom, jak přiřadit roli vlastník, naleznete v tématu [spravovat přístup k prostředkům Azure pomocí RBAC a webu Azure portal](../../role-based-access-control/role-assignments-portal.md).
    - Existuje v aktuálním adresáři, který je spojen s předplatným a v novém adresáři, který je ve které chcete přidružit předplatné do budoucna. Další informace o tom, jak přístup k jinému adresáři, najdete v části [jak správci služby Azure Active Directory přidat uživatele spolupráce B2B?](../b2b/add-users-administrator.md).

1. Ujistěte se, že nepoužíváte předplatného služby Azure Service poskytovatele CSP (Cloud) (MS-AZR - 0145P, MS - AZR - 0146P, MS - AZR - 159P), předplatné Microsoft Internal (MS-AZR - 0015P) nebo předplatné Microsoft Imagine (MS-AZR - 0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Přiřazení existujícího předplatného do adresáře služby Azure AD

1. Přihlaste se a vyberte předplatné, které chcete použít [stránce předplatná na webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Vyberte **změnit adresář**.

    ![Stránka předplatných se zvýrazněnou možností změnit adresář](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Zkontrolujte všechna upozornění, která se zobrazí a pak vyberte **změnu**.

    ![Změnit na stránku adresáře s adresářem změnit na](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Adresář se změní pro předplatné a se zobrazí zpráva o úspěchu.

    ![Zpráva o úspěchu](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    

4. Pomocí přepínače adresářů přejděte do nového adresáře. Správné zobrazení všech komponent může trvat až 10 minut.

    ![Stránka přepínání adresáře](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Změna adresáře předplatného je operace úroveň služby, takže to nijak neovlivní vlastnictví fakturace předplatného. Správce účtu může stále změnit správce služby z [centra pro účty](https://account.azure.com/subscriptions). Pokud chcete odstranit původní adresář, musíte přenést vlastnictví pro nového správce účtu. fakturace předplatného Další informace o tom, jak převést vlastnictví fakturace, najdete v tématu [Převod vlastnictví předplatného Azure na jiný účet](../../billing/billing-subscription-transfer.md). 

## <a name="post-association-steps"></a>Kroky po přidružení

Poté, co přidružíte předplatné na jiný adresář, mohou existovat další kroky, které musíte provést při obnovení operací.

1. Pokud máte jakékoli trezorů klíčů, je nutné změnit ID tenanta trezoru klíčů. Další informace najdete v tématu [Změna ID tenanta trezoru klíčů po přesunu předplatného](../../key-vault/key-vault-subscription-move-fix.md).

1. Pokud jste se zaregistrovali službě Azure Stack prostřednictvím tohoto předplatného, budete muset znovu zaregistrovat. Další informace najdete v tématu [registrace Azure Stack s využitím Azure](../../azure-stack/azure-stack-registration.md).

## <a name="next-steps"></a>Další postup

- K vytvoření nové služby Azure AD tenanta, přečtěte si téma [přístupu Azure Active Directory a vytvořit nového tenanta](active-directory-access-create-new-tenant.md)

- Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Další informace o tom, jak přiřadit role ve službě Azure AD najdete v tématu [přiřazení role adresáře pro uživatele se službou Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
