---
title: Kódy chyb Azure Media Player
description: Odkaz na kód chyby pro Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 89f063ab50cfd880552e80a77ba798e4d06de5b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709576"
---
# <a name="error-codes"></a>Kódy chyb #

Když přehrávání nepůjde spustit nebo se zastavilo, aktivuje se událost chyby a `error()` funkce vrátí kód a volitelnou zprávu, aby vývojář aplikace mohl získat další podrobnosti. `error().message` není zobrazena zpráva uživateli.  Zpráva zobrazená uživateli je založena na `error().code` službě bits 27-20, viz tabulka níže.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Kódy chyb, bity [31-28] (4 bity) ##

Popište oblast chyby.

- 0 – Neznámé
- 1 – AMP
- 2 – AzureHtml5JS
- 3 – bliknutí
- 4 – Silverlight
- 5. Html5
- 6 – Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Kódy chyb, bity [27-0] (28 bitů) ##

Popište podrobnosti o chybě. bity 27-20 poskytují vysokou úroveň, bity 19-0 poskytují více podrobností, pokud jsou k dispozici.


| amp. errorCode. název | Kódy, bity [27-0] (28 bitů) | Description |
|---|---:|---|
| **Rozsah chyb MEDIA_ERR_ABORTED (0x0100000-0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | Obecná chyba přerušení |
| abortedErrNotImplemented | 0x0100001 | Chyba přerušení, neimplementováno |
| abortedErrHttpMixedContentBlocked | 0x0100002 | Chyba přerušení, zakázaný smíšený obsah při načítání `http://` datového proudu ze stránky obvykle dochází k zablokování smíšeného obsahu `https://` |
| **Počáteční hodnota chyb MEDIA_ERR_NETWORK (0x0200000-0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | Obecná chyba sítě |
| networkErrHttpBadUrlFormat | 0x0200190 | Chybová odpověď HTTP 400 |
| networkErrHttpUserAuthRequired | 0x0200191 | Chybová odpověď HTTP 401 |
| networkErrHttpUserForbidden | 0x0200193 | Chybová odpověď HTTP 403 |
| networkErrHttpUrlNotFound | 0x0200194 | Chybová odpověď HTTP 404 |
| networkErrHttpNotAllowed | 0x0200195 | Chybová odpověď HTTP 405 |
| networkErrHttpGone | 0x020019A | Chybová odpověď HTTP 410 |
| networkErrHttpPreconditionFailed | 0x020019C | Chybová odpověď HTTP 412 |
| networkErrHttpInternalServerFailure | 0x02001F4 | Chybová odpověď HTTP 500
| networkErrHttpBadGateway | 0x02001F6 | Chybová odpověď HTTP 502 |
| networkErrHttpServiceUnavailable | 0x02001F7 | Chybová odpověď HTTP 503 |
| networkErrHttpGatewayTimeout | 0x02001F8 | Chybová odpověď HTTP 504 |
| networkErrTimeout | 0x0200258 | Chyba časového limitu sítě
| networkErrErr | 0x0200259 | Chybová odpověď síťového připojení |
| **Chyby MEDIA_ERR_DECODE (0x0300000-0x03FFFFF)** | | |
| decodeErrUnknown | 0x0300000 | Obecná chyba dekódování |
| **Chyby MEDIA_ERR_SRC_NOT_SUPPORTED (0x0400000-0x04FFFFF)** | | |
| srcErrUnknown | 0x0400000 | Nepodporovaná obecná zdrojová chyba |
| srcErrParsePresentation | 0x0400001 | Chyba analýzy prezentace |
| srcErrParseSegment | 0x0400002 | Chyba analýzy segmentů |
| srcErrUnsupportedPresentation | 0x0400003 | Prezentace není podporovaná. |
| srcErrInvalidSegment | 0x0400004 | Neplatný segment |
| **Počáteční hodnota chyb MEDIA_ERR_ENCRYPTED (0x0500000-0x05FFFFF)** | | |
| encryptErrUnknown | 0x0500000 | Obecná zašifrovaná chyba | 
| encryptErrDecrypterNotFound | 0x0500001 | Dešifrer se nenašel. |
| encryptErrDecrypterInit | 0x0500002 | Chyba inicializace dešifrování |
| encryptErrDecrypterNotSupported | 0x0500003 | Dešifrovací modul není podporovaný. |
| encryptErrKeyAcquire | 0x0500004 | Nepovedlo se získat klíč |
| encryptErrDecryption | 0x0500005 | Dešifrování segmentu selhalo. |
| encryptErrLicenseAcquire | 0x0500006 | Získání licence se nezdařilo. |
| **Počáteční hodnota chyb SRC_PLAYER_MISMATCH (0x0600000-0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | Obecný aktér bez odpovídajícího odborného hráče pro přehrání zdroje |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |Modul plug-in Flash není nainstalovaný, pokud je nainstalovaná, může zdroj přehrát. *Nebo* Flash 30 je nainstalovaný a přehrávání obsahu AES.  Pokud se jedná o tento případ, zkuste prosím použít jiný prohlížeč. Blesk 30 se nepodporovaný dnes od 7. června. Další podrobnosti najdete v tématu [známé problémy](azure-media-player-known-issues.md) . Poznámka: Pokud je 0x00600003, nejsou nainstalované ani Flash a Silverlight, pokud jsou zadané v techOrder.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | Modul plug-in Silverlight není nainstalován, pokud je nainstalovaná, může zdroj přehrát. Poznámka: Pokud je 0x00600003, nejsou nainstalované ani Flash a Silverlight, pokud jsou zadané v techOrder. |
| | 0x00600003 | Flash a Silverlight nejsou nainstalované, pokud jsou zadané v techOrder. |
| **Neznámé chyby (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | Neznámé chyby |

## <a name="user-error-messages"></a>Chybové zprávy uživatele ##

Zobrazená zpráva uživatele je založena na bitech v kódu chyby 27-20.

- MEDIA_ERR_ABORTED (1) – "přehrávání videa bylo přerušeno"
- MEDIA_ERR_NETWORK (2) – chyba sítě způsobila selhání stažení videa. "
- MEDIA_ERR_DECODE (3)-"přehrávání videa bylo přerušeno z důvodu problému s poškozením nebo vzhledem k tomu, že použité video nepodporovalo váš prohlížeč."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"video se nepovedlo načíst, protože server nebo síť selhala, nebo protože není podporovaný formát."
- MEDIA_ERR_ENCRYPTED (5) – "video je šifrované a nemáme k dekódování klíče."
- SRC_PLAYER_MISMATCH (6) – pro toto video se nenašel žádný kompatibilní zdroj.
- MEDIA_ERR_UNKNOWN (0xFF) – došlo k neznámé chybě.

## <a name="examples"></a>Příklady ##

### <a name="0x10600001"></a>0x10600001 ##

Pro toto video se nenašel žádný kompatibilní zdroj. se zobrazí koncovému uživateli.

Není k dispozici žádný odborný přehrávač, který by mohl přehrávat požadované zdroje, ale pokud je nainstalovaný modul plug-in Flash, je pravděpodobně možné přehrát zdroj.  

### <a name="0x20200194"></a>0x20200194 ###

"Chyba sítě způsobila neúspěšné stažení videa." se zobrazí koncovému uživateli.

AzureHtml5JS se nepodařilo přehrát z odpovědi HTTP 404.

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

### <a name="catching-a-specific-error"></a>Zachycení konkrétní chyby ###

Následující kód zachytává jenom 404 chyb:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)