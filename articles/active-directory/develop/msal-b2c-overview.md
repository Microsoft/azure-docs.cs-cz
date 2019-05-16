---
title: Naučte se integrovat s Azure AD B2C pomocí Microsoft Authentication Library (MSAL)
description: Microsoft Authentication Library (MSAL) vývojářům aplikací umožňuje integraci s Azure AD B2C a získat tokeny pro volání zabezpečená webová rozhraní API. Tato webová rozhraní API může být Microsoft Graphu, jiné APIS Microsoftu, třetích stran webová rozhraní API nebo svým vlastním rozhraním Web API.
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
ms.openlocfilehash: 6af78a593fb8fadb836d2dc4c02115d95a7f2712
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546036"
---
# <a name="integrate-microsoft-authentication-library-msal-with-azure-active-directory-b2c"></a>Integrace Microsoft Authentication Library (MSAL) s Azure Active Directory B2C

Microsoft Authentication Library (MSAL) vývojářům aplikací umožňuje ověřování uživatelů pomocí sociálních sítí a místních identit pomocí [Azure Active Directory (Azure AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/). Azure Active Directory (Azure AD) B2C je služba pro správu identit, která umožňuje přizpůsobení a řízení způsobu, jakým se zákazníci registrují, přihlašují a jakým při používání vašich aplikací spravují své profily.

Azure Active Directory (Azure AD) B2C můžete také vytvoření a přizpůsobení uživatelského rozhraní (UI) aplikace zajistit bezproblémové prostředí pro vaše zákazníky.

Tento kurz ukazuje, jak pomocí Microsoft Authentication Library (MSAL) můžete integrovat s Azure Active Directory (Azure AD) B2C.


## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nevytvořili vlastní [Tenanta Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), založte si ho teď. Můžete používat existujícího tenanta Azure AD B2C. 

## <a name="javascript"></a>JavaScript

Následující postup ukazuje, jak může jednostránková aplikace pomocí Azure AD B2C registrovat a přihlášení uživatele a volat chráněné webové rozhraní API.

### <a name="step-1-register-your-application"></a>Krok 1: Zaregistrujte svoji aplikaci.

Chcete-li implementovat ověřování, je třeba nejprve registrace vaší aplikace. Chcete-li zaregistrovat aplikaci, postupujte podle [registrace vaší aplikace](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) podrobné pokyny.

### <a name="steps-2-download-applications"></a>Kroky 2: Stažení aplikací

Stáhněte soubor .ZIP nebo naklonujte ukázku z Githubu.
>klon gitu https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git

### <a name="steps-3-authentication"></a>Kroky 3: Authentication

1. Otevřete soubor index.html ve vzorku.

2. Nakonfigurujte ukázku s ID aplikace a klíč, který jste si poznamenali dříve při registraci vaší aplikace. Změňte následující řádky kódu tak, že nahradíte hodnoty s názvy adresáře a rozhraní API:

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

Název [tok uživatele](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) použitá v tomto kurzu je B2C_1_signupsignin1. Pokud používáte jiné uživatelské jméno toku, použijte název vašeho toku uživatele v hodnotě autoritu.


### <a name="configure-application-to-use-b2clogincom"></a>Konfigurace aplikace pro použití `b2clogin.com`

Můžete použít `b2clogin.com` místo `login.microsoftonline.com` jako přesměrování adresu url, když nastavíte zprostředkovatele identity pro registraci a přihlašování v aplikaci Azure Active Directory (Azure AD) B2C.

**`b2clogin.com`** tj 
`https://your-tenant-name.b2clogin.com/your-tenant-guid` se používá pro následující:

- Nezabírá místo v záhlaví souboru cookie používané služby Microsoftu.
- Vaší adresy URL už nebude obsahovat odkaz na Microsoft. Například aplikace Azure AD B2C pravděpodobně odkazuje na login.microsoftonline.com


 Použití "b2clogin.com", budete muset aktualizovat konfiguraci vaší aplikace.  

1. Aktualizace ValidateAuthority: nastavte **validateAuthority** vlastnost `false`. Když **validateAuthority** je nastavena na hodnotu false, přesměrování můžou b2clogin.com.

Následující příklad ukazuje, jak může nastavit vlastnost:
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
> Aplikace Azure AD B2C se pravděpodobně odkazuje na login.microsoftonline.com na několika místech, jako jsou odkazy na tok uživatele a token koncových bodů. Ujistěte se, že koncový bod autorizace, koncový bod tokenu a vystavitele byl aktualizován na použití vašeho tenanta name.b2clogin.com.

Použít tento [MSAL JS ukázka](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) o tom, jak používat Microsoft Authentication Library ve verzi Preview pro jazyk JavaScript (msal.js) k získání přístupového tokenu a volat rozhraní API zabezpečené pomocí Azure AD B2C.

## <a name="next-steps"></a>Další postup

Další informace:

- [Vlastní zásady](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Přizpůsobení uživatelského rozhraní](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)