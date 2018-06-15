---
title: Při doručování licence na Widevine do služby Azure Media Services pomocí castLabs | Microsoft Docs
description: Tento článek popisuje, jak můžete použít Azure Media Services (AMS) k poskytování datový proud, který je dynamicky šifrovat pomocí PlayReady a Widevine technologiemi DRM AMS. Licence PlayReady pochází z Media Services PlayReady licenčního serveru a licence Widevine doručuje castLabs licenční server.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: cfowler
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: Mingfeiy;willzhan;Juliako
ms.openlocfilehash: aff5b94840e63176358d64a535c9cc0dd9ec617a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788498"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Distribuce licencí Widevine pro Azure Media Services pomocí castLabs
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Přehled
Tento článek popisuje, jak můžete použít Azure Media Services (AMS) k poskytování datový proud, který je dynamicky šifrovat pomocí PlayReady a Widevine technologiemi DRM AMS. Licence PlayReady pochází z Media Services PlayReady licenční server a je doručil licence Widevine **castLabs** licenční server.

K přehrávání streamování obsah chráněný CENC (PlayReady nebo Widevine), můžete použít [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). V tématu [AMP dokumentu](http://amp.azure.net/libs/amp/latest/docs/) podrobnosti.

Následující diagram ukazuje základní Azure Media Services a Architektura integrace castLabs.

![Integrace](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Typických systémových nastavení
* Mediální obsah uložený v AMS.
* Klíč ID obsahu klíče jsou uloženy v castLabs i AMS.
* castLabs a AMS mají součástí ověření tokenu. Následující části popisují tokeny ověřování. 
* Když klient požádá o Streamovat videa, je obsah dynamicky šifrován **Common Encryption** (CENC) a zabalené dynamicky podle AMS technologie Smooth Streaming a POMLČKY. Můžeme také poskytovat šifrování PlayReady M2TS základní stream pro protokol pro streamování HLS.
* Licence PlayReady se načítají AMS licenčního serveru a licence Widevine se načítají z castLabs licenční server. 
* Přehrávač médií automaticky rozhoduje, které licenci k načtení podle funkcí platformy klienta. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Generování tokenů ověřování pro získání licence
CastLabs i AMS podporují formát tokenu JWT (JSON Web Token) použitý k autorizaci licenci. 

### <a name="jwt-token-in-ams"></a>Token JWT v AMS
Následující tabulka popisuje token JWT v AMS. 

| Vystavitel | Řetězec vystavitele z vybraného zabezpečení tokenu služby (STS) |
| --- | --- |
| Cílová skupina |Cílová skupina řetězec z použité služby tokenů zabezpečení |
| Deklarace identity |Sadu deklarací identity |
| Neplatí před |Zahájení platnosti tokenu |
| Platí do |End platnosti tokenu |
| SigningCredentials |Klíč, který je sdílen PlayReady licenční Server castLabs licenční Server a službu tokenů zabezpečení, to může být buď symetrický, nebo asymetrický klíč. |

### <a name="jwt-token-in-castlabs"></a>Token JWT v castLabs
Následující tabulka popisuje token JWT v castLabs. 

| Název | Popis |
| --- | --- |
| optData |Řetězec formátu JSON, který obsahuje informace o vás. |
| CRT |Řetězec formátu JSON, který obsahuje informace o prostředku, jeho licenční informace a přehrávání práva. |
| IAT |Aktuální hodnota datetime v epoch. |
| jti |Jedinečný identifikátor o tento token (každý token může být použit pouze jednou v systému castLabs). |

## <a name="sample-solution-set-up"></a>Nastavení ukázkové řešení
[Ukázkové řešení](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) se skládá ze dvou projektů:

* Konzolovou aplikaci, která slouží k nastavení omezení DRM prostředek už ingestovaný pro technologie PlayReady a Widevine.
* Webovou aplikaci, která předá se tokeny, které může považovat za velmi zjednodušené verzi služby tokenů zabezpečení.

Použití konzolové aplikace:

1. Změňte app.config nastavit přihlašovací údaje AMS, castLabs přihlašovací údaje, konfigurace služby tokenů zabezpečení a sdílený klíč.
2. Odešlete prostředek do AMS.
3. Získat identifikátor UUID z nahrané Asset a změňte 32 řádku v souboru Program.cs:
   
      var objIAsset = _kontextu. Assets.Where (x = > x.Id == "nb:cid:UUID:dac53a5d-1 500-80bd-b864-f1e4b62594cf"). FirstOrDefault();
4. Použijte ID pro pojmenování prostředku v systému castLabs (44 řádku v souboru Program.cs).
   
   Je nutné nastavit ID pro **castLabs**; musí se jednat o jedinečný alfanumerický řetězec.
5. Spusťte program.

Používat webové aplikace (STS):

1. Změňte souboru web.config na instalační program castlabs obchodní ID, nastavení služby tokenů zabezpečení a sdílený klíč.
2. Nasaďte na weby Azure.
3. Přejděte na web.

## <a name="playing-back-a-video"></a>Přehrávání videa
K přehrávání videa šifrován běžné šifrování (PlayReady nebo Widevine), můžete použít [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Při spuštění aplikace konzoly, jsou opakována ID obsahu klíč a adresy URL Manifest.

1. Otevřete novou kartu a spusťte vaší služby tokenů zabezpečení: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Přejděte na [přehrávač médií Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3. Vložte adresu URL streamování.
4. Klikněte **pokročilé možnosti** zaškrtávací políčko.
5. V **ochrany** rozevíracího seznamu vyberte PlayReady nebo Widevine.
6. Vložte token, který jste získali z vaší služby tokenů zabezpečení do textového pole Token. 
   
   Není nutné castLab licenční server "nosiče =" předponu před token. Proto prosím odebrat před odesláním token.
7. Aktualizujte přehrávač.
8. By měl být přehrávání videa.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

