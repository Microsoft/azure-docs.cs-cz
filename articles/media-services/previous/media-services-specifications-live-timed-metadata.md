---
title: Azure Media Services – signalizace časovaných metadat v živém streamování | Dokumentace Microsoftu
description: Tato specifikace obsahuje dva režimy, které jsou podporovaných službou Media Services pro signalizace časovaných metadata v rámci živého streamování. To zahrnuje podporu pro obecný vypršel časový limit metadat signály, jakož i SCTE 35 signalizace pro vkládání reklam spojení.
services: media-services
documentationcenter: ''
author: johndeu
manager: cfowler
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2018
ms.author: johndeu;
ms.openlocfilehash: f29efb9a58c0b269f64d637fa3c5d59bb3610bbc
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265886"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Signalizace časovaných metadat v živém streamování


## <a name="1-introduction"></a>1 Úvod 
Pro usnadnění vkládání oznámení o inzerovaném programu, nebo vlastní události na klientský přehrávač, provozovatele vysílání často Zkontrolujte použití vypršel časový limit metadat vložený ve videu. Pokud chcete povolit tyto scénáře, služba Media Services poskytuje podporu pro přenos vypršel časový limit metadat spolu s médií, od bodu ingestování živého streamování kanálu do klientské aplikace.
Tato specifikace jsou podrobněji popsány dále dva režimy, které jsou podporovaných službou Media Services pro načasované metadata v rámci živé streamování signály:

1. [SCTE – 35] signálů, který heeds doporučených postupů uvedených v [SCTE 67]

2. Obecný časový limit vypršel signalizace režim pro zprávy, které nejsou metadat [SCTE – 35]

### <a name="12-conformance-notation"></a>1.2 zápis shody
Klíčová slova "musíte", "NOT musí", "Povinné", "SHALL", "Se NOT", "SHOULD", "By neměl", "Doporučené", "Může" a "Volitelné" v tomto dokumentu se budou interpretovat, jak je popsáno v dokumentu RFC 2119

### <a name="13-terms-used"></a>1.3 termínů

| Označení              | Definice                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Čas prezentace | Čas, který se zobrazí události prohlížeč. Čas představuje chvíli na časové ose média, prohlížeč zobrazí události. Čas prezentace zprávy SCTE 35 splice_info() příkaz je například splice_time(). |
| Čas přijetí      | Čas doručení zprávy událostí. Čas se obvykle liší od prezentační čas události, protože předem prezentace události, kterou jsou odesílány zprávy o událostech.                                     |
| Zhuštěný sledování      | média, které není průběžné sledování a čas je synchronizovaný s nadřazenou nebo ovládací prvek track.                                                                                                                                    |
| Zdroj            | Služby streamování médií Azure                                                                                                                                                                                                |
| Jímka kanálu      | Azure Media živého streamování služby                                                                                                                                                                                           |
| HLS               | Protokol Apple HTTP Live Streaming.                                                                                                                                                                                               |
| POMLČKA              | Dynamické adaptivní streamování přes HTTP                                                                                                                                                                                             |
| Vyhladit            | Technologie Smooth Streaming protokolu                                                                                                                                                                                                        |
| MPEG2-TS          | Datové proudy MPEG 2 přenosu                                                                                                                                                                                                         |
| RTMP              | Protokol v reálném čase multimédií                                                                                                                                                                                                    |
| uimsbf            | Celé číslo bez znaménka, nejvýznamnější bit nejprve.                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>Ingestování 2 vypršel časový limit metadat
## <a name="21-rtmp-ingest"></a>2.1 zpracování příjmu RTMP
RTMP podporuje odesílání jako vložený v rámci datového proudu RTMP zprávy upozornění tuk signálů vypršel časový limit metadat. Nějakou dobu před skutečné události můžou odesílat zprávy upozornění nebo reklam spojení je potřeba provést. Pro podporu tohoto scénáře, se v něm chceme odešle skutečný čas spojení nebo segmentu. Další informace najdete v tématu [AMF0].

Následující tabulka popisuje formátu datovou část zprávy tuk, který bude ingestovat Media Services.

Název zprávy tuk slouží k rozlišení různých datových proudů událostí stejného typu.  Zprávy [SCTE – 35] název tuk zprávy musí být "onAdCue" podle doporučení v [SCTE 67].  Všechna pole není uveden v seznamu musí být ignorovány, tak, aby se v budoucnu potlačen inovace tohoto návrhu.

### <a name="signal-syntax"></a>Syntaxe signálu
Jednoduchý režim RTMP Media Services podporuje jednu zprávu upozornění tuk nazývá "onAdCue" v následujícím formátu:

### <a name="simple-mode"></a>Jednoduchý režim

| Název pole | Typ pole | Povinné? | Popisy                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| upozornění        | Řetězec     | Požaduje se | Zpráva o události.  Musí být "SpliceOut" k označení jednoduchý režim splice.                                              |
| id         | Řetězec     | Požaduje se | Jedinečný identifikátor popisující spojení nebo segmentu. Určuje tato instance zprávy                            |
| doba trvání   | Číslo     | Požaduje se | Doba trvání spojení. Jednotky jsou zlomků sekund.                                                                |
| elapsed    | Číslo     | Nepovinné | Když signál, který se opakuje za účelem podpory nalaďte, toto pole musí být prezentace dobu, která uplynula od začátku spojení. Jednotky jsou zlomků sekund. Při použití režimu jednoduchého, tato hodnota by neměla překročit původní trvání spojení.                                                  |
| time       | Číslo     | Požaduje se | Musí být čas spojení, v čase prezentace. Jednotky jsou zlomků sekund.                                     |

---------------------------

### <a name="scte-35-mode"></a>Režim SCTE 35

| Název pole | Typ pole | Povinné? | Popisy                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| upozornění        | Řetězec     | Požaduje se | Zpráva o události.  Zprávy [SCTE – 35] musí být kódování base64 binární soubor (IETF RFC 4648) kódovaný splice_info_section() zprávy k odeslání HLS, Smooth a Dash klientům v souladu s [SCTE 67].                                              |
| type       | Řetězec     | Požaduje se | Název URN nebo adresy URL identifikující zprávu schéma. Zprávy [SCTE – 35] musí být "urn: scte:scte35:2013a:bin" zprávy k odeslání HLS, Smooth a Dash klientům v souladu s [SCTE 67].  |
| id         | Řetězec     | Požaduje se | Jedinečný identifikátor popisující spojení nebo segmentu. Určuje tato instance zprávy.  Zprávy se sémantikou ekvivalentní musí mít stejnou hodnotu.|
| doba trvání   | Číslo     | Požaduje se | Doba trvání události nebo ad spojení-segmentu, pokud je známý. Pokud je neznámý, hodnota musí být 0.                                                                 |
| elapsed    | Číslo     | Nepovinné | Při reklamním signálu [SCTE – 35] se se opakuje, aby bylo možné ladit, toto pole musí být prezentace dobu, která uplynula od začátku spojení. Jednotky jsou zlomků sekund. V režimu [SCTE – 35] Tato hodnota může překročit původní zadaná doba trvání spojení nebo segmentu.                                                  |
| time       | Číslo     | Požaduje se | Prezentace čas spojení události nebo ad.  Čas prezentace a doba trvání by MĚLO odpovídat s Stream přístup body (SAP) typu 1 nebo 2, jak jsou definovány v [ISO-14496-12] přílohy I. Pro výchozí přenos dat HLS čas a dobu trvání by MĚLO odpovídat segmentu hranice. Čas prezentace a doby trvání různých událostí zpráv v rámci stejného datového proudu událostí se nesmí překrývat. Jednotky jsou zlomků sekund.

---------------------------

#### <a name="211-cancellation-and-updates"></a>2.1.1 zrušení a aktualizace

Možné zrušit nebo aktualizaci zasláním více zpráv se stejnými čas prezentace a ID zprávy Čas prezentace a ID jednoznačné určení události, a poslední zprávy přijaté pro konkrétní prezentace dobu, která splňuje omezení před je zpráva, která je reagovali na ni. Aktualizovaná událost nahradí všechny dříve přijaté zprávy. Před omezení je čtyři sekundy. Zprávy přijaté alespoň čtyři sekundy před jejich prezentaci se má akce provést.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Ingestování fragmentovaného MP4 (technologie Smooth Streaming)
Další požadavky na živý stream ingestování získáte [LIVE FMP4]. Následující části obsahují podrobné informace o ingestování vypršel časový limit prezentace metadat.  Vypršel časový limit prezentace metadat ingestují jako zhuštěné sledování, která je definována v obou Live Manifest pole Server (viz MS SSTR) a pole Movie (moov).  Každý zhuštěné fragment se skládá z pole fragmentu Movie (moof) a média Data Box ("mdat"), kde '' okno mdat je binární zprávy.

#### <a name="221-live-server-manifest-box"></a>2.2.1 live manifestu pole serveru
Zhuštěný sledování musí být deklarována v poli Live Server Manifest s \<textstream\> položky a musí obsahovat následující atributy nastavit:

| **Název atributu** | **Typ pole** | **Vyžaduje?** | **Popis**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Číslo         | Požaduje se      | MUSÍ být "0", určující stopy s neznámým, proměnné s přenosovou rychlostí.                                                                                                                                                                                                 |
| parentTrackName    | Řetězec         | Požaduje se      | MUSÍ být název dráhy nadřazené časové kódy zhuštěné sledování jsou zarovnána časový rámec. Jeden směr určený nadřazený nemůže být zhuštěné sledování.                                                                                                                    |
| manifestOutput     | Logická hodnota        | Požaduje se      | MUSÍ být "true", označíte, že zhuštěné sledování vloží v manifestu klienta funkce Smooth.                                                                                                                                                               |
| Podtyp            | Řetězec         | Požaduje se      | MUSÍ být čtyři kód znaku "DATA".                                                                                                                                                                                                                         |
| Schéma             | Řetězec         | Požaduje se      | MUSÍ být název URN nebo adresa URL identifikaci schéma zprávy. Zprávy [SCTE – 35] musí být "urn: scte:scte35:2013a:bin" zprávy k odeslání HLS, Smooth a Dash klientům v souladu s [SCTE 67]. |
| TrackName          | Řetězec         | Požaduje se      | MUSÍ být název zhuštěné sledování. TrackName slouží k rozlišení více datových proudů událostí se stejným schématem. Každého datového proudu událostí jedinečný, musí mít název jedinečný sledování.                                                                           |
| Časová osa          | Číslo         | Nepovinné      | MUSÍ být na časové ose dráhy nadřazené.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 pole movie

Do pole Movie (moov) se řídí Live Manifest pole Server jako součást datový proud hlavičky zhuštěného stopu.

Okno ' moov ' by měla obsahovat **TrackHeaderBox (tkhd)** pole definované v [ISO-14496-12] s následujícími omezeními:

| **Název pole** | **Typ pole**          | **Vyžaduje?** | **Popis**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| doba trvání       | 64bitové celé číslo bez znaménka | Požaduje se      | By měla být 0, protože políčka sledování nemá žádnou ukázky a celková doba trvání vzorků v poli sledování, které je 0. |
-------------------------------------

Okno ' moov ' by měla obsahovat **HandlerBox (hdlr)** jak jsou definovány v [ISO-14496-12] s následujícími omezeními:

| **Název pole** | **Typ pole**          | **Vyžaduje?** | **Popis**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32bitové celé číslo bez znaménka | Požaduje se      | MUSÍ být 'meta'. |
-------------------------------------

Do pole 'stsd' by MĚL obsahovat MetaDataSampleEntry pole s názvem kódování, jak jsou definovány v [ISO-14496-12].  Například pro zprávy SCTE 35 kódování název by MĚL být "scte".

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 pole fragmentu filmů a média Data Box

Zhuštěný sledování fragmenty se skládají z pole fragmentu Movie (moof) a média Data Box (mdat).

MUSÍ obsahovat pole MovieFragmentBox (moof) **TrackFragmentExtendedHeaderBox (uuid)** pole definované v [MS-SSTR] s následující pole:

| **Název pole**         | **Typ pole**          | **Vyžaduje?** | **Popis**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64bitové celé číslo bez znaménka | Požaduje se      | MUSÍ být čas doručení události. Tato hodnota odpovídá zprávu s jeden směr určený nadřazený.   |
| fragment_duration      | 64bitové celé číslo bez znaménka | Požaduje se      | MUSÍ být doba trvání události. Doba trvání může být nula, že doba trvání je neznámý. |

------------------------------------


Do pole MediaDataBox (mdat) musí mít následující formát:

| **Název pole**          | **Typ pole**                   | **Vyžaduje?** | **Popis**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | 32-bit znaménka (uimsbf) | Požaduje se      | Určuje formát obsah '' okno mdat. Nerozpoznaná verze se bude ignorovat. Aktuálně je jedinou podporovanou verzí 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-bit znaménka (uimsbf) | Požaduje se      | Určuje tato instance zprávy. Zprávy se sémantikou ekvivalentní musí mít stejnou hodnotu To znamená, že zpracování jakékoli okno se zprávou jedna událost s tímto id je dostačující.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bit znaménka (uimsbf) | Požaduje se      | Součet fragment_absolute_time, zadaný v TrackFragmentExtendedHeaderBox a presentation_time_delta musí být čas prezentace události. Čas prezentace a doba trvání by MĚLO odpovídat s Stream přístup body (SAP) typu 1 nebo 2, jak jsou definovány v [ISO-14496-12] přílohy I. Pro výchozí přenos dat HLS čas a dobu trvání by MĚLO odpovídat segmentu hranice. Čas prezentace a doby trvání různých událostí zpráv v rámci stejného datového proudu událostí se nesmí překrývat. |
| zpráva                 | pole bajtů                       | Požaduje se      | Zpráva o události. Zprávy [SCTE – 35] zpráva je binární splice_info_section(), i když [SCTE 67] doporučí něco jiného. Zprávy [SCTE – 35] musí se jednat splice_info_section() zprávy k odeslání HLS, Smooth a Dash klientům v souladu s [SCTE 67]. Zprávy [SCTE – 35] binární splice_info_section() je datová část "" okno mdat a není kódování base64.                                                            |

------------------------------


### <a name="224-cancellation-and-updates"></a>2.2.4 zrušení a aktualizace
Možné zrušit nebo aktualizaci zasláním více zpráv se stejnými čas prezentace a ID zprávy  Čas prezentace a ID jednoznačné určení události. Poslední zprávy přijaté pro konkrétní prezentace času, která splňuje omezení před, je zpráva, která je reagovali na ni. Aktualizované zpráva nahradí všechny dříve přijaté zprávy.  Před omezení je čtyři sekundy. Zprávy přijaté alespoň čtyři sekundy před jejich prezentaci se má akce provést. 


## <a name="3-timed-metadata-delivery"></a>3 vypršel časový limit doručování metadat

Data datového proudu událostí je neprůhledný Media Services. Media Services předá tři údaje pouze mezi koncovým bodem ingestování a koncový bod klienta. Následující vlastnosti se doručují klientům v souladu s [SCTE 67] a/nebo klienta je streamovací protokol:

1.  Schéma – název URN nebo adresa URL identifikaci schéma zprávy.

2.  Prezentace čas – čas prezentace události na časové ose média.

3.  Doba trvání – Doba trvání události.

4.  ID – volitelné jedinečný identifikátor pro událost.

5.  Zpráva – data události.


## <a name="31-smooth-streaming-delivery"></a>3.1 streamování přenášel

Odkazovat na sadu zhuštěných sledování drobnosti v [MS-SSTR].

#### <a name="smooth-client-manifest-example"></a>Klient funkce Smooth manifestu příklad
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

## <a name="32--apple-hls-delivery"></a>3.2 Apple HLS doručování
Vypršel časový limit metadat pro Apple HTTP Live Streaming (HLS), může být vložen do seznamy stop segmentu v rámci vlastní značky M3U.  Aplikační vrstvu můžete analyzovat M3U stop a zpracování M3U značky. Azure Media Services bude vložení vypršel časový limit metadata ve značce ext, Přípona-X-startovací definované v [HLS].  Značka ext, Přípona-X-startovací aktuálně využívá DynaMux pro zprávy typu ADI3.  SCTE 35 a bez zpráv SCTE 35, nastavte atributy značky ext, Přípona-X-startovací jak je definována níže:

| **Název atributu** | **Typ**                      | **Vyžaduje?**                             | **Popis**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| UPOZORNĚNÍ                | řetězec v uvozovkách                 | Požaduje se                                  | Zpráva zakódován jako řetězec ve formátu base64, jak je popsáno v [IETF RFC 4648](http://tools.ietf.org/html/rfc4648). Zprávy [SCTE – 35] Toto je splice_info_section() kódování base64.                                                                                                |
| TYP               | řetězec v uvozovkách                 | Požaduje se                                  | Název URN nebo adresy URL identifikující zprávu schéma. Typ trvá v zájmu zprávy [SCTE – 35] zvláštní hodnota "scte35".                                                                                                                                |
| ID                 | řetězec v uvozovkách                 | Požaduje se                                  | Jedinečný identifikátor pro událost. Pokud ID není zadáno, když se přijaté zprávy, Azure Media Services bude generovat jedinečný identifikátor.                                                                                                                                          |
| DOBA TRVÁNÍ           | desítkové číslo s plovoucí desetinnou | Požaduje se                                  | Doba trvání události. Pokud je neznámý, hodnota musí být 0. Jednotky jsou factional sekund.                                                                                                                                                                                           |
| UPLYNUL            | desítkové číslo s plovoucí desetinnou | Volitelné, ale vyžaduje posuvné okno | Když signál, který se právě opakuje pro podporu posuvné okno prezentace, toto pole musí být prezentace dobu, která uplynula od začátku události. Jednotky jsou zlomků sekund. Tato hodnota může být delší než původní zadaná doba trvání spojení nebo segmentu. |
| ČAS               | desítkové číslo s plovoucí desetinnou | Požaduje se                                  | Prezentace čas události. Jednotky jsou zlomků sekund.                                                                                                                                                                                                                    |


Aplikační vrstvu player HLS použije typ určit formát zprávy, dekódovat zprávu, použít převody potřebný čas a zpracovat události.  Události jsou časově synchronizované v seznamy stop segmentu dráhy nadřazené podle časové razítko události.  Jsou vloženy před nejbližší segment (#EXTINF značky).

#### <a name="hls-segment-playlist-example"></a>Příklad seznamu stop segmentu HLS
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

#### <a name="hls-message-handling"></a>Zpracování zpráv HLS

Události jsou signál, v seznamu stop segmentu každé video a audiostreamů dráhy. Pozice EXT-X-CUE značky se vždy musí být buď bezprostředně před první HLS segmentu (pro uživatele programu splice out nebo segment spuštění) nebo bezprostředně po poslední HLS segment (pro uživatele programu splice v nebo segment end) které označují jeho atributy čas a dobu trvání, podle požadavku [HLS].

Obrysů posuvné okno prezentace značky ext, Přípona-X-upozornění je nutné opakovat dostatečně často, spojení nebo segment je vždy plně popsány v seznamy stop segmentu a musí být použit atribut uplynulý udávajících dobu spojení nebo segment má byl aktivní, podle požadavků [HLS].

Když je povolené posuvné okno prezentace, značky ext, Přípona-X-startovací odstraněna z seznamy stop segmentu, jakmile mimo posuvné okno prezentace se vrátila čas média, který se odkazuje na.

## <a name="33--dash-delivery"></a>3.3 DASH doručování
[DASH] nabízí tři způsoby, jak signál události:

1.  Signál, MPD události
2.  Události signalizován integrovaných v reprezentaci (pomocí okna se zprávou události (emsg)
3.  Kombinace 1 a 2

Signál, MPD události jsou užitečné pro streamování videa na Vyžádání, protože klienti měli přístup k všechny události, když má, okamžitě se stáhne MPD. V integrovaných řešení je užitečné pro živé streamování, protože klienti nemusí MPD stáhnout znovu. Pro segmentace podle času klienta Určuje adresu URL pro dalšího segmentu přidáním doba trvání a časové razítko aktuální segment. Který z žádostí selže, klient předpokládá diskontinuitu a soubory ke stažení MPD ale jinak bude pokračovat bez stažení MPD streamování.

Azure Media Services bude proveďte oba signalizace v MPD a vzdálené signalizace pomocí okna se zprávou události.

#### <a name="mpd-signaling"></a>Signalizace MPD

Události se signál v MPD pomocí EventStream elementu, který se zobrazí v rámci elementu období.

EventStream element má následující atributy:

| **Název atributu** | **Typ**                | **Vyžaduje?** | **Popis**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | řetězec                  | Požaduje se      | Určuje schéma zprávy. Schéma nastavena na hodnotu atributu schéma do pole Live manifestu serveru. Hodnota musí být název URN nebo adresa URL identifikaci schéma zprávy; například "urn: scte:scte35:2013a:bin".                                                                |
| hodnota              | řetězec                  | Nepovinné      | Další řetězcová hodnota, vlastníky schéma používané k úpravám sémantiku zprávy. Rozlišení více datových proudů událostí se stejným schématem, musí být hodnota nastavena na název datového proudu událostí (trackName pro protokol Smooth ingestování nebo tuk název zprávy pro RTMP ingestování). |
| Timescale          | 32bitové celé číslo bez znaménka | Požaduje se      | Časové osy v taktech za sekundu, časy a doba trvání polí v rámci pole "emsg".                                                                                                                                                                                                       |


Nula nebo více prvků události jsou obsaženy v rámci elementu EventStream a mají následující atributy:

| **Název atributu**  | **Typ**                | **Vyžaduje?** | **Popis**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | 64bitové celé číslo bez znaménka | Nepovinné      | MUSÍ být médium prezentace čas události vzhledem k počátku období. Čas prezentace a doba trvání by MĚLO odpovídat s Stream přístup body (SAP) typu 1 nebo 2, jak jsou definovány v [ISO-14496-12] přílohy I. |
| doba trvání            | 32bitové celé číslo bez znaménka | Nepovinné      | Doba trvání události. Toto musí být vynechána, pokud doba trvání neznámý.                                                                                                                                                 |
| id                  | 32bitové celé číslo bez znaménka | Nepovinné      | Určuje tato instance zprávy. Zprávy se sémantikou ekvivalentní musí mít stejnou hodnotu. Pokud ID není zadáno, když se přijaté zprávy, Azure Media Services bude generovat jedinečný identifikátor.             |
| Hodnota elementu Event | řetězec                  | Požaduje se      | Zpráva o události jako řetězec ve formátu base64, jak je popsáno v [IETF RFC 4648](http://tools.ietf.org/html/rfc4648).                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>Příklad pro DASH a syntaxe jazyka XML manifestu signalizační (MPD)

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
  <EventStream schemeIdUri="urn:scte:scte35:2013a:bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w==</Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">/DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==</Event>
        <Event presentationTime="15447167300227600" duration="600000000" id="1028">/DAlAAAAAAAAAP/wFAUAAAQEf+//KjkknP4AUmXAAAAAAAAAWcEldA==</Event>
        <Event presentationTime="15447168350227600" duration="600000000" id="1029">/DAlAAAAAAAAAP/wFAUAAAQFf+//KslyqP4AUmXAAAAAAAAAvKNt0w==</Event>
        <Event presentationTime="15447169400227600" duration="300000000" id="1030">/DAlAAAAAAAAAP/wFAUAAAQGf+//K1mIvP4AKTLgAAAAAAAAt2zEbw==</Event>
        <Event presentationTime="15447170450227600" duration="600000000" id="1031">/DAlAAAAAAAAAP/wFAUAAAQHf+//K+hc/v4AUmXAAAAAAAAANNRzVw==</Event>
    </EventStream>
~~~

>[!NOTE]
>Všimněte si, že presentationTime je čas prezentace události, není čas doručení zprávy.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 integrovaných událostí signalizace pole zprávy
Datového proudu událostí v vzdálené vyžaduje MPD mít InbandEventStream elementu na úrovni přizpůsobení nastavení.  Tento element má schemeIdUri povinný atribut a atribut volitelné časové osy, které se zobrazí také v případě okno se zprávou (emsg).  Události okna se zprávou s těmito identifikátory schéma, které nejsou definovány v MPD by neměl být k dispozici. Pokud klient DASH zjistí okno se zprávou události obsahující schéma, které není definovaný v MPD, očekává se klient ho ignorovat.
Do pole zpráva o události (emsg) poskytuje signalizace pro obecné události související s prezentace čas média. Pokud jsou k dispozici, libovolného pole 'emsg' musí být umístěn před libovolného pole "moof".

### <a name="dash-event-message-box-emsg"></a>Okno se zprávou události DASH "emsg.
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
| scheme_id_uri           | řetězec                  | Požaduje se      | Určuje schéma zprávy. Schéma nastavena na hodnotu atributu schéma do pole Live manifestu serveru. Hodnota musí být název URN nebo adresy URL identifikující zprávu schéma. Zprávy [SCTE – 35] to trvá v zájmu zvláštní hodnota "urn: scte:scte35:2013a:bin", přestože [SCTE 67] doporučí něco jiného. |
| Hodnota                   | řetězec                  | Požaduje se      | Další řetězcová hodnota, vlastníky schéma používané k úpravám sémantiku zprávy. Rozlišení více streamů událostí s stejné schéma, hodnota bude nastavena na název datového proudu událostí (trackName pro protokol Smooth ingestování nebo tuk název zprávy pro RTMP ingestování).                                                                  |
| Timescale               | 32bitové celé číslo bez znaménka | Požaduje se      | Časové osy v taktech za sekundu, časy a doba trvání polí v rámci pole "emsg".                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32bitové celé číslo bez znaménka | Požaduje se      | Prezentace media časový rozdíl času prezentace události a Nejdřívější čas prezentace v tomto segmentu. Čas prezentace a doba trvání by MĚLO odpovídat s Stream přístup body (SAP) typu 1 nebo 2, jak jsou definovány v [ISO-14496-12] přílohy I.                                                                                            |
| event_duration          | 32bitové celé číslo bez znaménka | Požaduje se      | Trvání události, nebo hodnotu 0xFFFFFFFF označující Neznámý doby trvání.                                                                                                                                                                                                                                                                                          |
| ID                      | 32bitové celé číslo bez znaménka | Požaduje se      | Určuje tato instance zprávy. Zprávy se sémantikou ekvivalentní musí mít stejnou hodnotu. Pokud ID není zadáno, když se přijaté zprávy, Azure Media Services bude generovat jedinečný identifikátor.                                                                                                                                                    |
| Message_data            | pole bajtů              | Požaduje se      | Zpráva o události. Zprávy [SCTE – 35] data zprávy je binární splice_info_section(), ale [SCTE 67] doporučí něco jiného.                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 pomlčka zpracování zpráv

Události jsou signalizován integrovaná, v rámci pole 'emsg' u videí a zvukových stop.  Signalizaci proběhne pro všechny požadavky, u kterých presentation_time_delta je 15 sekund segmentu nebo méně. Pokud je povoleno posuvné okno prezentace, zprávy o událostech při jsou odebrány ze MPD součet čas a dobu trvání zpráva o události je menší než čas multimediální data v manifestu.  Jinými slovy zprávy o událostech se odeberou z manifestu při mimo posuvné okno prezentace se vrátila čas média, na který odkazují.

## <a name="4-scte-35-ingest"></a>Ingestování 4 SCTE – 35

Zprávy [SCTE – 35] jsou přijaté v binárním formátu pomocí schéma **"urn: scte:scte35:2013a:bin"** pro protokol Smooth ingestování a typ **"scte35"** RTMP ingestování. Pro usnadnění převodu [SCTE – 35] časování, která je založena na MPEG-2 transport stream prezentace časová razítka (bodů), mapování mezi bodů (pts_time + pts_adjustment splice_time()) a časová osa média pochází od (čas události prezentace pole fragment_absolute_time pro protokol Smooth ingestování a pole čas pro RTMP ingestování). Mapování je nezbytné, protože hodnota bodů 33-bit navyšování přibližně každých 26,5 hodin.

Ingestování technologie Smooth Streaming, které musí obsahovat Data Box média (mdat) vyžaduje **splice_info_section()** definované v tabulce 8-1 [SCTE – 35]. RTMP ingestování, startovací atribut tuk zprávy je nastaven na base64encoded **splice_info_section()**. Pokud zprávy formátu je popsáno výše, jejich odesláním HLS, Smooth a Dash klientům v souladu s [SCTE 67].


## <a name="5-references"></a>Odkazy na 5

**[SCTE – 35]**  ANSI/SCTE 35 2013a – digitální Program vložení řízení zprávu pro kabel, 2013a

**[SCTE 67]**  ANSI/SCTE 67 2014 – doporučené postupy pro SCTE 35: Digitální Program vložení řízení zpráv pro kabel

**[DASH]**  ISO/IEC 23009 1 2014 – informačních technologií – dynamické adaptivní streamování přes HTTP (DASH) – část 1: Popis a segment média prezentace formátů, 2 edition

**[HLS]** [“HTTP Live Streaming”, draft-pantos-http-live-streaming-14, October 14, 2014,](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]**  ["Microsoft Smooth Streaming protokol", 15. května 2014](https://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]**  ["AMF0 formát zprávy akce"](http://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[LIVE FMP4]**  [Specifikace Ingestování fragmentovaného MP4 za služby azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)

**[ISO-14496-12]**  ISO/IEC 14496-12: Média základní část 12 ISO formát souboru, čtvrtý Edition 2012-07-15.

**[RTMP]**  ["Adobe v reálném čase protokol zasílání zpráv", 21. prosince 2012](https://www.adobe.com/devnet/rtmp.html) 

------------------------------------------

## <a name="next-steps"></a>Další postup
Postupy výuky zobrazení Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
