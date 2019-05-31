---
title: Předávání vlastního stavu v žádosti o ověření (knihovna Microsoft Authentication Library pro JavaScript) | Azure
description: Zjistěte, jak předat hodnotu parametru vlastního stavu v žádosti o ověření pomocí knihovna Microsoft Authentication Library pro JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f29d84838ddb11ac359d7a04dbce8e39dd05ac01
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420495"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Předávání vlastního stavu v žádosti o ověření pomocí MSAL.js
*Stavu* parametru, jak jsou definovány pomocí OAuth 2.0 je součástí požadavek na ověření a je také vrácen v odpovědi tokenu prevenci proti útokům padělání žádosti více webů. Ve výchozím nastavení, knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) předá náhodně vygenerovaný jedinečný *stavu* hodnota parametru v žádosti o ověření.

Parametr stavu můžete také použít ke kódování informace o stavu aplikace před přesměrování. Stav uživatele v aplikacích, jako jsou stránky nebo zobrazení, které byly na serveru, jako vstup pro tento parametr můžete předat. Knihovna MSAL.js umožňuje předat vlastní stav jako parametr stavu `Request` objektu:

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
};
```

Příklad:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: “page_url”
}

myMSALObj.loginPopup(loginRequest);
```

Předaný stavu se připojí jedinečný identifikátor GUID nastavil MSAL.js při odesílání požadavku. Při vrácení odpovědi MSAL.js kontroluje stav shody a vrací vlastní předané ve stavu v `Response` objektu jako `accountState`.

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

Další informace, přečtěte si informace o [sestavení jednostránkové aplikace (SPA)](scenario-spa-overview.md) využitím MSAL.js.