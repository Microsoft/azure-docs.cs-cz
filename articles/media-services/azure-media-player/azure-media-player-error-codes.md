---
title: Chybové kódy přehrávače Azure Media Player
description: Odkaz na kód chyby pro Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727252"
---
# <a name="error-codes"></a>Kódy chyb #

Když přehrávání nelze spustit nebo se zastavilo, dojde k `error()` chybě a funkce vrátí kód a volitelnou zprávu, která pomůže vývojáři aplikace získat další podrobnosti. `error().message`není zpráva zobrazená uživateli.  Zpráva zobrazená uživateli je `error().code` založena na bitech 27-20, viz tabulka níže.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Chybové kódy, bity [31-28] (4 bity) ##

Popište oblast chyby.

- 0 – Neznámé
- 1 - AMP
- 2 - AzureHtml5JS
- 3 - Záblesky
- 4 - SilverlightSS
- 5 - Html5
- 6 - Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Chybové kódy, bity [27-0] (28 bitů) ##

Popište podrobnosti o chybě, bity 27-20 poskytují vysokou úroveň, bity 19-0 poskytují další podrobnosti, pokud jsou k dispozici.


| amp.errorCode. [jméno] | Kódy, bity [27-0] (28 bitů) | Popis |
|---|---:|---|
| **rozsah chyb MEDIA_ERR_ABORTED (0x0100000 - 0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | Obecná chyba přerušení |
| abortedErrNotImplemented abortedAbort abortEdRNotImplemented aborted | 0x0100001 | Chyba přerušení, není implementována |
| abortedErrHttpMixedContentBlocked | 0x0100002 | Přerušit chybu, smíšený obsah blokován - `http://` obvykle dochází `https://` při načítání datového proudu ze stránky |
| **počáteční hodnota chyb MEDIA_ERR_NETWORK (0x0200000 - 0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | Obecná chyba sítě |
| networkRHttpBadUrlFormat | 0x0200190 | Odpověď na chybu http 400 |
| networkErrHttpUserAuthVyžadováno | 0x0200191 | Odpověď na chybu http 401 |
| networkErrHttpUserForbidden | 0x0200193 | Odpověď na chybu http 403 |
| networkErhttpUrlNotFound | 0x0200194 | Odpověď na chybu http 404 |
| networkErhttpNotAllowed | 0x0200195 | Odpověď na chybu http 405 |
| networkErrHttpGone | 0x020019A | Odpověď na chybu http 410 |
| networkRHttpPreconditionFailed | 0x020019C | Odpověď na chybu http 412 |
| networkRHttpInternalServerFailure | 0x02001F4 | Http 500 odpověď na chybu
| networkErhttpBadGateway | 0x02001F6 | Odpověď na chybu http 502 |
| networkRHttpServiceNení k dispozici | 0x02001F7 | Odpověď na chybu http 503 |
| networkRHttpGatewayTimeout | 0x02001F8 | Odpověď na chybu http 504 |
| networkErrTimeout | 0x0200258 | Chyba časového opojení sítě
| networkErrErr | 0x0200259 | Odpověď na chybu síťového připojení |
| **MEDIA_ERR_DECODE chyby (0x0300000 - 0x03FFFFF)** | | |
| decodeErrUnknown | 0x0300000 | Obecná chyba dekódování |
| **MEDIA_ERR_SRC_NOT_SUPPORTED chyby (0x0400000 - 0x04FFFFF)** | | |
| srcErrNeznámý | 0x0400000 | Chyba obecného zdroje není podporována |
| srcErrParsePrezentace | 0x0400001 | Chyba analýzy prezentace |
| srcErrParseSegment | 0x0400002 | Chyba analýzy segmentu |
| srcErrNepodporovanýPrezentace | 0x0400003 | Prezentace není podporována. |
| srcErrInvalidSegment | 0x0400004 | Neplatný segment |
| **MEDIA_ERR_ENCRYPTED chyby počáteční hodnota (0x0500000 - 0x05FFFFF)** | | |
| encryptErrUnknown | 0x0500000 | Obecná šifrovaná chyba | 
| encryptErrDecrypterNotFound | 0x0500001 | Dešifrovací systém nebyl nalezen. |
| encryptErrDecrypterInit | 0x0500002 | Chyba inicializace dešifrování |
| encryptErrDecrypterNotSupported | 0x0500003 | Dešifrovací program není podporován. |
| encryptErrKeyAcquire | 0x0500004 | Získání klíče se nezdařilo. |
| encryptErrDecryption | 0x0500005 | Dešifrování segmentu se nezdařilo. |
| encryptErrLicenseAcquire | 0x0500006 | Získání licence se nezdařilo. |
| **SRC_PLAYER_MISMATCH počáteční hodnota chyb (0x0600000 - 0x06FFFFF)** | | |
| srcPlayerMismatchNeznámý | 0x0600000 | Obecný žádný odpovídající tech přehrávač hrát zdroj |
| srcPlayerMismatchFlashNotinstalled | 0x0600001 |Flash plugin není nainstalován, je-li nainstalován zdroj může hrát. *NEBO* Flash 30 je nainstalován a přehrává obsah AES.  Pokud se jedná o tento případ, zkuste jiný prohlížeč. Flash 30 je dnes nepodporovaný od 7. Další podrobnosti najdete v [tématu Známé problémy.](azure-media-player-known-issues.md) Poznámka: Pokud 0x00600003, flash a Silverlight nejsou nainstalovány, pokud je uvedeno v techOrder.|
| srcPlayerMismatchSilverlightNotinstalled | 0x0600002 | Silverlight plugin není nainstalován, je-li nainstalován zdroj může hrát. Poznámka: Pokud 0x00600003, flash a Silverlight nejsou nainstalovány, pokud je uvedeno v techOrder. |
| | 0x00600003 | Flash a Silverlight nejsou nainstalovány, pokud je uvedeno v techOrder. |
| **Neznámé chyby (0x0FF00000)** | | |
| errNeznámý | 0xFF00000 | Neznámé chyby |

## <a name="user-error-messages"></a>Chybové zprávy uživatele ##

Zobrazená zpráva uživatele je založena na bitech kódu chyby 27-20.

- MEDIA_ERR_ABORTED (1) -"Přerušili jste přehrávání videa"
- MEDIA_ERR_NETWORK (2) - "Chyba sítě způsobila selhání stahování videa na částečný úvazek."
- MEDIA_ERR_DECODE (3) - "Přehrávání videa bylo přerušeno z důvodu problému s poškozením nebo proto, že použité video funkce, které váš prohlížeč nepodporoval."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"Video nelze načíst, protože server nebo síť selhala nebo protože formát není podporován."
- MEDIA_ERR_ENCRYPTED (5) - "Video je zašifrováno a nemáme klíče k jeho dešifrování."
- SRC_PLAYER_MISMATCH (6) - "Pro toto video nebyl nalezen žádný kompatibilní zdroj."
- MEDIA_ERR_UNKNOWN (0xFF) - "Došlo k neznámé chybě."

## <a name="examples"></a>Příklady ##

### <a name="0x10600001"></a>0x10600001 ##

"Pro toto video nebyl nalezen žádný kompatibilní zdroj." se zobrazí koncovému uživateli.

Neexistuje žádný tech přehrávač, který může hrát požadované zdroje, ale pokud flash plugin je nainstalován, je pravděpodobné, že zdroj by mohl být hrán.  

### <a name="0x20200194"></a>0x20200194 ###

"Chyba sítě způsobila selhání stahování videa na částečný úvazek." se zobrazí koncovému uživateli.

AzureHtml5JS se nepodařilo přehrát z odpovědi http 404.

### <a name="categorizing-errors"></a>Kategorizace chyb ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Zachycení určité chyby ###

Následující kód zachycuje pouze 404 chyb:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Další kroky ##

- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)