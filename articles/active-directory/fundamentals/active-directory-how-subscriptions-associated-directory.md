---
title: Přidání existujícího předplatného Azure do svého tenanta – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny k přidání existujícího předplatného Azure do svého tenanta Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 20c4be3741fa1e728cf59a148beb5adbc87b89c0
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452019"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Přiřazení nebo přidání předplatného Azure do svého tenanta Azure Active Directory
Předplatné Azure má vztah důvěryhodnosti s Azure Active Directory (Azure AD), což znamená, že předplatné vztahy důvěryhodnosti služby Azure AD k ověřování uživatelů, služeb a zařízení. Několik předplatných může důvěřovat stejnému adresáři Azure AD, ale každé předplatné může důvěřovat pouze jeden adresář.

Pokud vaše předplatné vyprší, ztratíte přístup k všechny další prostředky spojené s předplatným. Adresář Azure AD ale zůstává v Azure, umožňuje přiřadit a spravovat adresář pomocí jiného předplatného Azure.

Všichni uživatelé mají jeden "domovskou" adresář pro ověřování. Uživatelé však může být také hosty v dalších adresářích. Zobrazí se domácích a hosta adresář pro každého uživatele ve službě Azure AD.

>[!Important]
>Všechny [řízení přístupu na základě Role (RBAC)](../../role-based-access-control/role-assignments-portal.md) uživatelé s přiřazeného přístupu, spolu s všichni správci předplatného ztratí přístup po předplatné změny v adresáři. Kromě toho pokud máte jakékoli trezorů klíčů, budete také neplatila přesun předplatného. Který chcete vyřešit, je nutné [Změna ID tenanta trezoru klíčů](../../key-vault/key-vault-subscription-move-fix.md) před obnovení operací.


## <a name="before-you-begin"></a>Před zahájením
Předtím, než můžete přiřadit nebo přidejte svoje předplatné, je třeba provést následující úkoly:

- Přihlaste se pomocí účtu, který:
    - Má **RBAC vlastníka** s přístupem k předplatnému.

    - Existuje v aktuálním adresáři, který je spojen s předplatným a v novém adresáři, který je ve které chcete přidružit předplatné do budoucna. Další informace o tom, jak přístup k jinému adresáři, najdete v části [jak správci služby Azure Active Directory přidat uživatele spolupráce B2B?](../b2b/add-users-administrator.md).

- Ujistěte se, že nepoužíváte předplatného služby Azure Service poskytovatele CSP (Cloud) (MS-AZR - 0145P, MS - AZR - 0146P, MS - AZR - 159P), předplatné Microsoft Internal (MS-AZR - 0015P) nebo předplatné Microsoft Imagine (MS-AZR - 0144P).
    
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

## <a name="next-steps"></a>Další postup

- K vytvoření nové služby Azure AD tenanta, přečtěte si téma [přístupu Azure Active Directory a vytvořit nového tenanta](active-directory-access-create-new-tenant.md)

- Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Další informace o tom, jak přiřadit role ve službě Azure AD najdete v tématu [přiřazení role adresáře pro uživatele se službou Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
