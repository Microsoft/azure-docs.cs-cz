---
author: baanders
description: zahrnutý soubor pro přehled kroků a požadavky na oprávnění v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009636"
---
>[!NOTE]
>Tyto operace mají být dokončeny uživatelem s výkonem pro správu prostředků i přístupu uživatelů k předplatnému Azure. I když je možné provést některé kroky s nižšími oprávněními, bude nutné, aby se spolupráce někoho s těmito oprávněními kompletně nastavila v použitelné instanci. Další informace najdete v části [*požadavky: požadovaná oprávnění*](#prerequisites-permission-requirements) níže.

Úplná instalace nové instance digitálních vláken Azure se skládá ze tří částí:
1. **Vytvoření instance**
2. **Nastavení uživatelských oprávnění pro přístup k uživateli**: uživatelé Azure musí mít v instanci Azure Digital zdvojene *(Preview) přiřazenou roli Vlastník Azure Digital userss (Preview)* , aby ji bylo možné spravovat a její data. V tomto kroku se jako vlastník nebo správce předplatného Azure přiřadí tato role osobě, která bude spravovat vaši instanci digitálních vláken Azure. Může to být sami nebo někdo jiný ve vaší organizaci.
3. **Nastavení oprávnění k přístupu pro klientské aplikace**: je běžné napsat klientskou aplikaci, která bude použita k interakci s vaší instancí. Aby klientská aplikace měla přístup k digitálním událostem Azure, musíte nastavit *registraci aplikace* v [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) , kterou klientská aplikace použije k ověření instance.

Abyste mohli pokračovat, budete potřebovat předplatné Azure. Můžete si ho nastavit zdarma [tady](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites-permission-requirements"></a>Požadavky: požadavky na oprávnění

Aby bylo možné dokončit všechny kroky v tomto článku, musíte mít [ve svém předplatném roli](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) , která má následující oprávnění:
* Vytváření a Správa prostředků Azure
* Správa přístupu uživatelů k prostředkům Azure (včetně udělení a delegování oprávnění)

Mezi běžné role, které splňují tento požadavek, patří *vlastník*, *účet*správce nebo kombinace správce a *přispěvatele* *přístupu uživatele* . Úplné vysvětlení rolí a oprávnění, včetně toho, jaká oprávnění jsou součástí jiných rolí, najdete v části [*klasické role správců předplatného, role Azure a role Azure AD*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) v dokumentaci k Azure RBAC.

Pokud chcete zobrazit vaši roli ve svém předplatném, navštivte [stránku předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) v Azure Portal (můžete použít tento odkaz nebo vyhledat *předplatná* pomocí panelu hledání na portálu). Vyhledejte název předplatného, které používáte, a zobrazte jeho roli ve sloupci *Moje role* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Zobrazení stránky odběry v Azure Portal zobrazující uživatele jako vlastníka" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Pokud zjistíte, že hodnota je *Přispěvatel*nebo jiná role, která nemá požadovaná oprávnění popsaná výše, můžete se obrátit na uživatele, který má tato oprávnění (například vlastníka předplatného nebo správce účtu *), a* pokračovat jedním z následujících způsobů:
* Požádejte o dokončení kroků v tomto článku vaším jménem
* Zajistěte, aby v předplatném přešly na roli, abyste měli oprávnění k tomu, abyste mohli pokračovat sami. To, jestli je to vhodné, závisí na vaší organizaci a vaší roli.