---
title: Azure Media Services dynamické balení – přehled | Dokumentace Microsoftu
description: Téma s přehledem výhody dynamického balení ve službě Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: juliako
ms.openlocfilehash: cd4eacc918acdf50bb256077030b86e121f663f0
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985797"
---
# <a name="dynamic-packaging"></a>Dynamické balení

Microsoft Azure Media Services umožňuje doručovat mnoho média zdrojového formátů souborů, formáty, streamování médií a ochrana obsahu formáty do různých technologií klienta (třeba iOS a XBOX). Tito klienti pochopit různé protokoly, například iOS vyžaduje, že formát protokolu HTTP Live Streaming (HLS) a služby Xbox vyžadují, technologie Smooth Streaming. Pokud máte sadu s adaptivní přenosovou rychlostí (s více přenosovými rychlostmi) MP4 soubory (ISO Base médií 14496-12) nebo sadu s adaptivní přenosovou rychlostí soubory technologie Smooth Streaming, které mají sloužit ke klientům, kteří pochopit, HLS, MPEG DASH nebo Smooth Streaming, můžete využít výhod dynamického Balení. Balení je nezávislé na rozlišení, jsou podporovány SD/HD, High Density/UHD - 4 kB.

[Koncové body streamování](streaming-endpoint-concept.md) je služba dynamického balení ve službě Media Services používá k doručování multimediálního obsahu pro klientské přehrávače. Dynamické balení je funkce, která se dodává ve všech standardních **koncové body streamování** (Standard nebo Premium). Neexistuje žádné další náklady spojené s touto funkcí v Media Services v3. 

Abyste mohli využívat **dynamické balení**, musíte mít **Asset** sadu souborů MP4 a soubory manifestu obsahují. Jeden způsob, jak získat soubory se má zakódovat váš soubor mezzanine (zdrojový) pomocí služby Media Services. Pokud chcete, aby v prostředku (pomocí serverových a klientských manifestů a kódované soubory MP4 rychlostmi) k dispozici pro klienty pro přehrávání videa, je nutné vytvořit **Lokátor streamování** a následně vytvořit adresy URL pro streamování. Potom podle formátu určeného v manifestu klienta, můžete datový proud obdrželi v protokolu, kterou jste zvolili.

Díky tomu pak stačí uložit (a platit) soubory pouze v jednom úložném formátu a služba Media Services bude sestavovat a dodávat vhodný formát streamování v reakci na požadavky klientů. 

Ve službě Media Services se používá dynamické balení, zda jsou streamování živě nebo na vyžádání. Následující diagram znázorňuje streamování na vyžádání s dynamickým vytvářením paketů pracovního postupu.

![Dynamické kódování](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

## <a name="common-video-on-demand-workflow"></a>Běžné pracovní postup videa na vyžádání

Toto je běžné Media Services pracovní postup streamování, ve kterém se používá dynamické balení.

1. Nahrání vstupního souboru (označované jako soubor mezzanine). Například H.264 MP4 nebo WMV (seznam podporovaných formátů naleznete v části [formátů podporovaných Media Encoder Standard](media-encoder-standard-formats.md).
2. Zakódujte váš soubor mezzanine do sady H.264 MP4 s adaptivní přenosovou rychlostí.
3. Publikujte asset, který obsahuje s adaptivní přenosovou sady souborů MP4. Publikujete tak, že vytvoříte **Lokátor streamování**.
4. Vytvoření adres URL, které cílí různé formáty (HLS, Dash nebo Smooth Streaming). **Koncový bod streamování** by postará obsluhovat správný manifest a žádostí o těchto různých formátech.

## <a name="encode-to-adaptive-bitrate-mp4s"></a>Kódování s adaptivní přenosovou rychlostí soubory MP4 rychlostmi

Informace o [kódování videa pomocí Media Services](encoding-concept.md), najdete v následujících příkladech:

* [Kódování z adresy URL HTTPS pomocí předdefinované předvolby](job-input-from-http-how-to.md)
* [Kódování pomocí předdefinované předvolby místního souboru](job-input-from-local-file-how-to.md)
* [Vytvoření vlastního přednastaveny tak, aby cílit na konkrétní scénář nebo zařízení požadavky](customize-encoder-presets-how-to.md)

Seznam kodéru Media Encoder Standard formáty a kodeky, naleznete v tématu [formáty a kodeky](media-encoder-standard-formats.md)

## <a name="delivery-protocols"></a>Doručovací protokoly

|Protocol (Protokol)|Příklad:|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Technologie Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Video kodeků podporuje dynamické balení

Dynamické balení podporuje soubory MP4, které obsahují video zakódovány [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (AVC MPEG-4 nebo AVC1), [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC hev1 nebo hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Zvukových kodeků podporuje dynamické balení

Dynamické balení podporuje soubory MP4, které obsahují zvuk zakódovány [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE-AAC), [Dolby digitální Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (rozšířené AC-3 nebo E AC3), nebo [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, High Density, DTS HD, High Density beze ztrát).

> [!NOTE]
> Dynamické balení nepodporuje soubory, které obsahují [Dolby digitální](https://en.wikipedia.org/wiki/Dolby_Digital) zvuku (AC3) (je starší verze kodek).

## <a name="manifest-files-overview"></a>Přehled soubory manifestu

A **manifest** soubor se (seznamem testů) (založený na textu nebo na základě XML) zahrnuje, jako datový proud metadat: sledování typu (zvuk, video nebo text), sledování název, počáteční a koncový čas, s přenosovou rychlostí (Vlastnosti), sledování jazyků (posuvné okno prezentace okno pevnou platnost), video kodek (FourCC). Nastaví také přehrávače pro načtení další fragment tím, že poskytuje informace o tyto další možné přehrát video fragmenty dostupné a jejich umístění. Fragmenty (nebo segmenty) jsou skutečné "bloky" obsah videa.

Tady je příklad souboru manifestu HLS: 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

## <a name="next-steps"></a>Další postup

[Nahrávání, kódování videa služby stream](stream-files-tutorial-with-api.md)

