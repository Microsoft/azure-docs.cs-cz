---
title: Doručování licencí Widevine pro Azure Media Services pomocí castLabs | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete pomocí Azure Media Services (AMS) k zajištění datového proudu, která je dynamicky šifrovat pomocí AMS pomocí technologie PlayReady a Widevine technologiemi DRM. Licence PlayReady pochází ze serveru pro správu licencí Media Services PlayReady a Widevine licence se doručí castLabs licenční server.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: Mingfeiy;willzhan;Juliako
ms.openlocfilehash: 0b3d8759f13f48e5fa95ff709fa283ed41e0ea25
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003206"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Distribuce licencí Widevine pro Azure Media Services pomocí castLabs 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Přehled

Tento článek popisuje, jak můžete pomocí Azure Media Services (AMS) k zajištění datového proudu, která je dynamicky šifrovat pomocí AMS pomocí technologie PlayReady a Widevine technologiemi DRM. Licence PlayReady pochází ze serveru pro správu licencí Media Services PlayReady a licencování Widevine dodává **castLabs** licenční server.

Přehrávání zpět streamování obsahu chráněného službou CENC (PlayReady nebo Widevine), můžete použít [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Zobrazit [AMP dokumentu](http://amp.azure.net/libs/amp/latest/docs/) podrobnosti.

Následující diagram ukazuje základní služby Azure Media Services a architektura castLabs integrace.

![Integrace](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Typické nastavení systému

* Mediální obsah uložený v AMS.
* Klíč ID obsahu klíče jsou uloženy v castLabs a AMS.
* castLabs a AMS mají integrované ověřování pomocí tokenu. Následující části popisují ověřovacích tokenů. 
* Když klient požádá o Streamovat video, obsah bude dynamicky šifrovat pomocí **používat standard Common Encryption** (šifrování CENC) a dynamicky zabalené pomocí AMS technologie Smooth Streaming a DASH. Můžeme také poskytovat šifrování PlayReady M2TS základní stream pro protokol streamování HLS.
* Licence PlayReady je načten z AMS licenční server a licence Widevine je načten z castLabs licenční server. 
* Media Player automaticky určuje, na které licence k načtení podle funkcí platformy klienta. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Ověřování generování tokenů pro získání licence

CastLabs i AMS podporují formát tokenu JWT (JSON Web Token) používá k ověření licence. 

### <a name="jwt-token-in-ams"></a>Token JWT v AMS

Následující tabulka popisuje token JWT v AMS. 

| Vystavitel | Řetězec vystavitele z vybraného zabezpečit službu tokenů (STS) |
| --- | --- |
| Cílová skupina |Cílová skupina řetězec z použité služby tokenů zabezpečení |
| Deklarace identity |Sadu deklarací identity |
| neplatí před |Zahájení platnosti tokenu |
| Platí do |Ukončení platnosti tokenu |
| SigningCredentials |Klíč, který je sdílen mezi PlayReady licenční Server castLabs licenčního serveru a služba tokenů zabezpečení, může se jednat buď symetrický, nebo asymetrický klíč. |

### <a name="jwt-token-in-castlabs"></a>Token JWT v castLabs

Následující tabulka popisuje token JWT v castLabs. 

| Název | Popis |
| --- | --- |
| optData |Řetězec formátu JSON, který obsahuje informace o vás. |
| CRT |Řetězec formátu JSON, který obsahuje informace o prostředku, jeho licenční informace a přehrávání práva. |
| IAT |Aktuální datum a čas v unixovém. |
| jti |Jedinečný identifikátor o tento token (každý token jde použít jenom jednou v systému castLabs). |

## <a name="sample-solution-setup"></a>Ukázkové nastavení řešení

[Ukázkové řešení](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) se skládá ze dvou projektů:

* Konzolovou aplikaci, která slouží k nastavení prostředek již přijaté omezení DRM PlayReady a Widevine.
* Webové aplikace, která předá na tokeny, které může považovat za velmi zjednodušené verzi služby tokenů zabezpečení.

Použití konzolové aplikace:

1. Změna souboru app.config pro nastavení přihlašovacích údajů AMS, castLabs pověření, konfigurace služby tokenů zabezpečení a sdílený klíč.
2. Odeslat prostředek do AMS.
3. Získejte identifikátor UUID z nahraných Asset a změňte 32 řádek v souboru Program.cs:
   
      var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();
4. Použijte AssetId pro pojmenování prostředku v systému castLabs (44 řádek v souboru Program.cs).
   
   Je nutné nastavit AssetId pro **castLabs**; musí být jedinečný alfanumerický řetězec.
5. Spusťte program.

Použití webových aplikací (STS):

1. Změňte soubor web.config pro instalační program castlabs obchodní ID, konfigurace služby STS a sdílený klíč.
2. Nasaďte na web Azure.
3. Přejděte na web.

## <a name="playing-back-a-video"></a>Přehrávání videa

Přehrát video šifrován používat standard common encryption (PlayReady nebo Widevine), můžete použít [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Při spuštění aplikace konzoly, budou se vypisovat obsahu ID klíče a adresy URL manifestu.

1. Otevřete novou kartu a spusťte váš STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Přejděte na [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3. Vložte adresu URL streamování.
4. Klikněte na tlačítko **rozšířené možnosti** zaškrtávací políčko.
5. V **ochrany** rozevíracím seznamu vyberte PlayReady nebo Widevine.
6. Vložte token, který jste získali z vašich tokenů zabezpečení v textovém poli Token. 
   
   Není nutné castLab licenční server "nosiče =" předponu před token. Proto před odesláním token, který odeberte.
7. Aktualizujte přehrávač.
8. By měl přehrávání videa.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

