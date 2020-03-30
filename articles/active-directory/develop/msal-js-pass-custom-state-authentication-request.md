---
title: Předat vlastní stav v požadavcích na ověření (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak předat hodnotu parametru vlastního stavu v žádosti o ověření pomocí knihovny Microsoft Authentication Library for JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1c05956f83ad3a6491627be8916fac2c8be2b7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084933"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Předání vlastního stavu v požadavcích na ověření pomocí souboru MSAL.js

Parametr *stavu,* jak je definován oAuth 2.0, je součástí požadavku na ověření a je také vrácena v odpovědi tokenu zabránit útokům padělání požadavků na příčce webu. Ve výchozím nastavení předá Microsoft Authentication Library for JavaScript (MSAL.js) náhodně generovanou hodnotu parametru jedinečného *stavu* v požadavcích na ověření.

Parametr stavu lze také použít ke kódování informací o stavu aplikace před přesměrováním. Můžete předat stav uživatele v aplikaci, například stránku nebo zobrazení, na kterých byl, jako vstup do tohoto parametru. Knihovna MSAL.js umožňuje předat vlastní stav jako `Request` parametr stavu v objektu:

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
> Pokud chcete přeskočit token uložený v mezipaměti a přejít na `forceRefresh` server, předejte logický seznam do objektu AuthenticationParameters, který slouží k vytvoření požadavku na přihlášení/token.
> `forceRefresh`by neměl být používán ve výchozím nastavení, z důvodu dopadu na výkon na vaši aplikaci.
> Spoléhání se na mezipaměť poskytne uživatelům lepší zážitek.
> Přeskočení mezipaměti by mělo být použito pouze ve scénářích, kde víte, že aktuálně uložená data nemají aktuální informace.
> Například nástroj pro správu, který přidává role uživateli, který potřebuje získat nový token s aktualizovanými rolemi.

Například:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Předánve stavu je připojen k jedinečné GUID nastavené MSAL.js při odesílání požadavku. Po vrácení odpovědi msal.js zkontroluje shodu stavu a potom `Response` vrátí `accountState`vlastní předán ve stavu v objektu jako .

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

Další informace najdete v informacích o [vytváření jednostránkové aplikace (SPA)](scenario-spa-overview.md) pomocí souboru MSAL.js.