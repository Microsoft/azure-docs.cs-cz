---
title: Jak používat rozhraní API s povoleným vyhodnocováním průběžného přístupu v aplikacích | Azure
titleSuffix: Microsoft identity platform
description: Jak zvýšit zabezpečení a odolnost aplikací přidáním podpory pro průběžné hodnocení přístupu a povolením dlouhotrvajících přístupových tokenů, které je možné odvolat na základě kritických událostí a vyhodnocení zásad.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2020
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: f6ce792b3db0100d7356884bbc6ee2696580df10
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652054"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>Jak používat rozhraní API s povoleným vyhodnocováním průběžného přístupu ve vašich aplikacích

[Vyhodnocování průběžného přístupu](../conditional-access/concept-continuous-access-evaluation.md) (CAE) je nově vznikající oborový standard, který umožňuje odvolat přístupové tokeny na základě [kritických událostí](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) a [vyhodnocování zásad](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview) a nemusíte přitom spoléhat na vypršení platnosti tokenu na základě doby života. V případě některých rozhraní API prostředků se rizika a zásady vyhodnocují v reálném čase, což může zvýšit životnost tokenu až na 28 hodin. Tyto dlouhodobé tokeny se proaktivně aktualizují pomocí knihovny Microsoft Authentication Library (MSAL) a zvýší se tak odolnost vašich aplikací.

V tomto článku se dozvíte, jak ve svých aplikacích používat rozhraní API s podporou CAE.

## <a name="implementation-considerations"></a>Důležité informace o implementaci

Aby bylo možné používat hodnocení průběžného přístupu, musí být aplikace i rozhraní API prostředků, ke kterým přistupuje, zapnuté CAE. Příprava kódu pro použití prostředku s povoleným CAE ale nebrání v používání rozhraní API, která nejsou povolená CAE.

Pokud rozhraní API prostředků implementuje CAE a vaše aplikace ji deklaruje, může zpracovávat CAE, vaše aplikace získá tokeny CAE pro daný prostředek. Z tohoto důvodu, pokud deklarujete aplikaci CAE připravená, musí aplikace zpracovat výzvu CAE deklarací identity pro všechna rozhraní API prostředků, která přijímají tokeny přístupu Microsoft Identity Access. Pokud CAE odezvy v těchto voláních rozhraní API nezpracováváte, může vaše aplikace končit smyčkou opakováním volání rozhraní API s tokenem, který je stále v vráceném životnosti tokenu, ale byl odvolán z důvodu CAE.

## <a name="the-code"></a>Kód

Prvním krokem je přidání kódu pro zpracování odpovědi z rozhraní API prostředků, které volání přemítne z důvodu CAE. Pomocí CAE budou rozhraní API vracet stav 401 a hlavičku WWW-Authenticate, když přístupový token byl odvolán, nebo rozhraní API zjistí změnu v použité IP adrese. Hlavička WWW-Authenticate obsahuje deklaraci identity, kterou může aplikace použít k získání nového přístupového tokenu.

Příklad:

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
  authorization_uri="https://login.windows.net/common/oauth2/authorize",
  error="insufficient_claims",
  claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

Vaše aplikace by měla vyhledat:

- volání rozhraní API vrátilo stav 401.
- existence WWW-Authenticate hlavičky obsahující:
  - parametr "Error" s hodnotou "insufficient_claims"
  - parametr deklarací identity

Po splnění těchto podmínek může aplikace extrahovat a dekódovat výzvy deklarací identity.

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // ...
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        AuthenticationHeaderValue bearer = APIresponse.Headers.WwwAuthenticate.First
            (v => v.Scheme == "Bearer");
        IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
        var error = GetParameter(parameters, "error");

        if (null != error && "insufficient_claims" == error)
        {
            var claimChallengeParameter = GetParameter(parameters, "claims");
            if (null != claimChallengeParameter)
            {
                var claimChallengebase64Bytes = System.Convert.FromBase64String(claimChallengeParameter);
                var claimChallenge = System.Text.Encoding.UTF8.GetString(claimChallengebase64Bytes);
                var newAccessToken = await GetAccessTokenWithClaimChallenge(scopes, claimChallenge);
```

Vaše aplikace by pak použila výzvu deklarací identity k získání nového přístupového tokenu pro daný prostředek.

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // ...
```

Jakmile je vaše aplikace připravená na zpracování výzvy k deklaraci identity vrácené prostředkem CAE, můžete říct, že je vaše aplikace od Microsoftu připravená CAE připravena. Pokud to chcete provést v aplikaci MSAL, Sestavte si veřejného klienta pomocí možností klienta "CP1".

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

Aplikaci můžete testovat přihlášením uživatele k aplikaci a následným použitím Azure Portal k odvolání relací uživatele. Až aplikace příště zavolá rozhraní API s podporou CAE, zobrazí se uživateli výzva k opětovnému ověření.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [vyhodnocení průběžného přístupu](../conditional-access/concept-continuous-access-evaluation.md).
