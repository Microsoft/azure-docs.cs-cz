---
title: Mediální služby Azure – signalizace časovaných metadat v živém vysílání
description: Tato specifikace popisuje metody pro signalizaci časovaných metadat při ingestování a streamování do Azure Media Services. To zahrnuje podporu pro obecné časované metadata signály (ID3), stejně jako SCTE-35 signalizace pro vkládání reklam a splice stavu signalizace.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: 551fb0cb9f3745a62d5d84f2c4878bbbbe5ad9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137318"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Signalizace časovaných metadat v živém vysílání 

Naposledy aktualizováno: 2019-08-22

### <a name="conformance-notation"></a>Zápis shody

Klíčová slova "MUST", "MUST NOT", "REQUIRED", "MUST", "MUST NOT", "SHOULD", "SHOULD", "RECOMMENDED", "MAY" a "OPTIONAL" v tomto dokumentu se vykládají tak, jak je popsáno v Dokumentu RFC 2119

## <a name="1-introduction"></a>1. Úvod 

Aby bylo možné signalizovat vkládání reklam nebo vlastních událostí metadat v přehrávači klienta, provozovatelé vysílání často využívají časovaná metadata vložená do videa. Chcete-li povolit tyto scénáře, Media Services poskytuje podporu pro přenos časovaných metadat z bodu ingestování kanálu živého streamování do klientské aplikace.
Tato specifikace popisuje několik režimů, které jsou podporovány službou Media Services pro časovaná metadata v rámci živých přenosových signálů.

1. [SCTE-35] signalizace, která splňuje normy stanovené [SCTE-35], [SCTE-214-1], [SCTE-214-3] a [RFC8216]

2. [SCTE-35] signalizace, která splňuje starší specifikaci [Adobe-Primetime] pro signalizaci reklamrtmp.
   
3. Obecný režim signalizace časovaných metadat pro zprávy, které **nejsou** [SCTE-35] a mohou přenášet [ID3v2] nebo jiné vlastní schémata definované vývojářem aplikace.

## <a name="11-terms-used"></a>1.1 Použité termíny

| Označení                | Definice                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Přestávka na reklamy            | Místo nebo místo v čase, kde může být naplánováno jedno nebo více reklam; stejně jako příležitost k využití a umístění.                                                                                                                     |
| Rozhodnutí o reklamě | externí služba, která rozhoduje o tom, které reklamy a doby trvání se uživateli zobrazí. Služby jsou obvykle poskytovány partnerem a jsou mimo rozsah pro tento dokument.                                                                    |
| Startovací                 | Údaj o čase a parametrech nadcházející přestávky reklamy. Upozorňujeme, že podněty mohou naznačovat čekající přepnutí na konec reklamy, čekající na přepnutí na další reklamu v rámci přestávky reklamy a čekající na přepnutí z přerušení reklamy na hlavní obsah.           |
| Balírny            | Azure Media Services "Streaming Endpoint" poskytuje dynamické možnosti balení pro DASH a HLS a označuje se jako "Packager" v mediálním průmyslu.                                                                              |
| Čas prezentace   | Čas, kdy je událost prezentována divákovi. Čas představuje okamžik na časové ose média, kdy divák uvidí událost. Například čas prezentace příkazové zprávy SCTE-35 splice_info() je splice_time(). |
| Čas příjezdu        | Čas doručení zprávy o události. Čas se obvykle liší od doby prezentace události, protože zprávy o událostech jsou odesílány před časem prezentace události.                                                    |
| Řídká stopa        | stopa média, která není souvislá a je čassynchronizována s nadřazenou nebo ovládací stopou.                                                                                                                                                  |
| Zdroj              | Služba streamování médií Azure                                                                                                                                                                                                             |
| Kanál Jímka        | Služba živého streamování Azure Media                                                                                                                                                                                                        |
| HLS                 | Protokol Apple HTTP Live Streaming                                                                                                                                                                                                            |
| Pomlčka                | Dynamické adaptivní streamování přes protokol HTTP                                                                                                                                                                                                          |
| Hladké              | Protokol hladkého streamování                                                                                                                                                                                                                     |
| MPEG2-TS            | Dopravní proudy MPEG 2                                                                                                                                                                                                                      |
| RTMP                | Multimediální protokol v reálném čase                                                                                                                                                                                                                 |
| uimsbf              | Nepodepsané celé číslo, nejvýznamnější bit jako první.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1.2 Normativní reference

Následující dokumenty obsahují ustanovení, která prostřednictvím odkazu v tomto textu představují ustanovení tohoto dokumentu. Všechny dokumenty podléhají revizi normalizačními orgány a čtenáři se vyzývají, aby prozkoumali možnost použití nejnovějších vydání níže uvedených dokumentů. Čtenářům se také připomíná, že novější edice odkazovaných dokumentů nemusí být kompatibilní s touto verzí specifikace časovaných metadat pro Azure Media Services.


| Standard          | Definice                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Primetime Digitální program Vložení Signalizace Specifikace 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [Referenční příručka jazyka jazyka FLASH](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["Formát zprávy akce AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | DASH Průmysl forum Interop pokyny v 4,2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| To je v pořádku.         | Časovaná metadata pro živé vysílání protokolu HTTP -[https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Časovaná metadata ve společném formátu aplikace médií (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | Značka ID3 verze 2.4.0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: Část 12 ISO základní formát mediálního souboru, FourthEdition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Informační technologie -- Dynamické adaptivní streamování přes HTTP (DASH) -- Část 1: Popis prezentace médií a formáty segmentů. května 2014. Zveřejněna. Adresu url:https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Informační technologie -- Formát multimediální aplikace (MPEG-A) -- Část 19: Společný formát mediální aplikace (CMAF) pro segmentovaná média. ledna 2018. Zveřejněna. Adresu url:https://www.iso.org/standard/71975.html |
| To je v pořádku.        | Informační technologie -- Technologie systémů MPEG -- Část 7: Společné šifrování v souborech formátu základního mediálního souboru ISO. února 2016. Zveřejněna. Adresu url:https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", 15.](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-Ingest]  | [Specifikace fragmentace živého ingestu služby Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)                                                      |
| [RFC8216]         | R. Pantos, Ed.; W. Květen. Živé vysílání protokolu HTTP. srpna 2017. Informační. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | Kódování dat Base16, Base32 a Base64 -[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| To je v pořádku.            | ["Protokol zasílání zpráv společnosti Adobe v reálném čase", 21.](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35: 2019 - Zpráva o vložení digitálního programu pro kabel -https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH pro kabelové služby založené na IP části 1: Omezení a rozšíření MPD                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH pro kabelové služby založené na IP část 3: DASH/ FF profil                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – Plánování událostí a oznamovací rozhraní                                                                                                                                                  |
| [SCTE-250]        | Api pro správu událostí a signalizace (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. Singestované metadata načasované

Azure Media Services podporuje v reálném čase in-band metadata pro [RTMP] a hladké streamování [MS-SSTR-Ingest] protokoly. Metadata v reálném čase lze použít k definování vlastních událostí pomocí vlastních vlastních schémat (JSON, Binary, XML) a oborově definovaných formátů, jako je ID3 nebo SCTE-35 pro signalizaci reklamy ve streamu vysílání. 

Tento článek obsahuje podrobnosti o tom, jak odesílat vlastní časované signály metadat pomocí podporovaných ingestování protokolů Azure Media Services. Článek také vysvětluje, jak manifesty pro HLS, DASH a smooth streaming jsou zdobeny časované metadata signály, stejně jako jak se provádí v pásmu, když je obsah dodáván pomocí CMAF (FRAGMENTY MP4) nebo Transport Stream (TS) segmenty pro HLS. 

Mezi běžné scénáře použití časovaných metadat patří:

 - Reklamní signály SCTE-35 pro aktivaci reklamních přestávek v živé události nebo lineárním vysílání
 - Vlastní metadata ID3, která mohou spouštět události v klientské aplikaci (prohlížeč, iOS nebo Android)
 - Vlastní definovaná metadata JSON, Binární nebo XML pro aktivaci událostí v klientské aplikaci
 - Telemetrie z živého kodéru, IP kamery nebo dronu
 - Události z IP kamery, jako je pohyb, detekce obličeje atd.
 - Informace o zeměpisné poloze z akční kamery, dronu nebo pohybujícího se zařízení
 - Písně
 - Hranice programu na lineárním živém kanálu
 - Obrázky nebo rozšířená metadata, která se mají zobrazit v živém přenosu
 - Sportovní výsledky nebo informace o herních hodinách
 - Interaktivní reklamní balíčky, které se zobrazí vedle videa v prohlížeči
 - Kvízy nebo ankety
  
Azure Media Services Živé události a Packager jsou schopné přijímat tyto časované signály metadat a převést je do datového proudu metadat, které mohou oslovit klientské aplikace pomocí protokolů založených na standardech, jako je HLS a DASH.


## <a name="21-rtmp-timed-metadata"></a>2.1 Časovaná metadata RTMP

Protokol [RTMP] umožňuje, aby byly časované signály metadat odesílány pro různé scénáře včetně vlastních metadat a signálů reklam SCTE-35. 

Reklamní signály (cue zprávy) jsou odesílány jako [AMF0] cue zprávy vložené do [RTMP] proudu. Cue zprávy mohou být odeslány někdy před skutečnou událost nebo [SCTE35] ad splice signál musí dojít. Pro podporu tohoto scénáře je v rámci tázací zprávy odesláno skutečné časové razítko prezentace události. Další informace naleznete v tématu [AMF0].

Následující příkazy [AMF0] jsou podporované službou Azure Media Services pro ingestování RTMP:

- **onUserDataEvent** - používá se pro vlastní metadata nebo [ID3v2] časovaná metadata
- **onAdCue** - používá se především pro signalizaci možnosti umístění reklamy v živém přenosu. Podporovány jsou dvě formy tága, jednoduchý režim a režim "SCTE-35". 
- **onCuePoint** - podporován některými místními hardwarovými kodéry, jako je kodéru Elemental Live, k signalizaci zpráv [SCTE35]. 
  

Následující tabulka popisuje formát datové části zprávy AMF, kterou budou služby Media Services ingestovat pro režimy zpráv "jednoduché" i [SCTE35].

Název zprávy [AMF0] lze použít k rozlišení více datových proudů událostí stejného typu.  Pro zprávy [SCTE-35] a "jednoduchý" režim musí být název zprávy AMF "onAdCue", jak je požadováno ve specifikaci [Adobe-Primetime].  Všechna pole, která nejsou uvedena níže, budou službou Azure Media Services při ingestování ignorována.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP s vlastními metadaty pomocí "onUserDataEvent"

Pokud chcete poskytnout vlastní zdroje metadat z nadřazeného kodéru, IP kamery, drone nebo zařízení pomocí protokolu RTMP, použijte typ příkazu datové zprávy "onUserDataEvent" [AMF0].

Příkaz datové zprávy **"onUserDataEvent"** musí nést datovou část zprávy s následující definicí, která má být zachycena službou Media Services a zabalena do formátu souboru v pásmu, stejně jako manifesty pro HLS, DASH a Smooth Streaming.
Doporučuje se odesílat časované metadata zprávy ne častěji než jednou za 0,5 sekundy (500 ms) nebo může dojít k problémům se stabilitou s živým přenosem. Každá zpráva může agregovat metadata z více rámců, pokud potřebujete poskytnout metadata na úrovni rámce. Pokud odesíláte datové proudy s více přenosovými rychlostmi, doporučujeme také poskytnout metadata s jedním datovým tokem, abyste snížili šířku pásma a zabránili rušení zpracování videa a zvuku. 

Datová část **pro "onUserDataEvent"** by měla být zpráva formátu XML služby [MPEGDASH] EventStream. Díky tomu je snadné předat vlastní definované schémata, které mohou být provedeny v 'emsg' datové části in-band pro CMAF [MPEGCMAF] obsah, který je dodáván přes HLS nebo DASH protokoly. Každá zpráva dash události datového proudu obsahuje schemeIdUri, který funguje jako identifikátor schématu zpráv URN a definuje datovou část zprávy. Některá schématahttps://aomedia.org/emsg/ID3například " " pro [ID3v2] nebo **urn:scte:scte35:2013:bin** pro [SCTE-35] jsou standardizovány průmyslovými konsorcii pro interoperabilitu. Každý poskytovatel aplikace může definovat své vlastní schéma pomocí adresy URL, kterou řídí (vlastněná doména) a může poskytnout specifikaci na této adrese URL, pokud se rozhodnou. Pokud má hráč obslužnou rutinu pro definované schéma, pak je to jediná součást, která potřebuje pochopit datovou část a protokol.

Schéma datové části XML Aplikace [MPEG-DASH] je definováno jako (výňatek z dash ISO-IEC-23009-1-3rd Edition). Všimněte si, že v současné době je podporován pouze jeden "EventType" na "EventStream". Pouze první **Event** prvek bude zpracována, pokud více událostí jsou k dispozici v **EventStream**.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Příklad datového proudu událostí XML s ID3 id schématu a datovou datovou datovou částí kódovnou base64.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Příklad datového proudu událostí s vlastním ID schématu a binárními daty kódovna base64  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Příklad datového proudu událostí s vlastním ID schématu a vlastním jsonem  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>Integrované podporované identifikátory URI ID schématu
| Identifikátor URI id schématu                 | Popis                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https:\//aomedia.org/emsg/ID3 | Popisuje, jak mohou být metadata [ID3v2] přenášena jako časovaná metadata v fragmentovaném protokolu MP4 kompatibilním s CMAF. Další informace naleznete v [metadatech časované ve společném formátu aplikace médií (CMAF)](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Zpracování událostí a signalizace manifestu

Po přijetí platné události **"onUserDataEvent"** bude Služba Azure Media Services hledat platnou datovou část XML, která odpovídá typu EventStreamType (definovanému v [MPEGDASH] ), analyzuje datovou část XML a převede ji na pole [MPEGCMAF] MP4 fragmentu 1 pro ukládání v živém archivu a přenos do balíčku Mediálních služeb.   Packager detekuje 'emsg' box v živém přenosu a:

- a) "dynamicky balit" do segmentů TS za účelem doručení klientům HLS v souladu se specifikací metadat HLS s časovým odstupem [HLS-TMD], nebo
- b) projdou ji k dodání v fragmentech CMAF přes HLS nebo DASH, nebo 
- c) převést jej na řídký signál dráhy pro dodání prostřednictvím plynulého streamování [MS-SSTR].

Kromě in-band 'emsg' formátu CMAF nebo TS PES pakety pro HLS, manifesty pro DASH (MPD), a Smooth Streaming bude obsahovat odkaz na in-band události proudy (také známý jako řídký stream track v Smooth Streaming). 

Jednotlivé události nebo jejich datové části nejsou výstup přímo v HLS, DASH nebo Smooth manifesty. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Další informační omezení a výchozí hodnoty pro události onUserDataEvent

- Pokud není v elementu EventStream nastavena časová osa, použije se ve výchozím nastavení časová osa RTMP 1 kHz.
- Doručení zprávy onUserDataEvent je omezeno na jednou za 500 ms max. Pokud odesíláte události častěji, může to mít vliv na šířku pásma a stabilitu živého přenosu

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 RTMP ad cue signalizace s "onAdCue"

Azure Media Services můžete naslouchat a reagovat na několik typů zpráv [AMF0], které lze použít k signálu různých synchronizovaných metadat v reálném čase v živém vysílání.  Specifikace [Adobe-Primetime] definuje dva typy tágo nazývané "jednoduchý" a "SCTE-35" režim. Pro "jednoduchý" režim podporuje Media Services jednu cue zprávu AMF nazvanou "onAdCue" pomocí datové části, která odpovídá níže uvedené tabulce definované pro signál "Jednoduchý režim".  

Následující část ukazuje RTMP "jednoduchý" režim užitečné zatížení, které lze použít k signálu základní "spliceOut" ad signál, který bude proveden až do seznamu klienta pro HLS, DASH a Microsoft Smooth Streaming. To je velmi užitečné pro scénáře, kde zákazník nemá komplexní SCTE-35 na základě systému signalizace reklamy nebo vložení a používá základní místní kodér k odeslání cue zprávy prostřednictvím rozhraní API. Místní kodér obvykle podporuje rozhraní API založené na rest pro aktivaci tohoto signálu, které také "splice-condition" video stream vložením snímku IDR do videa a spuštěním nové ho GOP.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 RTMP ad cue signalizace s "onAdCue" - Jednoduchý režim

| Název pole | Typ pole | Povinné? | Popisy                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | Řetězec     | Požaduje se  | Zpráva o události.  Musí být "SpliceOut" určit jednoduchý režim splice.                                                                                                                                                                                                         |
| id         | Řetězec     | Požaduje se  | Jedinečný identifikátor popisující spoj nebo segment. Identifikuje tuto instanci zprávy.                                                                                                                                                                                       |
| doba trvání   | Číslo     | Požaduje se  | Doba trvání splice. Jednotky jsou zlomkové sekundy.                                                                                                                                                                                                                           |
| elapsed    | Číslo     | Nepovinné  | Při opakování signálu pro podporu naladění je toto pole doba prezentace, která uplynula od začátku spojení. Jednotky jsou zlomkové sekundy. Při použití jednoduchého režimu by tato hodnota neměla přesáhnout původní dobu trvání splice. |
| time       | Číslo     | Požaduje se  | Musí být čas splice, v době prezentace. Jednotky jsou zlomkové sekundy.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Příklad výstupu manifestu MPEG DASH při použití jednoduchého režimu Adobe RTMP

Viz příklad [3.3.2.1 MPEG DASH .mpd EventStream pomocí jednoduchého režimu Adobe](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Viz příklad [3.3.3.1 Manifest DASH s jednou tečkou a jednoduchým režimem Adobe](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Příklad výstupu manifestu HLS při použití jednoduchého režimu Adobe RTMP

Viz příklad [3.2.2 MANIFEST HLS pomocí jednoduchého režimu Adobe a značky EXT-X-CUE](#322-apple-hls-with-adobe-primetime-ext-x-cue-legacy)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 RtMP ad cue signalizace s "onAdCue" - SCTE-35 Mode

Při práci s pokročilejším pracovním postupem produkce vysílání, který vyžaduje, aby byla úplná zpráva o datové části SCTE-35 přenesena do manifestu HLS nebo DASH, je nejlepší použít "Režim SCTE-35" specifikace [Adobe-Primetime].  Tento režim podporuje in-band SCTE-35 signály odesílané přímo do místního živého kodéru, který pak kóduje signály do datového proudu RTMP pomocí režimu "SCTE-35" uvedeného ve specifikaci [Adobe-Primetime]. 

Zprávy SCTE-35 se obvykle mohou zobrazovat pouze ve vstupech datového proudu přenosu MPEG-2 (TS) v místním kodéru. Podrobnosti o konfiguraci přenosu datového proudu, který obsahuje SCTE-35, najdete u výrobce kodéru a povolte jej pro přenos do rtmp v režimu Adobe SCTE-35.

V tomto scénáři musí být odeslána následující datová část z místního kodéru pomocí typu zprávy **"onAdCue"** [AMF0].

| Název pole | Typ pole | Povinné? | Popisy                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Startovací        | Řetězec     | Požaduje se  | Zpráva o události.  Pro zprávy [SCTE-35] to musí být základní64 kódované [RFC4648] binární splice_info_section() v pořadí zprávy, které mají být odeslány hls, hladký a Dash klienty.                                                                                                                                                                                                                               |
| type       | Řetězec     | Požaduje se  | Urna nebo adresa URL identifikující schéma zpráv. Pro zprávy [SCTE-35] by to **mělo** být **"scte35",** aby zprávy byly odeslány klientům HLS, Smooth a Dash v souladu s [Adobe-Primetime]. Volitelně urn "urn:scte:scte35:2013:bin" může být také použit k signalizaci zprávy [SCTE-35].                                                                                                        |
| id         | Řetězec     | Požaduje se  | Jedinečný identifikátor popisující spoj nebo segment. Identifikuje tuto instanci zprávy.  Zprávy s rovnocennou sémantikou musí mít stejnou hodnotu.                                                                                                                                                                                                                                                       |
| doba trvání   | Číslo     | Požaduje se  | Doba trvání události nebo segmentu splice reklamy, je-li známa. Pokud není znám, hodnota **by měla** být 0.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Číslo     | Nepovinné  | Pokud se signál reklamy [SCTE-35] opakuje, aby bylo možné se naladit, toto pole bude doba prezentace, která uplynula od začátku spojení. Jednotky jsou zlomkové sekundy. V režimu [SCTE-35] může tato hodnota překročit původní zadanou dobu trvání splice nebo segmentu.                                                                                                                   |
| time       | Číslo     | Požaduje se  | Čas prezentace události nebo splice reklamy.  Doba prezentace a doba trvání **by měly** být v souladu s přístupovými body datového proudu (SAP) typu 1 nebo 2, jak jsou definovány v příloze I [ISO-14496-12]. Pro odchozí HLS by **měl** čas a doba trvání zarovnat s hranicemi segmentu. Čas prezentace a doba trvání různých zpráv událostí v rámci stejného datového proudu událostí se nesmí překrývat. Jednotky jsou zlomkové sekundy. |

---

#### <a name="example-mpeg-dash-mpd-manifest-with-scte-35-mode"></a>Příklad manifestu MPEG DASH .mpd s režimem SCTE-35
Viz [bod 3.3.3.2 příklad manifestu DASH s SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Příklad manifestu HLS .m3u8 se signálem režimu SCTE-35
Viz [bod 3.2.1.1 příklad HLS manifestu s SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)

## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 RTMP AD signalizace s "onCuePoint" pro Elemental Live

Místní kodér Elemental Live podporuje značky reklam v signálu RTMP. Mediální služby Azure v současné době podporují jenom typ značky reklamy onCuePoint pro RTMP.  To lze povolit v nastavení skupiny Adobe RTMP v nastavení kodéru Elemental Media Live nebo ROZHRANÍ API nastavením "**ad_markers**" na "onCuePoint".  Podrobnosti naleznete v dokumentaci Elemental Live. Povolení této funkce ve skupině RTMP předá signály SCTE-35 výstupům Adobe RTMP, které budou zpracovány službou Azure Media Services.

Typ zprávy "onCuePoint" je definován v [Adobe-Flash-AS] a má následující strukturu datové části při odeslání z elementárního živého výstupu RTMP.


| Vlastnost   | Popis                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| jméno       | Název by měl být '**scte35**' od Elemental Live.                                                                                                                                                                              |
| time       | Čas v sekundách, kdy došlo k startovacímu bodu ve videosouboru během časové osy                                                                                                                                           |
| type       | Typ startovacího bodu by měl být nastaven na "**událost**".                                                                                                                                                                             |
| parameters | Asociativní pole řetězce dvojice název/hodnota obsahující informace ze zprávy SCTE-35, včetně ID a trvání. Tyto hodnoty jsou analyzovány službou Azure Media Services a zahrnuty do značky dekorace manifestu. |


Při použití tohoto režimu značky reklamy je výstup manifestu HLS podobný režimu Adobe "Simple".


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Příklad MPEG DASH MPD, jednoperiodové signály, signály jednoduchého režimu Adobe

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
~~~

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Příklad HLS playlist, Adobe Simple mode signály pomocí EXT-X-CUE tag (zkrácený "..." pro stručnost)

Následující příklad ukazuje výstup z dynamického balíru Mediální chod služby pro datový proud ingestu RTMP pomocí signálů "jednoduchého" režimu Adobe a starší značky [Adobe-Primetime] EXT-X-CUE.  

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...

~~~

### <a name="216-cancellation-and-updates"></a>2.1.6 Zrušení a aktualizace

Zprávy lze zrušit nebo aktualizovat odesláním více zpráv se stejným časem prezentace a ID. Čas prezentace a ID jednoznačně identifikují událost a poslední zpráva přijatá pro konkrétní čas prezentace, který splňuje omezení před vrácením, je zpráva, která je zpracována. Aktualizovaná událost nahradí všechny dříve přijaté zprávy. Omezení před vrácením je čtyři sekundy. Zprávy přijaté nejméně čtyři sekundy před časem prezentace budou zpracovány.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Fragmentovaný ingest MP4 (plynulé streamování)

Požadavky na příjem živého datového proudu naleznete v [MS-SSTR-Ingest]. V následujících částech jsou uvedeny podrobnosti týkající se ingestování metadat časované prezentace.  Časovaná metadata prezentace jsou ingestována jako řídká stopa, která je definována jak v poli manifestu živého serveru (viz MS-SSTR), tak v movie boxu ("moov").  

Každý řídký fragment se skládá z movie fragment box ('moof') a Media Data Box ('mdat'), kde 'mdat' box je binární zpráva.

Aby bylo dosaženo rámce-přesné vložení reklam, kodér musí rozdělit fragment v době prezentace, kde je třeba cue být vložen.  Musí být vytvořen nový fragment, který začíná nově vytvořeným rámcem IDR nebo přístupovými body datového proudu (SAP) typu 1 nebo 2, jak je definováno v příloze I [ISO-14496-12]. To umožňuje Azure Media Packager správně generovat manifest HLS a DASH vícedobého manifestu, kde nové Období začíná na snímek přesné splice podmíněné doby prezentace.

### <a name="221-live-server-manifest-box"></a>2.2.1 Živý server Manifest Box

Řídká stopa **musí** být deklarována v poli Manifest živého serveru s položkou ** \<textstream\> ** a **musí** mít nastaveny následující atributy:

| **Název atributu** | **Typ pole** | **Požadované?** | **Popis**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Číslo         | Požaduje se      | **MUSÍ** být "0", označující stopu s neznámým, proměnným přenosovým tokem.                                                                                                                                                          |
| parentTrackName    | Řetězec         | Požaduje se      | **MUSÍ** být název nadřazené stopy, ke které jsou zarovnány kódy časových os řídké doby sledování. Nadřazená stopa nemůže být řídkou stopou.                                                                             |
| manifestOutput     | Logická hodnota        | Požaduje se      | **Musí** být "true", což znamená, že řídká stopa bude vložena do manifestu hladkého klienta.                                                                                                                        |
| Podtypu            | Řetězec         | Požaduje se      | **Musí** být čtyřznakový kód "DATA".                                                                                                                                                                                  |
| Schéma             | Řetězec         | Požaduje se      | **MUSÍ** být URNebo URL identifikující schéma zpráv. Pro zprávy [SCTE-35] to **musí** být "urn:scte:scte35:2013:bin", aby zprávy byly odeslány klientům HLS, Smooth a Dash v souladu s [SCTE-35]. |
| trackName          | Řetězec         | Požaduje se      | **Musí** být název řídké stopy. TrackName lze použít k rozlišení více datových proudů událostí se stejným schématem. Každý jedinečný proud událostí **musí** mít jedinečný název stopy.                                |
| Časové osy          | Číslo         | Nepovinné      | **Musí** být časová osa nadřazené stopy.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 Film Box

Movie Box ('moov') následuje za live server manifest box jako součást hlavičky datového proudu pro řídké stopy.

Pole 'moov' **by mělo** obsahovat pole **TrackHeaderBox ('tkhd')** definované v [ISO-14496-12] s následujícími omezeními:

| **Název pole** | **Typ pole**          | **Požadované?** | **Popis**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| doba trvání       | 64bitové celé celé číslo bez znaménka | Požaduje se      | **By měla** být 0, protože kolejnice má nulové vzorky a celková doba trvání vzorků v kolejišti je 0. |

---

Pole "moov" **by mělo** obsahovat **handlerbox ("hdlr"),** jak je definovánv [ISO-14496-12] s následujícími omezeními:

| **Název pole** | **Typ pole**          | **Požadované?** | **Popis**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32bitové celé celé číslo bez znaménka | Požaduje se      | **by měla** být 'meta'. |

---

Pole 'stsd' **by mělo** obsahovat pole MetaDataSampleEntry s kódovacím názvem definovaným v [ISO-14496-12].  Například pro zprávy SCTE-35 **by měl** být kódovací název 'scte'.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Video fragment box a mediální datová skříňka

Fragmenty řídké stopy se skládají z movie fragment boxu ("moof") a datové schránky médií ("mdat").

> [!NOTE]
> Aby bylo dosaženo rámce-přesné vložení reklam, kodér musí rozdělit fragment v době prezentace, kde je třeba cue být vložen.  Musí být vytvořen nový fragment, který začíná nově vytvořeným rámcem IDR nebo přístupovými body datového proudu (SAP) typu 1 nebo 2, jak je definováno v příloze I [ISO-14496-12]
> 

Pole MovieFragmentBox ('moof') **musí** obsahovat pole **TrackFragmentExtendedHeaderBox ('uuid')** definované v [MS-SSTR] s následujícími poli:

| **Název pole**         | **Typ pole**          | **Požadované?** | **Popis**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64bitové celé celé číslo bez znaménka | Požaduje se      | **Musí** být čas příjezdu události. Tato hodnota zarovná zprávu s nadřazenou stopou.           |
| fragment_duration      | 64bitové celé celé číslo bez znaménka | Požaduje se      | **Musí** být doba trvání události. Doba trvání může být nula označující, že doba trvání není známa. |

---


Pole MediaDataBox ('mdat') **musí** mít následující formát:

| **Název pole**          | **Typ pole**                   | **Požadované?** | **Popis**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                 | 32bitové celé číslo bez znaménka (uimsbf) | Požaduje se      | Určuje formát obsahu pole 'mdat'. Nerozpoznané verze budou ignorovány. V současné době je jedinou podporovanou verzí 1.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32bitové celé číslo bez znaménka (uimsbf) | Požaduje se      | Identifikuje tuto instanci zprávy. Zprávy s rovnocennou sémantikou musí mít stejnou hodnotu; to znamená, že zpracování jednoho okna zprávy události se stejným id je dostačující.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32bitové celé číslo bez znaménka (uimsbf) | Požaduje se      | Součet fragment_absolute_time, zadaný v TrackFragmentExtendedHeaderBox a presentation_time_delta **musí** být čas prezentace události. Doba prezentace a doba trvání **by měly** být v souladu s přístupovými body datového proudu (SAP) typu 1 nebo 2, jak jsou definovány v příloze I [ISO-14496-12]. Pro odchozí HLS by **měl** čas a doba trvání zarovnat s hranicemi segmentu. Čas prezentace a doba trvání různých zpráv událostí v rámci stejného datového proudu událostí **se nesmí** překrývat. |
| zpráva                 | pole bajtů                       | Požaduje se      | Zpráva o události. Pro zprávy [SCTE-35] je zpráva binární splice_info_section(). Pro zprávy [SCTE-35] to **musí** být splice_info_section() aby zprávy, které mají být odeslány hls, hladký a Dash klienty v souladu s [SCTE-35]. Pro zprávy [SCTE-35] binární splice_info_section() je datová část pole 'mdat' a **není** zakódována.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 Zrušení a aktualizace

Zprávy lze zrušit nebo aktualizovat odesláním více zpráv se stejným časem prezentace a ID.  Čas prezentace a ID jednoznačně identifikují událost. Poslední zpráva přijatá pro určitý čas prezentace, která splňuje omezení před vrácením, je zpráva, která je zpracována. Aktualizovaná zpráva nahradí všechny dříve přijaté zprávy.  Omezení před vrácením je čtyři sekundy. Zprávy přijaté nejméně čtyři sekundy před časem prezentace budou zpracovány. 


## <a name="3-timed-metadata-delivery"></a>3 Časované doručení metadat

Data datového proudu událostí jsou pro službu Media Services neprůhledná. Media Services pouze předává tři části informací mezi koncový bod ingestování a koncový bod klienta. Následující vlastnosti jsou dodávány klientovi v souladu s [SCTE-35] a/nebo protokolem streamování klienta:

1.  Schéma – URN nebo URL identifikující schéma zprávy.
2.  Čas prezentace – čas prezentace události na časové ose média.
3.  Doba trvání – doba trvání události.
4.  ID – volitelný jedinečný identifikátor události.
5.  Zpráva – data události.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Manifest plynulého streamování microsoftu  

Podrobnosti o formátování řídké stopy naleznete v části Zpracování stop [MS-SSTR]. Pro zprávy [SCTE35] bude plynulé streamování výstupem základní64 kódované splice_info_section() do řídké fragmentu.
StreamIndex **musí** mít podtyp "DATA" a CustomAttributes **musí** obsahovat atribut s Name="Schema" a Value="urn:scte:scte35:2013:bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Příklad manifestu hladkého klienta zobrazující splice_info_section kódovaného base64 [SCTE35]
~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Apple HLS Manifest dekorace

Azure Media Services podporuje následující značky manifestu HLS pro signalizaci informací o dostupnosti reklamy během živé události nebo události na vyžádání. 

- EXT-X-DATERANGE podle definice v Apple HLS [RFC8216]
- EXT-X-CUE, jak je definováno v [Adobe-Primetime] - tento režim je považován za "dědictví". Zákazníci by měli přijmout značku EXT-X-DATERANGE, pokud je to možné.

Výstup dat pro každou značku se bude lišit v závislosti na použitém režimu signálu ingestu. Například ingestování RTMP v režimu Adobe Simple neobsahuje úplnou datovou část kódovnou kódovku s kódováním SCTE-35.

## <a name="321-apple-hls-with-ext-x-daterange-recommended"></a>3.2.1 Apple HLS s EXT-X-DATERANGE (doporučeno)

Specifikace Apple HTTP Live Streaming [RFC8216] umožňuje signalizaci zpráv [SCTE-35]. Zprávy jsou vloženy do seznamu stop segmentu ve značce EXT-X-DATERANGE v sekci [RFC8216] s názvem "Mapování SCTE-35 do EXT-X-DATERANGE".  Klientská aplikační vrstva může analyzovat seznam skladeb M3U a zpracovávat značky M3U nebo přijímat události prostřednictvím rozhraní přehrávače Apple.  

**Doporučený** přístup ve službě Azure Media Services (verze 3 ROZHRANÍ API) je následovat [RFC8216] a použít značku EXT-X_DATERANGE pro [SCTE35] dekorace dostupnosti reklamy v manifestu.

## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35"></a>3.2.1.1 Příklad hls manifestu .m3u8 zobrazující signalizaci EXT-X-DATERANGE SCTE-35

Následující příklad HLS manifest výstup z media services dynamické packager ukazuje použití EXT-X-DATERANGE tag z [RFC8216] signalizace SCTE-35 události v datovém proudu. Kromě toho tento datový proud obsahuje značku "legacy" EXT-X-CUE pro [Adobe-Primetime].

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~


## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS s Adobe Primetime EXT-X-CUE (dědictví)

K dispozici je také "starší" implementace poskytované ve službě Azure Media Services (verze 2 a 3 API), který používá značku EXT-X-CUE, jak je definováno v [Adobe-Primetime] "SCTE-35 Mode". V tomto režimu azure media services vloží base64 kódované [SCTE-35] splice_info_section() ve značce EXT-X-CUE.  

Značka "legacy" EXT-X-CUE je definována níže a může být také normativní ve specifikaci [Adobe-Primetime]. To by mělo být použito pouze pro starší signál SCTE35 v případě potřeby, jinak doporučená značka je definována v [RFC8216] jako EXT-X-DATERANGE. 

| **Název atributu** | **Typ**                      | **Požadované?**                             | **Popis**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Startovací                | citovaný řetězec                 | Požaduje se                                  | Zpráva kódovaná jako řetězec kódovaný base64, jak je popsáno v [RFC4648]. Pro zprávy [SCTE-35] se jedná o splice_info_section().                                                                                                                                      |
| TYP               | citovaný řetězec                 | Požaduje se                                  | Urna nebo adresa URL identifikující schéma zpráv. Pro zprávy [SCTE-35] typ přebírá speciální hodnotu "scte35".                                                                                                                                                                          |
| ID                 | citovaný řetězec                 | Požaduje se                                  | Jedinečný identifikátor události. Pokud ID není zadán při požití zprávy, Azure Media Services vygeneruje jedinečné ID.                                                                                                                                              |
| Doba trvání           | desítkové číslo s plovoucí desetinnou čárkou | Požaduje se                                  | Doba trvání události. Pokud není znám, hodnota **by měla** být 0. Jednotky jsou frakční sekundy.                                                                                                                                                                                           |
| Uplynulý            | desítkové číslo s plovoucí desetinnou čárkou | Volitelné, ale povinné pro posuvné okno | Při opakování signálu pro podporu posuvného prezentačního okna **musí** být toto pole časem prezentace, který uplynul od začátku akce. Jednotky jsou zlomkové sekundy. Tato hodnota může překročit původní zadanou dobu trvání splice nebo segmentu. |
| ČAS               | desítkové číslo s plovoucí desetinnou čárkou | Požaduje se                                  | Čas prezentace události. Jednotky jsou zlomkové sekundy.                                                                                                                                                                                                                        |


Aplikační vrstva hráče HLS použije typ k identifikaci formátu zprávy, dekódování zprávy, použití potřebných časových převodů a zpracování události.  Události jsou časem synchronizované v segmentu playlistu nadřazené skladby podle časového razítka události.  Jsou vloženy před nejbližší segment (#EXTINF značka).

### <a name="323-hls-m3u8-manifest-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 HLS .m3u8 manifest příklad pomocí "Legacy" Adobe Primetime EXT-X-CUE

Následující příklad ukazuje dekoraci manifestu HLS pomocí značky Adobe Primetime EXT-X-CUE.  Parametr "CUE" obsahuje pouze vlastnosti TYP a Duration, což znamená, že se jednalo o zdroj RTMP využívající signalizaci "jednoduchého" režimu Adobe.  Pokud se jednalo o signál režimu SCTE-35, značka by obsahovala základní binární datovou část kódovku SCTE-35, jak je vidět v [příkladu 3.2.1.1](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 Zpracování zpráv HLS pro "starší" Adobe Primetime EXT-X-CUE

Události jsou signalizovány v segmentovém seznamu stop každé videoa zvukové stopy. Pozice značky EXT-X-CUE **musí** být vždy buď bezprostředně před prvním segmentem HLS (pro splice out nebo segment start), nebo bezprostředně za posledním segmentem HLS (pro splice in nebo segment end), na který se vztahují jeho atributy TIME a DURATION, jak to vyžaduje [Adobe-Primetime].

Je-li povoleno posuvné okno prezentace, **musí** se značka EXT-X-CUE opakovat dostatečně často, aby bylo spojení nebo segment vždy plně popsáno v seznamu stop segmentu a atribut ELAPSED **musí** být použit k označení doby, po kterou bylo spojení nebo segment aktivní, jak to vyžaduje [Adobe-Primetime].

Je-li povoleno posuvné okno prezentace, jsou značky EXT-X-CUE odebrány ze seznamu stop segmentu, když byl z posuvného okna prezentace vyválen čas médií, na který odkazují.

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 DASH manifest dekorace (MPD)

[MPEGDASH] poskytuje tři způsoby, jak signalizovat události:

1.  Události signalizované v MPD EventStream
2.  Události signalizované v pásmu pomocí okna se zprávou o události ('emsg')
3.  Kombinace 1 a 2

Události signalizované v MPD EventStream jsou užitečné pro streamování VOD, protože klienti mají přístup ke všem událostem, okamžitě po stažení MPD. Je také užitečné pro signalizaci SSAI, kde dodavatel snásledným SSAI potřebuje analyzovat signály z víceoborového manifestu MPD a dynamicky vkládat obsah reklamy.  Řešení in-band ('emsg') je užitečné pro živé vysílání, kde klienti nemusí znovu stahovat MPD nebo mezi klientem a původem nedochází k žádné manipulaci s manifestem SSAI. 

Výchozí chování Azure Media Services pro DASH je signál v MPD EventStream a v pásmu pomocí okna se zprávou události ('emsg').

Cue zprávy požití přes [RTMP] nebo [MS-SSTR-Ingest] jsou mapovány do DASH události, pomocí in-band 'emsg' boxy a / nebo in-MPD EventStreams. 

In-band SCTE-35 signalizace pro DASH se řídí definicí a požadavky definovanými v [SCTE-214-3] a také v [DASH-IF-IOP] sekci 13.12.2 ("Události SCTE35"). 

Pro in-band [SCTE-35] carriage, okno Zprávy události ('emsg') používá schemeId = "urn:scte:scte35:2013:bin". Pro dekoraci manifestu MPD používá id schemeStream "urn:scte:scte35:2014:xml+bin".  Tento formát je XML reprezentace události, která zahrnuje binární base64 kódovaný výstup kompletní zprávy SCTE-35, která byla doručena při požití. 

Normativní referenční definice přepravy startovacích zpráv [SCTE-35] v DASH jsou k dispozici v [SCTE-214-1] s 6.7.4 (MPD) a [SCTE-214-3] sec 7.3.2 (Přeprava cue zpráv SCTE 35).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG DASH (MPD) Signalizace eventstreamu

Manifest (MPD) dekorace událostí bude signalizována v MPD pomocí EventStream element, který se zobrazí v rámci Period element. Použité id schemeId je "urn:scte:scte35:2014:xml+bin".

> [!NOTE]
> Pro účely stručnosti [SCTE-35] umožňuje použití base64 kódované části v Signal.Binary element (spíše než Signal.SpliceInfoSection element) jako alternativu k přepravě zcela analyzované cue zprávy.
> Azure Media Services používá tento přístup xml+bin k signalizaci v manifestu MPD.
> Toto je také doporučená metoda použitá v [DASH-IF-IOP] - viz část s názvem ["Datové proudy událostí vkládání reklam" směrnice DASH IF IOP](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

Prvek EventStream má následující atributy:

| **Název atributu** | **Typ**                | **Požadované?** | **Popis**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | řetězec                  | Požaduje se      | Identifikuje schéma zprávy. Schéma je nastaveno na hodnotu atributu Scheme v poli Manifest živého serveru. Hodnota **by měla** být URN nebo URL identifikující schéma zpráv; Podporované výstupní schemeId by měl být "urn:scte:scte35:2014:xml+bin" za [SCTE-214-1] s 6.7.4 (MPD), protože služba podporuje pouze "xml+bin" v tomto okamžiku pro stručnost v MPD. |
| value              | řetězec                  | Nepovinné      | Další hodnota řetězce, kterou používají vlastníci schématu k přizpůsobení sémantiky zprávy. Chcete-li odlišit více datových proudů událostí se stejným schématem, **musí** být hodnota nastavena na název datového proudu událostí (trackName pro [MS-SSTR-Ingest] nebo název zprávy AMF pro ingestování [RTMP].                                                                         |
| Timescale          | 32bitové celé celé číslo bez znaménka | Požaduje se      | Časová osa v značek za sekundu.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 Ukázkové datové proudy událostí pro MPEG DASH

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 Příklad MPEG DASH .mpd manifest signalizace RTMP streaming pomocí adobe jednoduchého režimu

Následující příklad ukazuje výňatek EventStream z dynamického balíru Mediálních služeb pro datový proud RTMP pomocí signalizace "jednoduchého" režimu Adobe.

~~~ xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
~~~

#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 Příklad mpeg dash .mpd manifest signalizace rtmp proudu pomocí režimu Adobe SCTE-35

Následující příklad ukazuje výňatek EventStream z dynamického balíru Mediálních služeb pro datový proud RTMP pomocí signalizace režimu Adobe SCTE-35.

~~~ xml
<!-- Example EventStream element using xml+bin style signaling per [SCTE-214-1] -->

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
~~~

> [!IMPORTANT]
> Všimněte si, že presentationTime je čas prezentace události [SCTE-35] přeložené tak, aby byly relativní k času zahájení období, nikoli času doručení zprávy.
> [MPEGDASH] definuje Event@presentationTime jako "Určuje čas prezentace události vzhledem k začátku Období.
> Hodnota času prezentace v sekundách je rozdělení hodnoty tohoto atributu EventStream@timescale a hodnoty atributu.
> Pokud není k dispozici, hodnota času prezentace je 0.

#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 Příklad manifestu MPEG DASH (MPD) s jednoperiodovým eventstreamem, pomocí signálů jednoduchého režimu Adobe

Následující příklad ukazuje výstup z dynamického balíru Mediální služby pro zdrojový datový proud RTMP pomocí metody signálu reklamy v jednoduchém režimu Adobe. Výstup je jeden manifest období zobrazující EventStream pomocí schemeId Uri nastavena na "urn:com:adobe:dpi:simple:2015" a vlastnost hodnoty nastavena na "simplesignal".
Každý jednoduchý signál je k dispozici @presentationTimev @durationEvent @id element s , a vlastnosti naplněné na základě příchozíjednoduché signály.

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

#### <a name="3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling"></a>3.3.3.2 Příklad manifestu MPEG DASH (MPD) s vícedobým eventstreamem, pomocí signalizace režimu Adobe SCTE35

Následující příklad ukazuje výstup z dynamického balíru Mediální služby pro zdrojový datový proud RTMP pomocí signalizace režimu Adobe SCTE35.
V tomto případě je výstupní manifest víceoborový dash .mpd s @schemeIdUri elementem EventStream a vlastnost nastavená na "urn:scte:scte35:2014:xml+bin" a vlastnost nastavená @value na "scte35". Každý eventový prvek v EventStream obsahuje úplný binární signál SCTE35 kódovaný base64 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                    
                    <!--> ... aduio segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~
### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG DASH In-band Event Message Box Signalizace

Datový proud událostí v pásmu vyžaduje, aby mpd měl prvek InbandEventStream na úrovni adaptační sady.  Tento prvek má povinný schemeIdUri atribut a volitelné časové měřítko atribut, který se také zobrazí v poli zprávy události ('emsg').  Okna se zprávami událostí s identifikátory schématu, které nejsou definovány v MPD **by neměla** být k dispozici.

Pro in-band [SCTE-35] vozík, signály **MUSÍ** použít schemeId = "urn:scte:scte35:2013:bin".
Normativní definice přepravy inbandzpráv [SCTE-35] jsou definovány v [SCTE-214-3] sec 7.3.2 (Carriage of SCTE 35 cue messages).

Následující podrobnosti popisují konkrétní hodnoty, které by měl klient očekávat v "emsg" v souladu s [SCTE-214-3]:

| **Název pole**          | **Typ pole**          | **Požadované?** | **Popis**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | řetězec                  | Požaduje se      | Identifikuje schéma zprávy. Schéma je nastaveno na hodnotu atributu Scheme v poli Manifest živého serveru. Hodnota **musí** být URN identifikující schéma zpráv. Pro zprávy [SCTE-35] to **musí** být "urn:scte:scte35:2013:bin" v souladu s [SCTE-214-3]          |
| Hodnota                   | řetězec                  | Požaduje se      | Další hodnota řetězce, kterou používají vlastníci schématu k přizpůsobení sémantiky zprávy. Aby bylo možné odlišit více datových proudů událostí se stejným schématem, bude hodnota nastavena na název datového proudu událostí (trackName pro příjem vyhlazení nebo název zprávy AMF pro příjem RTMP). |
| Timescale               | 32bitové celé celé číslo bez znaménka | Požaduje se      | Časová osa polí časy a doby trvání v poli "emsg" v polích za sekundu.                                                                                                                                                                                                            |
| Presentation_time_delta | 32bitové celé celé číslo bez znaménka | Požaduje se      | Rozdíl času prezentace média času prezentace události a nejbližšího času prezentace v tomto segmentu. Doba prezentace a doba trvání **by měly** být v souladu s přístupovými body datového proudu (SAP) typu 1 nebo 2, jak jsou definovány v příloze I [ISO-14496-12].                                  |
| event_duration          | 32bitové celé celé číslo bez znaménka | Požaduje se      | Doba trvání události nebo 0xFFFFFFFF označuje neznámou dobu trvání.                                                                                                                                                                                                                              |
| ID                      | 32bitové celé celé číslo bez znaménka | Požaduje se      | Identifikuje tuto instanci zprávy. Zprávy s rovnocennou sémantikou musí mít stejnou hodnotu. Pokud ID není zadán při požití zprávy, Azure Media Services vygeneruje jedinečné ID.                                                                                        |
| Message_data            | pole bajtů              | Požaduje se      | Zpráva o události. Pro zprávy [SCTE-35] jsou data zprávy binární splice_info_section() v souladu s [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Příklad entity InBandEvenStream pro režim Adobe Simple
~~~ xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
~~~

### <a name="335-dash-message-handling"></a>3.3.5 Zpracování zpráv DASH

Události jsou signalizovány v pásmu, v rámečku 'emsg', pro video i audio stopy.  K signalizaci dochází pro všechny požadavky segmentu, pro které je presentation_time_delta 15 sekund nebo méně. 

Je-li povoleno posuvné okno prezentace, jsou zprávy událostí odebrány z mpd, pokud je součet času a doby trvání zprávy události menší než čas mediálních dat v manifestu.  Jinými slovy zprávy o události jsou odebrány z manifestu, když čas média, na které odkazují má vyválel z posuvné okno prezentace.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. Pokyny pro implementaci singestu SCTE-35 pro dodavatele kodérů

Následující pokyny jsou běžné problémy, které mohou mít vliv na implementaci této specifikace dodavatelem kodéru.  Níže uvedené pokyny byly shromážděny na základě zpětné vazby od partnerů v reálném světě, aby bylo snazší implementovat tuto specifikaci pro ostatní. 

Zprávy [SCTE-35] jsou ingestovány v binárním formátu pomocí schématu **"urn:scte:scte35:2013:bin"** pro [MS-SSTR-Ingest] a typu **"scte35"** pro ingestování [RTMP]. Pro usnadnění převodu časování [SCTE-35], které je založeno na časových razítkách přenosu datového proudu MPEG-2 (PTS), je mapování mezi PTS (pts_time + pts_adjustment splice_time()) a časovou osou médií poskytováno časem prezentace události ( pole fragment_absolute_time pro vyhlazení a časové pole pro ingestování RTMP). Mapování je nezbytné, protože 33bitová hodnota PTS se převádí přibližně každých 26,5 hodiny.

Smooth Streaming ingestest [MS-SSTR-Ingest] vyžaduje, aby mediální datový schránka ("mdat") **musí** obsahovat **splice_info_section()** definované v [SCTE-35]. 

Pro ingesting RTMP je atribut cue zprávy AMF nastaven na **splice_info_section()** kódované ho base64 definovanév [SCTE-35].  

Pokud mají zprávy výše popsaný formát, jsou odeslány klientům HLS, Smooth a DASH, jak je definováno výše.  

Při testování implementace s platformou Azure Media Services začněte nejprve testovat pomocí "předávací" LiveEvent, než přejdete k testování na kódování LiveEvent.

---

## <a name="change-history"></a>Historie změn

| Datum     | Změny                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Revidované ingesty RTMP pro podporu SCTE35, přidáno RTMP "onCuePoint" pro Elemental Live                                  |
| 08/22/19 | Aktualizováno pro přidání onUserDataEvent do RTMP pro vlastní metadata                                                          |
| 1/08/20  | Opravena chyba v režimu RTMP Simple a RTMP SCTE35. Změněno z "onCuePoint" na "onAdCue". Aktualizováno Tabulka jednoduchého režimu. |

## <a name="next-steps"></a>Další kroky
Zobrazit studijní cesty služby Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
