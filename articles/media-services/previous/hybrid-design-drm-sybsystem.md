---
title: Hybridní návrh systému DRM subsystem(s) pomocí Azure Media Services | Dokumentace Microsoftu
description: Toto téma popisuje hybridní návrh systému DRM subsystem(s) pomocí Azure Media Services.
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
ms.date: 02/08/2019
ms.author: willzhan;juliako
ms.openlocfilehash: 349bc45305539578aeebe851cf42df123bbd863e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998201"
---
# <a name="hybrid-design-of-drm-subsystems-legacy"></a>Hybridní návrh subsystémů DRM (starší verze)

Toto téma popisuje hybridní návrh systému DRM subsystem(s) pomocí Azure Media Services.

## <a name="overview"></a>Přehled

Azure Media Services poskytuje podporu pro následující tři DRM systému:

* PlayReady
* Widevine (modulární)
* FairPlay

Podpora DRM zahrnuje šifrování DRM (dynamické šifrování) a naším vlastním doručováním licencí, s Azure Media Player podporuje všechny 3 technologiemi DRM jako prohlížeč přehrávač SDK.

Podrobné zpracování návrh subsystému DRM/CENC a implementaci, najdete v dokumentu s názvem [CENC využívající Multi-DRM a Access Control](media-services-cenc-with-multidrm-access-control.md).

I když můžeme nabízet úplnou podporu pro tři systémy DRM, někdy zákazníci musí pomocí různých částí své vlastní infrastruktury nebo subsystémů kromě Azure Media Services můžete vytvářet hybridní subsystému DRM.

Níže jsou některé běžné dotazy nejčastější zákazníky:

* "Můžu použít vlastní servery licence DRM?" (V tomto případě zákazníci investovali ve farmě serverů licence DRM s logikou vložené business).
* "Můžu použít pouze váš doručování licencí DRM ve službě Azure Media Services bez hostování obsahu v AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modulární struktura platformy AMS DRM

Jako součást komplexní cloudové video platformy Azure Media Services DRM má návrh s použitím flexibilitu a modularitu v úvahu. Azure Media Services můžete použít s žádným z následujících různých kombinacích, které jsou popsané v následující tabulce (následuje vysvětlení notaci používané v tabulce). 

|**Hostování obsahu & zdroji**|**Šifrování obsahu**|**Doručování licencí DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Třetí strany|
|AMS|Třetí strany|AMS|
|AMS|Třetí strany|Třetí strany|
|Třetí strany|Třetí strany|AMS|

### <a name="content-hosting--origin"></a>Hostování obsahu & zdroji

* AMS: asset videa je hostován v AMS a streamování je prostřednictvím koncových bodů streamování AMS (ale ne nutně dynamické balení).
* Třetí strany: video je hostované a doručit na platformě streamování třetích stran mimo AMS.

### <a name="content-encryption"></a>Šifrování obsahu

* AMS: šifrování obsahu se provádí dynamicky nebo na vyžádání pomocí AMS dynamické šifrování.
* Třetí strany: mimo AMS předběžného zpracování pracovního postupu pomocí provádí šifrování obsahu.

### <a name="drm-license-delivery"></a>Doručování licencí DRM

* AMS: Licence DRM dodává službu doručování licencí AMS.
* Třetí strany: Licence DRM dodává licenčního serveru DRM třetích stran mimo AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurace na základě vaší hybridní scénáře

### <a name="content-key"></a>Klíč k obsahu

Prostřednictvím konfigurace klíče k obsahu můžete řídit následující atributy AMS dynamického šifrování a službu doručování licencí AMS:

* Klíč obsahu používají dynamické šifrování DRM.
* Obsah licence DRM doručit pomocí služeb doručování licencí: práva, klíč k obsahu a omezení.
* Typ **omezení zásad autorizace klíče obsahu**: otevřít, IP adresu nebo omezení s tokenem.
* Pokud **token** typ **omezení zásad autorizace klíče obsahu se používá**, **omezení zásad autorizace klíče obsahu** musí splnit před vydáním licenci.

### <a name="asset-delivery-policy"></a>Zásady doručení assetu

Prostřednictvím konfigurace zásad doručení prostředku můžete řídit následující atributy používá dynamické Balíčkovač AMS a dynamického šifrování koncový bod streamování AMS:

* Streaming DRM kombinace šifrování a protokol, jako je čárka v rámci CENC (PlayReady a Widevine), funkce smooth streaming pod PlayReady, HLS pod nebo PlayReady, Widevine.
* Výchozí/vložený licenční adresy URL doručování pro každou používané technologiemi DRM.
* Určuje, zda licenční získání adresy URL (LA_URLs) DASH MPD nebo HLS stop obsahovat řetězec dotazu klíče ID (děti) pro Widevine a FairPlay, v uvedeném pořadí.

## <a name="scenarios-and-samples"></a>Scénáře a ukázky

Podle vysvětlení v předchozí části, následujících pět hybridních scénářů použití příslušných **klíče k obsahu**-**zásady doručení Assetu** kombinace konfigurací (ukázky podle poslední podle sloupce v tabulce):

|**Hostování obsahu & zdroji**|**Šifrování DRM**|**Doručování licencí DRM**|**Konfigurovat klíče k obsahu**|**Konfigurace zásad doručení assetu**|**Ukázka**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Ano|Ano|Ukázka 1|
|AMS|AMS|Třetí strany|Ano|Ano|Ukázka 2|
|AMS|Třetí strany|AMS|Ano|Ne|Ukázka 3|
|AMS|Třetí strany|Vnější|Ne|Ne|Ukázka 4|
|Třetí strany|Třetí strany|AMS|Ano|Ne|    

V ukázkách ochranou PlayReady vyhovuje DASH a smooth streaming. Video níže uvedených adres URL jsou technologie smooth streaming adresy URL. Získat odpovídající adresy URL DASH, stačí přidat "(format = mpd-time-csf)". Můžete použít [testování azure media player](https://aka.ms/amtest) otestovat v prohlížeči. Umožňuje vám nakonfigurovat které protokol streamování chcete použít, podle které tech. Podpora technologie PlayReady přes EME IE11 a Microsoft Edge ve Windows 10. Další informace najdete v tématu [podrobné informace o nástroji test](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Ukázka 1

* Adresa URL zdroje (base): https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (DASH a smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (pomlčka): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Ukázka 2

* Adresa URL zdroje (base): http://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH a smooth): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Ukázka 3

* Zdrojová adresa URL: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH a smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Ukázka 4

* Zdrojová adresa URL: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH a smooth): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Souhrn

Stručně řečeno součásti Azure Media Services DRM jsou flexibilní, můžete použít je v hybridním scénáři tím, že správně nakonfigurujete klíče k obsahu a zásady doručení assetu, jak je popsáno v tomto tématu.

## <a name="next-steps"></a>Další postup
Postupy výuky zobrazení Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

