---
title: Vyhledávání uživatelského jména během přihlášení – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Jak zasílání zpráv na obrazovce odráží vyhledávání uživatelského jména během přihlašování ve službě Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024256"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Zjišťování domovské sféry pro přihlašovací stránky služby Azure Active Directory

Měníme naše chování přihlašování služby Azure Active Directory (Azure AD), abychom uvolnili místo pro nové metody ověřování a zlepšili použitelnost. Během přihlašování Azure AD určuje, kde uživatel potřebuje k ověření. Azure AD dělá inteligentní rozhodnutí čtením organizace a uživatelské nastavení pro uživatelské jméno zadané na přihlašovací stránce. Jedná se o krok směrem k budoucnosti bez hesla, která umožňuje další pověření, jako je FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Chování zjišťování domovské sféry

V minulosti se zjišťování domovské sféry řídilo doménou, která je k dispozici při přihlášení nebo zásadami zjišťování domovské sféry pro některé starší aplikace. Například v našem chování zjišťování může uživatel služby Azure Active Directory zadat své uživatelské jméno, ale přesto by se dostal na obrazovku shromažďování přihlašovacích údajů své organizace. K tomu dochází, když uživatel správně zadává název domény organizace "contoso.com". Toto chování neumožňuje rozlišovací schopnost přizpůsobit prostředí pro jednotlivé uživatele.

Pro podporu širší škály přihlašovacích údajů a zvýšení použitelnosti, Azure Active Directory uživatelské jméno vyhledávání chování během procesu přihlášení je nyní aktualizován. Nové chování provádí inteligentní rozhodnutí čtením nastavení na úrovni klienta a uživatele na základě uživatelského jména zadaného na přihlašovací stránce. Aby to bylo možné, služba Azure Active Directory zkontroluje, zda uživatelské jméno zadané na přihlašovací stránce existuje v zadané doméně, nebo přesměruje uživatele na zadání pověření.

Další výhodou této práce je lepší zasílání chybových zpráv. Tady je několik příkladů vylepšeného zasílání chyb při přihlašování k aplikaci, která podporuje jenom uživatele služby Azure Active Directory.

- Uživatelské jméno je chybně zadané nebo uživatelské jméno ještě nebylo synchronizováno se službou Azure AD:
  
    ![uživatelské jméno je chybně zadáno nebo nebylo nalezeno](./media/signin-realm-discovery/typo-username.png)
  
- Název domény je chybně zadán:
  
    ![název domény je chybně zadán nebo nebyl nalezen](./media/signin-realm-discovery/typo-domain.png)
  
- Uživatel se pokusí přihlásit pomocí známé domény příjemce:
  
    ![přihlášení se známou doménou příjemce](./media/signin-realm-discovery/consumer-domain.png)
  
- Heslo je chybně zadáno, ale uživatelské jméno je přesné:  
  
    ![heslo je chybně zadáno s dobrým uživatelským jménem](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Tato funkce může mít vliv na federované domény spoléhající na staré zjišťování domovské sféry na úrovni domény, které vynucuje federaci. Aktuální informace o tom, kdy bude přidána podpora federované domény, najdete [v tématu zjišťování domovské sféry během přihlašování ke službám Microsoft 365](https://azure.microsoft.com/updates/signin-hrd/). Do té doby některé organizace vyškolili své zaměstnance k přihlášení pomocí uživatelského jména, které ve službě Azure Active Directory neexistuje, ale obsahuje správný název domény, protože názvy domén směrují uživatele aktuálně do koncového bodu domény jejich organizace. Nové chování přihlášení to neumožňuje. Uživatel je upozorněn na opravu uživatelského jména a není povoleno přihlásit se pomocí uživatelského jména, které ve službě Azure Active Directory neexistuje.
>
> Pokud vy nebo vaše organizace máte postupy, které závisí na starém chování, je důležité, aby správci organizace aktualizovali dokumentaci k přihlášení a ověřování zaměstnanců a školili zaměstnance tak, aby k přihlášení používali své uživatelské jméno služby Azure Active Directory.
  
Pokud máte obavy z nového chování, zanechte své poznámky v části **Zpětná vazba** tohoto článku.  

## <a name="next-steps"></a>Další kroky

[Přizpůsobení přihlašovací značky](../fundamentals/add-custom-domain.md)
