---
title: Obsah chráněný přehrávačem médií Azure
description: Azure Media Player v současné době podporuje AES-128 bit obálky šifrovaný obsah a běžný šifrovaný obsah.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 64414d3ec31e8763b7c576af93374bf514141fd4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726491"
---
# <a name="protected-content"></a>Chráněný obsah #

Azure Media Player v současné době podporuje šifrovaný obsah a běžný šifrovaný obsah AES-128 bitovou obálku a běžný šifrovaný obsah (prostřednictvím PlayReady a Widevine) nebo šifrovaný obsah prostřednictvím FairPlay. Chcete-li správně přehrávat chráněný obsah, musíte `protectionInfo`sdělit programu Azure Media Player . Tyto informace existují podle zdroje a lze `<source>` je `data-setup`přidat přímo na značku prostřednictvím .  Můžete také přidat `protectionInfo` přímo jako parametr, pokud nastavení zdroje dynamicky.

`protectionInfo`přijímá objekt JSON a zahrnuje:

- `type`: `AES` `PlayReady` nebo `Widevine` nebo nebo`FairPlay`
- `certificateUrl`: to by mělo být přímý odkaz na hostované FairPlay cert

- `authenticationToken`: Toto je pole možností pro přidání nekódovaného ověřovacího tokenu

> [!IMPORTANT]
> Objekt **certificateUrl** je potřebný pouze pro FairPlay DRM.***
>[!NOTE]
> Výchozí techOrder byl změněn tak, aby `html5FairPlayHLS` vyhovoval novým technologiím- konkrétně přehrávání obsahu FairPlay nativně v prohlížečích, které jej podporují (Safari na OSX 8 +). Pokud máte obsah FairPlay k přehrávání **a** změnili jste výchozí techOrder na vlastní ve vaší aplikaci, budete muset přidat tuto novou technologii do svého objektu techOrder. Doporučujeme, abyste jej zahrnuli před silverlightSS, aby se váš obsah nepřehrával přes PlayReady.

## <a name="code-sample"></a>Ukázka kódu ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

nebo s více DRM

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> Ne všechny prohlížeče/platformy jsou schopny přehrávat chráněný obsah. Další informace o podporovaných informacích naleznete v části [Technologie přehrávání.](azure-media-player-playback-technology.md)
> [!IMPORTANT]
> Token předaný do přehrávače je určen pro zabezpečený obsah a používá se pouze pro ověřené uživatele. Předpokládá se, že aplikace používá SSL nebo jinou formu bezpečnostního opatření. Také koncový uživatel je assummed být důvěryhodný nezneužít token; Pokud tomu tak není, zapojte prosím své bezpečnostní odborníky.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)