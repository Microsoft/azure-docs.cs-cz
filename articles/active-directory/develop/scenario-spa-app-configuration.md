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
ms.openlocfilehash: 5ec98a62aab0b8d87645f3f58dc28219abe4e4fc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766272"
---
# <a name="single-page-application-code-configuration"></a>Jednostránkové aplikace: Konfigurace kódu

Naučte se konfigurovat kód pro jednostránkové aplikace (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>MSAL knihovny podporující implicitní tok

Platforma Microsoft Identity poskytuje následující knihovny Microsoft Authentication Library (MSAL) pro podporu implicitního toku pomocí doporučených postupů zabezpečení v oboru:  

| Knihovna MSAL | Popis |
|--------------|--------------|
| ![MSAL. js](media/sample-v2-code/logo_js.png) <br/> [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Prostá knihovna JavaScriptu pro použití v jakékoli webové aplikaci na straně klienta, která je sestavena prostřednictvím rozhraní JavaScript nebo SPA, jako je například úhlová Vue. js, a reaguje. js. |
| ![úhlový](media/sample-v2-code/logo_angular.png) MSAL <br/> [MSALý úhlový](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Obálka základní knihovny MSAL. js pro zjednodušení použití v aplikacích s jednou stránkou, které jsou sestaveny prostřednictvím úhlové architektury. Tato knihovna je ve verzi Preview a obsahuje [známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) s určitou úhlovou verzí a prohlížeči. |

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
Další informace o konfigurovatelných možnostech naleznete v tématu [inicializace aplikace pomocí MSAL. js](msal-js-initializing-client-applications.md).

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
