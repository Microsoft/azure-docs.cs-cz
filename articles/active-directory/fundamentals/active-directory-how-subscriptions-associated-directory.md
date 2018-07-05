---
title: Přidání existujícího předplatného Azure do adresáře služby Azure AD | Dokumentace Microsoftu
description: Přidání existujícího předplatného do adresáře služby Azure AD
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: bcae3f51e2245928c8110d06f3514177d57ac883
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449763"
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Přiřazení nebo přidání předplatného Azure do služby Azure Active Directory

V tomto článku najdete informace o vztahu mezi předplatným Azure a službou Azure Active Directory (Azure AD) a zjistíte, jak do svého adresáře služby Azure AD přidat existující předplatné. Vaše předplatné Azure má s Azure AD vztah důvěryhodnosti. To znamená, že adresáři svěřuje ověřování uživatelů, služeb a zařízení. Několik předplatných může důvěřovat stejnému adresáři, ale každé předplatné důvěřuje jenom jednomu adresáři. 

Vztah důvěryhodnosti, který má předplatné s adresářem, se liší od vztahu, který má předplatné se ostatními prostředky v Azure (webové stránky, databáze apod.). Pokud platnost předplatného vyprší, zastaví se i přístup k těmto dalším prostředkům přidruženým k předplatnému. Adresář služby Azure AD ale v Azure zůstane a vy k němu můžete přidružit jiné předplatné a spravovat adresář pomocí tohoto nového předplatného.

Všichni uživatelé mají jeden domovský adresář, který je ověřuje, ale mohou být také hosty v dalších adresářích. Ve službě Azure AD se vám zobrazí adresáře, kterých je váš uživatelský účet členem nebo hostem.

## <a name="before-you-begin"></a>Než začnete

* Abyste měli přístup k příslušnému předplatnému, musíte se přihlásit pomocí účtu, který má přiřazenou roli vlastníka předplatného RBAC.
* Musíte se přihlásit pomocí účtu, který existuje v aktuálním adresáři, ke kterému je předplatné přidruženo, i v adresáři, do kterého chcete předplatné přidat. Další informace o tom, jak získat přístup k jinému adresáři, najdete v tématu [Jak správci služby Azure Active Directory přidávají uživatele s možností B2B spolupráce?](../b2b/add-users-administrator.md)
* Tato funkce není dostupná pro předplatná CSP (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P) a předplatná Microsoft Imagine (MS-AZR-0144P).

## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Přiřazení existujícího předplatného do adresáře služby Azure AD

1. Přihlaste se a na stránce [Předplatná na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) vyberte předplatné.
2. Klikněte na **Změnit adresář**.

    ![Snímek obrazovky s tlačítkem Změnit adresář](./media/active-directory-how-subscriptions-associated-directory/edit-directory-button.PNG)
3. Přečtěte si upozornění. Jakmile se adresář předplatného změní, všichni uživatelé předplatného [Role-Based Access Control (RBAC)](../../role-based-access-control/role-assignments-portal.md) (Řízení přístupu na základě role), kteří k němu mají povolený přístup, a všichni správci předplatného k němu ztratí přístup.
4. Vyberte adresář.

    ![Snímek obrazovky s uživatelským rozhraním pro změnu adresáře](./media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.PNG)
5. Klikněte na **Změnit**.
6. Výborně! Pomocí přepínače adresářů přejděte do nového adresáře. Správné zobrazení všech komponent může trvat až 10 minut.

    ![Snímek obrazovky s oznámením o úspěšné změně adresáře](./media/active-directory-how-subscriptions-associated-directory/edit-directory-success.PNG)

    ![Snímek obrazovky s přepínačem](./media/active-directory-how-subscriptions-associated-directory/directory-switcher.PNG)


Přesun předplatného ovlivní také všechny služby Azure Key Vault, které máte, a proto [změňte ID tenanta Key Vault](../../key-vault/key-vault-subscription-move-fix.md) dříve, než budete pokračovat.

Změna adresáře předplatného představuje operaci na úrovni služby. Neovlivní vlastnictví fakturace předplatného a správce účtu bude stále moct změnit správce služby pomocí [Centra účtů](https://account.azure.com/subscriptions). Pokud chcete odstranit původní adresář, vlastnictví fakturace předplatného musíte převést na nového správce účtu. Další informace o tom, jak převést vlastnictví fakturace, najdete v tématu [Převod vlastnictví předplatného Azure na jiný účet](../../billing/billing-subscription-transfer.md). 

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak zdarma vytvořit nový adresář služby Azure AD, najdete v tématu [Jak získat klienta služby Azure Active Directory](../develop/active-directory-howto-tenant.md).
* Další informace o tom, jak převést vlastnictví fakturace předplatného Azure, najdete v tématu [Převod vlastnictví předplatného Azure na jiný účet](../../billing/billing-subscription-transfer.md).
* Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Další informace o tom, jak přiřadit role ve službě Azure AD, najdete v tématu [Přiřazení rolí správce ve službě Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
