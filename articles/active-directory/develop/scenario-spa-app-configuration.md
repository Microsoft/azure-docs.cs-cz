---
title: Konfigurace jednostránkové aplikace | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet jednostránkové aplikace (konfigurace kódu aplikace).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 5cbb576a7fcfb2daf492a149130aa7c99fe10ac5
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753615"
---
# <a name="single-page-application-code-configuration"></a>Jednostránkové aplikace: Konfigurace kódu

Naučte se konfigurovat kód pro jednostránkové aplikace (SPA).

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>Knihovny MSAL pro jednostránkové a podporované toky ověřování

Platforma Microsoft Identity Platform poskytuje následující knihovnu Microsoft Authentication Library pro JavaScript (MSAL.js), která podporuje implicitní tok toků a autorizačního kódu s PKCE pomocí doporučených postupů zabezpečení v oboru:

| Knihovna MSAL | Tok | Popis |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | Tok autorizačního kódu (PKCE) | Jednoduchá knihovna JavaScriptu pro použití v jakékoli webové aplikaci na straně klienta, která je sestavena prostřednictvím rozhraní JavaScript nebo SPA, jako je například úhlová, Vue.jsa a React.js. |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | Implicitní tok | Jednoduchá knihovna JavaScriptu pro použití v jakékoli webové aplikaci na straně klienta, která je sestavena prostřednictvím rozhraní JavaScript nebo SPA, jako je například úhlová, Vue.jsa a React.js. |
| ![MSALý úhlový](media/sample-v2-code/logo_angular.png) <br/> [MSALý úhlový](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Implicitní tok | Obálka základní knihovny MSAL.js pro zjednodušení použití v jednostránkovéch aplikacích, které jsou sestaveny prostřednictvím úhlové architektury. |

## <a name="application-code-configuration"></a>Konfigurace kódu aplikace

V knihovně MSAL jsou informace o registraci aplikace předány jako konfigurace při inicializaci knihovny.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Další informace o konfigurovatelných možnostech naleznete v tématu [inicializace aplikace pomocí MSAL.js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, [Přihlaste se a odhlaste](scenario-spa-sign-in.md)se.
