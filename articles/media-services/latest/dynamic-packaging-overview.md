---
title: Dynamické balení ve službě Azure Media Services v3
titleSuffix: Azure Media Services
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
ms.date: 03/17/2020
ms.author: juliako
ms.openlocfilehash: ae049d7486007696d8038eb4e6593cf996df659e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372597"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Dynamické balení v mediálních službách v3

Mediální služby Microsoft Azure lze použít ke kódování mnoha formátů souborů zdroje médií. Dodává je prostřednictvím různých protokolů streamování, s ochranou obsahu nebo bez něj, aby se dostali ke všem hlavním zařízením (jako jsou zařízení se systémem iOS a Android). Tito klienti rozumí různým protokolům. Například iOS vyžaduje, aby datové proudy byly doručovány ve formátu HTTP Live Streaming (HLS) a zařízení se systémem Android podporují HLS i MPEG DASH.

Ve službě Media Services představuje [koncový bod streamování](streaming-endpoint-concept.md) dynamickou službu balení a původu dat, která může dodávat váš živý obsah a obsah na vyžádání přímo do aplikace klientského přehrávače. Používá jeden z běžných protokolů datových proudů médií uvedených v následující části. Dynamické balení je funkce, která je standardem u všech koncových bodů streamování (Standard i Premium).

> [!NOTE]
> [Na portálu Azure](https://portal.azure.com/) můžete spravovat živé [události](live-events-outputs-concept.md)v3 , zobrazit [datové zdroje](assets-concept.md)v3 , získat informace o přístupu k virtuálním i matům. Pro všechny ostatní úlohy správy (například Transformace a úlohy) použijte [rozhraní REST API](https://docs.microsoft.com/rest/api/media/), ROZHRANÍ [CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

## <a name="to-prepare-your-source-files-for-delivery"></a><a id="delivery-protocols"/>Příprava zdrojových souborů k doručení

Chcete-li využít výhod dynamického balení, musíte [kódovat](encoding-concept.md) soubor mezaninové (zdrojové) do sady souborů MP4 (ISO Base Media 14496-12). Musíte mít [datový zdroj](assets-concept.md) s kódovaným mp4 a konfiguračními soubory streamování, které dynamické balení Mediálních služeb potřebuje. Z této sady souborů MP4 můžete použít dynamické balení k doručování videa prostřednictvím níže popsaných protokolů datových proudů médií.

> [!TIP]
> Jedním ze způsobů, jak získat mp4 a streamování konfiguračních souborů je [kódovat mezaninové soubor s Media Services](#encode-to-adaptive-bitrate-mp4s). 

Chcete-li zpřístupnit videa v kódovaném datovém zdroji klientům pro přehrávání, musíte vytvořit [lokátor streamování](streaming-locators-concept.md) a vytvořit adresy URL streamování. Potom na základě zadaného formátu v manifestu klienta streamování (HLS, MPEG DASH nebo Plynulé streamování) obdržíte datový proud ve zvoleném protokolu.

Díky tomu pak stačí uložit (a platit) soubory pouze v jednom úložném formátu a služba Media Services bude sestavovat a dodávat vhodný formát streamování v reakci na požadavky klientů.

Pokud chcete chránit obsah pomocí dynamického šifrování služby Media Services, přečtěte si část [Protokoly datových proudů a typy šifrování](content-protection-overview.md#streaming-protocols-and-encryption-types).

### <a name="hls-protocol"></a>Protokol HLS

Klient datových proudů může určit následující formáty HLS:

|Protocol (Protokol)|Příklad|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`||
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`||
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`||

### <a name="mpeg-dash-protocol"></a>Protokol MPEG-DASH

Klient datových proudů může určit následující formáty MPEG-DASH:

|Protocol (Protokol)|Příklad|
|---|---|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` ||
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` ||

### <a name="smooth-streaming-protocol"></a>Protokol plynulého streamování

Klient datových proudů může určit následující formáty plynulého streamování:

|Protocol (Protokol)|Poznámky/příklady| 
|---|---|
|Technologie Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`||
|Plynulé streamování 2.0 (starší manifest)|Ve výchozím nastavení obsahuje formát manifestu plynulé hodování značku opakování (značka r-tag). Někteří hráči však `r-tag`nepodporují . Klienti s těmito přehrávači mohou použít formát, který zakáže značku r:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> Plynulé streamování vyžaduje, aby ve vašem datovém proudu byl přítomen zvuk i video.

## <a name="on-demand-streaming-workflow"></a>Pracovní postup streamování na vyžádání

Následující kroky ukazují společný pracovní postup streamování mediálních služeb, kde se dynamické balení používá spolu se standardním kodérem ve službě Azure Media Services.

1. Nahrajte vstupní soubor, například soubor QuickTime/MOV nebo MXF. Tento soubor se také označuje jako mezipatře nebo zdrojový soubor. Seznam podporovaných formátů naleznete v [tématu Formáty podporované standardním kodérem](media-encoder-standard-formats.md).
1. [Zakódujte](#encode-to-adaptive-bitrate-mp4s) svůj mezaninový soubor do sady adaptivního přenosového rychlosti H.264/AAC MP4.
1. Publikujte výstupní datový zdroj, který obsahuje sadu MP4 s adaptivní přenosovou rychlostí. Publikovat vytvořením lokátoru streamování.
1. Vytvářejte adresy URL, které cílí na různé formáty (HLS, MPEG-DASH a plynulé streamování). **Koncový bod streamování** by se postaral o obsluhu správného manifestu a požadavků pro všechny tyto různé formáty.

Následující diagram znázorňuje streamování na vyžádání s pracovním postupem Dynamické balení.

![Diagram pracovního postupu pro streamování na vyžádání s dynamickým balením](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Kódování pro adaptivní přenosové rychlosti MP4

Následující články ukazují příklady kódování [videa pomocí mediálních služeb](encoding-concept.md):

* [Kódovat z adresy URL HTTPS pomocí vestavěných přednastavení](job-input-from-http-how-to.md).
* [Zakódujte místní soubor pomocí vestavěných přednastavení](job-input-from-local-file-how-to.md).
* [Vytvořte vlastní přednastavení, které bude cílit na konkrétní scénář nebo požadavky na zařízení](customize-encoder-presets-how-to.md).

Podívejte se na seznam [formátů standardního](media-encoder-standard-formats.md)kodéru a kodeků .

## <a name="live-streaming-workflow"></a>Pracovní postup živého streamování

Živou událost lze nastavit buď na *předávací* (místní živý kodér odešle datový proud s více datovým tokem) nebo *živé kódování* (místní živý kodér odešle jeden datový proud datového toku). 

Zde je běžný pracovní postup pro živé vysílání s dynamickým balením:

1. Vytvořte [živou událost](live-events-outputs-concept.md).
1. Získejte ingestující adresu URL a nakonfigurujte místní kodér tak, aby k odeslání kanálu příspěvků používal adresu URL.
1. Získejte adresu URL náhledu a použijte ji k ověření, že je přijímán vstup z kodéru.
1. Vytvořte nový datový zdroj.
1. Vytvořte živý výstup a použijte název datového zdroje, který jste vytvořili.<br />Živý výstup archivuje datový proud do datového zdroje.
1. Vytvořte lokátor streamování s předdefinovanými typy zásad streamování.<br />Pokud máte v úmyslu obsah šifrovat, přečtěte si [přehled ochrany obsahu](content-protection-overview.md).
1. Seznam cest na vyhledávači streamování získat adresy URL k použití.
1. Získejte název hostitele pro koncový bod streamování, ze kterého chcete streamovat.
1. Vytvářejte adresy URL, které cílí na různé formáty (HLS, MPEG-DASH a plynulé streamování). Koncový bod streamování se postará o obsluhu správného manifestu a požadavků pro různé formáty.

Tento diagram znázorňuje pracovní postup pro živé vysílání pomocí dynamického balení:

![Diagram pracovního postupu pro předávací kódování s dynamickým balením](./media/live-streaming/pass-through.svg)

Informace o živém vysílání ve službě Media Services v3 naleznete v tématu [Přehled živého streamování](live-streaming-overview.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Video kodeky podporované dynamickým balením

Dynamické balení podporuje soubory MP4, které obsahují video, které je kódováno [h.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC nebo AVC1) nebo [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 nebo hvc1).

> [!NOTE]
> S dynamickým balením byla testována rozlišení až 4K a snímkové frekvence až 60 snímků za sekundu. [Premium Encoder](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) podporuje kódování na H.265 přes starší rozhraní API v2.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a><a id="audio-codecs"/>Zvukové kodeky podporované dynamickým balením

Dynamické balení podporuje zvuk, který je kódován následujícími protokoly:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 nebo HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (vylepšená technologie AC-3 nebo E-AC3)
* Dolby Atmos<br />
   Streamování obsahu Dolby Atmos je podporováno pro standardy, jako je protokol MPEG-DASH s fragmentovaným mp4 Common Streaming Format (CSF) nebo Common Media Application Format (CMAF), a prostřednictvím http živého streamování (HLS) s CMAF.

* [Dts](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Kodeky DTS podporované formáty balení DASH-CSF, DASH-CMAF, HLS-M2TS a HLS-CMAF jsou:  

    * Digitální prostorový prostor DTS (dtsc)
    * DTS-HD s vysokým rozlišením a dts-hd master audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD Bezztrátový (bez jádra) (dtsl)

Dynamické balení podporuje více zvukových stop s DASH nebo HLS (verze 4 nebo novější) pro streamování datových zdrojů, které mají více zvukových stop s více kodeky a jazyky.

### <a name="additional-notes"></a>Další poznámky

Dynamické balení nepodporuje soubory, které obsahují zvuk [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (je to starší kodek).

> [!NOTE]
> [Premium Encoder](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) podporuje kódování do Dolby Digital Plus přes starší rozhraní API v2.

## <a name="manifests"></a>Manifesty

V dynamickém balení mediálních služeb jsou manifesty streamovaného klienta pro HLS, MPEG-DASH a Smooth Streaming dynamicky generovány na základě voliče formátu v adrese URL.  

Soubor manifestu obsahuje streamovaná metadata, jako je typ stopy (zvuk, video nebo text), název stopy, počáteční a koncový čas, datový tok (vlastnosti), jazyky stopy, okno prezentace (posuvné okno s pevnou dobou trvání) a kodek videa (FourCC). Také instruuje přehrávač k načtení dalšího fragmentu poskytnutím informací o dalších přehrávatelných fragmentech videa, které jsou k dispozici, a o jejich umístění. Fragmenty (nebo segmenty) jsou skutečné "bloky" video obsahu.

### <a name="examples"></a>Příklady

#### <a name="hls"></a>HLS

Zde je příklad manifestu HLS, který se také nazývá hlavní seznam skladeb HLS: 

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

#### <a name="mpeg-dash"></a>MPEG-POMLČKA

Zde je příklad manifestu MPEG-DASH, nazývaného také Popis prezentace média MPEG-DASH (MPD):

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
#### <a name="smooth-streaming"></a>Technologie Smooth Streaming

Tady je příklad souboru manifestu plynulého streamování:

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

### <a name="naming-of-tracks-in-the-manifest"></a>Pojmenování stop v manifestu

Pokud je v souboru ISM zadán název zvukové `Label` stopy, `AdaptationSet` služba Media Services přidá prvek do souboru, který určí texturní informace pro konkrétní zvukovou stopu. Příklad výstupního manifestu DASH:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

Přehrávač může prvek `Label` použít k zobrazení v jeho ui.

### <a name="signaling-audio-description-tracks"></a>Signalizační stopy zvukového popisu

Do videa můžete přidat skladbu mluveného komentáře, která pomůže zrakově postiženým klientům sledovat záznam videa poslechem mluveného komentáře. Je třeba oznamovat zvukovou stopu jako zvukový popis v manifestu. Chcete-li to provést, přidejte do souboru .ism parametry "usnadnění" a "role". Je vaší odpovědností správně nastavit tyto parametry tak, aby signalizovaly zvukovou stopu jako zvukový popis. Můžete například `<param name="accessibility" value="description" />` `<param name="role" value="alternate"` přidat a do souboru ISM pro určitou zvukovou stopu. 

Další informace naleznete v příkladu jak [signalizovat popisnou zvukovou stopu.](signal-descriptive-audio-howto.md)

#### <a name="smooth-streaming-manifest"></a>Manifest plynulého streamování

Pokud přehráváte datový proud plynulého streamování, `Accessibility` manifest `Role` by přenášel hodnoty a atributy pro tuto zvukovou stopu. Například `Role="alternate" Accessibility="description"` by být přidány `StreamIndex` do prvku k označení, že je zvukový popis.

#### <a name="dash-manifest"></a>Manifest DASH

Pro manifest DASH by byly přidány následující dva prvky, které signalizují zvukový popis:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>Seznam skladeb HLS

Pro HLS v7 `(format=m3u8-cmaf)`a výše `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` , jeho playlist by nést, když je signalizován zvuk popis stopy.

#### <a name="example"></a>Příklad

Další informace naleznete v [tématu Jak signalizovat stopy zvukových popisů](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Dynamický manifest

Chcete-li řídit počet stop, formátů, přenosových rychlostí a časových oken prezentace odeslaných přehrávačům, můžete pomocí dynamického balíčku Služby Media Services použít dynamické filtrování. Další informace naleznete [v tématu Předběžné filtrování manifestů pomocí dynamického balení](filters-dynamic-manifest-overview.md).

## <a name="dynamic-encryption"></a>Dynamické šifrování

*Dynamické šifrování* můžete použít k dynamickému šifrování živého obsahu nebo obsahu na vyžádání pomocí AES-128 nebo některého ze tří hlavních systémů správy digitálních práv (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Služba Media Services také poskytuje službu pro doručování klíčů AES a licencí DRM oprávněným klientům. Další informace naleznete v [tématu dynamické šifrování](content-protection-overview.md).

> [!NOTE]
> Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="more-information"></a>Další informace

Podívejte se na [komunitu Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="need-help"></a>Potřebujete pomoc?

Lístek podpory můžete otevřít tak, že přejdete na [Novou žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

[Nahrávání, kódování a streamování videí](stream-files-tutorial-with-api.md)
