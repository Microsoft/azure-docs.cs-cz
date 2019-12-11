---
title: Rozdíly mezi MSAL. js a ADAL. js | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o rozdílech mezi knihovnou Microsoft Authentication Library for JavaScript (MSAL. js) a knihovnou ověřování Azure AD pro JavaScript (ADAL. js) a postupem, jak zvolit, která z nich se má použít.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: d156ccd4b3f81081433962dcd939a91ab1bc7143
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963221"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Rozdíly mezi MSAL JS a ADAL JS

K ověření entit Azure AD a vyžádání tokenů z Azure AD se používají knihovny Microsoft Authentication Library pro JavaScript (MSAL. js) a knihovna ověřování Azure AD pro JavaScript (ADAL. js). Až do té doby se většina vývojářů pracovala se službou Azure AD pro vývojáře (v 1.0) k ověřování identit Azure AD (pracovní a školní účty) tím, že žádá o tokeny pomocí ADAL. Teď můžete pomocí MSAL. js ověřit širší sadu identit Microsoftu (identity Azure AD a účty Microsoft a sociální a místní účty prostřednictvím Azure AD B2C) prostřednictvím platformy Microsoft Identity Platform (v 2.0).

Tento článek popisuje, jak zvolit mezi knihovnou Microsoft Authentication Library pro JavaScript (MSAL. js) a knihovnou ověřování Azure AD pro JavaScript (ADAL. js) a porovnává dvě knihovny.

## <a name="choosing-between-adaljs-and-msaljs"></a>Výběr mezi ADAL. js a MSAL. js

Ve většině případů chcete použít Microsoft Identity Platform a MSAL. js, což je nejnovější generace knihoven ověřování od společnosti Microsoft. Pomocí MSAL. js získáte tokeny pro uživatele, kteří se přihlašují k vaší aplikaci pomocí Azure AD (pracovní a školní účty), účtů Microsoft (osobní) (MSA) nebo Azure AD B2C.

Pokud už jste obeznámeni s koncovým bodem v 1.0 (a ADAL. js), můžete si přečíst, [co se liší od koncového bodu v 2.0?](active-directory-v2-compare.md).

Pokud ale vaše aplikace potřebuje přihlašovat uživatele staršími verzemi [Active Directory Federation Services (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services), je stále nutné použít ADAL. js.

## <a name="key-differences-in-authentication-with-msaljs"></a>Klíčové rozdíly v ověřování pomocí MSAL. js

### <a name="core-api"></a>Základní rozhraní API

* ADAL. js používá [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) jako reprezentace instance připojení aplikace k autorizačnímu serveru nebo zprostředkovateli identity prostřednictvím adresy URL autority. V opačném případě je rozhraní API MSAL. js navrženo kolem klientské aplikace uživatelského agenta (formulář klientské aplikace, ve které se klientský kód spouští v uživatelském agentu, jako je například webový prohlížeč). Poskytuje třídu `UserAgentApplication`, která představuje instanci ověřovacího kontextu aplikace s autorizačním serverem. Další podrobnosti najdete v tématu [inicializace pomocí MSAL. js](msal-js-initializing-client-applications.md).

* V ADAL. js jsou metody získání tokenů přidružené k jedné autoritě nastavené v `AuthenticationContext`. V MSAL. js můžou žádosti o získání tokenu využít jiné hodnoty autority, než jaká je nastavena v `UserAgentApplication`. To umožňuje MSAL. js získat a ukládat tokeny samostatně pro více tenantů a uživatelských účtů ve stejné aplikaci.

* Metoda získání a obnovení tokenů v tichém režimu bez výzvy k zadání uživatelských `acquireToken` jmen v ADAL. js. V MSAL. js je tato metoda pojmenována `acquireTokenSilent`, aby byla výstižnější pro tuto funkci.

### <a name="authority-value-common"></a>Hodnota `common` oprávnění

V 1.0 používá `https://login.microsoftonline.com/common` autorita k tomu, aby se uživatelé mohli přihlašovat pomocí účtu Azure AD (pro libovolnou organizaci).

V v 2.0 se pomocí autority `https://login.microsoftonline.com/common` umožní uživatelům přihlásit se pomocí účtu organizace Azure AD nebo osobního účtu Microsoft (MSA). Pokud chcete omezit přihlášení jenom na účty Azure AD (stejné chování jako u ADAL. js), musíte použít `https://login.microsoftonline.com/organizations`. Podrobnosti najdete v tématu věnovaném možnosti konfigurace `authority` při [inicializaci pomocí MSAL. js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Obory pro získání tokenů
* Rozsah místo parametru prostředku v požadavcích na ověření pro získání tokenů

    protokol v 2.0 místo prostředků v požadavcích používá obory. Jinými slovy, pokud vaše aplikace potřebuje požádat o tokeny s oprávněním pro prostředek, jako je například MS Graph, rozdíl v hodnotách předaných metodám knihovny je následující:

    v 1.0: prostředek = https\://graph.microsoft.com

    v 2.0: Scope = https\://graph.microsoft.com/User.Read

    Pomocí identifikátoru URI rozhraní API můžete v tomto formátu vyžádat obory pro jakékoli rozhraní API: appidURI/Scope. Příklad: https:\//mytenant.onmicrosoft.com/myapi/api.read

    Pouze v případě, že je hodnota rozsahu `user.read` Graph API, mapuje na https:\//graph.microsoft.com/User.Read a lze je použít zaměnitelné.

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

    Při získávání tokenů pro rozhraní API V 1.0 pomocí MSAL. js si můžete vyžádat všechny statické obory registrované v rozhraní API připojením `.default` k identifikátoru URI ID aplikace rozhraní API jako oboru. Například:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Další kroky
Další informace najdete [v tématu porovnání v 1.0 a v 2.0](active-directory-v2-compare.md).
