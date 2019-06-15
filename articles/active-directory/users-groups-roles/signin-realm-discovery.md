---
title: Vyhledání uživatelského jména během přihlašování ověřování – Azure Active Directory | Dokumentace Microsoftu
description: Na obrazovce pro zasílání zpráv odráží vyhledávání uživatelské jméno při přihlašování
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: db627359b75aa0ea19e30a8d22bcacaa3409cb4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66418215"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Zjišťování domovské sféry pro Azure Active Directory přihlašovací stránky

Měníme přihlašovací chování Azure Active Directory (Azure AD), abychom zlepšili možnosti využití a udělali prostor pro nové metody ověřování. Během přihlášení Azure AD určí, kde se uživatel musí ověřit. Azure AD si přečte organizační a uživatelská nastavení pro uživatelské jméno zadané na přihlašovací stránce a na jejich základě se inteligentně rozhoduje. Jedná se o krok k budoucnosti bez hesel, který podporuje další přihlašovací údaje, jako je FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Chování zjišťování domovské sféry

V minulosti zjišťování domovské sféry se řídí podle domény, která je k dispozici při přihlášení nebo zásada zjišťování domovské sféry pro některé starší verze aplikace. Například v našem chování zjišťování uživatele služby Azure Active Directory může člověk nechtěně napíše špatně své uživatelské jméno, ale by stále dorazí na obrazovce kolekce přihlašovacích údajů jejich organizace. K tomu dojde, když uživatel správně poskytne název domény organizace "contoso.com". Toto chování ale neumožňuje další rozlišování a přizpůsobení prostředí pro jednotlivé uživatele.

Pro podporu většímu počtu přihlašovací údaje a zlepšit tak použitelnost, je teď aktualizovaný chování při vyhledávání uživatelského jména Azure Active Directory během procesu přihlášení. Nové chování umožňuje inteligentní rozhodování načtením tenanta a uživatelské nastavení úrovně podle zadané na stránce přihlašovací uživatelské jméno. Chcete-li to možné, Azure Active Directory zkontroluje, zda existuje v zadané doméně, která je zadána na přihlašovací stránce uživatelského jména, nebo přesměruje uživatele k zadání přihlašovacích údajů.

Další výhodou této práce je vylepšené chybové zprávy. Tady je několik příkladů vylepšení chybových zpráv při přihlašování k aplikaci, která podporuje jenom uživatele Azure Active Directory.

- Uživatelské jméno je zadáno chybně nebo uživatelské jméno nebylo dosud nebyly synchronizovány do Azure AD:
  
    ![uživatelské jméno je zadáno chybně nebo nebyl nalezen](./media/signin-realm-discovery/typo-username.png)
  
- Název domény je chybně:
  
    ![název domény je zadáno chybně nebo nebyl nalezen](./media/signin-realm-discovery/typo-domain.png)
  
- Uživatel se pokusí přihlásit pomocí známých uživatelů domény:
  
    ![Přihlaste se pomocí známých uživatelů domény](./media/signin-realm-discovery/consumer-domain.png)
  
- Heslo je chybně se zadala některá, ale uživatelské jméno je správné:  
  
    ![heslo je chybně pomocí vhodného uživatelského jména](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Tato funkce může mít vliv na federované domény spoléhat na úroveň starého domény vyhledávání domovské sféry přinutit federace. Když se přidá Podpora federovaných domén aktualizace, naleznete v tématu [zjišťování domovské sféry během přihlašování pro služby Microsoft 365](https://azure.microsoft.com/updates/signin-hrd/). Některé organizace mají do té doby trénuje jejich zaměstnanci mohli přihlásit pomocí uživatelského jména, která neexistuje v Azure Active Directory, ale obsahuje správného názvu domény, protože názvy domén nyní směruje uživatele na koncový bod domény organizace. Nové chování přihlášení to neumožňuje. Uživatel je upozorněn opravte uživatelské jméno a nejsou povoleny se přihlásit pomocí uživatelského jména, která neexistuje v Azure Active Directory.
>
> Pokud vy nebo vaše organizace postupy, které jsou závislé na staré chování, je důležité pro správce organizace aktualizovat zaměstnance přihlašování a ověřování dokumentaci a školení uživatelům používat jejich uživatelské jméno Azure Active Directory pro přihlášení.
  
Pokud máte obavy díky novému chování, nechte vaše příspěvky v **zpětnou vazbu** části tohoto článku.  

## <a name="next-steps"></a>Další postup

[Přizpůsobení přihlášení brandingu](../fundamentals/add-custom-domain.md)
