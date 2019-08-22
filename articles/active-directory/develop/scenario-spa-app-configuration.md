---
title: Jednostránkové aplikace (konfigurace kódu aplikace) – Microsoft Identity Platform
description: Naučte se vytvářet jednostránkové aplikace (konfigurace kódu aplikace).
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
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7b4fba03f9edf8a3f4e42b23c6a1b5e06518863
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891538"
---
# <a name="single-page-application---code-configuration"></a>Jedna stránka – konfigurace kódu aplikace

Naučte se konfigurovat kód pro jednostránkové aplikace (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Knihovny MSAL podporující implicitní tok

Platforma Microsoft Identity Platform poskytuje knihovnu MSAL. js k podpoře implicitního toku pomocí doporučených postupů zabezpečení v oboru.  

Knihovny podporující implicitní tok jsou:

| Knihovna MSAL | Popis |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Jednoduchá knihovna JavaScriptu pro použití v jakékoli webové aplikaci na straně klienta vytvořenou pomocí rozhraní JavaScript nebo SPA, jako je například úhlová, Vue. js, reagovat. js atd. |
| ![MSALý úhlový](media/sample-v2-code/logo_angular.png) <br/> [MSALý úhlový](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Obálka základní knihovny MSAL. js pro zjednodušení použití v aplikacích s jednou stránkou sestavenou s úhlovým rozhraním. Tato knihovna je ve verzi Preview a obsahuje [známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) s určitou úhlovou verzí a prohlížeči. |

## <a name="application-code-configuration"></a>Konfigurace kódu aplikace

V knihovně MSAL jsou informace o registraci aplikace předány jako konfigurace při inicializaci knihovny.

### <a name="javascript"></a>JavaScript

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
Další podrobnosti o konfigurovatelných možnostech, které jsou k dispozici, naleznete v tématu [inicializace aplikace pomocí MSAL. js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Úhlová

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](scenario-spa-sign-in.md)
