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
ms.date: 04/27/2019
ms.author: juliako
ms.openlocfilehash: 78e3897ec653326bcd88a538a6ea7d33938659b9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761951"
---
# <a name="dynamic-packaging"></a>Dynamické balení

Microsoft Azure Media Services umožňuje doručovat mnoho média zdrojového formátů souborů, formáty, streamování médií a ochrana obsahu formáty do různých technologií klienta (třeba iOS a XBOX). Tito klienti pochopit různé protokoly, například iOS vyžaduje, že formát protokolu HTTP Live Streaming (HLS) a služby Xbox vyžadují, technologie Smooth Streaming. Pokud máte sadu s adaptivní přenosovou rychlostí (s více přenosovými rychlostmi) MP4 soubory (ISO Base médií 14496-12) nebo sadu s adaptivní přenosovou rychlostí soubory technologie Smooth Streaming, které mají sloužit ke klientům, kteří pochopit, HLS, MPEG DASH nebo Smooth Streaming, můžete využít výhod  **Dynamické balení**. Balení je nezávislé na rozlišení, jsou podporovány SD/HD, High Density/UHD - 4 kB.

Ve službě Media Services [koncový bod streamování](streaming-endpoint-concept.md) představuje dynamické (just-in-time) obalu a původu službu, která může doručovat obsah na vyžádání a živé přímo do klientské aplikace přehrávače, jedním z běžných datových proudů mediálních protokolů (HLS nebo DASH). Dynamické balení je funkce, která se dodává ve všech standardních **koncové body streamování** (Standard nebo Premium). 

Abyste mohli využívat **dynamické balení**, musíte mít **Asset** sadu souborů MP4 a datových proudů konfigurační soubory potřebné pro dynamické balení Media Services. Jedním ze způsobů, jak tyto soubory získat, je zakódovat vaše soubory mezzanine (zdrojové soubory) pomocí Media Services. Chcete-li videa v zakódovanému Assetu dostupných klientů pro přehrávání, budete muset vytvořit **Lokátor streamování** a vytvoření adresy URL pro streamování. Potom podle formátu určeného v manifestu streamování klienta (HLS, DASH nebo Smooth), můžete datový proud obdrželi v protokolu, kterou jste zvolili.

Díky tomu pak stačí uložit (a platit) soubory pouze v jednom úložném formátu a služba Media Services bude sestavovat a dodávat vhodný formát streamování v reakci na požadavky klientů. 

Ve službě Media Services se používá dynamické balení, zda jsou streamování živě nebo na vyžádání. 

## <a name="common-on-demand-workflow"></a>Běžným pracovním postupem na vyžádání

Toto je běžné Media Services pracovní postup streamování, ve kterém se používá dynamické balení.

1. Nahrání vstupního souboru (označované jako soubor mezzanine). Například MP4, MOV nebo MXF (seznam podporovaných formátů naleznete v části [formátů podporovaných Media Encoder Standard](media-encoder-standard-formats.md).
2. Zakódujte váš soubor mezzanine do sady H.264 MP4 s adaptivní přenosovou rychlostí.
3. Publikujte asset, který obsahuje s adaptivní přenosovou sady souborů MP4. Publikujete tak, že vytvoříte **Lokátor streamování**.
4. Vytvoření adres URL, které cílí různé formáty (HLS, Dash nebo Smooth Streaming). **Koncový bod streamování** by postará obsluhovat správný manifest a žádostí o těchto různých formátech.

Následující diagram znázorňuje streamování na vyžádání s dynamickým vytvářením paketů pracovního postupu.

![Dynamické balení](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Kódování s adaptivní přenosovou rychlostí soubory MP4 rychlostmi

Informace o [kódování videa pomocí Media Services](encoding-concept.md), najdete v následujících příkladech:

* [Kódování z adresy URL HTTPS pomocí předdefinované předvolby](job-input-from-http-how-to.md)
* [Kódování pomocí předdefinované předvolby místního souboru](job-input-from-local-file-how-to.md)
* [Vytvoření vlastního přednastaveny tak, aby cílit na konkrétní scénář nebo zařízení požadavky](customize-encoder-presets-how-to.md)

Seznam kodéru Media Encoder Standard formáty a kodeky, naleznete v tématu [formáty a kodeky](media-encoder-standard-formats.md)

## <a name="common-live-streaming-workflow"></a>Běžné pracovní postup živého streamování

Tady jsou kroky pro pracovní postup živého streamování:

1. Vytvoření [živá událost](live-events-outputs-concept.md).
1. Získání adresy URL ingestování a konfiguraci vaší místní kodér použití adresy URL k odeslání příspěvku informačního kanálu.
1. Získat adresu URL ve verzi preview a použít ho k ověření, že je ve skutečnosti přijímají vstup z kodéru.
1. Vytvořte nový **Asset**.
1. Vytvoření **Live výstup** a používat název assetu, kterou jste vytvořili.<br/>**Live výstup** bude archivovat do datového proudu **Asset**.
1. Vytvoření **Lokátor streamování** pomocí integrované **streamování zásad** typy.<br/>Pokud máte v úmyslu šifrování obsahu, přečtěte si [Content protection přehled](content-protection-overview.md).
1. V seznamu cest **Lokátor streamování** získat zpět adresy URL používat.
1. Získání názvu hostitele pro **koncový bod streamování** chcete z datového proudu.
1. Vytvoření adres URL, které cílí různé formáty (HLS, Dash nebo Smooth Streaming). **Koncový bod streamování** by postará obsluhovat správný manifest a žádostí o těchto různých formátech.

Živá událost může být jeden ze dvou typů: Předávací tak pro živé kódování. Podrobnosti o živém streamování v Media Services v3 najdete v tématu [živého streamování přehled](live-streaming-overview.md).

Následující diagram znázorňuje živé streamování s dynamickým vytvářením paketů pracovního postupu.

![Průchozí](./media/live-streaming/pass-through.svg)

## <a name="delivery-protocols"></a>Doručovací protokoly

|Protocol|Příklad:|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Video kodeků podporuje dynamické balení

Dynamické balení podporuje soubory MP4, které obsahují video zakódovány [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (AVC MPEG-4 nebo AVC1), [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC hev1 nebo hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Zvukových kodeků podporuje dynamické balení

Dynamické balení podporuje soubory MP4, které obsahují zvuk zakódovány [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE-AAC), [Dolby digitální Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(rozšířené AC-3 nebo E AC3), Dolby Atmos nebo [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, High Density, DTS HD, High Density beze ztrát). Streamování obsahu Dolby Atmos se podporuje u standardy, jako jsou MPEG-DASH protokol Common streamování formátu (CSF) nebo společné média aplikace formátu (CMAF) fragmentovaného MP4 a přes HTTP Live Streaming (HLS) s CMAF.

> [!NOTE]
> Dynamické balení nepodporuje soubory, které obsahují [Dolby digitální](https://en.wikipedia.org/wiki/Dolby_Digital) zvuku (AC3) (je starší verze kodek).

## <a name="dynamic-encryption"></a>Dynamické šifrování

**Dynamické šifrování** umožňuje dynamicky šifrovat živě nebo na vyžádání obsahu pomocí AES-128 nebo některý z systémy tři hlavní digital rights management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM (PlayReady, Widevine a FairPlay) licence autorizovaným klientům. Další informace najdete v tématu [dynamického šifrování](content-protection-overview.md).

## <a name="manifests"></a>Manifesty 
 
Služba Media Services podporuje HLS, MPEG DASH, Smooth Streaming protokoly. Jako součást **dynamické balení**, streamování manifesty klientů (seznam stop hlavní HLS, DASH média prezentace popis (MPD) a technologie Smooth Streaming) jsou dynamicky generován a základě modulu pro výběr formátu v adrese URL. Zobrazit protokoly doručování v [v této části](#delivery-protocols). 

Soubor manifestu obsahuje, jako datový proud metadat: sledování typu (zvuk, video nebo text), sledování název, počáteční a koncový čas, s přenosovou rychlostí (Vlastnosti), sledování jazyků, prezentace okno (posuvné okno pevnou platnost), kodek videa (FourCC). Nastaví také přehrávače pro načtení další fragment tím, že poskytuje informace o tyto další možné přehrát video fragmenty dostupné a jejich umístění. Fragmenty (nebo segmenty) jsou skutečné "bloky" obsah videa.

### <a name="hls-master-playlist"></a>Seznam testů hlavní HLS

Tady je příklad souboru manifestu HLS: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="dash-media-presentation-description-mpd"></a>Popis prezentace média pomlčka (MPD)

Tady je příklad DASH manifestu:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
### <a name="smooth-streaming"></a>Smooth Streaming

Tady je příklad manifestu technologie Smooth Streaming:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="dynamic-manifest"></a>Dynamic Manifest

Dynamické filtrování se používá k řízení počet stop, formáty, přenosových rychlostí a prezentace časových oken, které se pošlou hráči. Další informace najdete v tématu [filtrů a dynamických manifestů](filters-dynamic-manifest-overview.md).

> [!NOTE]
> Aktuálně nemůžete spravovat prostředky v3 pomocí webu Azure Portal. Použijte rozhraní [REST API](https://aka.ms/ams-v3-rest-ref), [rozhraní příkazového řádku](https://aka.ms/ams-v3-cli-ref) nebo některou z podporovaných sad [SDK](media-services-apis-overview.md#sdks).

## <a name="ask-questions-give-feedback-get-updates"></a>Klást otázky, váš názor, získávat aktualizace

Podívejte se [komunita Azure Media Services](media-services-community.md) článek a zobrazit různé způsoby můžete klást otázky, poskytnout zpětnou vazbu a aktualizace o Media Services.

## <a name="next-steps"></a>Další postup

[Nahrávání, kódování videa služby stream](stream-files-tutorial-with-api.md)

