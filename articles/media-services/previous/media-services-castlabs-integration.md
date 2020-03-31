---
title: Použití castLabs dodat Widevine licence pro Azure Media Services | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak můžete pomocí Azure Media Services (AMS) dodat datový proud, který je dynamicky šifrována AMS s PlayReady a Widevine DRM.
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
ms.date: 03/14/2019
ms.author: Juliako
ms.reviewer: willzhan
ms.openlocfilehash: 29a344c739d8d99da2e5c81d41a11c601e48022e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74969135"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Distribuce licencí Widevine pro Azure Media Services pomocí castLabs 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Přehled

Tento článek popisuje, jak můžete pomocí Azure Media Services (AMS) dodat datový proud, který je dynamicky šifrována AMS s PlayReady a Widevine DRM. Licence PlayReady pochází z licenčního serveru Media Services PlayReady a licence Widevine je dodávána licenčním serverem **castLabs.**

Chcete-li přehrávat streamovaný obsah chráněný cenc (PlayReady a/nebo Widevine), můžete použít [Azure Media Player](https://aka.ms/azuremediaplayer). Podrobnosti najdete v [dokumentu AMP.](https://amp.azure.net/libs/amp/latest/docs/)

Následující diagram znázorňuje architekturu integrace Azure Media Services na vysoké úrovni a castLabs.

![Integrace](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Typické nastavení systému

* Mediální obsah je uložen v AMS.
* ID klíčů obsahu jsou uložena v kolečkách i v AMS.
* castLabs a AMS mají zabudované ověřování tokenů. Následující části popisují ověřovací tokeny. 
* Když klient požádá o streamování videa, obsah je dynamicky šifrován **pomocí společného šifrování** (CENC) a dynamicky zabalen ams na hladké streamování a DASH. Dodáváme také PlayReady M2TS základní stream šifrování pro HLS streaming protokol.
* Licence PlayReady je načtena z licenčního serveru AMS a licence Widevine je načtena z licenčního serveru castLabs. 
* Program Media Player automaticky rozhoduje o tom, kterou licenci má načíst, a to na základě možnosti klientské platformy. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Generování ověřovacího tokenu pro získání licence

Kolečka i AMS podporují formát tokenu JWT (JSON Web Token), který se používá k autorizaci licence. 

### <a name="jwt-token-in-ams"></a>JWT token v AMS

Následující tabulka popisuje token JWT v AMS. 

| Vystavitel | Řetězec vystavittele z vybrané služby Zabezpečených tokenů (STS) |
| --- | --- |
| Cílová skupina |Řetězec publika z použitého STS |
| Deklarace identity |Soubor nároků |
| Není před |Zahájit platnost tokenu |
| Platnost vyprší |Konec platnosti tokenu |
| Podepisování přihlašovacích údajů |Klíč, který je sdílen mezi PlayReady License Server, castLabs License Server a STS, může být buď symetrický nebo asymetrický klíč. |

### <a name="jwt-token-in-castlabs"></a>JWT token v castLabs

Následující tabulka popisuje token JWT v castLabs. 

| Name (Název) | Popis |
| --- | --- |
| optData |Řetězec JSON obsahující informace o vás. |
| Crt |Řetězec JSON obsahující informace o datovém zdroji, jeho informace o licenci a práva na přehrávání. |
| iat |Aktuální datetime v epochě. |
| jti |Jedinečný identifikátor o tomto tokenu (každý token lze použít pouze jednou v systému castLabs). |

## <a name="sample-solution-setup"></a>Nastavení ukázkového roztoku

[Vzorové řešení](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) se skládá ze dvou projektů:

* Konzolová aplikace, kterou lze použít k nastavení omezení DRM pro již ingestovaný prostředek pro playready i widevine.
* Webová aplikace, která rozdává žetony, které by mohly být považovány za velmi zjednodušené verze STS.

Použití konzolové aplikace:

1. Změňte soubor app.config na nastavení přihlašovacích údajů AMS, pověření castLabs, konfigurace SLUŽBY STS a sdíleného klíče.
2. Nahrajte datový zdroj do AMS.
3. Získejte UUID z nahraného datového zdroje a změňte řádek 32 v souboru Program.cs:
   
      var objIAsset = _context. Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault();
4. Použijte AssetId pro pojmenování datového zdroje v systému castLabs (řádek 44 v souboru Program.cs).
   
   Musíte nastavit AssetId pro **castLabs**; musí se jedná o jedinečný alfanumerický řetězec.
5. Spusťte program.

Použití webové aplikace (STS):

1. Změňte web.config na nastavení id obchodníka castlabs, konfiguraci STS a sdílený klíč.
2. Nasazujte se na weby Azure.
3. Přejděte na web.

## <a name="playing-back-a-video"></a>Přehrávání videa

Chcete-li přehrát video zašifrované běžným šifrováním (PlayReady a/nebo Widevine), můžete použít [Azure Media Player](https://aka.ms/azuremediaplayer). Při spuštění aplikace konzoly se zobrazí ID klíče obsahu a adresa URL manifestu.

1. Otevřete novou kartu a spusťte STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Přejděte na [Azure Media Player](https://aka.ms/azuremediaplayer).
3. Vložte do adresy URL streamování.
4. Zaškrtněte políčko **Upřesnit možnosti.**
5. V rozevíracím souboru **Ochrana** vyberte PlayReady a/nebo Widevine.
6. Vložte token, který jste získali z vašeho STS do textového pole Token. 
   
   Licenční server castLab nepotřebuje předponu "Bearer=" před tokenem. Takže prosím, odstraňte, že před odesláním tokenu.
7. Aktualizujte přehrávač.
8. Video by se mělo přehrávat.

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

