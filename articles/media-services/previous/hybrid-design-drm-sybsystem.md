---
title: Hybridní návrh subsystému (subsystémů) DRM pomocí mediálních služeb Azure | Dokumenty společnosti Microsoft
description: Toto téma popisuje hybridní návrh subsystémů DRM pomocí služby Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: d2f4ddfbff791fbfeb2eb006a628c0fdeb4fdce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975189"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Hybridní konstrukce subsystémů DRM 

Toto téma popisuje hybridní návrh subsystémů DRM pomocí služby Azure Media Services.

## <a name="overview"></a>Přehled

Azure Media Services poskytuje podporu pro následující tři driody:

* PlayReady
* Widevine (modulární)
* FairPlay

Podpora DRM zahrnuje šifrování DRM (dynamické šifrování) a doručování licencí, přičemž Azure Media Player podporuje všechny 3 DRM jako sdk přehrávačprohlížeče prohlížeče.

Podrobné informace o návrhu a implementaci subsystému DRM/CENC naleznete v dokumentu s názvem [CENC s multi-DRM a řízením přístupu](media-services-cenc-with-multidrm-access-control.md).

I když nabízíme úplnou podporu pro tři systémy DRM, někdy zákazníci potřebují používat různé části své vlastní infrastruktury nebo subsystémů kromě Azure Media Services k vytvoření hybridního subsystému DRM.

Níže jsou uvedeny některé běžné otázky kladené zákazníky:

* "Mohu použít vlastní licenční servery DRM?" (V tomto případě zákazníci investovali do farmy serveru licencí DRM s integrovanou obchodní logikou).
* "Můžu ve službě Azure Media Services používat jenom doručování licencí DRM bez hostování obsahu v AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modularita platformy AMS DRM

Jako součást komplexní cloudové video platformy má DRM Azure Media Services návrh s ohledem na flexibilitu a modularitu. Azure Media Services můžete použít s některou z následujících různých kombinací popsaných v následující tabulce (vysvětlení zápisu použitého v tabulce). 

|**Obsah hosting & původu**|**Šifrování obsahu**|**Doručování licencí DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Třetí strana|
|AMS|Třetí strana|AMS|
|AMS|Třetí strana|Třetí strana|
|Třetí strana|Třetí strana|AMS|

### <a name="content-hosting--origin"></a>Obsah hosting & původu

* AMS: video asset je hostován v AMS a streamování je prostřednictvím koncových bodů streamování AMS (ale ne nutně dynamické balení).
* Třetí strana: video je hostováno a doručováno na platformě streamování třetích stran mimo AMS.

### <a name="content-encryption"></a>Šifrování obsahu

* AMS: šifrování obsahu se provádí dynamicky/ na vyžádání dynamickým šifrováním AMS.
* Třetí strana: šifrování obsahu se provádí mimo AMS pomocí pracovního postupu předběžného zpracování.

### <a name="drm-license-delivery"></a>Doručování licencí DRM

* AMS: DRM licence je dodávána službou doručování licencí AMS.
* Třetí strana: Licence DRM je doručována licenčním serverem DRM jiného výrobce mimo AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurace na základě hybridního scénáře

### <a name="content-key"></a>Klíč obsahu

Pomocí konfigurace klíče obsahu můžete řídit následující atributy dynamického šifrování AMS i služby doručování licencí AMS:

* Klíč obsahu používaný pro dynamické šifrování DRM.
* Obsah licence DRM, který má být dodán prostřednictvím doručovacích služeb licencí: práva, klíč obsahu a omezení.
* Typ **omezení zásad autorizace klíče obsahu: omezení**otevření, IP nebo tokenu.
* Pokud je použit typ **tokenu** **omezení zásad autorizace klíče obsahu**, musí být před vydáním licence splněno omezení zásad **autorizace klíče obsahu.**

### <a name="asset-delivery-policy"></a>Zásady poskytování majetku

Prostřednictvím konfigurace zásad pro doručování majetku můžete řídit následující atributy používané dynamickým balíčkovačem AMS a dynamické šifrování koncového bodu streamování AMS:

* Streamovací protokol a kombinace šifrování DRM, například DASH pod CENC (PlayReady a Widevine), plynulé streamování pod PlayReady, HLS pod Widevine nebo PlayReady.
* Výchozí/vložené adresy URL doručování licencí pro každou z dotčených driod.
* Zda adresy URL získání licencí (LA_URLs) v seznamu stop DASH MPD nebo HLS obsahují řetězec dotazu ID klíče (KID) pro Widevine a FairPlay.

## <a name="scenarios-and-samples"></a>Scénáře a ukázky

Na základě vysvětlení v předchozí části následujících pět hybridních scénářů používá příslušné kombinace zásad konfigurace**zásad doručování** **prostředků klíče**-obsahu (ukázky uvedené v posledním sloupci následují podle tabulky):

|**Obsah hosting & původu**|**Šifrování DRM**|**Doručování licencí DRM**|**Konfigurovat klíč obsahu**|**Konfigurace zásad pro doručování majetku**|**Ukázka**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Ano|Ano|Ukázka 1|
|AMS|AMS|Třetí strana|Ano|Ano|Ukázka 2|
|AMS|Třetí strana|AMS|Ano|Ne|Ukázka 3|
|AMS|Třetí strana|Mimo|Ne|Ne|Ukázka 4|
|Třetí strana|Třetí strana|AMS|Ano|Ne|    

Ve vzorcích funguje ochrana PlayReady jak pro DASH, tak pro plynulé streamování. Níže uvedené adresy URL videa jsou adresy URL plynulého streamování. Chcete-li získat odpovídající adresy URL DASH, stačí připojit "(format=mpd-time-csf)". Můžete použít [azure media test player](https://aka.ms/amtest) k testování v prohlížeči. To vám umožní nakonfigurovat, který streaming protokol použít, pod kterým tech. IE11 a Microsoft Edge v systému Windows 10 podporují PlayReady prostřednictvím EME. Další informace naleznete [v podrobnostech o testovacím nástroji](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Ukázka 1

* Zdrojová (základní) adresa URL:https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (DASH & hladká):https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (DASH):https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL (HLS):https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Ukázka 2

* Zdrojová (základní) adresa URL:https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH & hladká):http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Ukázka 3

* Zdrojová adresa URL:https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & hladká):https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Ukázka 4

* Zdrojová adresa URL:https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & hladká):https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="summary"></a>Souhrn

Stručně řečeno, součásti DRM Azure Media Services jsou flexibilní, můžete je použít v hybridním scénáři správnou konfigurací klíče obsahu a zásad poskytování prostředků, jak je popsáno v tomto tématu.

## <a name="next-steps"></a>Další kroky
Zobrazit studijní cesty služby Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

