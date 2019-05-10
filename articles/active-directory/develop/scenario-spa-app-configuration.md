---
title: Jednostránková aplikace (konfigurace kódu aplikace) – platforma identit Microsoft
description: Další informace o vytváření jednostránkové aplikace (konfigurace kódu aplikace)
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
ms.openlocfilehash: b71454fc553a0f81c26426a6a9588f15d5311e38
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406422"
---
# <a name="single-page-application---code-configuration"></a>Jednostránková aplikace - kód konfigurace

Zjistěte, jak nakonfigurovat kódu jednostránkové aplikace (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Knihovna MSAL knihovny podporuje implicitní tok

Platforma identit Microsoft poskytuje MSAL.js knihovny pro podporu implicitní tok pomocí odvětví doporučené postupy pro zabezpečení.  

Knihovny podporuje implicitní tok jsou:

| Knihovna MSAL | Popis |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Standardní knihovny JavaScript pro použití ve všech aplikacích webové na straně klienta vytvořených pomocí jazyka JavaScript nebo SPA architektury, jako jsou Angular, Vue.js, React.js atd. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Obálka základní knihovny MSAL.js zjednodušuje použití v jednostránkové aplikace vytvořené pomocí rozhraní Angular. Tato knihovna je ve verzi preview a má [známé problémy](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) s určitými verzemi Angular a prohlížeče. |

## <a name="application-code-configuration"></a>Konfigurace kódu aplikace

Informace o registraci aplikace je v knihovně MSAL předána jako konfigurace během inicializace knihovny.

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
Další podrobnosti o konfigurovatelných možností, které jsou k dispozici, najdete v části [inicializace aplikace s MSAL.js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Úhlová

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientId: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přihlášení a odhlášení](scenario-spa-sign-in.md)
