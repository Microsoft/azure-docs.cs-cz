---
title: Hybridní návrh subsystémů DRM pomocí Azure Media Services | Microsoft Docs
description: Toto téma popisuje hybridní návrh subsystémů DRM pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: a48e761c4fb74802c6b1db63884ee192446720a4
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016266"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Hybridní návrh subsystémů DRM

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Toto téma popisuje hybridní návrh subsystémů DRM pomocí Azure Media Services.

## <a name="overview"></a>Přehled

Azure Media Services poskytuje podporu pro následující tři systémy DRM:

* PlayReady
* Widevine (modulární)
* FairPlay

Podpora DRM zahrnuje šifrování DRM (dynamické šifrování) a doručování licencí. Azure Media Player podporuje všechny 3 několikanásobnou jako sadu SDK pro aktéry prohlížeče.

Podrobné postupy týkající se návrhu a implementace subsystému DRM/CENC najdete v dokumentu [s názvem CENC s více technologiemi DRM a Access Control](media-services-cenc-with-multidrm-access-control.md).

I když nabízíme kompletní podporu pro tři systémy DRM, někdy zákazníci potřebují kromě Azure Media Services vytvořit hybridní subsystém DRM i různé části vlastní infrastruktury a subsystémů.

Níže jsou uvedeny některé běžné dotazy, které si zákazníci vyžádali:

* Můžu používat vlastní licenční servery DRM? " (V tomto případě se zákazníci investovali do farmy licenčních serverů DRM s vloženou obchodní logikou).
* "Můžu použít jenom doručování licencí DRM v Azure Media Services bez hostování obsahu v AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modulární platforma AMS DRM

V rámci komplexní cloudové videokamery má Azure Media Services DRM návrh s ohledem na flexibilitu a modularitu. Azure Media Services můžete použít s kteroukoli z následujících kombinací popsaných v následující tabulce (vysvětlení zápisu používaného v tabulce). 

|**Hostování obsahu & původu**|**Šifrování obsahu**|**Doručování licencí DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Třetí strana|
|AMS|Třetí strana|AMS|
|AMS|Třetí strana|Třetí strana|
|Třetí strana|Třetí strana|AMS|

### <a name="content-hosting--origin"></a>Hostování obsahu & původu

* AMS: prostředek videa je hostovaný v AMS a streamování je prostřednictvím koncových bodů streamování AMS (ale ne nutně dynamického balení).
* Třetí strana: video je hostované a doručuje na platformě streamování třetí strany mimo AMS.

### <a name="content-encryption"></a>Šifrování obsahu

* AMS: šifrování obsahu se provádí dynamicky a na vyžádání pomocí dynamického šifrování AMS.
* Třetí strana: šifrování obsahu se provádí mimo AMS pomocí pracovního postupu předběžného zpracování.

### <a name="drm-license-delivery"></a>Doručování licencí DRM

* AMS: licence DRM je poskytována službou doručování licencí AMS.
* Třetí strana: licence DRM se doručuje prostřednictvím licenčního serveru DRM třetí strany mimo AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurace na základě vašeho hybridního scénáře

### <a name="content-key"></a>Klíč obsahu

Prostřednictvím konfigurace klíče obsahu můžete řídit následující atributy dynamického šifrování AMS a služby doručování licencí AMS:

* Klíč obsahu, který se používá pro dynamické šifrování DRM.
* Obsah licence DRM, který bude dodán službami doručování licencí: práva, klíč obsahu a omezení.
* Typ **omezení zásad autorizace klíče obsahu**: otevřená, IP nebo omezení tokenu.
* Pokud se používá typ **tokenu** **omezení zásad autorizace klíče obsahu**, musí být před vystavením licence splněné **omezení zásad autorizace klíče obsahu** .

### <a name="asset-delivery-policy"></a>Zásady doručení assetu

Prostřednictvím konfigurace zásad doručení assetů můžete řídit následující atributy používané dynamickým balíčkem AMS a dynamickým šifrováním koncového bodu pro streamování AMS:

* Kombinace protokolů pro streamování a šifrování DRM, jako je například POMLČKa v CENC (PlayReady a Widevine), hladké streamování v oblasti PlayReady, HLS v oblasti Widevine nebo PlayReady.
* Adresy URL pro doručování s výchozími a vloženými licencemi pro každý ze zúčastněných několikanásobnou
* Zda jsou adresy URL pro získání licence (LA_URLs) v záHLSm znaku MPD nebo v seznamu testů pro Widevine a FairPlay řetězce dotazu obsahující ID klíče (KID).

## <a name="scenarios-and-samples"></a>Scénáře a ukázky

Na základě vysvětlení v předchozí části používají následující pět hybridních scénářů příslušné  - kombinace konfigurace **zásad doručení assetů** obsahu (ukázky uvedené v posledním sloupci následují v tabulce):

|**Hostování obsahu & původu**|**Šifrování DRM**|**Doručování licencí DRM**|**Konfigurovat klíč obsahu**|**Konfigurace zásad doručení assetu**|**Ukázka**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Yes|Yes|Ukázka 1|
|AMS|AMS|Třetí strana|Yes|Yes|Ukázka 2|
|AMS|Třetí strana|AMS|Yes|No|Ukázka 3|
|AMS|Třetí strana|Nenachází|No|No|Ukázka 4|
|Třetí strana|Třetí strana|AMS|Yes|No|    

V ukázkách funguje ochrana PlayReady pro PŘERUŠOVANé i hladké streamování. Níže uvedené adresy URL videa jsou vyhlazené adresy URL streamování. Chcete-li získat odpovídající POMLČKy URL, stačí připojit "(Format = MPD-Time-CSF)". K otestování v prohlížeči můžete použít [přehrávač Azure Media test Player](https://aka.ms/amtest) . Umožňuje vám nakonfigurovat, který protokol streamování se má použít, na kterém tech. IE11 a Microsoft Edge na Windows 10 podporují PlayReady prostřednictvím EME. Další informace najdete v [podrobnostech o testovacím nástroji](./offline-playready-streaming-windows-10.md).

### <a name="sample-1"></a>Ukázka 1

* Adresa URL zdroje (základní): `https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest` 
* PlayReady LA_URL (PŘERUŠOVANé & hladké): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 
* Widevine LA_URL (POMLČKa): `https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4` 
* FairPlay LA_URL (HLS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8` 

### <a name="sample-2"></a>Ukázka 2

* Adresa URL zdroje (základní): https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (PŘERUŠOVANé & hladké): `http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx` 

### <a name="sample-3"></a>Ukázka 3

* Zdrojová adresa URL: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (PŘERUŠOVANé & hladké): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 

### <a name="sample-4"></a>Ukázka 4

* Zdrojová adresa URL: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (PŘERUŠOVANé & hladké): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx` 

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="summary"></a>Souhrn

V souhrnu Azure Media Services komponenty DRM flexibilní, můžete je použít v hybridním scénáři tím, že správně nakonfigurujete klíč obsahu a zásady doručování prostředků, jak je popsáno v tomto tématu.

## <a name="next-steps"></a>Další kroky
Zobrazení Media Servicesch cest výuky.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]