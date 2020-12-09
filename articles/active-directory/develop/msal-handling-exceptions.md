---
title: Chyby a výjimky (MSAL)
titleSuffix: Microsoft identity platform
description: Naučte se zpracovávat chyby a výjimky, podmíněný přístup a problémy deklarací v aplikacích MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5b4ed1e21c33a952b639009b619db4f497f2cfbf
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860061"
---
# <a name="handle-msal-exceptions-and-errors"></a>Zpracování výjimek a chyb MSAL

Tento článek obsahuje přehled různých typů chyb a doporučení pro zpracování běžných chyb při přihlašování.

## <a name="msal-error-handling-basics"></a>Základy zpracování chyb MSAL

Výjimky v knihovně Microsoft Authentication Library (MSAL) jsou určené vývojářům aplikací k řešení potíží – ne pro zobrazení koncovým uživatelům. Zprávy výjimek nejsou lokalizovány.

Při zpracování výjimek a chyb můžete použít samotný typ výjimky a kód chyby k rozlišení mezi výjimkami.  Seznam kódů chyb najdete v tématu [kódy chyb ověřování a autorizace](reference-aadsts-error-codes.md).

Během přihlašování se může zobrazit chyba týkající se souhlasu, podmíněného přístupu (MFA, Správa zařízení, omezení na základě umístění), vystavení a uplatnění tokenu a vlastností uživatele.

Další informace o zpracování chyb pro vaši aplikaci najdete v následující části, které odpovídají jazyku, který používáte.

## <a name="net"></a>[.NET](#tab/dotnet)

Při zpracování výjimek rozhraní .NET můžete použít samotný typ výjimky a `ErrorCode` člen k rozlišení mezi výjimkami. `ErrorCode` hodnoty jsou konstanty typu [MsalError](/dotnet/api/microsoft.identity.client.msalerror).

Můžete se také podívat na pole [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)a [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception).

Pokud je vyvolána výjimka [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) , vyzkoušejte [kódy chyb ověřování a autorizace](reference-aadsts-error-codes.md) , abyste viděli, zda je kód uveden v seznamu.

### <a name="common-net-exceptions"></a>Běžné výjimky .NET

Zde jsou uvedeny běžné výjimky, které mohou být vyvolány, a některé možné zmírnění:  

| Výjimka | Kód chyby | Omezení rizik|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: uživatel nebo správce nesouhlasí s používáním aplikace s ID {appId} s názvem {appName}. Odešlete interaktivní žádost o autorizaci pro tohoto uživatele a prostředek.| Nejdřív musíte získat souhlas s uživatelem. Pokud nepoužíváte .NET Core (který nemá žádné webové uživatelské rozhraní), zavolejte (pouze jednou) `AcquireTokeninteractive` . Pokud používáte .NET Core nebo ho nechcete provést `AcquireTokenInteractive` , uživatel může přejít na adresu URL, aby mohl udělit souhlas: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read` . pro volání `AcquireTokenInteractive` : `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: uživatel musí používat službu [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md).| Nedochází k žádnému zmírnění. Pokud je pro vašeho tenanta nakonfigurované MFA a Azure Active Directory (AAD) se rozhodne ho vyhovět, budete se muset vrátit k interaktivnímu toku, například `AcquireTokenInteractive` nebo `AcquireTokenByDeviceCode` .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: typ grantu se nepodporuje přes koncové body */běžné* nebo */consumers* . Použijte */Organizations* nebo koncový bod pro konkrétního tenanta. Použili jste */běžné*.| Jak je vysvětleno ve zprávě z Azure AD, autorita musí mít tenanta nebo jinak */Organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: tělo žádosti musí obsahovat následující parametr: `client_secret or client_assertion` .| Tato výjimka může být vyvolána, pokud vaše aplikace nebyla registrována jako veřejná klientská aplikace v Azure AD. V Azure Portal upravte manifest aplikace a nastavte `allowPublicClient` na `true` . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: Nepovedlo se identifikovat přihlášeného uživatele.| Knihovna se nemohla dotázat na aktuálně přihlášeného uživatele Windows nebo tento uživatel není připojený k AD ani AAD (uživatelé připojení k pracovišti nejsou podporováni). Zmírnění 1: na UWP ověřte, že má aplikace následující možnosti: podnikové ověřování, privátní sítě (klient a Server), informace o uživatelském účtu. Zmírnění 2: implementace vlastní logiky pro načtení uživatelského jména (například john@contoso.com ) a použití `AcquireTokenByIntegratedWindowsAuth` formuláře, který přebírá uživatelské jméno.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Tato metoda spoléhá na protokol vystavený službou Active Directory (AD). Pokud byl uživatel vytvořen v Azure Active Directory bez služby AD ("spravovaný" uživatel), tato metoda se nezdaří. Uživatelům vytvořeným ve službě AD a zálohovaným pomocí AAD ("federované" uživatelé) může tato neinteraktivní metoda ověřování těžit z výhod. Zmírnění: použijte interaktivní ověřování.|

### `MsalUiRequiredException`

Jeden z běžných stavových kódů vrácených z MSAL.NET při volání `AcquireTokenSilent()` je `MsalError.InvalidGrantError` . Tento stavový kód znamená, že aplikace by měla znovu zavolat knihovnu ověřování, ale v interaktivním režimu (AcquireTokenInteractive nebo AcquireTokenByDeviceCodeFlow pro veřejné klientské aplikace a dělat výzvy ve službě Web Apps). Důvodem je to, že před vydáním ověřovacího tokenu je nutné provést další zásahy uživatele.

Většinou v době, kdy se chyba `AcquireTokenSilent` nezdařila, je důvodem to, že mezipaměť tokenů nemá tokeny vyhovující vaší žádosti. Přístupové tokeny vyprší za 1 hodinu a `AcquireTokenSilent` pokusí se načíst nový na základě aktualizačního tokenu (v OAuth2 podmínek se jedná o tok "Refresh token"). Tento tok může také selhat z různých důvodů, například pokud správce tenanta nakonfiguruje přísnější zásady přihlašování. 

Interakce se zaměřuje na to, že uživatel provede akci. Některé z těchto podmínek můžou uživatelé snadno vyřešit (například přijmout podmínky použití jediným kliknutím) a některé se nedají vyřešit pomocí aktuální konfigurace (třeba aby se počítač mohl připojit ke konkrétní podnikové síti). Některé vám pomůžou s nastavením vícefaktorového ověřování pro uživatele nebo na zařízení nainstalovat Microsoft Authenticator.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` výčet klasifikace

MSAL zpřístupňuje `Classification` pole, které si můžete přečíst a zajistit tak lepší činnost koncového uživatele, třeba sdělit uživateli, že jeho heslo vypršelo nebo že budou muset vyjádřit souhlas s používáním některých prostředků. Podporované hodnoty jsou součástí `UiRequiredExceptionClassification` výčtu:

| Classification    | Význam           | Doporučené zpracování |
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

## <a name="javascript"></a>[JavaScript](#tab/javascript)

MSAL.js poskytuje objekty chyb, které abstraktní a klasifikují různé typy běžných chyb. Poskytuje také rozhraní pro přístup k určitým podrobnostem o chybách, jako jsou například chybové zprávy, aby je správně zpracovávala.

### <a name="error-object"></a>Error – objekt

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

Rozšířením třídy Error máte přístup k následujícím vlastnostem:
- `AuthError.message`: Totéž jako `errorMessage` .
- `AuthError.stack`: Trasování zásobníku pro vyvolané chyby.

### <a name="error-types"></a>Typy chyb

K dispozici jsou následující typy chyb:

- `AuthError`: Základní třída Error pro knihovnu MSAL.js, která se používá také pro neočekávané chyby.

- `ClientAuthError`: Error – třída, která označuje problém s ověřením klienta. Většina chyb, které pocházejí z knihovny, bude ClientAuthErrors. Tyto chyby jsou výsledkem akcí, jako je volání metody Login v případě, že přihlášení již probíhá, uživatel zruší přihlášení atd.

- `ClientConfigurationError`: Error Class, je `ClientAuthError` vyvoláno před provedením požadavků, pokud jsou zadané parametry konfigurace uživatele poškozené nebo chybí.

- `ServerError`: Error Class, představuje chybové řetězce odeslané ověřovacím serverem. Můžou to být chyby, jako jsou neplatné formáty nebo parametry požadavků nebo jakékoli jiné chyby, které brání serveru v ověřování nebo autorizaci uživatele.

- `InteractionRequiredAuthError`: Error Class, rozšiřuje, `ServerError` aby představovala chyby serveru, které vyžadují interaktivní volání. Tato chyba je vyvolána, `acquireTokenSilent` Pokud je uživatel nutný k interakci se serverem za účelem poskytnutí pověření nebo souhlasu pro ověřování/autorizaci. Kódy chyb zahrnují `"interaction_required"` , `"login_required"` a `"consent_required"` .

Pro zpracování chyb v tokůch ověřování pomocí metod přesměrování ( `loginRedirect` , `acquireTokenRedirect` ) bude nutné zaregistrovat zpětné volání, které je voláno s funkcí úspěch nebo neúspěch po přesměrování pomocí `handleRedirectCallback()` metody, následovně:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Metody pro místní prostředí ( `loginPopup` , `acquireTokenPopup` ) vracejí příslibů, takže můžete použít vzor Promise (. then a. catch) k jejich zpracování, jak je znázorněno níže:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Chyby, které vyžadují interakci

Při pokusu o použití neinteraktivní metody získání tokenu, jako je například `acquireTokenSilent` , ale MSAL by nedokázala tuto chybu provést v tichém režimu, se vrátí chyba.

Možné důvody:

- musíte se přihlásit.
- musíte souhlasit.
- musíte projít prostředím Multi-Factor Authentication.

Nápravou je zavolat interaktivní metodu, například `acquireTokenPopup` nebo `acquireTokenRedirect` :

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

V MSAL pro Python je většina chyb přenášena jako návratová hodnota z volání rozhraní API. Tato chyba je reprezentována jako slovník obsahující odpověď JSON od platformy Microsoft identity.

* Úspěšná odpověď obsahuje `"access_token"` klíč. Formát odpovědi je definován protokolem OAuth2. Další informace najdete v tématu [5,1 úspěšná odezva](https://tools.ietf.org/html/rfc6749#section-5.1) .
* Chybná odpověď obsahuje `"error"` a obvykle `"error_description"` . Formát odpovědi je definován protokolem OAuth2. Další informace najdete v tématu [5,2 chybová odezva](https://tools.ietf.org/html/rfc6749#section-5.2) .

Když se vrátí chyba, `"error_description"` klíč obsahuje zprávu, která je čitelná pro čtení, která zase obvykle obsahuje kód chyby platformy Microsoft identity. Podrobnosti o různých kódech chyb najdete v tématu [kódy chyb ověřování a autorizace](./reference-aadsts-error-codes.md).

V MSAL pro Python jsou výjimky zřídka, protože většina chyb je zpracována vrácením hodnoty chyby. `ValueError`Výjimka je vyvolána pouze v případě, že dojde k potížím s postupem, jak se pokoušíte použít knihovnu – například pokud jsou parametry rozhraní API poškozeny.

## <a name="java"></a>[Java](#tab/java)

V MSAL pro jazyk Java existují tři typy výjimek: `MsalClientException` , `MsalServiceException` , a `MsalInteractionRequiredException` ; všechny, které dědí z `MsalException` .

- `MsalClientException` je vyvolána, když dojde k chybě, která je místní pro knihovnu nebo zařízení.
- `MsalServiceException` je vyvolána, když služba tokenů zabezpečení (STS) vrátí chybovou odpověď nebo dojde k jiné chybě sítě.
- `MsalInteractionRequiredException` je vyvolána, pokud je k úspěšnému ověření nutná interakce uživatelského rozhraní.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` zveřejňuje hlavičky HTTP vrácené v požadavcích na službu STS. Přístup k nim přes `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Jeden z běžných stavových kódů vrácených z MSAL pro Java při volání `AcquireTokenSilently()` je `InvalidGrantError` . To znamená, že před vydáním ověřovacího tokenu je nutné provést další zásahy uživatele. Vaše aplikace by měla znovu zavolat knihovnu ověřování, ale v interaktivním režimu odesláním `AuthorizationCodeParameters` nebo `DeviceCodeParameters` pro veřejné klientské aplikace.

Většinou v době, kdy se chyba `AcquireTokenSilently` nezdařila, je to proto, že mezipaměť tokenů nemá token, který by odpovídal vaší žádosti. Přístupové tokeny vyprší za jednu hodinu a `AcquireTokenSilently` pokusí se o získání nového na základě aktualizačního tokenu. V OAuth2 se jedná o tok obnovovacího tokenu. Tento tok může selhat také z různých důvodů, například když správce tenanta nakonfiguruje přísnější zásady přihlašování.

Některé podmínky, jejichž výsledkem je tato chyba, jsou pro uživatele snadno vyřešené. Například může být nutné přijmout podmínky použití. Nebo možná nelze požadavek splnit aktuální konfigurací, protože počítač musí být připojen ke konkrétní podnikové síti.

MSAL zpřístupňuje `reason` pole, které můžete použít k zajištění lepšího uživatelského prostředí. `reason`Pole může například vést k tomu, abyste uživateli oznámili, že platnost hesla vypršela nebo že budou muset vyjádřit souhlas s používáním některých prostředků. Podporované hodnoty jsou součástí  `InteractionRequiredExceptionReason` výčtu:

| Důvod | Význam | Doporučené zpracování |
|---------|-----------|-----------------------------|
| `BasicAction` | Podmínku lze vyřešit interakcí uživatele během interaktivního toku ověřování. | Volání `acquireToken` pomocí interaktivních parametrů |
| `AdditionalAction` | Podmínku lze vyřešit pomocí další nápravné interakce se systémem mimo tok interaktivního ověřování. | Voláním `acquireToken` interaktivních parametrů zobrazíte zprávu s vysvětlením nápravné akce, která má být provedena. Volající aplikace se může rozhodnout skrýt toky, které vyžadují další akci, pokud uživatel pravděpodobně nedokončí akci nápravy. |
| `MessageOnly` | V tuto chvíli nelze vyřešit podmínku. Spustit tok interaktivního ověřování, který zobrazí zprávu s vysvětlením podmínky. | Voláním `acquireToken` interaktivních parametrů zobrazíte zprávu, která vysvětluje podmínku. `acquireToken` Vrátí `UserCanceled` chybu poté, co uživatel přečte zprávu a zavře okno. Aplikace může zvolit, že se mají skrýt toky, které mají za následek zprávu, pokud se uživateli pravděpodobně nebudete moci vytěžit ze zprávy. |
| `ConsentRequired`| Chybí souhlas uživatele nebo byl odvolán. |Zavolejte `acquireToken` pomocí interaktivních parametrů, aby uživatel mohl udělit souhlas. |
| `UserPasswordExpired` | Vypršela platnost hesla uživatele. | Volání `acquireToken` s interaktivním parametrem, aby uživatel mohl resetovat svoje heslo |
| `None` |  Další podrobnosti jsou k dispozici. Tuto podmínku může vyřešit interakce uživatele během toku interaktivního ověřování. | Volání `acquireToken` pomocí interaktivních parametrů |

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

Úplný seznam chyb MSAL pro iOS a macOS najdete v části [výčet MSALError](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Všechny chyby MSAL jsou vráceny s `MSALErrorDomain` doménou.

V případě systémových chyb vrátí MSAL originál `NSError` z rozhraní API systému. Pokud se například získání tokenu nezdařilo z důvodu nedostatku síťového připojení, MSAL vrátí chybu s `NSURLErrorDomain` doménou a `NSURLErrorNotConnectedToInternet` kódem.

Na straně klienta doporučujeme zpracovat alespoň následující dvě chyby MSAL:

- `MSALErrorInteractionRequired`: Uživatel musí provést interaktivní požadavek. Existuje mnoho podmínek, které mohou vést k této chybě, jako je například relace ověřování s vypršenou platností nebo nutnost dalších požadavků na ověření. Pro obnovení zavolejte rozhraní API pro získání interaktivního tokenu MSAL. 

- `MSALErrorServerDeclinedScopes`: Některé nebo všechny obory byly odmítnuty. Rozhodněte, zda chcete pokračovat pouze s udělenými obory, nebo Zastavte proces přihlášení.

> [!NOTE]
> `MSALInternalError`Výčet by měl být použit pouze pro referenci a ladění. Nepokoušejte se tyto chyby automaticky zpracovat za běhu. Pokud vaše aplikace narazí na jakékoli chyby, `MSALInternalError` ke kterým došlo, můžete zobrazit obecné informace o uživateli, které objasňují, co se stalo.

Například `MSALInternalErrorBrokerResponseNotReceived` znamená, že uživatel nedokončil ověřování a ručně se vrátil do aplikace. V takovém případě by se měla v aplikaci zobrazit obecná chybová zpráva s vysvětlením, že ověřování nebylo dokončeno a navrhuje, aby se znovu pokusila o ověření.

Následující vzorový kód v jazyce C předvádí osvědčené postupy pro zpracování některých běžných chybových podmínek:

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

## <a name="conditional-access-and-claims-challenges"></a>Podmíněný přístup a výzvy k deklaracím

Při tichém získávání tokenů může vaše aplikace obdržet chyby, když se v rozhraní API, ke kterému se snažíte získat přístup, vyžaduje zásada MFA [, jako je](../azuread-dev/conditional-access-dev-guide.md) například zásada MFA.

Vzor pro zpracování této chyby je interaktivní získání tokenu pomocí MSAL. Při interaktivním získání tokenu se uživateli zobrazí výzva, aby si vyžádali splnění požadovaných zásad podmíněného přístupu.

V některých případech při volání rozhraní API, které vyžaduje podmíněný přístup, můžete z rozhraní API obdržet výzvu s deklarací identity. Pokud má například zásada podmíněného přístupu spravované zařízení (Intune), bude mít tato chyba něco jako [AADSTS53000: vaše zařízení musí být spravované pro přístup k tomuto prostředku](reference-aadsts-error-codes.md) nebo podobným způsobem. V takovém případě můžete deklarace identity předat voláním metody získat token, aby se uživateli zobrazila výzva, aby splnila příslušné zásady.

### <a name="net"></a>.NET

Při volání rozhraní API, které vyžaduje podmíněný přístup ze MSAL.NET, bude vaše aplikace muset zpracovat výjimky pro výzvy k deklaracím identity. Tato vlastnost se zobrazí jako [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) , kde vlastnost [deklarací](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) nebude prázdná.

Pro zpracování výzvy deklarací identity budete muset použít `.WithClaim()` metodu `PublicClientApplicationBuilder` třídy.

### <a name="javascript"></a>JavaScript

Při tichém získávání tokenů (pomocí `acquireTokenSilent` ) pomocí MSAL.js může vaše aplikace obdržet chyby, [Conditional Access claims challenge](../azuread-dev/conditional-access-dev-guide.md) když rozhraní API, ke kterému se snažíte získat přístup, vyžaduje zásadu MFA, jako je například zásada MFA.

Vzor pro zpracování této chyby je provést interaktivní volání metody získat token v MSAL.js, jako je například `acquireTokenPopup` nebo `acquireTokenRedirect` jako v následujícím příkladu:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

K interaktivnímu získání tokenu se uživateli zobrazí výzva a dává jim možnost splnit požadované zásady podmíněného přístupu.

Při volání rozhraní API, které vyžaduje podmíněný přístup, můžete z rozhraní API obdržet výzvu s deklarací identity. V takovém případě můžete předat deklarace vracené v chybě do `claimsRequest` pole `AuthenticationParameters.ts` třídy, aby splňovala příslušné zásady. 

Další informace najdete v tématu [vyžádání dalších deklarací identity](active-directory-optional-claims.md) .

### <a name="msal-for-ios-and-macos"></a>MSAL pro iOS a MacOS

MSAL pro iOS a macOS umožňuje žádat o konkrétní deklarace ve scénářích interaktivního i tichého získání tokenu.

Chcete-li požádat o vlastní deklarace identity, zadejte `claimsRequest` v `MSALSilentTokenParameters` nebo `MSALInteractiveTokenParameters` .

Další informace najdete v tématu [žádosti o vlastní deklarace identity pomocí MSAL pro iOS a MacOS](request-custom-claims.md) .

## <a name="retrying-after-errors-and-exceptions"></a>Opakování po chybách a výjimkách

Očekává se, že budete při volání MSAL implementovat vlastní zásady opakování. MSAL provádí volání HTTP do služby AAD a občasná selhání mohou nastat, například síť může přijít dolů nebo je server přetížený.  

### <a name="http-error-codes-500-600"></a>Kódy chyb HTTP 500-600

MSAL.NET implementuje jednoduchý mechanismus opakovaného pokusu o chyby s kódy chyb HTTP 500-600.

### <a name="http-429"></a>HTTP 429

Pokud je server tokenu služby (STS) přetížený s příliš velkým počtem požadavků, vrátí chybu HTTP 429 s pomocným parametrem o tom, jak dlouho se můžete pokusit znovu v `Retry-After` poli Response.

### <a name="net"></a>.NET

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
    …
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```
