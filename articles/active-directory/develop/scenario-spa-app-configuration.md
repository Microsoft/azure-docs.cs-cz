---
title: Konfigurace jednostránkové aplikace – platforma identit Microsoftu | Azure
description: Přečtěte si, jak vytvořit jednostránkovou aplikaci (konfigurace kódu aplikace)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f1e0bf44515aab18019b19b4f0a6f84183e5aac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160079"
---
# <a name="single-page-application-code-configuration"></a>Jednostránková aplikace: Konfigurace kódu

Přečtěte si, jak nakonfigurovat kód pro jednostránkovou aplikaci (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>Knihovny MSAL, které podporují implicitní tok

Platforma identit společnosti Microsoft poskytuje následující knihovny Microsoft Authentication Library (MSAL), které podporují implicitní tok pomocí postupů zabezpečení doporučených oborem:  

| Knihovna MSAL | Popis |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Plain JavaScript knihovna pro použití v každém klient-side webové aplikace, která je postavena prostřednictvím JavaScript nebo SPA frameworks jako Angular, Vue.js a React.js. |
| ![MSAL Úhlové](media/sample-v2-code/logo_angular.png) <br/> [MSAL Úhlové](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Obálka základní knihovny MSAL.js pro zjednodušení použití v jednostránkových aplikacích, které jsou vytvořeny prostřednictvím úhlového rámce. Tato knihovna je ve verzi Preview a [má známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) s některými úhlovými verzemi a prohlížeči. |

## <a name="application-code-configuration"></a>Konfigurace kódu aplikace

V knihovně MSAL jsou informace o registraci aplikace předány jako konfigurace během inicializace knihovny.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Další informace o konfigurovatelných možnostech naleznete v [tématu Inicializace aplikace pomocí souboru MSAL.js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](scenario-spa-sign-in.md)
