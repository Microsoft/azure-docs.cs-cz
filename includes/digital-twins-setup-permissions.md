---
author: baanders
description: zahrnutý soubor pro požadavek oprávnění v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205474"
---
## <a name="prerequisites-permission-requirements"></a>Požadavky: požadavky na oprávnění

Aby bylo možné dokončit všechny kroky v tomto článku, musíte mít [ve svém předplatném roli](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) , která má následující oprávnění:
* Vytváření a Správa prostředků Azure
* Správa přístupu uživatelů k prostředkům Azure (včetně udělení a delegování oprávnění)

Mezi běžné role, které splňují tento požadavek, patří *vlastník*, *účet* správce nebo kombinace správce a *přispěvatele* *přístupu uživatele* . Úplné vysvětlení rolí a oprávnění, včetně toho, jaká oprávnění jsou součástí jiných rolí, najdete v části [*klasické role správců předplatného, role Azure a role Azure AD*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) v dokumentaci k Azure RBAC.

Pokud chcete zobrazit vaši roli ve svém předplatném, navštivte [stránku předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) v Azure Portal (můžete použít tento odkaz nebo vyhledat *předplatná* pomocí panelu hledání na portálu). Vyhledejte název předplatného, které používáte, a zobrazte jeho roli ve sloupci *Moje role* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Zobrazení stránky odběry v Azure Portal zobrazující uživatele jako vlastníka" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Pokud zjistíte, že hodnota je *Přispěvatel* nebo jiná role, která nemá požadovaná oprávnění popsaná výše, můžete se obrátit na uživatele, který má tato oprávnění (například vlastníka předplatného nebo správce účtu *), a* pokračovat jedním z následujících způsobů:
* Požádejte o dokončení kroků v tomto článku vaším jménem
* Zajistěte, aby v předplatném přešly na roli, abyste měli oprávnění k tomu, abyste mohli pokračovat sami. To, jestli je to vhodné, závisí na vaší organizaci a vaší roli.