---
title: Zpracování chyb a výjimek v MSAL.NET
titleSuffix: Microsoft identity platform
description: Naučte se zpracovávat chyby a výjimky, problémy s deklarací identity podmíněného přístupu a opakované pokusy v MSAL.NET.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 565acd745ba5d7fdec71f306d3851e599838f7d9
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584040"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>Zpracování chyb a výjimek v MSAL.NET

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>Zpracování chyb v MSAL.NET

Při zpracování výjimek rozhraní .NET můžete použít samotný typ výjimky a `ErrorCode` člen k rozlišení mezi výjimkami. `ErrorCode` hodnoty jsou konstanty typu [MsalError](/dotnet/api/microsoft.identity.client.msalerror).

Můžete se také podívat na pole [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)a [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception).

Pokud je vyvolána výjimka [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) , vyzkoušejte [kódy chyb ověřování a autorizace](reference-aadsts-error-codes.md) , abyste viděli, zda je kód uveden v seznamu.

### <a name="common-net-exceptions"></a>Běžné výjimky .NET

Zde jsou uvedeny běžné výjimky, které mohou být vyvolány, a některé možné zmírnění:  

| Výjimka | Kód chyby | Omezení rizik|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: uživatel nebo správce nesouhlasí s používáním aplikace s ID {appId} s názvem {appName}. Odešlete interaktivní žádost o autorizaci pro tohoto uživatele a prostředek.| Nejdřív Získejte souhlas s uživatelem. Pokud nepoužíváte .NET Core (který nemá žádné webové uživatelské rozhraní), zavolejte (pouze jednou) `AcquireTokeninteractive` . Pokud používáte .NET Core nebo ho nechcete provést `AcquireTokenInteractive` , uživatel může přejít na adresu URL, aby mohl udělit souhlas: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read` . pro volání `AcquireTokenInteractive` : `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: uživatel musí používat službu [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md).| Nedochází k žádnému zmírnění. Pokud je pro vašeho tenanta nakonfigurované MFA a Azure Active Directory (AAD) se rozhodne ho vyhovět, vraťte se k interaktivnímu toku, například `AcquireTokenInteractive` nebo `AcquireTokenByDeviceCode` .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: typ grantu se nepodporuje přes koncové body */běžné* nebo */consumers* . Použijte */Organizations* nebo koncový bod pro konkrétního tenanta. Použili jste */běžné*.| Jak je vysvětleno ve zprávě z Azure AD, autorita musí mít tenanta nebo jinak */Organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: tělo žádosti musí obsahovat následující parametr: `client_secret or client_assertion` .| Tato výjimka může být vyvolána, pokud vaše aplikace nebyla registrována jako veřejná klientská aplikace v Azure AD. V Azure Portal upravte manifest aplikace a nastavte `allowPublicClient` na `true` . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: Nepovedlo se identifikovat přihlášeného uživatele.| Knihovna se nemohla dotázat na aktuálně přihlášeného uživatele Windows nebo tento uživatel není připojen ke službě AD nebo Azure AD (nepodporují se žádné připojené uživatele). Zmírnění 1: na UWP ověřte, že má aplikace následující možnosti: podnikové ověřování, privátní sítě (klient a Server), informace o uživatelském účtu. Zmírnění 2: implementace vlastní logiky pro načtení uživatelského jména (například john@contoso.com ) a použití `AcquireTokenByIntegratedWindowsAuth` formuláře, který přebírá uživatelské jméno.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Tato metoda spoléhá na protokol vystavený službou Active Directory (AD). Pokud byl uživatel vytvořen ve službě Azure AD bez zálohování služby AD ("spravovaný" uživatel), tato metoda se nezdaří. Tato neinteraktivní metoda ověřování může být výhodná pro uživatele vytvořená ve službě AD a zálohovaná pomocí Azure AD ("federované" uživatelé). Zmírnění: použijte interaktivní ověřování.|

### `MsalUiRequiredException`

Jeden z běžných stavových kódů vrácených z MSAL.NET při volání `AcquireTokenSilent()` je `MsalError.InvalidGrantError` . Tento stavový kód znamená, že aplikace by měla znovu zavolat knihovnu ověřování, ale v interaktivním režimu (AcquireTokenInteractive nebo AcquireTokenByDeviceCodeFlow pro veřejné klientské aplikace máme v Web Apps výzvu). Důvodem je to, že před vydáním ověřovacího tokenu je nutné provést další zásahy uživatele.

Většinou v době, kdy se chyba `AcquireTokenSilent` nezdařila, je důvodem to, že mezipaměť tokenů nemá tokeny vyhovující vaší žádosti. Přístupové tokeny vyprší za 1 hodinu a `AcquireTokenSilent` pokusí se načíst nový na základě aktualizačního tokenu (v OAuth2 podmínek se jedná o tok "Refresh token"). Tento tok může také selhat z různých důvodů, například pokud správce tenanta nakonfiguruje přísnější zásady přihlašování. 

Interakce se zaměřuje na to, že uživatel provede akci. Některé z těchto podmínek můžou uživatelé snadno vyřešit (například přijmout podmínky použití jediným kliknutím) a některé se nedají vyřešit pomocí aktuální konfigurace (třeba aby se počítač mohl připojit ke konkrétní podnikové síti). Některé vám pomůžou s nastavením vícefaktorového ověřování pro uživatele nebo na zařízení nainstalovat Microsoft Authenticator.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` výčet klasifikace

MSAL zpřístupňuje `Classification` pole, které si můžete přečíst a zajistit tak lepší uživatelské prostředí. Můžete například sdělit uživateli, že platnost hesla vypršela nebo že budou muset vyjádřit souhlas s používáním některých prostředků. Podporované hodnoty jsou součástí `UiRequiredExceptionClassification` výčtu:

| Klasifikace    | Význam           | Doporučené zpracování |
|-------------------|-------------------|----------------------|
| BasicAction | Podmínku lze vyřešit interakcí uživatele během interaktivního toku ověřování. | Zavolejte AcquireTokenInteractively (). |
| AdditionalAction | Podmínku lze vyřešit pomocí další nápravné interakce se systémem, mimo tok interaktivního ověřování. | Zavolejte AcquireTokenInteractively (), aby se zobrazila zpráva s vysvětlením nápravné akce. Volání aplikace se může rozhodnout pro skrytí toků vyžadujících additional_action, pokud uživatel pravděpodobně nedokončí akci nápravy. |
| MessageOnly      | V tuto chvíli nelze vyřešit podmínku. Spuštění toku interaktivního ověřování zobrazí zprávu s vysvětlením podmínky. | Zavolejte AcquireTokenInteractively () k zobrazení zprávy, která vysvětluje podmínku. AcquireTokenInteractively () vrátí UserCanceled chybu poté, co uživatel přečte zprávu a zavře okno. Volání aplikace se může rozhodnout pro skrytí toků, které mají za následek message_only v případě, že se uživateli pravděpodobně nevýhodá Tato zpráva.|
| ConsentRequired  | Chybí souhlas uživatele nebo byl odvolán. | Chcete-li uživateli udělit souhlas, zavolejte AcquireTokenInteractively (). |
| UserPasswordExpired | Vypršela platnost hesla uživatele. | Zavolejte AcquireTokenInteractively (), aby uživatel mohl resetovat svoje heslo. |
| PromptNeverFailed| Interaktivní ověřování bylo voláno s parametrem Prompt = nikdy, což MSAL vynutí spoléhání na soubory cookie prohlížeče, a ne pro zobrazení prohlížeče. Tato operace se nezdařila. | Volat AcquireTokenInteractively () bez výzvy. None |
| AcquireTokenSilentFailed | Sada MSAL SDK nemá dostatek informací pro načtení tokenu z mezipaměti. To může být způsobeno tím, že v mezipaměti nejsou žádné tokeny, nebo nebyl nalezen žádný účet. Chybová zpráva obsahuje další podrobnosti.  | Zavolejte AcquireTokenInteractively (). |
| Žádné    | Nejsou k dispozici žádné další podrobnosti. Podmínku může vyřešit interakce uživatele během toku interaktivního ověřování. | Zavolejte AcquireTokenInteractively (). |

## <a name="net-code-example"></a>Příklad kódu .NET

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Při volání rozhraní API, které vyžaduje podmíněný přístup ze MSAL.NET, bude vaše aplikace muset zpracovat výjimky pro výzvy k deklaracím identity. Tato vlastnost se zobrazí jako [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) , kde vlastnost [deklarací](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) nebude prázdná.

Pro zpracování výzvy deklarací identity budete muset použít `.WithClaim()` metodu `PublicClientApplicationBuilder` třídy.

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>Kódy chyb HTTP 500-600

MSAL.NET implementuje jednoduchý mechanismus opakovaného pokusu o chyby s kódy chyb HTTP 500-600.

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) povrchy `System.Net.Http.Headers.HttpResponseHeaders` jako vlastnost `namedHeaders` . Pomocí dalších informací z kódu chyby můžete zlepšit spolehlivost svých aplikací. V případě popsaného případu můžete použít `RetryAfterproperty` (typ `RetryConditionHeaderValue` ) a výpočetní čas, kdy se to opakuje.

Tady je příklad pro aplikaci démona, která používá tok přihlašovacích údajů klienta. Tuto možnost můžete přizpůsobit libovolné metodě pro získání tokenu.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>Další kroky

Zvažte možnost povolit [protokolování v MSAL.NET](msal-logging-dotnet.md) , které vám pomůžou diagnostikovat a ladit problémy.
