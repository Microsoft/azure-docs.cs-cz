---
title: Rozdíly mezi msal.js a ADAL.js | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o rozdílech mezi Knihovnou ověřování Microsoftu pro JavaScript (MSAL.js) a Azure AD Authentication Library pro JavaScript (ADAL.js) a jak zvolit, které chcete použít.
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
ms.openlocfilehash: 7238a78279528b4522d09178d00bf916f14bad88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696414"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Rozdíly mezi MSAL JS a ADAL JS

Knihovna Ověřování Microsoftu pro JavaScript (MSAL.js) a Azure AD Authentication Library pro JavaScript (ADAL.js) se používají k ověřování entit Azure AD a vyžádání tokenů z Azure AD. Až dosud většina vývojářů spolupracovala s Azure AD pro vývojáře (v1.0) k ověření identit Azure AD (pracovní a školní účty) vyžádáním tokenů pomocí ADAL. Teď pomocí MSAL.js můžete ověřit širší sadu identit Microsoftu (identity Azure AD a účty Microsoft a sociální a místní účty prostřednictvím Azure AD B2C) prostřednictvím platformy microsoft identit (v2.0).

Tento článek popisuje, jak si vybrat mezi Microsoft Authentication Library pro JavaScript (MSAL.js) a Azure AD Authentication Library pro JavaScript (ADAL.js) a porovnává dvě knihovny.

## <a name="choosing-between-adaljs-and-msaljs"></a>Výběr mezi ADAL.js a MSAL.js

Ve většině případů chcete použít platformu identit společnosti Microsoft a soubor MSAL.js, což je nejnovější generace ověřovacích knihoven společnosti Microsoft. Pomocí msal.js získáte tokeny pro uživatele, kteří se přihlašují k vaší aplikaci pomocí Azure AD (pracovní a školní účty), účty Microsoft (osobní) (MSA) nebo Azure AD B2C.

Pokud jste již obeznámeni s koncovým bodem v1.0 (a ADAL.js), možná budete chtít přečíst [Co se liší o koncovém bodu v2.0?](active-directory-v2-compare.md).

ADAL.js je však stále nutné použít, pokud aplikace potřebuje přihlásit uživatele pomocí starších verzí [služby ADFS (ADFS).](/windows-server/identity/active-directory-federation-services)

## <a name="key-differences-in-authentication-with-msaljs"></a>Klíčové rozdíly v ověřování pomocí souboru MSAL.js

### <a name="core-api"></a>Základní rozhraní API

* Soubor ADAL.js používá [nástroj AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) jako reprezentaci instance připojení aplikace k autorizačnímu serveru nebo zprostředkovateli identity prostřednictvím adresy URL autority. Naopak Rozhraní MSAL.js API je navrženo kolem klientské aplikace uživatelského agenta (forma veřejné klientské aplikace, ve které je klientský kód spuštěn v uživatelském agentovi, jako je webový prohlížeč). Poskytuje třídu `UserAgentApplication` představující instanci kontextu ověřování aplikace s autorizačním serverem. Další podrobnosti naleznete v [tématu Inicializovat pomocí souboru MSAL.js](msal-js-initializing-client-applications.md).

* V souboru ADAL.js jsou metody získání tokenů přidruženy `AuthenticationContext`k jedinéautoritě nastavené v souboru . V MSAL.js požadavky na získání tokenu může trvat různé `UserAgentApplication`hodnoty autority, než co je nastaveno v . To umožňuje MSAL.js získat a mezipaměti tokeny samostatně pro více klientů a uživatelských účtů ve stejné aplikaci.

* Metoda pro získání a obnovení tokenů tiše bez `acquireToken` zobrazení výzvy uživatelům je pojmenována v souboru ADAL.js. V msal.js je tato `acquireTokenSilent` metoda pojmenována jako popisnější této funkce.

### <a name="authority-value-common"></a>Hodnota autority`common`

V v1.0 pomocí `https://login.microsoftonline.com/common` oprávnění umožní uživatelům přihlásit se pomocí libovolného účtu Azure AD (pro všechny organizace).

V v2.0, `https://login.microsoftonline.com/common` pomocí oprávnění, umožní uživatelům přihlásit se pomocí libovolného účtu organizace Azure AD nebo osobní účet Microsoft (MSA). Chcete-li omezit přihlášení pouze na účty Azure AD (stejné chování jako `https://login.microsoftonline.com/organizations`u adal.js), musíte použít . Podrobnosti naleznete `authority` v možnosti konfigurace v [inicializaci pomocí souboru MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Obory pro získání tokenů
* Obor namísto parametru prostředku v požadavcích na ověřování pro získání tokenů

    V2.0 protokol používá obory namísto prostředku v požadavcích. Jinými slovy, když vaše aplikace potřebuje požadovat tokeny s oprávněními pro prostředek, jako je Například MS Graph, rozdíl v hodnotách předaných metodám knihovny je následující:

    v1.0: zdroj\:= https //graph.microsoft.com

    v2.0: rozsah\:= https //graph.microsoft.com/User.Read

    Můžete požádat o obory pro libovolné rozhraní API prostředků pomocí identifikátoru URI rozhraní API\/v tomto formátu: appidURI/scope Například: https: /mytenant.onmicrosoft.com/myapi/api.read

    Pouze pro Rozhraní API MS `user.read` Graph, hodnota\/oboru se mapuje na https: /graph.microsoft.com/User.Read a lze je zaměnit.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Dynamické obory pro přírůstkový souhlas.

    Při vytváření aplikací pomocí v1.0, je třeba zaregistrovat úplnou sadu oprávnění (statické obory) vyžadované aplikací pro uživatele souhlas v době přihlášení. Ve v2.0 můžete použít parametr oboru k vyžádání oprávnění v době, kdy je chcete. Tyto obory se nazývají dynamické obory. To umožňuje uživateli poskytnout přírůstkový souhlas s obory. Takže pokud na začátku chcete, aby se uživatel přihlásil k vaší aplikaci a nepotřebujete žádný přístup, můžete tak učinit. Pokud později budete potřebovat možnost číst kalendář uživatele, pak můžete požádat o rozsah kalendáře v acquireToken metody a získat souhlas uživatele. Například:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Obory pro v1.0 API

    Při získávání tokenů pro rozhraní API V1.0 pomocí souboru MSAL.js můžete požádat `.default` o všechny statické obory registrované v rozhraní API připojením k identifikátoru URI ID aplikace rozhraní API jako oboru. Například:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Další kroky
Další informace naleznete [v porovnání v1.0 a v2.0](active-directory-v2-compare.md).
