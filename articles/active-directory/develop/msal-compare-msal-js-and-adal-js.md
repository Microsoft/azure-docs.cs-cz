---
title: Rozdíly mezi MSAL.js a ADAL.js | Azure
description: Přečtěte si o rozdílech mezi knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) a Azure AD Authentication Library pro JavaScript (ADAL.js) a jak zvolit, která se použije.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10b5169d3f06e265b3effa3ec18ad8e4f69959d3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121960"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Rozdíly mezi MSAL JS a ADAL JS

Knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) i Azure AD Authentication Library pro JavaScript (ADAL.js) se používají k ověření služby Azure AD entity a požádat o tokeny ze služby Azure AD. Až dosud Většina vývojářů pracovali s Azure AD pro vývojáře (verze 1.0) k ověření identit Azure AD (pracovní a školní účty) pomocí žádosti o tokeny pomocí knihovny ADAL. Nyní využitím MSAL.js, můžete ověřovat pestřejší škálu identit společnosti Microsoft (identit Azure AD a účty Microsoft a účty sociálních sítí a místní prostřednictvím Azure AD B2C) prostřednictvím Microsoft identity platform (verze 2.0).

Tento článek popisuje, jak si vybrat mezi knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) a Azure AD Authentication Library pro JavaScript (ADAL.js) a porovná dvě knihovny.

## <a name="choosing-between-adaljs-and-msaljs"></a>Volba mezi ADAL.js a MSAL.js

Ve většině případů, které chcete používat Microsoft identity platform a MSAL.js, což je nejnovější generace knihovny Microsoft authentication Library. Využitím MSAL.js získat tokeny uživatele přihlášení do vaší aplikace s Azure AD (pracovní a školní účty), účty Microsoft (osobní) (MSA) nebo Azure AD B2C.

Pokud jste již obeznámeni s koncovým bodem verze 1.0 (a ADAL.js), můžete chtít číst [Čím se liší koncovým bodem v2.0?](active-directory-v2-compare.md).

Však budete stále muset použít ADAL.js Pokud vaše aplikace potřebuje k přihlášení uživatelů s předchozími verzemi [Active Directory Federation Services (ADFS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Hlavní rozdíly v ověřování pomocí MSAL.js

### <a name="core-api"></a>Základní rozhraní API

* Používá ADAL.js [kontextu AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) jako reprezentace instance připojení vaší aplikace, které se autorizační server nebo zprostředkovatele identity prostřednictvím adresy URL autority. Naopak MSAL.js API navržené s ohledem na uživatelské agenta klientská aplikace (formulář veřejné klientské aplikace 00Z klientský kód spouští v uživatelský agent jako je webový prohlížeč). Poskytuje `UserAgentApplication` pro reprezentaci instance kontext ověřování vaší aplikace pomocí autorizačního serveru. Další podrobnosti najdete v tématu [inicializovat využitím MSAL.js](msal-js-initializing-client-applications.md).

* V ADAL.js, metody k získání tokenů jsou přidruženy k jedné autoritu nastavenou `AuthenticationContext`. Získání tokenu žádosti MSAL.js, může trvat autority jiné hodnoty než hodnota nastavená `UserAgentApplication`. To umožňuje MSAL.js a získat tokeny mezipaměti odděleně pro více tenantů a uživatelské účty ve stejné aplikaci.

* Název metody, které chcete získat a obnovit tokeny tiše bez vyzvání uživatele `acquireToken` v ADAL.js. V MSAL.js, je tato metoda s názvem `acquireTokenSilent` být konkrétnější této funkce.

### <a name="authority-value-common"></a>Hodnota `common`

V v1.0 pomocí `https://login.microsoftonline.com/common` autority vám umožní uživatelům přihlašovat se pomocí libovolného účtu služby Azure AD (pro každou organizaci).

V v2.0 pomocí `https://login.microsoftonline.com/common` autority, vám umožní uživatelům přihlašovat se pomocí libovolného účtu organizace služby Azure AD nebo osobní účet Microsoft (MSA). Pokud chcete omezit přihlašování pouze účty Azure AD (stejně jako s ADAL.js), budete muset použít `https://login.microsoftonline.com/organizations`. Podrobnosti najdete v tématu `authority` možnost konfigurace v [inicializovat využitím MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Obory pro získávání tokenů
* Obor místo zdroje parametru v žádosti o ověření k získání tokenů

    protokol v2.0 používá obory místo prostředek v žádosti. Jinými slovy když vaše aplikace potřebuje požádat o tokeny s oprávněními pro určitý prostředek, jako je například MS Graphu, rozdíl v hodnoty předané do metod knihovny vypadá takto:

    verze 1.0: prostředku =https://graph.microsoft.com

    verze 2.0: rozsah = https://graph.microsoft.com/User.Read

    Můžete požádat o rozsahy pro libovolné prostředky rozhraní API pomocí identifikátoru URI rozhraní API v tomto formátu: appidURI nebo oboru, například: https:\//mytenant.onmicrosoft.com/myapi/api.read

    Pouze pro Graph API MS, hodnota rozsahu `user.read` mapuje https://graph.microsoft.com/User.Read a zaměnitelné.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Dynamické obory pro přírůstkové souhlas.

    Při sestavování aplikací s použitím verze 1.0, jste museli zaregistrovat úplnou sadu oprávnění (obory statické) vyžadovaného aplikací pro uživatele souhlas v době přihlášení. V verze 2.0 můžete použít parametr oboru v době chcete požádat o oprávnění. Nazývají se dynamické obory. To umožňuje uživateli zadat přírůstkové vyjadřujete souhlas s obory. Takže pokud na začátku je vhodné uživatele k přihlášení do vaší aplikace a nepotřebujete jiný druh přístupu, lze provést. Pokud později potřebujete oprávnění ke čtení kalendáře uživatele, můžete požádat o oboru kalendáře v metodách acquireToken a vyžadovat souhlas uživatele. Příklad:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Obory pro rozhraní API V1.0

    Při získávání tokenů pro V1.0 API s využitím MSAL.js, můžete požádat o všechny statické obory registrován na rozhraní API přidáním `.default` na identifikátor URI ID aplikace rozhraní API jako obor. Příklad:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Další postup
Další informace najdete v [porovnání v1.0 a v2.0](active-directory-v2-compare.md).
