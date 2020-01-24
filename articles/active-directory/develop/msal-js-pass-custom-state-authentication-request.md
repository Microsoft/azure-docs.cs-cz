---
title: Předání vlastního stavu v žádostech o ověření (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak předat hodnotu parametru vlastního stavu v žádosti o ověření pomocí knihovny Microsoft Authentication Library pro JavaScript (MSAL. js).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: be2dd887358aa00c87a4b5668a99c425d83b59dc
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696023"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Předání vlastního stavu v žádostech o ověření pomocí MSAL. js

Parametr *stavu* definovaný protokolem OAuth 2,0 je součástí žádosti o ověření a je také vrácen v odpovědi tokenu, aby nedocházelo k útokům proti padělání požadavků mezi weby. Ve výchozím nastavení projde knihovna Microsoft Authentication Library pro JavaScript (MSAL. js) náhodně generovanou hodnotu parametru *stavu* v žádostech o ověření.

Parametr State lze také použít ke kódování informací o stavu aplikace před přesměrování. Do tohoto parametru můžete předat stav uživatele v aplikaci, jako je například stránka nebo zobrazení, na kterých byly, jako vstup. Knihovna MSAL. js umožňuje předat vlastní stav jako parametr stavu v objektu `Request`:

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
> Pokud chcete přeskočit token uložený v mezipaměti a přejít na server, předejte prosím logickou `forceRefresh` do objektu AuthenticationParameters, který jste použili k vytvoření žádosti o přihlášení nebo token.
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

Předaný stav je připojen k jedinečnému identifikátoru GUID nastavenému pomocí MSAL. js při odesílání žádosti. Když je vrácena odpověď, MSAL. js zkontroluje shodu stavu a potom vrátí vlastní předaný stav objektu `Response` jako `accountState`.

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