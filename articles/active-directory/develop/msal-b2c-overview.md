---
title: Použití služby MSAL s Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for JavaScript (MSAL.js) umožňuje aplikacím pracovat s Azure AD B2C a získávat tokeny pro volání zabezpečených webových rozhraní API. Tato webová rozhraní API mohou být Microsoft Graph, další rozhraní API společnosti Microsoft, webová rozhraní API od jiných uživatelů nebo vlastní webové rozhraní API.
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
ms.openlocfilehash: dc8a330bc09f37f7941534ed7c17d1ffd14d08c5
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875958"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Práce s Azure Active Directory B2C pomocí knihovny Microsoft Authentication Library pro JavaScript

[Microsoft Authentication Library for JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) umožňuje vývojářům JavaScriptu ověřovat uživatele pomocí sociálních a místních identit pomocí [Azure Active Directory B2C (Azure AD B2C).](https://docs.microsoft.com/azure/active-directory-b2c/) Pomocí Služby Azure AD B2C jako služby správy identit můžete přizpůsobit a řídit způsob, jakým se zákazníci přihlašují, přihlašují a spravují své profily při používání vašich aplikací.

Azure AD B2C také umožňuje značkovat a přizpůsobit uživatelské prostředí vašich aplikací během procesu ověřování, aby bylo možné zákazníkům poskytovat bezproblémové prostředí.

Tento článek ukazuje, jak používat MSAL.js pro práci s Azure AD B2C a shrnuje klíčové body, které byste měli být vědomi. Kompletní diskusi a kurz, naleznete [v dokumentaci Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview).

## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nevytvořili vlastní [tenanta Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), začněte s jeho vytvořením teď (můžete taky použít existujícího klienta Azure AD B2C, pokud už ho máte).

Tato ukázka obsahuje dvě části:

- jak chránit webové rozhraní API.
- jak zaregistrovat jednostránkovou aplikaci k ověření a volání *tohoto* webového rozhraní API.

## <a name="nodejs-web-api"></a>Webové rozhraní Node.js

> [!NOTE]
> V tuto chvíli je msal.js pro uzel stále ve vývoji (viz [plán](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). Do té doby doporučujeme použít [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad), ověřovací knihovny pro Node.js vyvinuté a podporované společností Microsoft.

Následující kroky ukazují, jak **webové rozhraní API** můžete použít Azure AD B2C k ochraně sebe sama a vystavit vybrané obory klientské aplikace.

### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace

Chcete-li chránit vaše webové rozhraní API pomocí Azure AD B2C, musíte ho nejprve zaregistrovat. Podrobné kroky naleznete [v tématu Registrace aplikace.](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications)

### <a name="step-2-download-the-sample-application"></a>Krok 2: Stažení ukázkové aplikace

Stáhněte si ukázku jako soubor zip nebo ji naklonujte z GitHubu:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Krok 3: Konfigurace ověřování

1. Otevřete `config.js` soubor v ukázce.

2. Nakonfigurujte ukázku s pověřeními aplikace, které jste získali dříve při registraci aplikace. Změňte následující řádky kódu nahrazením hodnot názvy vašeho clientID, hostitele, tenantId a název zásady.

```JavaScript
const clientID = "<Application ID for your Node.js Web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Další informace naleznete v této [ukázce rozhraní Node.js B2C web API](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

Následující kroky ukazují, jak **jednostránková aplikace** můžete použít Azure AD B2C k registraci, přihlášení a volání chráněné webové rozhraní API.

### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace

Chcete-li implementovat ověřování, musíte nejprve zaregistrovat aplikaci. Podrobné kroky naleznete [v tématu Registrace aplikace.](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications)

### <a name="step-2-download-the-sample-application"></a>Krok 2: Stažení ukázkové aplikace

Stáhněte si ukázku jako soubor zip nebo ji naklonujte z GitHubu:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Krok 3: Konfigurace ověřování

Konfigurace aplikace je velmi zajímavá dvěma body:

- Konfigurace koncového bodu rozhraní API a exponovaných oborů
- Konfigurace parametrů ověřování a oborů tokenů

1. Otevřete `apiConfig.js` soubor v ukázce.

2. Nakonfigurujte ukázku s parametry, které jste získali dříve při registraci webového rozhraní API. Změňte následující řádky kódu nahrazením hodnot adresou webového rozhraní API a vystavených oborů.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello" 
    };
   ```

3. Otevřete `authConfig.js` soubor v ukázce.

4. Nakonfigurujte ukázku s parametry, které jste získali dříve při registraci jednostránkové aplikace. Změňte následující řádky kódu nahrazením hodnot s vaším ClientId, metadata autority a obory žádosti o tokeny.

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

Další informace naleznete v této [jednostránkové ukázce aplikace JavaScript B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>Další kroky

Další informace:
- [Toky uživatelů](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Vlastní zásady](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Přizpůsobení uživatelského prostředí](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
