---
title: Hybridního návrhu DRM subsystem(s) využívající Azure Media Services | Microsoft Docs
description: Toto téma popisuje hybridního návrhu DRM subsystem(s) využívající Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: willzhan;juliako
ms.openlocfilehash: 1584dab96d1cd772bf04620c68dbe1f133304a1c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="hybrid-design-of-drm-subsystems"></a>Hybridního návrhu DRM subsystem(s)

Toto téma popisuje hybridního návrhu DRM subsystem(s) využívající Azure Media Services.

## <a name="overview"></a>Přehled

Azure Media Services poskytuje podporu pro následující tři DRM systému:

* PlayReady
* Widevine (modulární)
* FairPlay

Podpora DRM zahrnuje šifrování DRM (dynamické šifrování) a doručování licencí, s podporuje všechny 3 technologiemi DRM jako prohlížeč přehrávač SDK Azure Media Player.

Pro podrobné zpracování DRM nebo CENC subsystému návrhu a implementace, najdete v dokumentu s názvem [CENC s více technologiemi DRM a řízením přístupu](media-services-cenc-with-multidrm-access-control.md).

I když jsme plně podporují pro tři systémy DRM, někdy zákazníci muset pomocí různých částí vlastní infrastrukturu nebo subsystémy kromě Azure Media Services můžete vytvářet hybridní subsystému DRM.

Níže jsou některé běžné dotazy požaduje od zákazníků:

* "Je možné pomocí vlastní servery licence DRM?" (V tomto případě zákazníků investovaly do serverové farmy licence DRM s embedded obchodní logiku).
* "Je možné pomocí pouze vaše doručování licencí DRM ve službě Azure Media Services bez hostování obsahu v AMS?"

## <a name="modularity-of-the-ams-drm-platform"></a>Modularitu platformy AMS DRM

V rámci komplexní cloudové platformy, video má Azure Media Services DRM návrh s flexibilitu a modularitu v paměti. Azure Media Services můžete použít s žádným z následujících různé kombinace popsané v následující tabulce (následuje vysvětlení zápis použitý v tabulce). 

|**Hostování obsahu & počátek**|**Šifrování obsahu**|**Doručování licencí DRM**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Třetí strany|
|AMS|Třetí strany|AMS|
|AMS|Třetí strany|Třetí strany|
|Třetí strany|Třetí strany|AMS|

### <a name="content-hosting--origin"></a>Hostování obsahu & počátek

* AMS: asset videa je hostován v AMS a vysílání datového proudu je prostřednictvím koncových bodů streamování AMS (ale nemusí být dynamické balení).
* Třetí strany: video je hostovaná a doručit na platformě streamování třetích stran mimo AMS.

### <a name="content-encryption"></a>Šifrování obsahu

* AMS: šifrování obsahu se provádí dynamicky nebo na vyžádání pomocí dynamického šifrování AMS.
* Třetí strany: šifrování obsahu se provádí mimo AMS použití předem zpracování pracovního postupu.

### <a name="drm-license-delivery"></a>Doručování licencí DRM

* AMS: Licence DRM je doručil doručení licenční služby AMS.
* Třetích stran: Licence DRM doručuje licenčním serverem třetích stran DRM mimo AMS.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurace na základě v hybridním scénáři

### <a name="content-key"></a>Klíč obsahu

Prostřednictvím konfigurace klíč obsahu můžete řídit následujícími atributy AMS dynamického šifrování a doručení licenční služby AMS:

* Klíč obsahu pro dynamické šifrování DRM.
* Obsah licence DRM tak, aby přijímá služeb doručování licence: práva, klíče k obsahu a omezení.
* Typ **obsahu omezení zásad autorizace pro klíč**: otevřete, IP adresu nebo omezení s tokenem.
* Pokud **tokenu** typ **slouží k omezení zásad autorizace klíče obsahu**, **obsahu omezení zásad autorizace pro klíč** musí být splněné před vydáním licenci.

### <a name="asset-delivery-policy"></a>Zásady doručení assetu

Prostřednictvím konfigurace zásady pro doručení assetu můžete řídit následujícími atributy používané dynamické Balíčkovač AMS a dynamické šifrování koncový bod streamování AMS:

* Streamování kombinace šifrování DRM a protokolu, jako je například DASH pod CENC (PlayReady a Widevine), funkce smooth streaming v rámci technologie PlayReady, HLS pod Widevine nebo PlayReady.
* Výchozí nebo vložených licence doručení adresy URL pro každou související se situací technologiemi DRM.
* Jestli licence získání adresy URL (LA_URLs) v DASH MPD nebo HLS seznam stop obsahovat řetězec dotazu klíče ID (DĚTSKÝ) pro Widevine a FairPlay, v uvedeném pořadí.

## <a name="scenarios-and-samples"></a>Scénáře a ukázky

Podle vysvětlení v předchozí části, použijte následující pět hybridní scénáře příslušných **klíč obsahu**-**zásady doručení Assetu** kombinace konfigurací (ukázky uvedený v posledním sloupci, postupujte podle kroků v tabulce):

|**Hostování obsahu & počátek**|**Šifrování DRM**|**Doručování licencí DRM**|**Konfigurace klíč obsahu**|**Konfigurace zásad doručení assetu**|**Ukázka**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Ano|Ano|Ukázka 1|
|AMS|AMS|Třetí strany|Ano|Ano|Ukázka 2|
|AMS|Třetí strany|AMS|Ano|Ne|Ukázka 3|
|AMS|Třetí strany|Mimo|Ne|Ne|Ukázka 4|
|Třetí strany|Třetí strany|AMS|Ano|Ne|    

V ukázkách funguje ochrana technologií PlayReady pro DASH a technologie smooth streaming. Video adresy URL. níže jsou technologie smooth streaming adresy URL. Pokud chcete získat odpovídající adresy URL DASH, stačí připojit "(formát = mpd. čas csf)". Můžete použít [testování azure media player](http://aka.ms/amtest) k testování v prohlížeči. Umožňuje nakonfigurovat které protokol pro streamování chcete použít, pod které technologie. Podpora technologie PlayReady prostřednictvím EME IE11 a MS Edge ve Windows 10. Další informace najdete v tématu [podrobné informace o nástroji test](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Ukázka 1

* Adresa URL zdroje (základní): https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* LA_URL PlayReady (DASH & smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (pomlčka): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* LA_URL FairPlay (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Ukázka 2

* Adresa URL zdroje (základní): http://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* LA_URL PlayReady (DASH & smooth): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Ukázka 3

* Adresa URL zdroje: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* LA_URL PlayReady (DASH & smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Ukázka 4

* Adresa URL zdroje: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* LA_URL PlayReady (DASH & smooth): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Souhrn

V souhrnu jsou součástí Azure Media Services DRM flexibilní, můžete je v hybridním scénáři tak, že správně nakonfigurujete klíč obsahu a zásady doručení assetu, jak je popsáno v tomto tématu.

## <a name="next-steps"></a>Další postup
Zobrazení Media Services kurzů.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

