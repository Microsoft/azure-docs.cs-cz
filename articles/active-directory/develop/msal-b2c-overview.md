---
title: Použití služby MSAL s Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) umožňuje aplikacím spolupracovat s Azure AD B2C a získat tokeny pro volání zabezpečených webových rozhraní API. Tato webová rozhraní API mohou být Microsoft Graph, další rozhraní API společnosti Microsoft, webová rozhraní API od jiných uživatelů nebo vlastní webové rozhraní API.
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
ms.openlocfilehash: e25564e64410701754390024a5bcfd39321343e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696448"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Použití knihovny ověřování Microsoftu ke slučovací mu s Azure Active Directory B2C

Microsoft Authentication Library (MSAL) umožňuje vývojářům aplikací ověřovat uživatele pomocí sociálních a místních identit pomocí [Azure Active Directory B2C (Azure AD B2C).](https://docs.microsoft.com/azure/active-directory-b2c/) Azure AD B2C je služba správy identit. Pomocí něj můžete přizpůsobit a řídit způsob, jakým se zákazníci přihlašují, přihlašují a spravují své profily při používání vašich aplikací.

Azure AD B2C také umožňuje značky a přizpůsobit uživatelské prostředí vašich aplikací poskytovat bezproblémové prostředí pro vaše zákazníky.

Tento kurz ukazuje, jak používat MSAL pro propojení s Azure AD B2C.

## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nevytvořili vlastního [klienta Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), vytvořte ho teď. Můžete také použít existující klienta Azure AD B2C.

## <a name="javascript"></a>JavaScript

Následující kroky ukazují, jak jednostránková aplikace můžete použít Azure AD B2C k registraci, přihlášení a volání chráněné webové rozhraní API.

### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace

Chcete-li implementovat ověřování, musíte nejprve zaregistrovat aplikaci. Podrobné kroky naleznete [v tématu Registrace aplikace.](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c)

### <a name="step-2-download-the-sample-application"></a>Krok 2: Stažení ukázkové aplikace

Stáhněte si ukázku jako soubor zip nebo ji naklonujte z GitHubu:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Krok 3: Konfigurace ověřování

1. Otevřete soubor **index.html** v ukázce.

1. Nakonfigurujte ukázku s ID klienta a klíčem, které jste zaznamenali dříve při registraci aplikace. Změňte následující řádky kódu nahrazením hodnot názvy adresáře a rozhraní API:

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

Název [toku uživatele](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) v tomto kurzu je **B2C_1_signupsignin1**. Pokud používáte jiný název toku uživatele, nastavte **hodnotu autority** na tento název.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Krok 4: Konfigurace aplikace pro použití`b2clogin.com`

Místo adresy `b2clogin.com` `login.microsoftonline.com` URL přesměrování můžete použít. Uděláte to v aplikaci Azure AD B2C při nastavení zprostředkovatele identity pro registraci a přihlášení.

Použití `b2clogin.com` v souvislosti `https://your-tenant-name.b2clogin.com/your-tenant-guid` s má následující účinky:

- Služby společnosti Microsoft spotřebovávají méně místa v záhlaví souborů cookie.
- Adresy URL již neobsahují odkaz na společnost Microsoft. Například vaše aplikace Azure AD B2C `login.microsoftonline.com`pravděpodobně odkazuje na .

 Chcete-li použít `b2clogin.com`, je třeba aktualizovat konfiguraci aplikace.  

- Nastavte **vlastnost validateAuthority** na , aby mohlo dojít k `false`přesměrováním pomocí. `b2clogin.com`

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
> Vaše aplikace Azure AD B2C `login.microsoftonline.com` pravděpodobně odkazuje na několika místech, jako jsou odkazy na tok uživatele a koncové body tokenu. Ujistěte se, že koncový bod autorizace, koncový bod `your-tenant-name.b2clogin.com`tokenu a vystavittel byly aktualizovány na použití .

Postupujte podle [tohoto vzorku MSAL JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) o tom, jak používat MSAL Náhled pro JavaScript (MSAL.js). Ukázka získá přístupový token a volá rozhraní API zabezpečené Azure AD B2C.

## <a name="next-steps"></a>Další kroky

Další informace:

- [Vlastní zásady](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Přizpůsobení uživatelského rozhraní](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)