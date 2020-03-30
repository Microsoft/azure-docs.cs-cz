---
title: Chyby a výjimky (MSAL)
titleSuffix: Microsoft identity platform
description: Zjistěte, jak zpracovat chyby a výjimky, podmíněný přístup a nároky na problémy v aplikacích MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/22/2019
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: f78c64fc0ba25dc3310b24e873dbae266ea2f281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050321"
---
# <a name="handle-msal-exceptions-and-errors"></a>Zpracování výjimek a chyb MSAL

Tento článek poskytuje přehled různých typů chyb a doporučení pro zpracování běžných chyb přihlášení.

## <a name="msal-error-handling-basics"></a>Základy zpracování chyb MSAL

Výjimky v knihovně Ověřování společnosti Microsoft (MSAL) jsou určeny pro vývojáře aplikací k řešení potíží – nikoli pro zobrazení koncovým uživatelům. Zprávy o výjimce nejsou lokalizovány.

Při zpracování výjimek a chyb můžete použít samotný typ výjimky a kód chyby k rozlišení mezi výjimkami.  Seznam kódů chyb naleznete v tématu [Ověřování a autorizace chybových kódů](reference-aadsts-error-codes.md).

Během přihlašování se může dojít k chybám týkajícím se souhlasů, podmíněného přístupu (MFA, správa zařízení, omezení založených na poloze), vystavování a uplatnění tokenů a uživatelských vlastností.

Další podrobnosti o zpracování chyb v aplikaci najdete v následující části, která odpovídá jazyku, který používáte.

## <a name="net"></a>[.NET](#tab/dotnet)

Při zpracování výjimek .NET můžete použít samotný `ErrorCode` typ výjimky a člena k rozlišení mezi výjimkami. `ErrorCode`hodnoty jsou konstanty typu [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Můžete se také podívat na pole [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)a [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Pokud je vyvolána [msalserviceException,](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) zkuste [ověřování a autorizace chybové kódy](reference-aadsts-error-codes.md) chcete-li zjistit, zda je kód uveden zde.

### <a name="common-net-exceptions"></a>Běžné výjimky rozhraní .NET

Zde jsou běžné výjimky, které mohou být vyvolány a některé možné skutečnosti snižující závažnost rizika:  

| Výjimka | Kód chyby | Omezení rizik|
| --- | --- | --- |
| [MsalUiPožadovaná výjimka](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: Uživatel nebo správce nesouhlasil s použitím aplikace s ID {appId}' s názvem {appName}. Odešlete interaktivní žádost o autorizaci pro tohoto uživatele a prostředek.| Nejprve musíte získat souhlas uživatele. Pokud nepoužíváte .NET Core (který nemá žádné webové uživatelské rozhraní), volání `AcquireTokeninteractive`(pouze jednou) . Pokud používáte jádro rozhraní .NET nebo nechcete použít , `AcquireTokenInteractive`může uživatel přejít `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`na adresu URL a udělit souhlas: . volání `AcquireTokenInteractive`:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiPožadovaná výjimka](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: Uživatel je povinen používat vícefaktorové ověřování (MFA).| Neexistuje žádné zmírnění. Pokud je mfa nakonfigurovaná pro vašeho klienta a Služba Azure Active Directory (AAD) se rozhodne vynutit, musíte záložní k interaktivnímu toku, jako `AcquireTokenInteractive` je například nebo `AcquireTokenByDeviceCode`.|
| [Výjimka služby MsalService](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: Typ grantu není podporován přes koncové body */common* nebo */consumers.* Použijte koncový bod */organizations* nebo tenant-specific. Použili jste */common*.| Jak je vysvětleno ve zprávě z Azure AD, úřad musí mít klienta nebo jinak */organizace*.|
| [Výjimka služby MsalService](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: Tělo požadavku musí obsahovat `client_secret or client_assertion`následující parametr: .| Tato výjimka může být vyvolána, pokud vaše aplikace nebyla registrována jako veřejná klientská aplikace ve službě Azure AD. Na webu Azure Portal upravte manifest `allowPublicClient` pro `true`vaši aplikaci a nastavte na . |
| [Výjimka msalklienta](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: Nelze identifikovat přihlášeného uživatele.| Knihovna nemohla zadat dotaz na aktuálního přihlášeného uživatele systému Windows nebo tento uživatel není připojen ke službě AD nebo AAD (uživatelé přilehlí k pracovnímu místu nejsou podporováni). Zmírnění 1: u UPW zkontrolujte, zda má aplikace následující možnosti: Podnikové ověřování, Privátní sítě (klient a server), informace o uživatelském účtu. Zmírnění 2: Implementujte vlastní logiku pro načtení uživatelského `AcquireTokenByIntegratedWindowsAuth` jména (například) a použijte formulář, john@contoso.comkterý přebírá uživatelské jméno.|
| [Výjimka msalklienta](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Tato metoda závisí na protokolu vystaveném službou Active Directory (AD). Pokud byl uživatel vytvořen ve službě Azure Active Directory bez podpory služby AD ("spravovaný" uživatel), tato metoda se nezdaří. Uživatelé vytvoření ve službě AD a podporovaní službou AAD ("federovaní" uživatelé) mohou těžit z této neinteraktivní metody ověřování. Zmírnění rizik: Použijte interaktivní ověřování.|

### `MsalUiRequiredException`

Jeden z běžných stavových `AcquireTokenSilent()` kódů vrácených z MSAL.NET při volání je `MsalError.InvalidGrantError`. Tento stavový kód znamená, že aplikace by měla znovu volat ověřovací knihovnu, ale v interaktivním režimu (AcquireTokenInteractive nebo AcquireTokenByDeviceCodeFlow pro veřejné klientské aplikace a provést výzvu ve webových aplikacích). Důvodem je, že další interakce uživatele je vyžadována před vydáním ověřovacího tokenu.

Většinu času, `AcquireTokenSilent` když selže, je to proto, že mezipaměť tokenů nemá tokeny odpovídající vašemu požadavku. Platnost přístupových tokenů vyprší `AcquireTokenSilent` za 1 hodinu a pokusí se načíst nový token založený na aktualizačním tokenu (v podmínkách OAuth2 se jedná o tok obnovovacího tokenu). Tento tok může také selhat z různých důvodů, například pokud správce klienta nakonfiguruje přísnější zásady přihlášení. 

Interakce se zaměřuje na to, aby uživatel udělal akci. Některé z těchto podmínek je pro uživatele snadné vyřešit (například přijmout podmínky použití jediným kliknutím) a některé nelze vyřešit s aktuální konfigurací (například daný počítač se musí připojit ke konkrétní podnikové síti). Některé pomáhají uživateli nastavit vícefaktorové ověřování nebo nainstalovat Microsoft Authenticator do svého zařízení.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`počet klasifikací

MSAL zveřejňuje `Classification` pole, které si můžete přečíst poskytnout lepší uživatelské prostředí, například sdělit uživateli, že jejich heslo vypršelo, nebo že budete muset poskytnout souhlas k použití některých prostředků. Podporované hodnoty jsou součástí `UiRequiredExceptionClassification` výčtu:

| Classification    | Význam           | Doporučená manipulace |
|-------------------|-------------------|----------------------|
| Základní akce | Podmínku lze vyřešit interakcí uživatele během toku interaktivního ověřování. | Volání AcquireTokenInteractively(). |
| Další akce | Podmínku lze vyřešit další nápravnou interakcí se systémem mimo interaktivní tok ověřování. | Volání AcquireTokenInteractively() zobrazí zprávu, která vysvětluje nápravnou akci. Volající aplikace se může rozhodnout skrýt toky, které vyžadují additional_action pokud je nepravděpodobné, že uživatel dokončí nápravnou akci. |
| MessageOnly      | Stav nelze v tuto chvíli vyřešit. Spuštění interaktivního ověřovacího toku zobrazí zprávu vysvětlující podmínku. | Volání AcquireTokenInteractively() zobrazí zprávu, která vysvětluje podmínku. AcquireTokenInteractively() vrátí chybu UserCanceled poté, co uživatel přečte zprávu a zavře okno. Volající aplikace může zvolit skrýt toky, které mají za následek message_only, pokud uživatel pravděpodobně nebude mít prospěch ze zprávy.|
| SouhlasVyžadováno  | Souhlas uživatele chybí nebo byl odvolán. | Volání AcquireTokenInteractively() pro uživatele udělit souhlas. |
| Vypršela platnost uživatelského hesla | Platnost hesla uživatele vypršela. | Volání AcquireTokenInteractively(), aby uživatel mohl obnovit své heslo. |
| PromptNeverFailed| Interaktivní ověřování bylo voláno s parametrem prompt=never, což přinutilo msal spoléhat se na soubory cookie prohlížeče a nezobrazovat prohlížeč. To se nezdařilo. | Volání AcquireTokenInterly() bez Prompt.None |
| AcquireTokenSilentFailed | Sada MSAL SDK nemá dostatek informací k načtení tokenu z mezipaměti. To může být způsobeno tím, že v mezipaměti nejsou žádné tokeny nebo nebyl nalezen účet. Chybová zpráva má další podrobnosti.  | Volání AcquireTokenInteractively(). |
| Žádný    | Žádné další podrobnosti nejsou uvedeny. Podmínku lze vyřešit interakcí uživatele během interaktivního toku ověřování. | Volání AcquireTokenInteractively(). |

## <a name="net-code-example"></a>Příklad kódu rozhraní .NET

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

## <a name="javascript"></a>[Javascript](#tab/javascript)

Soubor MSAL.js poskytuje chybové objekty, které abstrahují a klasifikují různé typy běžných chyb. Poskytuje také rozhraní pro přístup k konkrétní podrobnosti o chybách, jako jsou chybové zprávy pro jejich zpracování odpovídajícím způsobem.

### <a name="error-object"></a>Objekt chyby

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

Rozšířením třídy chyb máte přístup k následujícím vlastnostem:
- `AuthError.message`: Stejné `errorMessage`jako .
- `AuthError.stack`: Trasování zásobníku pro vyvržené chyby.

### <a name="error-types"></a>Typy chyb

K dispozici jsou následující typy chyb:

- `AuthError`: Základní třída chyb pro knihovnu MSAL.js, která se používá také pro neočekávané chyby.

- `ClientAuthError`: Třída Error, která označuje problém s ověřováním klienta. Většina chyb, které pocházejí z knihovny bude ClientAuthErrors. Tyto chyby vyplývají z věcí, jako je volání metody přihlášení, když přihlášení již probíhá, uživatel zruší přihlášení a tak dále.

- `ClientConfigurationError`: Error Class, `ClientAuthError` extends throwd před požadavky jsou provedeny, když jsou chybné nebo chybějící parametry konfigurace daného uživatele.

- `ServerError`: Třída Error, představuje chybové řetězce odeslané ověřovacím serverem. Mohou se jedná o chyby, jako jsou neplatné formáty nebo parametry požadavků, nebo jiné chyby, které brání serveru v ověřování nebo autorizaci uživatele.

- `InteractionRequiredAuthError`: Třída Error, `ServerError` rozšiřuje se na chyby serveru, které vyžadují interaktivní volání. Tato chyba je `acquireTokenSilent` vyvolána, pokud je uživatel povinen komunikovat se serverem za účelem poskytnutí pověření nebo souhlasu s ověřováním nebo autorizací. Kódy `"interaction_required"`chyb `"login_required"`zahrnují `"consent_required"`, a .

Pro zpracování chyb v tocích`loginRedirect`ověřování `acquireTokenRedirect`s metodami přesměrování ( , ), budete muset zaregistrovat zpětné `handleRedirectCallback()` volání, které se volá s úspěchem nebo neúspěchem po přesměrování pomocí metody následujícím způsobem:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Metody pro pop-up`loginPopup`zkušenosti `acquireTokenPopup`( , ) vrátí sliby, takže můžete použít vzor slibu (.then a .catch) k jejich zpracování, jak je znázorněno:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Chyby, které vyžadují interakci

Při pokusu o použití neinteraktivní metody získání tokenu, například `acquireTokenSilent`, ale msal to nemohl udělat tiše, je vrácena chyba.

Možné důvody jsou:

- musíte se přihlásit
- musíte souhlasit
- musíte projít vícefaktorovým ověřováním.

Náprava je volání interaktivní metody, `acquireTokenPopup` `acquireTokenRedirect`jako je například nebo :

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="python"></a>[Python](#tab/python)

V MSAL pro Python většina chyb jsou zprostředkovány jako vrácená hodnota z volání rozhraní API. Chyba je reprezentována jako slovník obsahující odpověď JSON z platformy identit microsoftu.

* Úspěšná odpověď `"access_token"` obsahuje klíč. Formát odpovědi je definován protokolem OAuth2. Další informace naleznete v tématu [5.1 Successful Response](https://tools.ietf.org/html/rfc6749#section-5.1)
* Odpověď na `"error"` chybu `"error_description"`obsahuje a obvykle . Formát odpovědi je definován protokolem OAuth2. Další informace naleznete v tématu [5.2 Error Response](https://tools.ietf.org/html/rfc6749#section-5.2)

Při vrácení chyby `"error_description"` klíč obsahuje zprávu čitelnou pro člověka; který zase obvykle obsahuje kód chyby platformy identity společnosti Microsoft. Podrobnosti o různých kódech chyb naleznete v [tématu Ověřování a chybové kódy autorizace](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes).

V MSAL pro Python výjimky jsou vzácné, protože většina chyb jsou zpracovány vrácením chybové hodnoty. Výjimka `ValueError` je vyvolána pouze v případě, že došlo k problému s tím, jak se pokoušíte použít knihovnu , například když jsou parametry rozhraní API poškozeny.

## <a name="java"></a>[Java](#tab/java)

V MSAL pro Javu existují tři `MsalClientException` `MsalServiceException`typy `MsalInteractionRequiredException`výjimek: , , a ; všechny, které `MsalException`dědí od .

- `MsalClientException`je vyvolána, když dojde k chybě, která je místní do knihovny nebo zařízení.
- `MsalServiceException`je vyvolána, když služba zabezpečeného tokenu (STS) vrátí chybovou odpověď nebo dojde k jiné chybě sítě.
- `MsalInteractionRequiredException`je vyvolána, když je vyžadována interakce ui pro úspěšné ověření.

### <a name="msalserviceexception"></a>Výjimka služby MsalService

`MsalServiceException`zpřístupňuje hlavičky HTTP vrácené v požadavcích na STS. Přístup k nim přes`MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Jeden z běžných stavových kódů vrácených z MSAL pro Java při volání `AcquireTokenSilently()` je `InvalidGrantError`. To znamená, že před vydáním ověřovacího tokenu je vyžadována další interakce s uživatelem. Aplikace by měla znovu volat ověřovací knihovnu, `AuthorizationCodeParameters` `DeviceCodeParameters` ale v interaktivním režimu odesláním nebo pro veřejné klientské aplikace.

Většinu času, `AcquireTokenSilently` když se nezdaří, je to proto, že mezipaměť tokenů nemá token odpovídající váš požadavek. Platnost přístupových tokenů vyprší `AcquireTokenSilently` za jednu hodinu a pokusí se získat nový na základě obnovovacího tokenu. V podmínkách OAuth2 se jedná o tok tokenu aktualizace. Tento tok může také selhat z různých důvodů, například když správce klienta nakonfiguruje přísnější zásady přihlášení.

Některé podmínky, které vedou k této chybě, jsou pro uživatele snadno vyřešit. Mohou například potřebovat přijmout podmínky použití. Nebo možná požadavek nelze splnit s aktuální konfigurací, protože počítač potřebuje připojení k určité podnikové síti.

MSAL zpřístupňuje `reason` pole, které můžete použít k zajištění lepší uživatelské prostředí. Toto `reason` pole vás například může vést k tomu, abyste uživateli řekli, že jeho heslo vypršelo nebo že bude muset poskytnout souhlas s použitím některých prostředků. Podporované hodnoty jsou součástí `InteractionRequiredExceptionReason` výčtu:

| Důvod | Význam | Doporučená manipulace |
|---------|-----------|-----------------------------|
| `BasicAction` | Podmínku lze vyřešit interakcí uživatele během interaktivního toku ověřování. | Volání `acquireToken` s interaktivními parametry |
| `AdditionalAction` | Podmínku lze vyřešit další nápravnou interakcí se systémem mimo interaktivní tok ověřování. | Volání `acquireToken` s interaktivními parametry zobrazí zprávu, která vysvětluje nápravné opatření, které má být vykonat. Volající aplikace se může rozhodnout skrýt toky, které vyžadují další akci, pokud uživatel pravděpodobně nedokončí nápravnou akci. |
| `MessageOnly` | Stav nelze v tuto chvíli vyřešit. Spuštěním interaktivního ověřovacího toku zobrazíte zprávu s vysvětlením podmínky. | Volání `acquireToken` s interaktivními parametry zobrazí zprávu, která vysvětluje podmínku. `acquireToken`vrátí `UserCanceled` chybu poté, co uživatel přečte zprávu a zavře okno. Aplikace se může rozhodnout skrýt toky, které mají za následek zprávu, pokud je nepravděpodobné, že by uživatel měl ze zprávy prospěch. |
| `ConsentRequired`| Souhlas uživatele chybí nebo byl odvolán. |Volejte `acquireToken` s interaktivními parametry, aby uživatel mohl udělit souhlas. |
| `UserPasswordExpired` | Platnost hesla uživatele vypršela. | Volání `acquireToken` s interaktivním parametrem, aby uživatel mohl resetovat své heslo |
| `None` |  Další podrobnosti jsou uvedeny. Podmínka může být vyřešena interakcí uživatele během toku interaktivního ověřování. | Volání `acquireToken` s interaktivními parametry |

### <a name="code-example"></a>Příklad kódu

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

Úplný seznam chyb MSAL pro iOS a macOS je uveden ve [výčtu MSALError](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Všechny chyby vytvořené msal jsou vráceny s doménou. `MSALErrorDomain`

Pro systémové chyby MSAL `NSError` vrátí originál ze systémového rozhraní API. Například pokud se nezdaří získání tokenu z důvodu nedostatku připojení `NSURLErrorDomain` k `NSURLErrorNotConnectedToInternet` síti, MSAL vrátí chybu s doménou a kódem.

Doporučujeme, abyste na straně klienta zpracovat alespoň následující dvě chyby MSAL:

- `MSALErrorInteractionRequired`: Uživatel musí provést interaktivní požadavek. Existuje mnoho podmínek, které mohou vést k této chybě, jako je například relace ověřování s ukončenou platností nebo potřeba dalších požadavků na ověřování. Volání MSAL interaktivní token akvizice rozhraní API obnovit. 

- `MSALErrorServerDeclinedScopes`: Některé nebo všechny obory byly odmítnuty. Rozhodněte se, zda chcete pokračovat pouze s udělenými obory, nebo zastavte proces přihlášení.

> [!NOTE]
> Výčet `MSALInternalError` by měl být použit pouze pro odkaz a ladění. Nepokoušejte se tyto chyby automaticky zpracovávat za běhu. Pokud vaše aplikace narazí na některou `MSALInternalError`z chyb, které spadají pod , můžete chtít zobrazit obecnou zprávu, která bude čelit uživateli vysvětlující, co se stalo.

Například `MSALInternalErrorBrokerResponseNotReceived` znamená, že uživatel nedokončil ověřování a ručně se vrátil do aplikace. V takovém případě by aplikace měla zobrazit obecnou chybovou zprávu s vysvětlením, že ověřování nebylo dokončeno, a navrhnout, aby se pokusili znovu ověřit.

Následující ukázkový kód objective-c ukazuje osvědčené postupy pro zpracování některých běžných chybových stavů:

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>Podmíněný přístup a nároky na výzvy

Při získávání tokenů tiše, vaše aplikace může přijímat chyby, když [nároky podmíněného přístupu výzvu,](../azuread-dev/conditional-access-dev-guide.md) jako je například zásady MFA je vyžadována rozhraní API, ke kterým se pokoušíte získat přístup.

Vzor pro zpracování této chyby je interaktivně získat token pomocí MSAL. Interaktivní získání tokenu vyzve uživatele a dává mu možnost splnit požadované zásady podmíněného přístupu.

V některých případech při volání rozhraní API vyžadující podmíněný přístup, můžete obdržet výzvu deklarace identity v chybě z rozhraní API. Například pokud zásady podmíněného přístupu má mít spravované zařízení (Intune), chyba bude něco jako [AADSTS53000: Vaše zařízení je nutné spravovat pro přístup k tomuto prostředku](reference-aadsts-error-codes.md) nebo něco podobného. V takovém případě můžete předat deklarace identity v volání tokenu získání tak, aby uživatel je vyzván ke splnění příslušné zásady.

### <a name="net"></a>.NET

Při volání rozhraní API vyžadující podmíněný přístup z MSAL.NET, aplikace bude muset zpracovat výjimky deklarace. Zobrazí se jako [MsalServiceException,](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) kde [claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) vlastnost nebude prázdná.

Chcete-li zpracovat výzvu deklarace, `.WithClaim()` budete `PublicClientApplicationBuilder` muset použít metodu třídy.

### <a name="javascript"></a>JavaScript

Při získávání tokenů tiše `acquireTokenSilent`(pomocí) pomocí MSAL.js, aplikace může přijímat chyby při [problém deklarace podmíněného přístupu,](../azuread-dev/conditional-access-dev-guide.md) jako je například zásady MFA je vyžadována rozhraní API, ke kterým se pokoušíte získat přístup.

Vzor pro zpracování této chyby je provést interaktivní volání získat token v `acquireTokenPopup` `acquireTokenRedirect` MSAL.js, jako je nebo jako v následujícím příkladu:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

Interaktivní získání tokenu vyzve uživatele a dává mu možnost splnit požadované zásady podmíněného přístupu.

Při volání rozhraní API vyžadující podmíněný přístup, můžete obdržet výzvu deklarace identity v chybě z rozhraní API. V takovém případě můžete předat deklarace vrácené `claimsRequest` v `AuthenticationParameters.ts` chybě do pole třídy ke splnění příslušné zásady. 

Další podrobnosti naleznete [v tématu Požadování dalších deklarací.](active-directory-optional-claims.md)

### <a name="msal-for-ios-and-macos"></a>MSAL pro iOS a MacOS

MSAL pro iOS a macOS umožňuje požadovat konkrétní deklarace identity ve scénářích interaktivních i tichých tokenů.

Chcete-li požádat `claimsRequest` `MSALSilentTokenParameters` o `MSALInteractiveTokenParameters`vlastní deklarace identity, zadejte v nebo .

Další informace najdete [v tématu Žádost o vlastní deklarace identity pomocí MSAL pro iOS a macOS.](request-custom-claims.md)

## <a name="retrying-after-errors-and-exceptions"></a>Opakování po chybách a výjimkách

Očekává se, že implementovat vlastní zásady opakování při volání MSAL. MSAL provádí volání http služby AAD a může dojít k občasným chybám, například může dojít k výpadku sítě nebo je server přetížen.  

### <a name="http-error-codes-500-600"></a>Kódy chyb HTTP 500-600

MSAL.NET implementuje jednoduchý mechanismus opakování jednou pro chyby s kódy chyb HTTP 500-600.

### <a name="http-429"></a>HTTP 429

Pokud je server tokenů služby (STS) přetížen příliš mnoha požadavky, vrátí chybu HTTP 429 s `Retry-After` nápovědou o tom, jak dlouho budete moci akci znovu v poli odpovědi.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) `System.Net.Http.Headers.HttpResponseHeaders` povrchy `namedHeaders`jako vlastnost . Další informace z kódu chyby můžete použít ke zlepšení spolehlivosti aplikací. V případě popsaných můžete použít `RetryAfterproperty` (typu) `RetryConditionHeaderValue`a vypočítat, kdy opakovat.

Zde je příklad pro aplikaci daemon pomocí toku pověření klienta. Můžete přizpůsobit některou z metod pro získání tokenu.

```csharp
do
{
    retry = false;
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                           .ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (ex.ErrorCode == "temporarily_unavailable")
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
    …
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```
