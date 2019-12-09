---
title: Použití MSAL s Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: Knihovna Microsoft Authentication Library (MSAL) umožňuje aplikacím spolupracovat s Azure AD B2C a získávat tokeny pro volání zabezpečených webových rozhraní API. Tato webová rozhraní API můžou být Microsoft Graph, jiná rozhraní API Microsoftu, webová rozhraní API od ostatních nebo vaše vlastní webové rozhraní API.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: f080c14cd0aa20bd312b4be8d9eacd8d901b7cef
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917025"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Spolupráce s Azure Active Directory B2C pomocí knihovny Microsoft Authentication Library

Knihovna Microsoft Authentication Library (MSAL) umožňuje vývojářům aplikací ověřovat uživatele pomocí sociálních a místních identit pomocí [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C je služba správy identit. Díky tomu můžete přizpůsobit a řídit, jak se zákazníci při používání vašich aplikací přihlásí, přihlásí a budou spravovat jejich profily.

Azure AD B2C také umožňují snadnou značku a přizpůsobit uživatelské rozhraní svých aplikací, aby poskytovaly zákazníkům bezproblémové prostředí.

V tomto kurzu se dozvíte, jak pomocí MSAL spolupracovat s Azure AD B2C.

## <a name="prerequisites"></a>Předpoklady

Pokud jste ještě nevytvořili vlastního [tenanta Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), vytvořte ho hned teď. Můžete také použít stávajícího klienta Azure AD B2C.

## <a name="javascript"></a>JavaScript

Následující kroky ukazují, jak může aplikace s jednou stránkou používat Azure AD B2C k registraci, přihlášení a volání chráněného webového rozhraní API.

### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace

Chcete-li implementovat ověřování, musíte nejprve zaregistrovat aplikaci. Podrobný postup najdete v tématu věnovaném [registraci aplikace](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) .

### <a name="step-2-download-the-sample-application"></a>Krok 2: stažení ukázkové aplikace

Stáhněte si ukázku jako soubor ZIP nebo ho naklonujte z GitHubu:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Krok 3: Konfigurace ověřování

1. V ukázce otevřete soubor **index. html** .

1. Nakonfigurujte ukázku pomocí ID klienta a klíče, který jste si poznamenali dříve při registraci aplikace. Změňte následující řádky kódu nahrazením hodnot názvy vašich adresářů a rozhraní API:

   ```javascript
   // The current application coordinates were pre-registered in a B2C tenant.

    var appConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };

    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902" //This is your client/application ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    // create UserAgentApplication instance
    const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   ```

Název [toku uživatele](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) v tomto kurzu je **B2C_1_signupsignin1**. Pokud používáte jiný název toku uživatele, nastavte hodnotu **autority** na tento název.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Krok 4: Konfigurace aplikace pro použití `b2clogin.com`

Místo `login.microsoftonline.com` můžete jako adresu URL pro přesměrování použít `b2clogin.com`. Při nastavování poskytovatele identity pro registraci a přihlášení tak uděláte v aplikaci Azure AD B2C.

Použití `b2clogin.com` v kontextu `https://your-tenant-name.b2clogin.com/your-tenant-guid` má následující důsledky:

- Služby společnosti Microsoft spotřebovávají méně místa v hlavičce souboru cookie.
- Vaše adresy URL už neobsahují odkaz na Microsoft. Například vaše aplikace Azure AD B2C pravděpodobně odkazuje na `login.microsoftonline.com`.

 Chcete-li použít `b2clogin.com`, je nutné aktualizovat konfiguraci aplikace.  

- Nastavte vlastnost **validateAuthority** na `false`, aby mohla probíhat přesměrování pomocí `b2clogin.com`.

Následující příklad ukazuje, jak můžete nastavit vlastnost:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> Vaše aplikace Azure AD B2C pravděpodobně odkazuje na `login.microsoftonline.com` na několika místech, jako jsou například odkazy na uživatelský tok a koncové body tokenů. Ujistěte se, že byl koncový bod autorizace, koncový bod tokenu a vystavitele aktualizovaný, aby používal `your-tenant-name.b2clogin.com`.

Podle [této ukázky MSAL JavaScriptu](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) použijte MSAL ve verzi Preview pro JavaScript (MSAL. js). Ukázka získá přístupový token a zavolá rozhraní API zabezpečené Azure AD B2C.

## <a name="next-steps"></a>Další kroky

Další informace:

- [Vlastní zásady](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Přizpůsobení uživatelského rozhraní](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)