---
title: Azure Media Services - signalizace vypršel časový limit Metadata v živé streamování | Microsoft Docs
description: Tato specifikace popisuje dva režimy, které jsou podporovány službou Media Services pro signalizační vypršel metadata v rámci živé vysílání datového proudu. To zahrnuje podporu pro obecné vypršel metadata signály, jakož i SCTE 35 signalizace pro uživatele programu splice vkládání reklam.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2018
ms.author: johndeu;
ms.openlocfilehash: ae726b141f5f44b1eb0887cbd988881e41e163c0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Signalizace vypršel časový limit Metadata v živé vysílání datového proudu


## <a name="1-introduction"></a>1 Úvod 
Pro usnadnění vkládání oznámení o inzerovaném programu, nebo vlastní události na klientský přehrávač televizního často Zkontrolujte použití vypršel metadata vkládán videa. Chcete-li tyto scénáře, Media Services poskytuje podporu pro přenos vypršel metadat spolu s média, z bodu ingestování živé streamování kanálu do klientské aplikace.
Tato specifikace jsou podrobněji popsány dále dva režimy, které jsou podporovaných službou Media Services pro vypršel metadata v rámci živé streamování signálů:

1. Signalizace [SCTE – 35], který heeds doporučené postupy ohraničená [SCTE-67]

2. Obecný signalizace režim pro zprávy, které nejsou metadat [SCTE – 35] vypršel časový limit.

### <a name="12-conformance-notation"></a>1.2 zápis shoda
Klíčová slova "musíte", "Nesmí", "REQUIRED", "SHALL", "Není se", "SHOULD", "By neměl", "Doporučené", "Může" a "Volitelné" v tomto dokumentu se budou interpretovat, jak je popsáno v dokumentu RFC 2119

### <a name="13-terms-used"></a>1.3 termínů používaných

| Označení              | Definice                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Čas prezentace | Čas, který událost se zobrazí jako prohlížeč. Čas představuje chvíli na časové ose média prohlížeč by najdete v části události. Prezentace době zprávu SCTE 35 splice_info() příkazu je třeba splice_time(). |
| Čas doručení      | Čas doručení zprávy události. Čas se obvykle liší od prezentace čas události, protože prezentace předstihu události jsou odesílány zprávy o událostech.                                     |
| Zhuštěný sledování      | sledování média, které není průběžné a je čas synchronizovaný s nadřazenou nebo ovládacích track.                                                                                                                                    |
| Zdroj            | Službu Azure Media streamování                                                                                                                                                                                                |
| Jímka kanálu      | Azure Media živé streamování služby                                                                                                                                                                                           |
| HLS               | Protokol Apple HTTP Live Streaming.                                                                                                                                                                                               |
| POMLČKA              | Dynamické adaptivní datové proudy přes protokol HTTP                                                                                                                                                                                             |
| Smooth            | Smooth protokol pro streamování                                                                                                                                                                                                        |
| MPEG2-TS          | Datové proudy MPEG 2 Transport                                                                                                                                                                                                         |
| RTMP              | Protokol v reálném čase multimédií                                                                                                                                                                                                    |
| uimsbf            | Celé číslo bez znaménka, nejvýznamnější nejprve bitů.                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>Ingestování 2 vypršel metadat
## <a name="21-rtmp-ingest"></a>2.1 Ingestování RTMP
RTMP podporuje odesílání jako zprávy cue tuk vložených v rámci datového proudu RTMP signálů vypršel metadat. Cue zprávy mohou být odeslány nějakou dobu před skutečné události nebo vložení uživatele programu splice ad musí proběhnout. Pro podporu tohoto scénáře, se ve zprávě Vytvrdit odesílají skutečný čas uživatele programu splice nebo segmentu. Další informace najdete v tématu [AMF0].

Následující tabulka popisuje formát datové části tuk zprávy, která bude ingestování Media Services.

Název tuk zpráva slouží k odlišení víc datových proudů událostí stejného typu.  Zprávy [SCTE – 35] název tuk zprávy musí být "onAdCue", podle doporučení v [SCTE-67].  Všechna pole není uveden v seznamu musí být ignorovány, tak, aby inovace tohoto návrhu není potlačen v budoucnu.

### <a name="signal-syntax"></a>Syntaxe signál
Jednoduchý režim RTMP služba Media Services podporuje do jedné zprávy cue tuk názvem "onAdCue" v následujícím formátu:

### <a name="simple-mode"></a>Jednoduchý režim

| Název pole | Typ pole | Povinné? | Popisy                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| Cue        | Řetězec     | Požaduje se | Zprávy událostí.  Musí být "SpliceOut" určit jednoduchý režim splice.                                              |
| id         | Řetězec     | Požaduje se | Jedinečný identifikátor popisující uživatele programu splice nebo segmentu. Identifikuje této instance zprávy                            |
| Doba trvání   | Číslo     | Požaduje se | Doba trvání uživatele programu splice. Jednotky jsou zlomků sekund.                                                                |
| Uplynulý čas    | Číslo     | Nepovinné | Při signál Probíhá opakování za účelem podpory naladit, v tomto poli musí být množství času prezentace, které uplynul od začátku uživatele programu splice. Jednotky jsou zlomků sekund. Při použití jednoduchého režimu, tato hodnota by neměla překročit původní trvání uživatele programu splice.                                                  |
| time       | Číslo     | Požaduje se | Musí být čas uživatele programu splice, časem prezentace. Jednotky jsou zlomků sekund.                                     |

---------------------------

### <a name="scte-35-mode"></a>Režim SCTE 35

| Název pole | Typ pole | Povinné? | Popisy                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| Cue        | Řetězec     | Požaduje se | Zprávy událostí.  Zprávy [SCTE – 35], musí být Base64, pomocí splice_info_section() v pořadí pro zprávy zasílané klientům HLS, Smooth a Dash [SCTE-67] v souladu s kódováním binární (IETF RFC 4648).                                              |
| type       | Řetězec     | Požaduje se | Název URN nebo adresa URL identifikace schéma zpráva; například "urn: Příklad: signalizace: 1.0".  Zprávy [SCTE – 35] musí se jednat "urn: scte:scte35:2013a:bin" v pořadí pro zprávy zasílané klientům HLS, Smooth a Dash v souladu s [SCTE-67].  |
| id         | Řetězec     | Požaduje se | Jedinečný identifikátor popisující uživatele programu splice nebo segmentu. Identifikuje této instance zprávy.  Zprávy s ekvivalentní sémantiku musí mít stejnou hodnotu.|
| Doba trvání   | Číslo     | Požaduje se | Doba trvání události nebo ad uživatele programu splice – segmentu, pokud je znám. Pokud je neznámý, hodnota musí být 0.                                                                 |
| Uplynulý čas    | Číslo     | Nepovinné | Při Chcete-li ladit Probíhá opakování signál ad [SCTE – 35], v tomto poli musí být množství času prezentace, které uplynul od začátku uživatele programu splice. Jednotky jsou zlomků sekund. V režimu [SCTE – 35] Tato hodnota může překročit původní zadaná doba trvání uživatele programu splice nebo segmentu.                                                  |
| time       | Číslo     | Požaduje se | Splice – prezentace čas události nebo ad.  Prezentace čas a dobu trvání by MĚL zarovnat s datového proudu přístup body (SAP) typu 1 nebo 2, jak jsou definovány v [ISO-14496-12] přílohy I. Odchozí HLS, čas a dobu trvání zarovnán s hranicemi segmentu. Prezentace čas a dobu trvání různých událostí zprávy v rámci stejného datového proudu událostí se nesmí překrývat. Jednotky jsou zlomků sekund.

---------------------------

#### <a name="211-cancelation-and-updates"></a>2.1.1 zrušení a aktualizace

Zprávy lze zrušena nebo aktualizovat odesláním více zpráv se stejným prezentace čas a ID. Prezentace čas a ID jednoznačné určení události a poslední zprávy pro konkrétní prezentace čas, který splňuje před omezení je zprávu, která je reagovali na ni. Aktualizované události nahradí všechny dříve přijaté zprávy. Před omezení je čtyři sekund. Zprávy přijaté nejméně čtyři sekund před časem prezentace budou reagovali na ni.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 fragmentovaných MP4 Ingestování (technologie Smooth Streaming)
Další požadavky na živý datový proud ingestování získáte [živé-FMP4]. Následující části obsahují podrobnosti týkající se ingestování vypršel prezentace metadat.  Metadata vypršel prezentace je konzumována jako zhuštěné sledovat, která je definována v obou Live Manifest pole Server (viz MS-SSTR) a pole Movie (moov).  Každý zhuštěných fragment se skládá z film Fragment pole (moof) a pole Data média ('mdat'), kde '' okno mdat je binární zpráva.

#### <a name="221-live-server-manifest-box"></a>2.2.1 pole manifestu aktivní Server
Zhuštěný sledování musí být deklarován v poli Live Manifest serveru s \<textstream\> položky a musí mít následující atributy nastavit:

| **Název atributu** | **Typ pole** | **Vyžaduje?** | **Popis**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Číslo         | Požaduje se      | MUSÍ být "0" označující sledovat s neznámé, proměnné přenosovou rychlostí.                                                                                                                                                                                                 |
| parentTrackName    | Řetězec         | Požaduje se      | MUSÍ být název nadřazené sledovat, ke kterému jsou kódy čas zhuštěných sledovat zarovnán časový rámec. Sledování nadřazené nemůže být zhuštěných sledovat.                                                                                                                    |
| manifestOutput     | Logická hodnota        | Požaduje se      | MUSÍ být "PRAVDA", k označení, že zhuštěných sledovat budou vloženy do manifestu Smooth klienta.                                                                                                                                                               |
| Podtyp            | Řetězec         | Požaduje se      | MUSÍ být čtyři znakové kódu "DATA".                                                                                                                                                                                                                         |
| Schéma             | Řetězec         | Požaduje se      | MUSÍ být název URN nebo adresa URL identifikace schéma zpráva; například "urn: Příklad: signalizace: 1.0". Zprávy [SCTE – 35] musí se jednat "urn: scte:scte35:2013a:bin" v pořadí pro zprávy zasílané klientům HLS, Smooth a Dash v souladu s [SCTE-67]. |
| trackName          | Řetězec         | Požaduje se      | MUSÍ být název zhuštěných sledovat. TrackName slouží k rozlišení různých datových proudů událostí s stejné schéma. Každý datový proud událostí jedinečný musí mít název jedinečný sledovat.                                                                           |
| Časová osa          | Číslo         | Nepovinné      | MUSÍ být časový rámec dráhy nadřazené.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 film pole

Pole Movie (moov) následuje za provozu Manifest pole Server jako součást hlavičky datového proudu pro zhuštěné sledovat.

Okno ' moov ' by MĚL obsahovat **TrackHeaderBox (tkhd)** pole, jak jsou definovány v [ISO-14496-12] s těmito omezeními:

| **Název pole** | **Typ pole**          | **Vyžaduje?** | **Popis**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| Doba trvání       | 64bitové celé číslo bez znaménka | Požaduje se      | MUSÍ být 0, protože pole sledovat má nulové ukázky a celkovou dobu trvání ukázky v poli sledovat je 0. |
-------------------------------------

Okno ' moov ' by MĚL obsahovat **HandlerBox (hdlr)** definovaným v [ISO-14496-12] s těmito omezeními:

| **Název pole** | **Typ pole**          | **Vyžaduje?** | **Popis**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32bitové celé číslo bez znaménka | Požaduje se      | MUSÍ být 'meta'. |
-------------------------------------

Pole 'stsd' by MĚLO obsahovat MetaDataSampleEntry pole s názvem kódování, jak jsou definovány v [ISO-14496-12].  Například pro zprávy SCTE 35 kódování název by MĚL být 'scte'.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 film Fragment pole a pole Data média

Zhuštěný sledovat fragmenty obsahovat pole Fragment Movie (moof) a pole Data média (mdat).

MUSÍ obsahovat pole MovieFragmentBox (moof) **TrackFragmentExtendedHeaderBox (uuid)** pole, jak jsou definovány v [FMP4] s následující pole:

| **Název pole**         | **Typ pole**          | **Vyžaduje?** | **Popis**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64bitové celé číslo bez znaménka | Požaduje se      | MUSÍ být čas doručení události. Tato hodnota zarovnává zprávu s nadřazenou sledovat.   |
| fragment_duration      | 64bitové celé číslo bez znaménka | Požaduje se      | Doba trvání události musí být. Doba trvání může být nula indikující, že doba trvání neznámý. |

------------------------------------


Pole MediaDataBox (mdat) musí mít následující formát:

| **Název pole**          | **Typ pole**                   | **Vyžaduje?** | **Popis**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| verze                 | celé číslo bez znaménka 32-bit (uimsbf) | Požaduje se      | Určuje formát obsah '' okno mdat. Nerozpoznané verze se budou ignorovat. Aktuálně je jediná podporovaná verze 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | celé číslo bez znaménka 32-bit (uimsbf) | Požaduje se      | Identifikuje této instance zprávy. Zprávy s ekvivalentní sémantiku musí mít stejnou hodnotu; zpracování všech jednu událost se zprávou se stejným id je dostatečná.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | celé číslo bez znaménka 32-bit (uimsbf) | Požaduje se      | Součet fragment_absolute_time, zadaný v TrackFragmentExtendedHeaderBox a presentation_time_delta musí být čas prezentace události. Prezentace čas a dobu trvání by MĚL zarovnat s datového proudu přístup body (SAP) typu 1 nebo 2, jak jsou definovány v [ISO-14496-12] přílohy I. Odchozí HLS, čas a dobu trvání zarovnán s hranicemi segmentu. Prezentace čas a dobu trvání různých událostí zprávy v rámci stejného datového proudu událostí se nesmí překrývat. |
| zpráva                 | bajtové pole                       | Požaduje se      | Zprávy událostí. Zprávy [SCTE – 35] zpráva je binární splice_info_section(), i když [SCTE-67] doporučuje něco jiného. Zprávy [SCTE – 35] musí se jednat splice_info_section() v pořadí pro zprávy zasílané klientům HLS, Smooth a Dash v souladu s [SCTE-67]. Zprávy [SCTE – 35] binární splice_info_section() je datovou část '' okno mdat a není kódováním base64.                                                            |

------------------------------


### <a name="224-cancelation-and-updates"></a>2.2.4 zrušení a aktualizace
Zprávy lze zrušena nebo aktualizovat odesláním více zpráv se stejným prezentace čas a ID.  Prezentace čas a ID jednoznačné určení události. Poslední zprávy pro konkrétní prezentace čas, který splňuje před omezení, je zprávu, která je reagovali na ni. Aktualizované zpráva nahradí všechny dříve přijaté zprávy.  Před omezení je čtyři sekund. Zprávy přijaté nejméně čtyři sekund před časem prezentace budou reagovali na ni. 


## <a name="3-timed-metadata-delivery"></a>3 Metadata doručení vypršel časový limit.

Data události datového proudu je plné krytí ke službě Media Services. Služba Media Services jenom předá tři údaje mezi koncového bodu ingestování a koncového bodu klienta. Tyto vlastnosti budou doručeny do klienta, v souladu s [SCTE-67] nebo klienta je streamování protokolu:

1.  Schéma – název URN nebo adresa URL identifikace schéma zprávy.

2.  Prezentace čas – čas prezentace události na časové ose média.

3.  Doba trvání – Doba trvání události.

4.  ID – jedinečný identifikátor volitelné pro událost.

5.  Zpráva – data události.


## <a name="31-smooth-streaming-delivery"></a>3.1 smooth streamovaný

Odkazovat na zhuštěný sledování zpracování podrobnosti v specifikace [FMP4] a [MS-SSTR].

#### <a name="smooth-client-manifest-example"></a>Příklad manifestu Smooth klienta
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-event-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013a:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- <f> contains base-64 encoded splice_info_section() message 
    <c t=”600000000” d=”300000000”>
<f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia> 
~~~ 

## <a name="32--apple-hls-delivery"></a>3.2 Apple HLS doručení
V seznamu stop segmentu v rámci vlastní značky M3U může být vložen vypršel metadata pro Apple HTTP Live Streaming (HLS).  Aplikační vrstvu můžete analyzovat seznam stop M3U a zpracovat M3U značky. Azure Media Services bude vložení vypršel metadata ve značce EXT-X-CUE definované v [HLS].  Značky EXT-X-upozornění je aktuálně používána DynaMux pro zprávy typu ADI3.  Pro podporu zprávy SCTE 35 SCTE 35 a jiný, nastavte atributy značky EXT-X-CUE jak je definována níže:

| **Název atributu** | **Typ**                      | **Vyžaduje?**                             | **Popis**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CUE                | řetězec v uvozovkách                 | Požaduje se                                  | Zpráva kódovaný jako base64 řetězec, jak je popsáno v [IETF RFC 4648](http://tools.ietf.org/html/rfc4648). Zprávy [SCTE – 35] Toto je splice_info_section() kódováním base64.                                                                                                |
| TYP               | řetězec v uvozovkách                 | Požaduje se                                  | Název URN nebo adresa URL identifikace schéma zpráva; například "urn: Příklad: signalizace: 1.0". Zprávy [SCTE – 35] načítá typ speciální hodnotu "scte35".                                                                                                                                |
| ID                 | řetězec v uvozovkách                 | Požaduje se                                  | Jedinečný identifikátor pro událost. Pokud ID není zadáno, když je konzumována zprávy, Azure Media Services vygeneruje jedinečné id.                                                                                                                                          |
| DOBA TRVÁNÍ           | desítkové číslo s plovoucí desetinnou | Požaduje se                                  | Doba trvání události. Pokud je neznámý, hodnota musí být 0. Jednotky jsou factional sekund.                                                                                                                                                                                           |
| UPLYNULÝ ČAS            | desítkové číslo s plovoucí desetinnou | Volitelné, ale vyžaduje posuvném okně | Při signál Probíhá opakování pro podporu posuvné okno prezentace, v tomto poli musí být množství času prezentace, které uplynul od začátku události. Jednotky jsou zlomků sekund. Tato hodnota může být vyšší než původní zadaná doba trvání uživatele programu splice nebo segmentu. |
| ČAS               | desítkové číslo s plovoucí desetinnou | Požaduje se                                  | Prezentace čas události. Jednotky jsou zlomků sekund.                                                                                                                                                                                                                    |


Aplikační vrstvu HLS player použije typ k identifikaci formát zprávy, dekódování se zpráva, použije převody potřebný čas a zpracovat událost.  Události jsou časově synchronizované v segmentu stop dráhy nadřazené podle časové razítko události.  Vložení před segment nejbližší (značka #EXTINF).

#### <a name="hls-segment-playlist-example"></a>Příklad seznamu stop HLS segmentu
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:6
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:6.000000,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=60000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
#EXT-X-CUE: ID=”metadata-12.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”12.000000”, DURATION=”18.000000”,CUE=”HrwOi8vYmWVkaWEvhhaWFRlRDa=”
Fragments(video=120000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=180000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=240000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=300000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=360000000,format=m3u8-aapl)
#EXT-X-CUE: ID=”metadata-42.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”42.000000”, DURATION=”60.000000”,CUE=”PD94bWwgdm0iMS4wIiBlbmNvpD4=”
#EXTINF:6.000000,no-desc
Fragments(video=420000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=480000000,format=m3u8-aapl)
…
~~~

#### <a name="hls-message-handling"></a>Zpracování zpráv HLS

V seznamu stop segment každý dráhy videa a zvuku jsou signál události. Pozice EXT-X-CUE značky se vždy musí být buď bezprostředně před první HLS segmentu (pro uživatele programu splice out nebo segment spuštění) nebo bezprostředně po poslední HLS segment (pro uživatele programu splice v nebo segment end) které označují jeho atributy čas a dobu trvání, podle požadavku [[ HLS].

Pokud je povoleno posuvné okno prezentace, značce EXT-X-CUE opakuje často, uživatele programu splice nebo segmentu je vždy plně popsané v seznamu stop segmentu a atribut uplynulý musí použít k označení množství času uživatele programu splice nebo má segment byla aktivní, podle požadavků [HLS].

Pokud je povoleno posuvné okno prezentace, EXT-X-CUE značky se odeberou ze seznamu stop segmentu, když čas média, které odkazují provedl mimo posuvné okno prezentace.

## <a name="33--dash-delivery"></a>3.3 DASH doručení
[DASH] poskytuje tři způsoby, jak signál události:

1.  Signalizace v MPD události
2.  V rámci reprezentace (událost se zprávou (emsg) s použitím integrovaného signalizace událostí
3.  Kombinace 1 a 2

Je užitečné pro VOD streamování, protože klienti měli přístup k všechny události, okamžitě, když se stáhne MPD události signalizovala v MPD. Integrované řešení je užitečné pro živé streamování, protože není nutné znovu stáhnout MPD klientů. V případě segmentace založené na čase klienta Určuje adresu URL pro další segment přidáním doba trvání a časové razítko aktuální segment. V případě selhání, požadavku, klient předpokládá nespojitost a soubory ke stažení MPD, ale v opačném případě pokračuje streamování aniž byste museli stáhnout MPD.

Azure Media Services bude nezadávejte i signalizace v MPD a vzdálené signalizace pomocí pole zprávy událostí.

#### <a name="mpd-signaling"></a>Signalizace MPD

Události bude signál v MPD pomocí EventStream element, který se zobrazí v daném období elementu.

EventStream element má následující atributy:

| **Název atributu** | **Typ**                | **Vyžaduje?** | **Popis**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | řetězec                  | Požaduje se      | Určuje schéma zprávy. Schéma nastavena na hodnotu atributu schéma v poli Live Manifest serveru. Hodnota musí být název URN nebo adresa URL identifikace schéma zpráva; například "urn: Příklad: signalizace: 1.0".                                                                |
| hodnota              | řetězec                  | Nepovinné      | Další řetězcová hodnota, které používají vlastníci schéma k přizpůsobení sémantiku zprávy. Rozlišení různých datových proudů událostí se stejným schématem, musí být hodnota nastavena na název datového proudu událostí (trackName pro protokol Smooth ingestování nebo tuk název zprávy pro RTMP ingestování). |
| Časová osa          | 32bitové celé číslo bez znaménka | Požaduje se      | Časové osy v rysky za sekundu, časy a doba trvání pole v rámci pole 'emsg'.                                                                                                                                                                                                       |


Jsou nula nebo více událostí elementů obsažených v elementu EventStream a mají následující atributy:

| **Název atributu**  | **Typ**                | **Vyžaduje?** | **Popis**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | 64bitové celé číslo bez znaménka | Nepovinné      | MUSÍ být média prezentace čas události relativně k začátku období. Prezentace čas a dobu trvání by MĚL zarovnat s datového proudu přístup body (SAP) typu 1 nebo 2, jak jsou definovány v [ISO-14496-12] přílohy I. |
| Doba trvání            | 32bitové celé číslo bez znaménka | Nepovinné      | Doba trvání události. Toto musí být vynechána, pokud doba trvání neznámý.                                                                                                                                                 |
| id                  | 32bitové celé číslo bez znaménka | Nepovinné      | Identifikuje této instance zprávy. Zprávy s ekvivalentní sémantiku musí mít stejnou hodnotu. Pokud ID není zadáno, když je konzumována zprávy, Azure Media Services vygeneruje jedinečné id.             |
| Hodnota elementu události | řetězec                  | Požaduje se      | Zprávy událostí jako řetězec base64, jak je popsáno v [IETF RFC 4648](http://tools.ietf.org/html/rfc4648).                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>Syntaxe jazyka XML a příklad DASH manifest signalizační (MPD)

~~~ xml
<!-- XML Syntax -->
<xs:complexType name=”EventStreamType”>
  <xs:sequence>
    <xs:element name=”Event” type=”EventType” minOccurs=”0” maxOccurs=”unbounded”/>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute ref=”xlink:href”/>
  <xs:attribute ref=”xlink:actuate” default=”onRequest”/>
  <xs:attribute name=”schemeIdUri” type=”xs:anyURI” use=”required”/>
  <xs:attribute name=”value” type=”xs:string”/>
  <xs:attribute name=”timescale” type=”xs:unsignedInt”/>
</xs:complexType>
<!-- Event -->
<xs:complexType name=”EventType”>
  <xs:sequence>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute name=”presentationTime” type=”xs:unsignedLong” default=”0”/>
  <xs:attribute name=”duration” type=”xs:unsignedLong”/>
  <xs:attribute name=”id” type=”xs:unsignedInt”/>
  <xs:anyAttribute namespace=”##other” processContents=”lax”/>
</xs:complexType><?xml version=”1.0” encoding=”utf-8”?>


<!-- Example Section in MPD -->

<EventStream schemeIdUri=”urn:example:signaling:1.0” timescale=”1000” value=”player-statistics”>
  <Event presentationTime=”0” duration=”10000” id=”0”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
  <Event presentationTime=”20000” duration=”10000” id=”1”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
</EventStream>
~~~

>[!NOTE]
>Všimněte si, že presentationTime je čas prezentace události, není čas doručení zprávy.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 integrované událostí zprávy signalizace pole
Datového proudu událostí integrované vyžaduje MPD tak, aby měl InbandEventStream element na úrovni nastavit přizpůsobení.  Tento element má schemeIdUri povinný atribut a atribut volitelné časový rámec, které se také zobrazí události okno se zprávou (emsg).  Okna zpráv událostí s identifikátory schéma, které nejsou definovány v MPD by neměl být k dispozici. Pokud klient DASH zjistí zprávou události s schéma, které nejsou definované v MPD, klient se očekává ji ignorovat.
Do pole zpráva události (emsg) poskytuje signalizace pro obecné události související s časem prezentace média. Pokud je k dispozici, umístí se před libovolného pole, moof' libovolného pole, emsg'.

### <a name="dash-event-message-box-emsg"></a>Událost se zprávou 'emsg' čárka
~~~
Box Type: `emsg’
Container: Segment
Mandatory: No
Quantity: Zero or more
~~~

~~~ c
aligned(8) class DASHEventMessageBox extends FullBox(‘emsg’, version = 0, flags =0) 
{
    string scheme_id_uri;
    string value;
    unsigned int(32) timescale;
    unsigned int(32) presentation_time_delta;
    unsigned int(32) event_duration;
    unsigned int(32) id;
    unsigned int(8) message_data[];
}
~~~

Pole DASHEventMessageBox jsou definovány takto:

| **Název pole**          | **Typ pole**          | **Vyžaduje?** | **Popis**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | řetězec                  | Požaduje se      | Určuje schéma zprávy. Schéma nastavena na hodnotu atributu schéma v poli Live Manifest serveru. Hodnota musí být název URN nebo adresa URL identifikace schéma zpráva; například "urn: Příklad: signalizace: 1.0". Zprávy [SCTE – 35] to trvá speciální hodnotu "urn: scte:scte35:2013a:bin", i když [SCTE-67] doporučuje něco jiného. |
| Hodnota                   | řetězec                  | Požaduje se      | Další řetězcová hodnota, které používají vlastníci schéma k přizpůsobení sémantiku zprávy. K rozlišení různých datových proudů událostí s stejné schéma, hodnota se nastaví na název datového proudu událostí (trackName pro protokol Smooth ingestování nebo tuk název zprávy pro RTMP ingestování).                                                                  |
| Časová osa               | 32bitové celé číslo bez znaménka | Požaduje se      | Časové osy v rysky za sekundu, časy a doba trvání pole v rámci pole 'emsg'.                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32bitové celé číslo bez znaménka | Požaduje se      | Média prezentace časový rozdíl prezentace čas události a nejdřívější času prezentace v tomto segmentu. Prezentace čas a dobu trvání by MĚL zarovnat s datového proudu přístup body (SAP) typu 1 nebo 2, jak jsou definovány v [ISO-14496-12] přílohy I.                                                                                            |
| event_duration          | 32bitové celé číslo bez znaménka | Požaduje se      | Doba trvání události nebo 0xFFFFFFFF označující Neznámý doba trvání.                                                                                                                                                                                                                                                                                          |
| ID                      | 32bitové celé číslo bez znaménka | Požaduje se      | Identifikuje této instance zprávy. Zprávy s ekvivalentní sémantiku musí mít stejnou hodnotu. Pokud ID není zadáno, když je konzumována zprávy, Azure Media Services vygeneruje jedinečné id.                                                                                                                                                    |
| Message_data            | bajtové pole              | Požaduje se      | Zprávy událostí. Zprávy [SCTE – 35] daty zprávy je binární splice_info_section(), i když [SCTE-67] doporučuje něco jiného.                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 čárka zpracování zpráv

Události jsou signál, integrované, do pole 'emsg' pro videosoubory a zvukové sleduje.  Signalizace dochází pro všechny požadavky, u kterých presentation_time_delta je 15 sekund segmentovat nebo méně. Pokud je povoleno posuvné okno prezentace, zprávy o událostech se odeberou z MPD, když součet času a doby trvání události zprávy je menší než čas média dat v manifestu.  Zprávy událostí jinými slovy, se odeberou z manifestu, když čas média, kterého se týkají provedl mimo posuvné okno prezentace.

## <a name="4-scte-35-ingest"></a>4 SCTE – 35 Ingestování

Zprávy [SCTE – 35] jsou požity v binárním formátu pomocí schéma **"urn: scte:scte35:2013a:bin"** Smooth ingestování a typ **"scte35"** RTMP ingestování. Pro usnadnění převodu časování [SCTE – 35], která je založena na MPEG-2 transport stream prezentace časová razítka (bodů), mapování mezi bodů (pts_time + pts_adjustment splice_time()) a média časovou osu poskytl (čas prezentace událostí pole fragment_absolute_time pro protokol Smooth ingestování a pole pro čas pro RTMP ingestování). Mapování je nezbytné, protože hodnota b. 33 bitů navyšování přibližně každých 26,5 hodin.

Technologie Smooth Streaming ingestování vyžaduje, které musí obsahovat pole dat média (mdat) **splice_info_section()** definované v tabulce 8-1 [SCTE-35]. RTMP ingestování, atribut cue tuk zprávy je nastaven na base64encoded **splice_info_section()**. Pokud zprávy formát popsané výše, se odešlou klientům HLS, Smooth a Dash v souladu s [SCTE-67].


## <a name="5-references"></a>5 odkazy

**[SCTE – 35]**  ANSI/SCTE 35 2013a – digitální Program vložení řízení zpráv pro kabel, 2013a

**[SCTE-67]**  ANSI/SCTE 67 2014 – doporučené postupy pro SCTE 35: digitální Program vložení řízení zpráv pro kabel

**[DASH]**  ISO/IEC 23009-1 2014 – informačních technologií – dynamické adaptivní datové proudy přes protokol HTTP (pomlčka) – část 1: formáty popis a segment média prezentace, 2. vydání

**[HLS]** [“HTTP Live Streaming”, draft-pantos-http-live-streaming-14, October 14, 2014,](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]**  [(Microsoft Smooth Streaming Protocol), 15 může 2014](http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]**  ["Akce zpráva formátu AMF0"](http://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[FMP4]**  [IIS funkce Smooth streamování specifikaci formátu souboru nebo přenosu](https://microsoft.sharepoint.com/teams/mediaservices/_layouts/15/WopiFrame.aspx?sourcedoc=%7bAC5A31A4-E455-4000-96E1-AB17BD083144%7d&file=IIS%20Smooth%20Streaming%20File%20Format%20Specification%20-%20v%202%203%2001%20latest%20draft.docx&action=default)

**[LIVE FMP4]**  [Služby azure Media Services fragmentovaných MP4 Live Ingestování specifikace](https://microsoft.sharepoint.com/teams/mediaservices/_layouts/15/WopiFrame.aspx?sourcedoc=%7b5CEE1122-AA28-4368-BC8E-9C0048BF1529%7d&file=AMS%20F-MP4%20Live%20Ingest%20Specification.docx&action=default)

**[ISO-14496-12]**  ISO/IEC 14496 – 12: část 12 ISO média základní formát souboru, čtvrtý 2012 edice-07-15.

**[RTMP]**  ["Na adobe v reálném čase zasílání zpráv protokolu", 21 prosince 2012](http://wwwimages.adobe.com/www.adobe.com/content/dam/Adobe/en/devnet/rtmp/pdf/rtmp_specification_1.0.pdf) 

------------------------------------------

## <a name="next-steps"></a>Další postup
Zobrazení Media Services kurzů.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
