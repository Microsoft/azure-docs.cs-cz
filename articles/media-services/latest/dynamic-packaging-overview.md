---
title: Azure Media Services dynamické balení – přehled | Dokumentace Microsoftu
description: Tento článek poskytuje přehled dynamického balení ve službě Azure Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: 4836ec4bb66bbf8ced921dd1095665d004f8a28b
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542578"
---
# <a name="dynamic-packaging"></a>Dynamické balení

Microsoft Azure Media Services umožňuje doručovat mnoho média zdrojového formátů souborů, formáty, streamování médií a ochrana obsahu formáty do různých technologií klienta (třeba iOS a XBOX). Tito klienti pochopit různé protokoly, například iOS vyžaduje, že formát protokolu HTTP Live Streaming (HLS) a služby Xbox vyžadují, technologie Smooth Streaming. Pokud máte sadu s adaptivní přenosovou rychlostí (s více přenosovými rychlostmi) MP4 soubory (ISO Base médií 14496-12) nebo sadu s adaptivní přenosovou rychlostí soubory technologie Smooth Streaming, které mají sloužit ke klientům, kteří pochopit, HLS, MPEG DASH nebo Smooth Streaming, můžete využít výhod  *dynamické balení*. Balení je nezávislé na rozlišení, jsou podporovány SD/HD, High Density/UHD - 4 kB.

Ve službě Media Services [koncový bod streamování](streaming-endpoint-concept.md) představuje dynamické (just-in-time) obalu a původu službu, která může doručovat obsah na vyžádání a živé přímo do klientské aplikace přehrávače, jedním z běžných datových proudů mediálních protokolů (HLS nebo DASH). Dynamické balení je funkce, která se dodává ve všech standardních **koncové body streamování** (Standard nebo Premium). 

Abyste mohli využívat výhody dynamického balení, musíte mít **Asset** sadu souborů MP4 a datových proudů konfigurační soubory potřebné pro dynamické balení Media Services. Jedním ze způsobů, jak tyto soubory získat, je zakódovat vaše soubory mezzanine (zdrojové soubory) pomocí Media Services. Chcete-li videa v zakódovanému Assetu dostupných klientů pro přehrávání, budete muset vytvořit **Lokátor streamování** a vytvoření adresy URL pro streamování. Potom podle formátu určeného v manifestu streamování klienta (HLS, DASH nebo Smooth), můžete datový proud obdrželi v protokolu, kterou jste zvolili.

Díky tomu pak stačí uložit (a platit) soubory pouze v jednom úložném formátu a služba Media Services bude sestavovat a dodávat vhodný formát streamování v reakci na požadavky klientů. 

Ve službě Media Services se používá dynamické balení, zda jsou streamování živě nebo na vyžádání. 

> [!NOTE]
> Aktuálně nemůžete spravovat prostředky v3 pomocí webu Azure Portal. Použijte rozhraní [REST API](https://aka.ms/ams-v3-rest-ref), [rozhraní příkazového řádku](https://aka.ms/ams-v3-cli-ref) nebo některou z podporovaných sad [SDK](media-services-apis-overview.md#sdks).

## <a name="on-demand-streaming-workflow"></a>Pracovní postup streamování na vyžádání

Tady je běžné pracovní postup pro Media Services streamování na vyžádání s dynamickým vytvářením paketů:

1. Nahrajte vstupní nebo zdrojový soubor (volá se *mezzanine* souboru). Mezi příklady patří soubor MP4, MOV nebo MXF. 
1. Zakódujte váš soubor mezzanine do sady H.264 MP4 s adaptivní přenosovou rychlostí. 
1. Publikujte asset, který obsahuje s adaptivní přenosovou sady souborů MP4. Publikujete tak, že vytvoříte Lokátor streamování.
1. Vytvoření adres URL, které cílí různé formáty (HLS, MPEG-DASH a Smooth Streaming). Koncový bod streamování se postará o obsluhující správný manifest a žádostí o různých formátech.

Tento diagram znázorňuje pracovní postup pro streamování na vyžádání s dynamickým vytvářením paketů:

![Diagram pracovního postupu pro streamování na vyžádání díky dynamickému balení](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="live-streaming-workflow"></a>Pracovní postup živého streamování

Živé události může být jeden ze dvou typů: průchozí nebo živé kódování. 

Tady je s běžným pracovním postupem pro živé streamování s dynamickým vytvářením paketů:

1. Vytvoření [živou událost](live-events-outputs-concept.md).
1. Získat adresu URL ingestování a konfiguraci vaší místní kodér použití adresy URL k odeslání příspěvku informačního kanálu.
1. Získat adresu URL ve verzi preview a použít ho k ověření, že se přijímají vstup z kodéru.
1. Vytvoření nového prostředku.
1. Vytvoření živé výstupu a používat název assetu, kterou jste vytvořili.<br />Za výstupní datový proud archivuje do assetu.
1. V rámci předdefinovaných typů datových proudů zásady vytvořte Lokátor streamování.<br />Pokud máte v úmyslu šifrování obsahu, přečtěte si [Content protection přehled](content-protection-overview.md).
1. Seznam cest na Lokátor streamování získat adresy URL používat.
1. Získáte název hostitele pro koncový bod streamování, které chcete z datového proudu.
1. Vytvoření adres URL, které cílí různé formáty (HLS, MPEG-DASH a Smooth Streaming). Koncový bod streamování se postará o obsluhující správný manifest a žádostí o různých formátech.

Tento diagram znázorňuje pracovní postup pro živé streamování s dynamickým vytvářením paketů:

![Diagram pracovního postupu pro průchozí kódování díky dynamickému balení](./media/live-streaming/pass-through.svg)

Informace o živém streamování v Media Services v3 najdete v tématu [živého streamování přehled](live-streaming-overview.md).

## <a name="delivery-protocols"></a>Doručovací protokoly

Můžete použít tyto doručovací protokoly pro váš obsah v dynamické balení Media Services:

|Protocol|Příklad:|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Technologie Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="encode-to-adaptive-bitrate-mp4s"></a>Kódování s adaptivní přenosovou rychlostí soubory MP4 rychlostmi

Následující články popisují příklady [kódování videa pomocí Media Services](encoding-concept.md):

* [Kódování z adresy URL HTTPS pomocí předdefinované předvolby](job-input-from-http-how-to.md)
* [Zakódovat do místního souboru s použitím předdefinované předvolby](job-input-from-local-file-how-to.md)
* [Vytvoření vlastního přednastaveny tak, aby cílit na konkrétní scénář nebo zařízení požadavky](customize-encoder-presets-how-to.md)

Zobrazit seznam kodéru Media Encoder Standard [formáty a kodeky](media-encoder-standard-formats.md).

## <a name="video-codecs"></a>Video kodeků

Dynamické balení podporuje následující video kodeků:
* Soubory MP4, které budou obsahovat video, které jsou zakódovány [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (AVC MPEG-4 nebo AVC1) nebo [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC hev1, nebo hvc1).

## <a name="audio-codecs"></a>Zvukových kodeků

Dynamické balení podporuje následující protokoly pro zvuk:
* Soubory MP4
* Více zvukové stopy

Dynamické balení nepodporuje soubory, které obsahují [Dolby digitální](https://en.wikipedia.org/wiki/Dolby_Digital) zvuku (AC3) (je starší verze kodek).

### <a name="mp4-files"></a>Soubory MP4

Dynamické balení podporuje soubory MP4, které obsahují zvuk, který je zakódován pomocí těchto protokolů: 

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 nebo v2 HE-AAC)
* [Dolby digitální Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC 3 E AC3)
* Dolby Atmos<br />
   Streamování obsahu Dolby Atmos se podporuje u standardy, jako jsou MPEG-DASH protokolu běžné streamování formátu (CSF) nebo společné média aplikace formátu (CMAF) fragmentovaného MP4 a přes HTTP Live Streaming (HLS) s CMAF.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Kodeky DTS podporovaných formátů DASH-CSF, DASH CMAF, HLS M2TS a HLS CMAF balíčků jsou:  

    * Obklopit digitální služby DTS (dtsc)
    * HD typy nasazení s vysokým rozlišením a hlavní služby DTS HD zvuku (dtsh)
    * Typy nasazení Express (dtse)
    * DTS HD beze ztrát (žádné jader) (dtsl)

### <a name="multiple-audio-tracks"></a>Více zvukové stopy

Dynamické balení podporuje zvukové stopy s více pro HLS výstup (verze 4 nebo novější) pro streamování prostředky, které mají více zvukové stopy s více kodeky a jazyky.

## <a name="dynamic-encryption"></a>Dynamické šifrování

Můžete použít *dynamického šifrování* dynamicky šifrovat živě nebo na vyžádání obsahu pomocí AES-128 nebo některým z tři systémů hlavní digital rights management (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu k doručování klíčů AES a DRM licence autorizovaným klientům. Další informace najdete v tématu [dynamického šifrování](content-protection-overview.md).

## <a name="manifest-examples"></a>Příklady manifestu 
 
V dynamické balení Media Services, jsou manifesty klientů streamování HLS, MPEG-DASH a Smooth Streaming dynamicky generován a základě modulu pro výběr formátu v adrese URL. Další informace najdete v tématu [doručovací protokoly](#delivery-protocols). 

Soubor manifestu zahrnuje datový proud metadat – například typ sledování (zvuk, video nebo text), sledovat, název, počáteční a koncový čas, s přenosovou rychlostí (Vlastnosti), sledování jazyků, prezentace okno (klouzavým 5minutovým pevnou platnost) a kodek videa (FourCC). Nastaví také přehrávače pro načtení další fragment tím, že poskytuje informace o další možné přehrát video fragmentů, které jsou k dispozici a jejich umístění. Fragmenty (nebo segmenty) jsou skutečné "bloky" obsah videa.

### <a name="hls"></a>HLS

Tady je příklad souboru manifestu HLS, také nazývané HLS hlavní seznam testů: 

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

### <a name="mpeg-dash"></a>MPEG-DASH

Tady je příklad souboru manifestu MPEG-DASH, také nazývané MPEG-DASH média prezentace popis (MPD):

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
### <a name="smooth-streaming"></a>Technologie Smooth Streaming

Tady je příklad souboru manifestu technologie Smooth Streaming:

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

## <a name="dynamic-manifest"></a>Dynamický manifest

Pokud chcete nastavit počet stop, formáty, přenosových rychlostí a prezentace časových oken, které se pošlou hráči, můžete použít dynamické filtrování pomocí dynamické packager Media Services. Další informace najdete v tématu [předem filtrování manifesty s dynamické packager](filters-dynamic-manifest-overview.md).

## <a name="more-information"></a>Další informace

Podívejte se na [komunita Azure Media Services](media-services-community.md) zobrazíte různé způsoby můžete klást otázky, poskytnout zpětnou vazbu a aktualizace o Media Services.

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nahrávání, kódování a streamování videí](stream-files-tutorial-with-api.md).

