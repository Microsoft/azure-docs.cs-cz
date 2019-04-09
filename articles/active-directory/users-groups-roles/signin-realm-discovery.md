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
ms.date: 04/03/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91d0dbaf9b648f42ef535d8b491df04b2c7042d7
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007518"
---
# <a name="home-realm-discovery-during-sign-in-for-microsoft-365-services"></a>Zjišťování domovské sféry během přihlašování pro služby Microsoft 365

Měníme naši Azure Active Directory (Azure AD) přihlášení chování uvolnilo místo pro nové metody ověřování a zlepšit použitelnost. Při přihlašování Azure AD Určuje, kdy uživatel potřebuje ověřit. Azure AD umožňuje inteligentní rozhodování načtením nastavení organizace a uživatele pro zadané na stránce přihlašovací uživatelské jméno. Toto je krok k zabezpečení bez hesla budoucí umožňující další přihlašovací údaje jako FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Chování zjišťování domovské sféry

V minulosti zjišťování domovské sféry se řídí podle domény, která je k dispozici při přihlášení nebo zásada zjišťování domovské sféry pro některé starší verze aplikace. Například staré chování zjišťování uživatele služby Azure Active Directory může člověk nechtěně napíše špatně své uživatelské jméno, ale bude stále dorazí na obrazovce kolekce přihlašovacích údajů jejich organizace. K tomu dojde, když uživatel správně poskytne název domény organizace "contoso.com". Toto chování neumožňuje členitosti k přizpůsobení prostředí pro jednotlivé uživatele.

Pro podporu většímu počtu přihlašovací údaje a zlepšit tak použitelnost, je teď aktualizovaný chování při vyhledávání uživatelského jména Azure Active Directory během procesu přihlášení. Nové chování umožňuje inteligentní rozhodování načtením tenanta a uživatelské nastavení úrovně podle zadané na stránce přihlašovací uživatelské jméno. Chcete-li to možné, Azure Active Directory zkontroluje, zda existuje v zadané doméně, která je zadána na přihlašovací stránce uživatelského jména, nebo přesměruje uživatele k zadání přihlašovacích údajů. 

Další výhodou této práce je vylepšené chybové zprávy. Tady je několik příkladů vylepšení chybových zpráv při přihlašování k aplikaci, která podporuje jenom uživatele Azure Active Directory.

1. Uživatelské jméno je zadáno chybně nebo uživatelské jméno nebylo dosud nebyly synchronizovány do Azure AD:
  
    ![uživatelské jméno je zadáno chybně nebo nebyl nalezen](./media/signin-realm-discovery/typo-username.png)
  
2. Název domény je chybně:
  
    ![název domény je zadáno chybně nebo nebyl nalezen](./media/signin-realm-discovery/typo-domain.png)
  
3. Uživatel se pokusí přihlásit pomocí známých uživatelů domény:
  
    ![Přihlaste se pomocí známých uživatelů domény](./media/signin-realm-discovery/consumer-domain.png)
  
4. Heslo je chybně se zadala některá, ale uživatelské jméno je správné:  
  
    ![heslo je chybně pomocí vhodného uživatelského jména](./media/signin-realm-discovery/incorrect-password.png)
  
## <a name="additional-info"></a>Další informace

Kromě vylepšené přihlašovací uživatelské prostředí tato změna zahrnuje mechanismy, které může pomoci zmírnit zneužití výčet uživatelské jméno ve velkém měřítku.

Tato změna je původně určené pro spravované domény a začne zavádět. května 2019, ale nespustí, zavádí do federované domény do konce roku 2019. Přesná data zavedení u federovaných domén závisí na zpětné vazby od zákazníků.

> [!IMPORTANT]
> Tato funkce může mít významný dopad na federované domény spoléhat na úroveň starého domény vyhledávání domovské sféry přinutit federace. Některé organizace mají trénuje jejich zaměstnanci mohli přihlásit pomocí uživatelského jména, která neexistuje v Azure Active Directory, ale obsahuje správného názvu domény, protože názvy domén nyní směruje uživatele na koncový bod domény organizace. Nové chování přihlášení to neumožňuje. Uživatel je upozorněn opravte uživatelské jméno a nejsou povoleny se přihlásit pomocí uživatelského jména, která neexistuje v Azure Active Directory.
>
> Pokud vy nebo vaše organizace postupy, které jsou závislé na staré chování, je důležité pro správce organizace aktualizovat zaměstnance přihlašování a ověřování dokumentaci a školení uživatelům používat jejich uživatelské jméno Azure Active Directory pro přihlášení.
  
Pokud máte obavy díky novému chování, nechejte prosím vaše příspěvky v **zpětnou vazbu** části tohoto článku.  

## <a name="next-steps"></a>Další postup

[Přizpůsobení přihlášení brandingu](../fundamentals/add-custom-domain.md)