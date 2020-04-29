---
title: Použití MSAL s Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: Knihovna Microsoft Authentication Library pro JavaScript (MSAL. js) umožňuje aplikacím pracovat s Azure AD B2C a získat tokeny pro volání zabezpečených webových rozhraní API. Tato webová rozhraní API můžou být Microsoft Graph, jiná rozhraní API Microsoftu, webová rozhraní API od ostatních nebo vaše vlastní webové rozhraní API.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8e076dfd6670265d458eb35d8e1b3e4500009a12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534478"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Použití knihovny Microsoft Authentication Library pro jazyk JavaScript pro práci s Azure Active Directory B2C

[Knihovna Microsoft Authentication Library pro JavaScript (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) umožňuje vývojářům JavaScriptu ověřovat uživatele pomocí sociálních a místních identit pomocí [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Pomocí Azure AD B2C jako služby správy identit můžete přizpůsobit a řídit, jak se zákazníci při používání vašich aplikací přihlásí, přihlásí a budou spravovat jejich profily.

Azure AD B2C také vám umožní přizpůsobit uživatelské rozhraní svých aplikací během procesu ověřování za účelem zajištění bezproblémového prostředí pro vaše zákazníky.

Tento článek ukazuje, jak pomocí MSAL. js pracovat s Azure AD B2C a shrnuje klíčové body, o kterých byste měli vědět. Kompletní diskuzi a kurz najdete v [dokumentaci Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview).

## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nevytvořili vlastního [klienta Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), začněte tím, že ho vytvoříte hned (můžete také použít stávajícího klienta Azure AD B2C, pokud ho už máte).

Tato ukázka obsahuje dvě části:

- Jak chránit webové rozhraní API.
- Jak zaregistrovat jednostránkovou aplikaci pro ověřování *a volání webového* rozhraní API

## <a name="nodejs-web-api"></a>Webové rozhraní API Node.js

> [!NOTE]
> V tomto okamžiku je MSAL. js pro uzel stále ve vývoji (viz [plán](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). Mezitím doporučujeme používat [Passport-Azure-AD](https://github.com/AzureAD/passport-azure-ad), což je knihovna ověřování pro Node. js vyvinutá a podporovaná Microsoftem.

Následující kroky ukazují, jak může **webové rozhraní API** použít Azure AD B2C k ochraně sebe sama a vystavit vybrané obory klientské aplikaci.

### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace

Pokud chcete své webové rozhraní API chránit pomocí Azure AD B2C, musíte ho nejdřív zaregistrovat. Podrobný postup najdete v tématu věnovaném [registraci aplikace](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) .

### <a name="step-2-download-the-sample-application"></a>Krok 2: stažení ukázkové aplikace

Stáhněte si ukázku jako soubor ZIP nebo ho naklonujte z GitHubu:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Krok 3: Konfigurace ověřování

1. Otevřete `config.js` soubor v ukázce.

2. Nakonfigurujte ukázku pomocí přihlašovacích údajů aplikace, které jste získali dříve při registraci aplikace. Změňte následující řádky kódu nahrazením hodnot názvy clientID, Host, tenantId a název zásady.

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Další informace najdete v [ukázce tohoto ukázkového webového rozhraní API pro Node. js B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

Následující kroky ukazují, jak může **aplikace s jednou stránkou** používat Azure AD B2C k registraci, přihlášení a volání chráněného webového rozhraní API.

### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace

Chcete-li implementovat ověřování, musíte nejprve zaregistrovat aplikaci. Podrobný postup najdete v tématu věnovaném [registraci aplikace](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) .

### <a name="step-2-download-the-sample-application"></a>Krok 2: stažení ukázkové aplikace

Stáhněte si ukázku jako soubor ZIP nebo ho naklonujte z GitHubu:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Krok 3: Konfigurace ověřování

Při konfiguraci aplikace je potřeba mít dva důležité skutečnosti:

- Konfigurace koncového bodu rozhraní API a vystavených oborů
- Konfigurace parametrů ověřování a oborů tokenů

1. Otevřete `apiConfig.js` soubor v ukázce.

2. Nakonfigurujte ukázku pomocí parametrů, které jste získali dříve během registrace webového rozhraní API. Změňte následující řádky kódu nahrazením hodnot adresou vašeho webového rozhraní API a vystavenými obory.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. Otevřete `authConfig.js` soubor v ukázce.

4. Nakonfigurujte ukázku pomocí parametrů, které jste získali dříve při registraci jednostránkové aplikace. Změňte následující řádky kódu nahrazením hodnot hodnotami ID ClientId, autority a žádostí o tokeny.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Další informace najdete v tomto [ukázkovém B2C aplikaci pro JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>Další kroky

Další informace:
- [Toky uživatelů](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Vlastní zásady](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Přizpůsobení uživatelského prostředí](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
