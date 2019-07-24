---
title: Přečtěte si, jak můžou aplikace spolupracovat s Azure AD B2C pomocí knihovny Microsoft Authentication Library.
description: Knihovna Microsoft Authentication Library (MSAL) umožňuje aplikacím spolupracovat s Azure AD B2C a získávat tokeny pro volání zabezpečených webových rozhraní API. Tato webová rozhraní API můžou být Microsoft Graph, jiná rozhraní API Microsoftu, webová rozhraní API od ostatních nebo vaše vlastní webové rozhraní API.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: db05f59faf945e425761fe7a20bad3e263246a39
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849344"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Spolupráce s Azure Active Directory B2C pomocí knihovny Microsoft Authentication Library

Knihovna Microsoft Authentication Library (MSAL) umožňuje vývojářům aplikací ověřovat uživatele pomocí sociálních a místních identit pomocí [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C je služba správy identit. Díky tomu můžete přizpůsobit a řídit, jak se zákazníci při používání vašich aplikací přihlásí, přihlásí a budou spravovat jejich profily.

Azure AD B2C také umožňují snadnou značku a přizpůsobit uživatelské rozhraní svých aplikací, aby poskytovaly zákazníkům bezproblémové prostředí.

V tomto kurzu se dozvíte, jak pomocí MSAL spolupracovat s Azure AD B2C.

## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nevytvořili vlastního [tenanta Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), vytvořte ho hned teď. Můžete také použít stávajícího klienta Azure AD B2C.

## <a name="javascript"></a>JavaScript

Následující kroky ukazují, jak může aplikace s jednou stránkou používat Azure AD B2C k registraci, přihlášení a volání chráněného webového rozhraní API.

### <a name="step-1-register-your-application"></a>Krok 1: Registrace vaší aplikace

Chcete-li implementovat ověřování, musíte nejprve zaregistrovat aplikaci. Podrobný postup najdete v tématu věnovaném [registraci aplikace](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) .

### <a name="step-2-download-the-sample-application"></a>Krok 2: Stažení ukázkové aplikace

Stáhněte si ukázku jako soubor ZIP nebo ho naklonujte z GitHubu:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Krok 3: Konfigurace ověřování

1. V ukázce otevřete soubor **index. html** .

1. Nakonfigurujte ukázku pomocí ID aplikace a klíče, který jste si poznamenali dříve při registraci aplikace. Změňte následující řádky kódu nahrazením hodnot názvy vašich adresářů a rozhraní API:

   ```javascript
   // The current application coordinates were pre-registered in a B2C directory.

   const msalConfig = {
       auth:{
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
           b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
           webApi: 'http://localhost:5000/hello',
     };

   // create UserAgentApplication instance
   const myMSALObj = new UserAgentApplication(msalConfig);
   ```

Název [toku uživatele](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) v tomto kurzu je **B2C_1_signupsignin1**. Pokud používáte jiný název toku uživatele, nastavte hodnotu autority na  tento název.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Krok 4: Konfigurace aplikace pro použití`b2clogin.com`

Místo adresy URL pro přesměrování můžete použít `b2clogin.com`. `login.microsoftonline.com` Při nastavování poskytovatele identity pro registraci a přihlášení tak uděláte v aplikaci Azure AD B2C.

`b2clogin.com` Použití v`https://your-tenant-name.b2clogin.com/your-tenant-guid` kontextu má následující důsledky:

- Služby společnosti Microsoft spotřebovávají méně místa v hlavičce souboru cookie.
- Vaše adresy URL už neobsahují odkaz na Microsoft. Například vaše aplikace Azure AD B2C pravděpodobně odkazuje na `login.microsoftonline.com`.

 Chcete- `b2clogin.com`li použít, je třeba aktualizovat konfiguraci aplikace.  

- Nastavte vlastnost **validateAuthority** na `false`, aby mohlo dojít k přesměrování pomocí `b2clogin.com` .

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
> Vaše aplikace Azure AD B2C pravděpodobně odkazuje na `login.microsoftonline.com` několik míst, jako jsou například odkazy na tok uživatele a koncové body tokenu. Ujistěte se, že jste aktualizovali koncový bod autorizace, koncový bod tokenu a vystavitele pro použití `your-tenant-name.b2clogin.com`.

Podle [této ukázky MSAL JavaScriptu](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) použijte MSAL ve verzi Preview pro JavaScript (MSAL. js). Ukázka získá přístupový token a zavolá rozhraní API zabezpečené Azure AD B2C.

## <a name="next-steps"></a>Další postup

Další informace pro:

- [Vlastní zásady](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Přizpůsobení uživatelského rozhraní](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)