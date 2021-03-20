---
title: Vyhledávání uživatelského jména během přihlašování Azure Active Directory | Microsoft Docs
description: Jak zasílání zpráv na obrazovce odráží vyhledávání uživatelského jména během přihlašování v Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: overview
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ec804029919f0b9c4be26b044cd7a3eeddf6690
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547827"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Zjišťování domovské sféry pro Azure Active Directory přihlašovací stránky

Měníme naše chování při přihlašování Azure Active Directory (Azure AD), abychom uvolnili místo pro nové metody ověřování a vylepšili použitelnost. Během přihlašování Azure AD určuje, kde se uživatel musí ověřit. Azure AD umožňuje inteligentní rozhodnutí načtením organizace a uživatelského nastavení pro uživatelské jméno zadané na přihlašovací stránce. Toto je krok do budoucna bez hesla, která umožňuje další přihlašovací údaje, jako je FIDO 2,0.

## <a name="home-realm-discovery-behavior"></a>Chování zjišťování domovské sféry

Historicky se zjišťování domovské sféry řídí doménou, která je poskytována při přihlášení, nebo zásadou zjišťování domovské sféry pro některé starší aplikace. Například v našem chování zjišťování mohl uživatel Azure Active Directory zadat své uživatelské jméno, ale pořád přijde na obrazovku pro shromažďování přihlašovacích údajů organizace. K tomu dojde, když uživatel správně zadá název domény organizace "contoso.com". Toto chování neumožňuje přizpůsobení prostředí pro jednotlivé uživatele.

Pro podporu širší škály přihlašovacích údajů a zvýšení použitelnosti se teď v průběhu procesu přihlašování aktualizuje Azure Active Directory chování vyhledávání uživatelského jména. Nové chování usnadňuje inteligentní rozhodnutí čtením nastavení na úrovni organizace a uživatele na základě uživatelského jména zadaného na přihlašovací stránce. Aby to bylo možné, Azure Active Directory zkontroluje, jestli uživatelské jméno, které je zadáno na přihlašovací stránce, existuje v zadané doméně, nebo přesměruje uživatele na zadání přihlašovacích údajů.

Další výhodou této práce je vylepšené zasílání zpráv o chybách. Tady jsou některé příklady vylepšeného zasílání zpráv o chybách při přihlašování k aplikaci, která podporuje jenom Azure Active Directory uživatele.

- Uživatelské jméno není typu nebo se uživatelské jméno ještě nesynchronizoval do služby Azure AD:
  
    ![uživatelské jméno je netypové nebo se nenašlo.](./media/signin-realm-discovery/typo-username.png)
  
- Název domény je netypového typu:
  
    ![název domény je Netypový nebo se nenašel.](./media/signin-realm-discovery/typo-domain.png)
  
- Uživatel se pokusí přihlásit se známou doménou příjemce:
  
    ![přihlášení se známou doménou příjemce](./media/signin-realm-discovery/consumer-domain.png)
  
- Heslo je nesprávně typové, ale uživatelské jméno je přesné:  
  
    ![heslo je chybně typu s dobrým uživatelským jménem.](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Tato funkce může mít dopad na federované domény spoléhající se na staré zjišťování domovské sféry na úrovni domény, aby vynutila federace. Aktualizace, když se přidá podpora federovaných domén, najdete v tématu [zjištění domovské sféry během přihlašování pro Microsoft 365 služby](https://azure.microsoft.com/updates/signin-hrd/). Mezitím si některé organizace vyškole své zaměstnance, aby se přihlásili pomocí uživatelského jména, které v Azure Active Directory neexistuje, ale obsahuje správný název domény, protože názvy domén směrují uživatele v současnosti do koncového bodu domény organizace. Nové chování přihlášení to nepovoluje. Uživateli se upozorní, že má uživatelské jméno opravit a že se jim nemůžou přihlásit pomocí uživatelského jména, které v Azure Active Directory neexistuje.
>
> Pokud vy nebo vaše organizace máte postupy, které jsou závislé na starém chování, je důležité, aby správci organizace aktualizovali dokumentaci pro přihlášení a ověřování zaměstnanců a mohli se přihlašovat zaměstnancům, aby používali své Azure Active Directory uživatelské jméno.
  
Pokud máte obavy s novým chováním, ponechte své poznámky v tomto článku v části **názory na zpětnou vazbu** .  

## <a name="next-steps"></a>Další kroky

[Přizpůsobení brandingu přihlašování](../fundamentals/add-custom-domain.md)
