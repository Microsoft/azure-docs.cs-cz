---
title: Rozdíly mezi MSAL.js a ADAL.js | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o rozdílech mezi knihovnou Microsoft Authentication Library pro JavaScript (MSAL.js) a s knihovnou ověřování Azure AD pro JavaScript (ADAL.js) a postupem, jak zvolit, které chcete použít.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5d91514af9aea3dd7ea13b94681fbb27c53772fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88120894"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Rozdíly mezi MSAL JS a ADAL JS

Pro ověřování entit služby Azure AD a žádosti o tokeny od Azure AD se používá knihovna ověřování Microsoft pro JavaScript (MSAL.js) a knihovnu ověřování Azure AD pro JavaScript (ADAL.js). Až do té doby se většina vývojářů pracovala se službou Azure AD pro vývojáře (v 1.0) k ověřování identit Azure AD (pracovní a školní účty) tím, že žádá o tokeny pomocí ADAL. Teď můžete pomocí MSAL.js ověřit širší sadu identit Microsoftu (identity Azure AD a účty Microsoft a sociální a místní účty prostřednictvím Azure AD B2C) prostřednictvím platformy Microsoft Identity Platform (v 2.0).

Tento článek popisuje, jak zvolit mezi knihovnou Microsoft Authentication Library pro JavaScript (MSAL.js) a knihovnou ověřování Azure AD pro JavaScript (ADAL.js) a porovnává dvě knihovny.

## <a name="choosing-between-adaljs-and-msaljs"></a>Výběr mezi ADAL.js a MSAL.js

Ve většině případů chcete použít platformu Microsoft Identity Platform a MSAL.js, což je nejnovější generace knihoven ověřování od společnosti Microsoft. Pomocí MSAL.js získáte tokeny pro uživatele, kteří se přihlašují ke svojí aplikaci, pomocí Azure AD (pracovní a školní účty), účtů Microsoft (osobní) (MSA) nebo Azure AD B2C.

Pokud už jste obeznámeni s koncovým bodem v 1.0 (a ADAL.js), můžete si přečíst, [co se liší od koncového bodu v 2.0?](../azuread-dev/azure-ad-endpoint-comparison.md).

Je však stále nutné použít ADAL.js, pokud se vaše aplikace musí přihlašovat uživatelům pomocí dřívějších verzí [Active Directory Federation Services (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Klíčové rozdíly v ověřování pomocí MSAL.js

### <a name="core-api"></a>Základní rozhraní API

* ADAL.js používá [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) jako reprezentace instance připojení aplikace k autorizačnímu serveru nebo zprostředkovateli identity prostřednictvím adresy URL autority. V opačném případě je MSAL.js rozhraní API navrženo kolem klientské aplikace uživatelských agentů (formulář aplikace veřejné aplikace, ve které se klientský kód spouští v uživatelském agentu, jako je například webový prohlížeč). Poskytuje `UserAgentApplication` třídu, která představuje instanci ověřovacího kontextu aplikace s autorizačním serverem. Další podrobnosti najdete v tématu [inicializace pomocí MSAL.js](msal-js-initializing-client-applications.md).

* V ADAL.js metody získání tokenů jsou přidruženy k jedné autoritě nastavené v `AuthenticationContext` . V MSAL.js můžou žádosti o získání tokenu brát v úvahu jiné hodnoty autority než nastavení `UserAgentApplication` . To umožňuje MSAL.js získat a ukládat tokeny samostatně pro více klientů a uživatelských účtů ve stejné aplikaci.

* Metoda získání a obnovení tokenů v tichém režimu bez výzvy k zadání uživatelských jmen `acquireToken` v ADAL.js. V MSAL.js je tato metoda pojmenována `acquireTokenSilent` tak, aby byla výstižnější jako tato funkce.

### <a name="authority-value-common"></a>Hodnota autority `common`

V 1.0 se pomocí `https://login.microsoftonline.com/common` autority uživatelům umožní přihlásit pomocí účtu Azure AD (pro libovolnou organizaci).

V 2.0 se pomocí `https://login.microsoftonline.com/common` autority umožní uživatelům přihlásit se pomocí účtu organizace Azure AD nebo osobního účtu Microsoft (MSA). Pokud chcete omezit přihlášení jenom na účty Azure AD (stejné chování jako u ADAL.js), musíte použít `https://login.microsoftonline.com/organizations` . Podrobnosti najdete v tématu věnovaném `authority` konfiguraci při [inicializaci pomocí MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Obory pro získání tokenů
* Rozsah místo parametru prostředku v požadavcích na ověření pro získání tokenů

    protokol v 2.0 místo prostředků v požadavcích používá obory. Jinými slovy, pokud vaše aplikace potřebuje požádat o tokeny s oprávněním pro prostředek, jako je například MS Graph, rozdíl v hodnotách předaných metodám knihovny je následující:

    v 1.0: prostředek = https \: //Graph.Microsoft.com

    v 2.0: Scope = https \: //Graph.Microsoft.com/user.Read

    Pomocí identifikátoru URI rozhraní API můžete v tomto formátu vyžádat obory pro jakékoli rozhraní API: appidURI/Scope, například: https: \/ /mytenant.onmicrosoft.com/MyAPI/API.Read

    Pouze v případě, že je hodnota oboru typu MS Graph API `user.read` mapována na https: \/ /Graph.Microsoft.com/user.Read a lze ji použít zaměnitelné.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Dynamické obory pro přírůstkový souhlas.

    Při sestavování aplikací pomocí verze 1.0 je nutné zaregistrovat úplnou sadu oprávnění (statické obory), které aplikace požaduje, aby uživatel mohl udělit souhlas v době přihlášení. V verze 2.0 můžete použít parametr scope k vyžádání oprávnění v době, kdy chcete. Tyto jsou označovány jako dynamické obory. To umožňuje uživateli poskytnout k oboru přírůstkové vyjádření souhlasu. Takže když na začátku budete chtít, aby se uživatel přihlásil ke svojí aplikaci a nepotřebujete žádný přístup, můžete tak učinit. Pokud budete později potřebovat možnost číst kalendář uživatele, můžete požádat o obor kalendáře v metodách acquireToken a získat souhlas uživatele. Například:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Obory pro rozhraní API V 1.0

    Při získávání tokenů pro rozhraní API V 1.0 pomocí MSAL.js můžete požádat o všechny statické obory registrované v rozhraní API připojením `.default` k identifikátoru URI ID aplikace rozhraní API jako oboru. Například:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Další kroky
Další informace najdete [v tématu porovnání v 1.0 a v 2.0](../azuread-dev/azure-ad-endpoint-comparison.md).