---
title: Předání vlastního stavu v žádostech o ověření (Microsoft Authentication Library pro JavaScript) | Azure
description: Přečtěte si, jak předat hodnotu parametru vlastního stavu v žádosti o ověření pomocí knihovny Microsoft Authentication Library pro JavaScript (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ae12624b3d897f05437f7795d1a1eee32ca37a
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532746"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Předání vlastního stavu v žádostech o ověření pomocí MSAL. js
Parametr *stavu* definovaný protokolem OAuth 2,0 je součástí žádosti o ověření a je také vrácen v odpovědi tokenu, aby nedocházelo k útokům proti padělání požadavků mezi weby. Ve výchozím nastavení projde knihovna Microsoft Authentication Library pro JavaScript (MSAL. js) náhodně generovanou hodnotu parametru *stavu* v žádostech o ověření.

Parametr State lze také použít ke kódování informací o stavu aplikace před přesměrování. Do tohoto parametru můžete předat stav uživatele v aplikaci, jako je například stránka nebo zobrazení, na kterých byly, jako vstup. Knihovna MSAL. js umožňuje předat vlastní stav jako parametr stavu v `Request` objektu:

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

Předaný stav je připojen k jedinečnému identifikátoru GUID nastavenému pomocí MSAL. js při odesílání žádosti. Když je vrácena odpověď, MSAL. js zkontroluje shodu stavu a potom vrátí vlastní předaný stav `Response` objektu jako. `accountState`

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

Další informace najdete v tématu [Vytvoření jednostránkové aplikace (Spa)](scenario-spa-overview.md) pomocí MSAL. js.