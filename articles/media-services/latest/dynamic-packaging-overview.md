---
title: Dynamické balení v Azure Media Services V3
titleSuffix: Azure Media Services
description: Tento článek obsahuje přehled dynamického balení v Azure Media Services.
author: myoungerman
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 797ba00820e7ff9d96868acdfc1dddfff3d21623
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91598275"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Dynamické balení v Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Microsoft Azure Media Services lze použít ke kódování mnoha mediálních formátů souborů. Dodává je prostřednictvím různých protokolů streamování s ochranou obsahu nebo bez nich, aby se dosáhlo všech hlavních zařízení (jako jsou zařízení s iOS a Androidem). Tito klienti rozumí různým protokolům. Například iOS vyžaduje doručení datových proudů ve formátu HTTP Live Streaming (HLS) a zařízení s Androidem podporují HLS a také formát MPEG POMLČKy.

V Media Services představuje [koncový bod streamování](streaming-endpoint-concept.md) (Origin) dynamické (za běhu) balení a službu origining, která může doručovat obsah živě a na vyžádání přímo do aplikace klienta v přehrávači. Používá jeden z běžných protokolů multimediálních datových proudů uvedených v následující části. *Dynamické balení* je funkce, která nabízí standard pro všechny koncové body streamování.

> [!NOTE]
> Pomocí [Azure Portal](https://portal.azure.com/) můžete spravovat V3 [Live události](live-events-outputs-concept.md), zobrazit [prostředky](assets-concept.md)v3 a získat informace o přístupu k rozhraním API. Pro všechny ostatní úlohy správy (například transformace a úlohy) použijte [REST API](/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref)nebo jednu z podporovaných [sad SDK](media-services-apis-overview.md#sdks).

## <a name="to-prepare-your-source-files-for-delivery"></a>Příprava zdrojových souborů na doručení

Pokud chcete využít výhod dynamického balení, musíte soubor Mezzanine (zdrojový soubor) [zakódovat](encoding-concept.md) do sady souborů MP4 s více přenosovými rychlostmi (ISO Base Media 14496-12). Potřebujete mít [Asset](assets-concept.md) s šifrovanými konfiguračními soubory MP4 a streaming, které vyžaduje Media Services dynamické balení. Z této sady souborů MP4 můžete použít dynamické balení k doručování videa prostřednictvím protokolů multimediálního datového proudu popsaných níže.

Azure Media Services dynamické balení podporuje pouze videosoubory a zvukové soubory ve formátu kontejneru MP4. Zvukové soubory musí být zakódované do kontejneru MP4 i při použití alternativních kodeků, jako je Dolby.  

> [!TIP]
> Jedním ze způsobů, jak získat konfigurační soubory MP4 a streamování, je [kódování souboru Mezzanine pomocí Media Services](#encode-to-adaptive-bitrate-mp4s). 

Chcete-li zpřístupnit pro klienty přehrávání videa v kódovaném prostředku, je nutné vytvořit [Lokátor streamování](streaming-locators-concept.md) a vytvořit adresy URL streamování. Na základě zadaného formátu v manifestu klienta streamování (HLS, MPEG POMLČKa nebo Smooth Streaming) obdržíte datový proud v protokolu, který jste zvolili.

Díky tomu pak stačí uložit (a platit) soubory pouze v jednom úložném formátu a služba Media Services bude sestavovat a dodávat vhodný formát streamování v reakci na požadavky klientů.

Pokud plánujete ochranu obsahu pomocí Media Services dynamického šifrování, přečtěte si téma [protokoly streamování a typy šifrování](content-protection-overview.md#streaming-protocols-and-encryption-types).

### <a name="hls-protocol"></a>Protokol HLS

Váš klient streamování může určit následující formáty HLS:

|Protokol|Příklad|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`||
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`||
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`||

> [!NOTE]
> V předchozích pokynech od společnosti Apple se doporučuje, aby záloha pro sítě s malou šířkou pásma poskytovala datový proud, který je jen pro zvuk.  V současné době Media Services kodér automaticky vygeneruje zvuk, který se jenom sleduje.  Pokyny pro Apple nyní obsahují informace o tom, *že by nemělo být* zahrnuté zvukové stopy, zejména pro distribuci Apple TV.  Chcete-li zabránit přehrávači ve výchozím nastavení zvukového zvuku, doporučujeme použít značku "pouze zvuk = false" v adrese URL, která odebere pouze zvukovou verzi v HLS, nebo jednoduše použít HLS-v3. Například, `http://host/locator/asset.ism/manifest(format=m3u8-aapl,audio-only=false)`.

### <a name="mpeg-dash-protocol"></a>Protokol MPEG-SPOJOVNÍK

Váš klient streamování může určit následující formáty MPEG-SPOJOVNÍK:

|Protokol|Příklad|
|---|---|
|MPEG-SPOJOVNÍK CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` ||
|MPEG-SPOJOVNÍK CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` ||

### <a name="smooth-streaming-protocol"></a>Protokol Smooth Streaming

Váš klient streamování může určit následující formáty Smooth Streaming:

|Protokol|Poznámky a příklady| 
|---|---|
|Technologie Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`||
|Smooth Streaming 2,0 (starší manifest)|Ve výchozím nastavení obsahuje Smooth Streaming formát manifestu značku opakování (značka r). Někteří hráči ale nepodporují `r-tag` . Klienti s těmito hráči můžou používat formát, který zakazuje značku r:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> Smooth Streaming vyžaduje, aby byl ve vašem datovém proudu přítomen zvuk i video.

## <a name="on-demand-streaming-workflow"></a>Pracovní postup streamování na vyžádání

Následující kroky ukazují běžný pracovní postup streamování Media Services, kde se používá dynamické balení spolu se standardním kodérem v Azure Media Services.

1. [Nahrajte vstupní soubor](job-input-from-http-how-to.md) , jako je MP4, QuickTime nebo MOV, nebo jiný podporovaný formát souboru. Tento soubor se také označuje jako Mezzanine nebo zdrojový soubor. Seznam podporovaných formátů najdete v tématu [formáty podporované kodérem Standard](media-encoder-standard-formats.md).
1. [Zakódovat](#encode-to-adaptive-bitrate-mp4s) soubor Mezzanine do sady H. 264/AAC MP4 s adaptivní přenosovou rychlostí.

    Pokud již máte kódované soubory a chcete soubory pouze zkopírovat a streamovat, použijte rozhraní API: [CopyVideo](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#copyvideo) a [CopyAudio](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#copyaudio) . V důsledku toho se vytvoří nový soubor MP4 s manifestem streamování (soubor. ISM).
1. Publikujte výstupní Asset, který obsahuje sadu MP4 s adaptivní přenosovou rychlostí. Publikujete vytvořením [lokátoru streamování](streaming-locators-concept.md).
1. Vytvářejte adresy URL, které cílí na různé formáty (HLS, MPEG-POMLČKa a Smooth Streaming). *Koncový bod streamování* by postaral o poskytování správného manifestu a požadavků pro všechny tyto různé formáty.
    
Následující diagram znázorňuje streamování na vyžádání s dynamickým pracovním postupem pro balení.

![Diagram pracovního postupu pro streamování na vyžádání s dynamickým balením](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Cesta ke stažení je k dispozici ve výše uvedeném obrázku, aby bylo vidět, že si můžete stáhnout soubor MP4 přímo prostřednictvím *koncového bodu streamování* (počátek) (zadáte [zásadu streamování](streaming-policy-concept.md) ke stažení na lokátoru streamování).<br/>Dynamický balíček nemění soubor. Můžete volitelně použít rozhraní API služby Azure Blob Storage pro přístup k MP4 přímo pro progresivní stahování, pokud chcete obejít funkce *koncového bodu streamování* (Origin). 

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Kódovat do adaptivní přenosové rychlosti rychlostmi

V následujících článcích se [dozvíte, jak zakódovat video pomocí Media Services](encoding-concept.md):

* [Kódování z adresy URL HTTPS pomocí integrovaných přednastavení](job-input-from-http-how-to.md).
* [Kódování místního souboru pomocí integrovaných přednastavení](job-input-from-local-file-how-to.md).
* [Vytvořte vlastní předvolbu, která bude cílit na konkrétní scénář nebo požadavky na zařízení](customize-encoder-presets-how-to.md).

Podívejte se na seznam [formátů a kodeků](media-encoder-standard-formats.md)Standard Encoder.

## <a name="live-streaming-workflow"></a>Pracovní postup živého streamování

Živá událost může být nastavená na *předávací* (místní živý kodér posílá datový proud s více přenosovými rychlostmi) nebo *živé kódování* (místní kodér Live Encoder posílá datový proud s jednou přenosovou rychlostí). 

Tady je běžný pracovní postup pro živé streamování s *dynamickým balením*:

1. Vytvořte [živou událost](live-events-outputs-concept.md).
1. Získejte adresu URL pro přijímání a nakonfigurujte místní kodér tak, aby používal adresu URL k odeslání informačního kanálu příspěvku.
1. Získejte adresu URL náhledu a použijte ji k ověření, že se vstup z kodéru přijímá.
1. Vytvořte nový Asset.
1. Vytvořte živý výstup a použijte název assetu, který jste vytvořili.<br />Živý výstup archivuje Stream do assetu.
1. Vytvořte Lokátor streamování s integrovanými typy zásad streamování.<br />Pokud máte v úmyslu zašifrovat svůj obsah, Projděte si [Přehled ochrany obsahu](content-protection-overview.md).
1. Vypíšete cesty na lokátoru streamování, abyste získali adresy URL, které se mají použít.
1. Získejte název hostitele pro koncový bod streamování, ze kterého chcete streamovat.
1. Vytvářejte adresy URL, které cílí na různé formáty (HLS, MPEG-POMLČKa a Smooth Streaming). *Koncový bod streamování* se stará o poskytování správného manifestu a požadavků pro různé formáty.

Tento diagram znázorňuje pracovní postup živého streamování s *dynamickým balením*:

![Diagram pracovního postupu pro průchozí kódování s dynamickým balením](./media/live-streaming/pass-through.svg)

Informace o živém streamování v Media Services V3 najdete v tématu [Přehled živého streamování](live-streaming-overview.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Video kodeky podporované dynamickým balením

Dynamické balení podporuje videosoubory, které jsou ve formátu souboru kontejneru MP4, a obsahuje video, které je kódované pomocí [h. 264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC nebo AVC1) nebo [H. 265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 nebo hvc1).

> [!NOTE]
> Rozlišení až 4K a snímkových frekvencí až 60 snímků za sekundu byly testovány s *dynamickým balením*.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Zvukové kodeky podporované dynamickým balením

Dynamické balení podporuje také zvukové soubory, které jsou uloženy ve formátu kontejneru souboru MP4, který obsahuje kódovaný zvukový stream v jednom z následujících kodeků:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 nebo HE-AAC v2). 
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 nebo E-AC3).  Aby bylo možné pracovat s dynamickým balíčkem, musí být kódovaný zvuk uložený ve formátu kontejneru MP4.
* Dolby ATMOS

   Streamování formátu Dolby ATMOS je podporováno pro standardy, jako je protokol MPEG-dispomlčka (CSF) nebo Common Media Application Format (CMAF) fragmentované MP4 a prostřednictvím HTTP Live Streaming (HLS) s CMAF.
* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Kodeky DTS podporované ČÁRKou-CSF, POMLČKou CMAF, HLS-M2TS a HLS-CMAF formáty balení:  

    * DTS Digital Surround (dtsc)
    * DTS – HD s vysokým rozlišením a DTS – HD \ audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD – bezeztrátová (bez jádra) (dtsl)

Dynamické balení podporuje více zvukových stop s POMLČKou nebo HLS (verze 4 nebo novější) pro streamování assetů, které mají více zvukových stop s více kodeky a jazyky.

Pro všechny výše uvedené zvukové kodeky musí být kódovaný zvuk uložený ve formátu kontejneru MP4, aby fungoval s dynamickým balením. Služba nepodporuje ve službě BLOB Storage nezpracované základní formáty souborů streamu (například následující by se nepodporovaly –. DTS,. AC3.) 

Pro vytváření zvukových balíčků jsou podporovány pouze soubory s příponou. mp4 rozšíření. mp4a. 

### <a name="limitations"></a>Omezení

#### <a name="ios-limitation-on-aac-51-audio"></a>omezení iOS na zvuku AAC 5,1

Zařízení Apple iOS nepodporují zvukový kodek 5,1 AAC. Zvuk ve více kanálech musí být kódovaný pomocí kodeků Dolby Digital nebo Dolby Digital Plus.

Podrobné informace najdete v tématu [specifikace vytváření HLS pro zařízení Apple](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices).

> [!NOTE]
> Media Services nepodporuje kódování zvukového formátu Dolby Digital, Dolby digitálního plus nebo Dolby Digital Plus s formáty Dolby ATMOS Multi-Channel.

#### <a name="dolby-digital-audio"></a>Zvuk Dolby Digital

Media Services dynamické balení v současné době nepodporuje soubory, které obsahují zvuk [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (to znamená, že se považuje za starší kodek od formátu Dolby).

## <a name="manifests"></a>Manifesty

V Media Services *dynamického balení*se dynamicky generují manifesty klienta streamování pro HLS, MPEG-pomlčky a Smooth Streaming na základě selektoru formátu v adrese URL.  

Soubor manifestu obsahuje streamovaná metadata, jako je například typ stopy (zvuk, video nebo text), název stopy, počáteční a koncový čas, rychlost (kvality), sledovací jazyky, okno prezentace (posuvné okno pevné doby trvání) a kodek videa (FourCC). Také instruuje přehrávač, aby načetl další fragment poskytnutím informací o dalších dostupných fragmentech videa a jejich umístění. Fragmenty (nebo segmenty) jsou skutečnými "bloky obsahu videa.

### <a name="examples"></a>Příklady

#### <a name="hls"></a>HLS

Tady je příklad souboru manifestu HLS, kterému se taky říká hlavní seznam testů HLS: 

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

Tady je příklad souboru manifestu MPEG-SPOJOVNÍK, označovaného také jako popis prezentace médií MPEG-SPOJOVNÍK (MPD):

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

Tady je příklad souboru manifestu Smooth Streaming:

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

### <a name="naming-of-tracks-in-the-manifest"></a>Pojmenovávání stop v manifestu

Pokud je v souboru. ISM zadán název zvukové stopy, Media Services přidá `Label` prvek v rámci `AdaptationSet` , aby určoval informace o textural pro konkrétní zvukovou stopu. Příklad manifestu výstupní POMLČKy:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

Přehrávač může použít `Label` element k zobrazení v uživatelském rozhraní.

### <a name="signaling-audio-description-tracks"></a>Signalizace stopy Popis zvuku

Do videa můžete přidat záznam mluveného komentáře, který bude vizuálně postiženým klientům pomáhat při nahrávání videa, a to poslechem mluveného komentáře. V manifestu musíte opatřit zvukovou stopu jako Popis zvuku. Uděláte to tak, že do souboru. ISM přidáte parametry usnadnění a role. Vaše zodpovědnost za správné nastavení těchto parametrů k signalizaci zvukové stopy jako zvukového popisu. Například přidejte `<param name="accessibility" value="description" />` a `<param name="role" value="alternate"` do souboru. ISM pro konkrétní zvukovou stopu. 

Další informace najdete v příkladech [signalizace popisné zvukové stopy](signal-descriptive-audio-howto.md) .

#### <a name="smooth-streaming-manifest"></a>Smooth Streaming manifest

Pokud přehráváte Smooth Streaming Stream, manifest by přenese hodnoty v `Accessibility` a `Role` atributy pro tuto zvukovou stopu. Například by bylo `Role="alternate" Accessibility="description"` přidáno v `StreamIndex` prvku, aby označovalo, že se jedná o zvukový popis.

#### <a name="dash-manifest"></a>SPOJOVNÍK – manifest

V případě POMLČKového manifestu by se měly přidat následující dva elementy k signalizaci zvukového popisu:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS seznam testů

V případě HLSch v7 a vyšších `(format=m3u8-cmaf)` seznamů se seznam testů přenese, `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` když je zvuková stopa signalizace.

#### <a name="example"></a>Příklad

Další informace najdete v tématu [postup signalizace stopy zvukového popisu](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Dynamický manifest

Chcete-li řídit počet běhů, formátů, přenosové rychlosti a časová období prezentace, která jsou odesílána přehrávačům, můžete použít dynamické filtrování pomocí nástroje Media Services Dynamic packageer. Další informace najdete v tématu [předběžné filtrování manifestů pomocí dynamického balíčku](filters-dynamic-manifest-overview.md).

## <a name="dynamic-encryption"></a>Dynamické šifrování

*Dynamické šifrování* můžete použít k dynamickému šifrování živého obsahu nebo na vyžádání pomocí AES-128 nebo kteréhokoli ze tří hlavních systémů DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine a Apple Fairplay. Media Services taky poskytuje službu pro doručování klíčů AES a licencí DRM autorizovaným klientům. Další informace najdete v tématu [dynamické šifrování](content-protection-overview.md).

> [!NOTE]
> Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="more-information"></a>Další informace

Podívejte se na [Azure Media Services komunita](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="need-help"></a>Potřebujete pomoc?

Lístek podpory můžete otevřít tak, že přejdete na [novou žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

[Nahrávání, kódování a streamování videí](stream-files-tutorial-with-api.md)
