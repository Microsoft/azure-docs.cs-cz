---
title: Azure Media Player chráněný obsah
description: Azure Media Player aktuálně podporuje šifrovaný obsah obálek AES-128 a běžný zašifrovaný obsah.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 83f144c06c23f3ab5507e3561be4a12350e20a42
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329682"
---
# <a name="protected-content"></a>Chráněný obsah #

Azure Media Player aktuálně podporuje šifrovaný obsah obálek AES-128 a společný šifrovaný obsah (přes PlayReady a Widevine) nebo zašifrovaný obsah prostřednictvím FairPlay. Aby bylo možné správně přehrát chráněný obsah, je nutné sdělit Azure Media Player `protectionInfo` . Tyto informace existují na zdroj a lze je přidat přímo na `<source>` značku prostřednictvím `data-setup` .  V `protectionInfo` případě dynamického nastavování zdroje můžete také přidat přímo jako parametr.

`protectionInfo` přijímá objekt JSON a obsahuje:

- `type`: `AES` nebo `PlayReady` nebo `Widevine` or `FairPlay`
- `certificateUrl`: mělo by to být přímý odkaz na hostovaný FairPlay certifikát.

- `authenticationToken`: Toto je pole možností pro přidání nekódovaného ověřovacího tokenu.

> [!IMPORTANT]
> Objekt **certificateUrl** je potřeba jenom pro Fairplay DRM. * * *
>[!NOTE]
> Výchozí techOrder byl změněn tak, aby vyhovovala novému technickému `html5FairPlayHLS` obsahu, konkrétně k přehrávání FairPlayého obsahu v prohlížečích, které ho podporují (Safari na OSX 8 +). Pokud jste FairPlay obsah pro přehrávání **a** v aplikaci jste změnili výchozí techOrder na vlastní, budete ho muset přidat do svého objektu techOrder. Doporučujeme, abyste ho zahrnuli před Silverlightem, takže váš obsah nebude přehrán přes PlayReady.

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

nebo, s více DRM

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
> Ne všechny prohlížeče/platformy umožňují přehrávání chráněného obsahu. Další informace o tom, co je podporováno, najdete v části [technologie přehrávání](azure-media-player-playback-technology.md) .
> [!IMPORTANT]
> Token předaný do přehrávače je určen pro zabezpečený obsah a používá se pouze pro ověřené uživatele. Předpokládá se, že aplikace používá protokol SSL, nebo jinou formu bezpečnostního opatření. Také je možné, aby koncový uživatel byl assummed důvěryhodný, aby token nezneužitíal. v takovém případě se prosím zapojíte s odborníky na zabezpečení.

## <a name="next-steps"></a>Další kroky ##

- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)