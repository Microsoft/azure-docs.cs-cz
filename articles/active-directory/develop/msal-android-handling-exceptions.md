---
title: Chyby a výjimky (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se zpracovávat chyby a výjimky, podmíněný přístup a problémy s deklaracemi v aplikacích pro Android MSAL.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: 4185206e92a78c2684ba1690f03700ef2532cc5e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761391"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>Zpracování výjimek a chyb v MSAL pro Android

Výjimky v knihovně Microsoft Authentication Library (MSAL) jsou určeny k usnadnění řešení potíží vývojářům aplikací. Zprávy výjimek nejsou lokalizovány.

Při zpracování výjimek a chyb můžete použít samotný typ výjimky a kód chyby k rozlišení mezi výjimkami.  Seznam kódů chyb najdete v tématu [kódy chyb ověřování a autorizace](reference-aadsts-error-codes.md).

Během přihlašování se může zobrazit chyba týkající se souhlasu, podmíněného přístupu (MFA, Správa zařízení, omezení na základě umístění), vystavení a uplatnění tokenu a vlastností uživatele.


|Error – třída | Příčina/chyba řetězce| Postup zpracování |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`: Obnovovací token použitý k uplatnění přístupového tokenu je neplatný, vypršela jeho platnost nebo byl odvolán. Tato výjimka může být způsobena změnou hesla. </li><li>`NO_TOKENS_FOUND`: Přístupový token neexistuje a nelze najít žádný obnovovací token pro uplatnění přístupového tokenu.</li> <li>Vyžaduje se krokování<ul><li>MFA</li><li>Chybějící deklarace</li></ul></li><li>Blokováno podmíněným přístupem (například vyžaduje se instalace [zprostředkovatele ověřování](./msal-android-single-sign-on.md) )</li><li>`NO_ACCOUNT_FOUND`: V mezipaměti není k dispozici žádný účet pro tiché ověřování.</li></ul> |Zavolejte `acquireToken()` na výzvu, aby uživatel zadal své uživatelské jméno a heslo, a případně souhlas a provede Multi-Factor Authentication.|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: Uživatel nebo server nepřijal všechny obory. Server může odmítnout rozsah, pokud požadovaný obor není podporován, nebyl rozpoznán nebo není podporován pro konkrétní účet. </li></ul>| Vývojář by se měl rozhodnout, jestli chcete pokračovat v ověřování s udělenými obory nebo ukončit proces ověřování. Možnost znovu odeslat žádost o získání tokenu pouze pro udělené obory a poskytnout nápovědu pro udělení oprávnění předáním `silentParametersForGrantedScopes` a voláním `acquireTokenSilent` . |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`: V této žádosti chybí povinný parametr, obsahuje neplatný parametr, obsahuje parametr více než jednou, nebo je jinak poškozen. </li><li>`SERVICE_NOT_AVAILABLE`: Reprezentuje 500/503/506 kódů chyb z důvodu nefunkční služby. </li><li>`UNAUTHORIZED_REQUEST`: Klient nemá autorizaci k vyžádání autorizačního kódu.</li><li>`ACCESS_DENIED`: Vlastník prostředku nebo autorizační Server odepřel požadavek.</li><li>`INVALID_INSTANCE`: `AuthorityMetadata` ověření se nezdařilo.</li><li>`UNKNOWN_ERROR`: Požadavek na server se nezdařil, ale není k dispozici žádná chyba a `error_description` vrátí se ze služby.</li><ul>| Tato třída výjimky reprezentuje chyby při komunikaci se službou, může být z koncových bodů autorizace nebo tokenu. MSAL přečte chybu a error_description z odpovědi serveru. Obecně jsou tyto chyby vyřešeny opravou konfigurací aplikace buď v kódu, nebo na portálu pro registraci aplikací. Výjimečné výpadky služby můžou toto upozornění aktivovat, což se dá zmírnit jenom čekáním, až se služba obnoví.  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`: Bylo nalezeno více záznamů mezipaměti a sada SDK nemůže identifikovat správný přístup nebo obnovovací token z mezipaměti. Tato výjimka obvykle indikuje chybu v sadě SDK pro ukládání tokenů nebo že autorita není uvedena v tichém požadavku a je nalezeno více shodných tokenů. </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: Na zařízení není k dispozici žádná aktivní síť. </li><li>`JSON_PARSE_FAILURE`: Sada SDK nemohla analyzovat formát JSON.</li><li>`IO_ERROR`: `IOException` došlo k chybě zařízení nebo sítě. </li><li>`MALFORMED_URL`: Adresa URL je poškozená. Pravděpodobnou příčinou při vytváření žádosti o ověření, autority nebo identifikátoru URI pro přesměrování. </li><li>`UNSUPPORTED_ENCODING`: Kódování není zařízením podporováno. </li><li>`NO_SUCH_ALGORITHM`: Algoritmus používaný k vygenerování výzvy [PKCE](https://tools.ietf.org/html/rfc7636) není podporován. </li><li>`INVALID_JWT`: `JWT` vrácený serverem není platný nebo je prázdný nebo má špatný formát. </li><li>`STATE_MISMATCH`: Stav z odpovědi na autorizaci se neshodoval s stavem v žádosti o autorizaci. V případě žádostí o autorizaci SDK ověří stav vrácený při přesměrování a ten odeslaný v žádosti. </li><li>`UNSUPPORTED_URL`: Nepodporovaná adresa URL, nejde provést ověření autority ADFS. </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: Autorita není podporována pro ověření autority. Sada SDK podporuje B2C autority, ale nepodporuje ověření autority B2C. Podporován bude pouze dobře známý hostitel. </li><li>`CHROME_NOT_INSTALLED`: Chrom není v zařízení nainstalovaný. Sada SDK používá vlastní kartu Chrome pro žádosti o autorizaci, pokud jsou k dispozici, a vrátí se do prohlížeče Chrome. </li><li>`USER_MISMATCH`: Uživatel zadaný v žádosti o získání tokenu se neshoduje s uživatelem vráceným ze serveru.</li></ul>|Tato třída výjimky představuje obecné chyby, které jsou místní pro knihovnu. Tyto výjimky lze zpracovat opravou požadavku.|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: Uživatel zahájil interaktivní tok a před přijetím tokenů, jejichž žádost zrušila zpět. </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`: Autorita musí být určena pro `acquireTokenSilent` .</li></ul>|Tyto chyby můžou zmírnit správné argumenty vývojářů a zajistit, aby se poskytla aktivita pro interaktivní ověřování, zpětné volání dokončení, obory a účet s platným ID.|


## <a name="catching-errors"></a>Zachycení chyb

Následující fragment kódu ukazuje příklad zachycení chyb v případě tichého `acquireToken` volání.

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [protokolování v MSAL pro Android](msal-logging-android.md).