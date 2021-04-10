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
ms.openlocfilehash: fe73832ec5eaee62a2dc2d397c12f82334e2efd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010694"
---
# <a name="single-page-application-code-configuration"></a>Jednostránkové aplikace: Konfigurace kódu

Naučte se konfigurovat kód pro jednostránkové aplikace (SPA).

## <a name="microsoft-libraries-supporting-single-page-apps"></a>Knihovny Microsoftu podporující jednostránkové aplikace 

Jednostránkové aplikace podporují následující knihovny Microsoftu:

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

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
