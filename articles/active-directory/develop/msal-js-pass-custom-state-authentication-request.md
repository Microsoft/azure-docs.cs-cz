---
title: Předat vlastní stav v žádostech o ověření (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak předat hodnotu parametru vlastního stavu v žádosti o ověření pomocí knihovny Microsoft Authentication Library pro JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5c1fad96ea6e3b75b3afdfd4a4d3baac43308541
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063667"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Předání vlastního stavu v žádostech o ověření pomocí MSAL.js

Parametr *stavu* definovaný protokolem OAuth 2,0 je součástí žádosti o ověření a je také vrácen v odpovědi tokenu, aby nedocházelo k útokům proti padělání požadavků mezi weby. Ve výchozím nastavení projde knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) náhodně generovanou hodnotu parametru *stavu* v žádostech o ověření.

Parametr State lze také použít ke kódování informací o stavu aplikace před přesměrování. Do tohoto parametru můžete předat stav uživatele v aplikaci, jako je například stránka nebo zobrazení, na kterých byly, jako vstup. Knihovna MSAL.js umožňuje předat vlastní stav jako parametr stavu v `Request` objektu:

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
    forceRefresh?: boolean;
};
```

> [!Note]
> Pokud chcete přeskočit token z mezipaměti a přejít na server, předejte prosím logickou hodnotu `forceRefresh` do objektu AuthenticationParameters, který se používá k vytvoření žádosti o přihlášení nebo token.
> `forceRefresh` by neměl být ve výchozím nastavení použit, protože má vliv na výkon vaší aplikace.
> Spoléhání se na mezipaměť, aby vaši uživatelé měli lepší zkušenosti.
> Přeskočení mezipaměti by mělo být použito pouze ve scénářích, kde víte, že data aktuálně uložených v mezipaměti nemají aktuální informace.
> Například nástroj pro správu, který přidává role uživateli, který potřebuje získat nový token s aktualizovanými rolemi.

Například:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Předaný stav je připojen k jedinečnému identifikátoru GUID nastavenému MSAL.js při odesílání žádosti. Pokud je vrácena odpověď, MSAL.js zkontroluje shodu stavu a potom vrátí vlastní předaný stav `Response` objektu jako `accountState` .

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

Další informace najdete v článku o [vytváření jednostránkové aplikace (Spa)](scenario-spa-overview.md) pomocí MSAL.js.