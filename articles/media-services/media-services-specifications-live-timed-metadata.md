---
title: Azure Media Services – signalizace časované metadata v živém streamování
description: Tato specifikace popisuje metody pro signalizaci při ingestování a streamování do Azure Media Services, při přijímání a streamování. Zahrnuje podporu pro obecné časované signály metadat (ID3) a také signalizaci SCTE-35 pro vkládání a signalizaci podmínek pro vložení AD a podmínky spojení.
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
ms.openlocfilehash: f826ee9ef3c9fff0b721a9c79d3c12e0adbd5f7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336390"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Signalizace při živém streamování vyprší metadata 

Poslední aktualizace: 2019-08-22

### <a name="conformance-notation"></a>Zápis shody

Klíčová slova "musí", "nesmí", "požadováno", "musí", "nesmí", "by" neměla "," by "neměla být", "doporučeno", "by" měly být "nepovinné" v tomto dokumentu budou interpretována tak, jak je popsáno v dokumentu RFC 2119

## <a name="1-introduction"></a>1. Úvod 

Aby bylo možné signalizovat vložení reklamy nebo vlastních událostí metadat na klientském přehrávači, všesměrové vysílání často využívá ve videu ve videu časově omezená metadata vložená. Chcete-li tyto scénáře povolit, Media Services poskytuje podporu pro přenos počasovanéch metadat z bodu ingestování kanálu živého streamování do klientské aplikace.
Tato specifikace popisuje několik režimů, které jsou podporovány Media Services pro časované metadata v rámci signálu živého streamování.

1. [SCTE-35] signalizace, která odpovídá standardům, které jsou uvedeny v [SCTE-35], [SCTE-214-1], [SCTE-214-3] a [RFC8216]

2. [SCTE-35] signalizace, která je v souladu se specifikací starší verze [Adobe-primetime] pro signál RTMP AD.
   
3. Obecný časový režim signalizace metadat pro zprávy, které **nejsou [SCTE** -35], a mohl by přenášet [ID3v2] nebo další vlastní schémata definovaná vývojářem aplikace.

## <a name="11-terms-used"></a>Použité výrazy 1,1

| Označení                | Definice                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Přerušení reklamy            | Místo nebo bod v čase, kdy je možné naplánovat doručení jedné nebo více reklam. stejné jako možnosti k dispozici a umístění.                                                                                                                     |
| Služba pro rozhodování AD | externí služba, která určuje, které AD a doby trvání budou zobrazeny uživateli. Služby jsou obvykle poskytovány partnerem a nejsou v oboru pro tento dokument.                                                                    |
| Informovat                 | Označení času a parametrů nadcházejícího přerušení služby AD. Všimněte si, že hromádky můžou indikovat nedokončený přepínač na přerušení reklamy, čeká na přepnutí na další službu AD v rámci přerušení reklamy a čeká na přepnutí z přerušení reklamy na hlavní obsah.           |
| Packager            | Azure Media Services "koncový bod streamování" poskytuje dynamické možnosti balení pro POMLČKy a HLS a označuje se jako "balíček" v odvětví multimédií.                                                                              |
| Čas prezentace   | Čas, kdy se událost prezentuje prohlížeči. Čas představuje moment na časové ose média, který by mohl zobrazit událost v prohlížeči. Například čas prezentace příkazu SCTE-35 splice_info () je splice_time (). |
| Čas doručení        | Čas, kdy zpráva události dorazí. Čas se obvykle liší od doby prezentace události, protože zprávy o událostech jsou odesílány před dobou prezentace události.                                                    |
| Zhuštěná stopa        | stopa multimédií není souvislá a je synchronizována s nadřazenou nebo řídicí dráhou.                                                                                                                                                  |
| Zdroj              | Služba streamování médií Azure                                                                                                                                                                                                             |
| Jímka kanálu        | Služba Azure Media Live Streaming Service                                                                                                                                                                                                        |
| HLS                 | Protokol Apple HTTP Live Streaming                                                                                                                                                                                                            |
| PŘERUŠENÍ                | Dynamické adaptivní streamování přes HTTP                                                                                                                                                                                                          |
| Dokonalejší              | Protokol Smooth Streaming                                                                                                                                                                                                                     |
| MPEG2 – TS            | Proudy přenosu MPEG 2                                                                                                                                                                                                                      |
| RTMP                | Real-Time multimediální protokol                                                                                                                                                                                                                 |
| uimsbf              | Celé číslo bez znaménka, nejvýznamnější bit jako první.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1,2 normativních odkazů

Následující dokumenty obsahují pravidla, která prostřednictvím odkazu v tomto textu představují ustanovení tohoto dokumentu. Všechny dokumenty podléhají revizi podle standardů a čtenáři se doporučuje prozkoumat možnost použít nejnovější verze níže uvedených dokumentů. Čtenáři jsou také upozorněni, že novější edice odkazovaných dokumentů nemusí být kompatibilní s touto verzí specifikace časovanéch metadat pro Azure Media Services.


| Standard          | Definice                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-primetime] | [Specifikace signalizace vkládání digitálního programu primetime 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [Reference jazyka ActionScript pro FLASH](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["AMF0 zpráv o akcích"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [POMLČKA-IF-IOP]     | Doporučení pro spolupráci s POMLČKou v oboru fóra v 4,2 [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Časované metadata pro HTTP Live Streaming – [https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Časované metadata ve formátu Common Media Application Format (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | 2.4.0 značky ID3 verze  [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: formát základního mediálního souboru ISO v části 12, FourthEdition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Informační technologie – dynamické adaptivní streamování přes HTTP (POMLČKa) – část 1: Popis prezentace multimédií a formáty segmentů. Květen 2014. Zveřejněna. Adresa URL https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Informační technologie – formát multimediální aplikace (MPEG-A) – část 19: Common Media Application Format (CMAF) pro segmentovaná média. Leden 2018. Zveřejněna. Adresa URL https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Informační technologie – systémy MPEG Systems – část 7: běžné šifrování ve formátech souborů formátu základního média ISO. Února 2016. Zveřejněna. Adresa URL https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | [Protokol Microsoft Smooth Streaminge, 15. května 2014](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-ingestování]  | [Azure Media Services fragmentované specifikace ingestování MP4 v reálném čase](./media-services-fmp4-live-ingest-overview.md)                                                      |
| [RFC8216]         | Í. Pantos, Ed.; W. květen. HTTP Live Streaming. Srpen 2017. Informativní. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | Kódování dat Base16, Base32 a base64 – [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| RTMP            | ["Protokol zasílání zpráv Real-Time od společnosti Adobe", od 21. prosince 2012](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35:2019 – zpráva cueing pro vložení digitálního programu pro kabel https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – POMLČKa MPEG pro IP-Based kabelové služby – část 1: omezení a rozšíření MPD                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG POMLČKa pro IP-Based kabelové služby část 3: POMLČKa/FF – profil                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – rozhraní pro plánování událostí a oznamování                                                                                                                                                  |
| [SCTE-250]        | Rozhraní API pro správu událostí a signálů (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. časované podobyní metadat

Azure Media Services podporuje místní metadata v reálném čase pro protokoly [RTMP] i Smooth Streaming [MS-SSTR-ingestovat]. Metadata v reálném čase lze použít k definování vlastních událostí s vlastními jedinečnými vlastními schématy (JSON, binary, XML), jakož i s definovanými formáty, jako je ID3, nebo SCTE-35 pro signalizaci reklamy ve vysílání datového proudu. 

Tento článek poskytuje podrobné informace o tom, jak odesílat vlastní časované signály metadat pomocí podporovaných protokolů ingestování Azure Media Services. Článek také vysvětluje, jakým způsobem jsou manifesty pro HLS, POMLČKu a Smooth Streaming upraveny pomocí signálů s časovým limitem a způsobu jejich přemístění, když je obsah dodán pomocí CMAF (fragmenty MP4) nebo segmentů přenosu dat (TS) pro HLS. 

Běžné scénáře použití pro časované metadata zahrnují:

 - Signály AD SCTE-35 AD pro aktivaci přerušení reklamy v živé události nebo v případě lineárního vysílání
 - Metadata vlastního ID3, která můžou aktivovat události v klientské aplikaci (v prohlížeči, iOS nebo Androidu)
 - Vlastní definovaná metadata JSON, Binary nebo XML pro aktivaci událostí v klientské aplikaci
 - Telemetrii z živého kodéru, kamery IP nebo pomocí dronů
 - Události z kamery IP, jako je pohyb, detekce obličeje atd.
 - Informace o zeměpisné poloze z fotoaparátu akce, pomocí dronů nebo přesunutí zařízení
 - Texty skladby
 - Hranice programu pro lineární živý kanál
 - Obrázky nebo rozšířená metadata, která se mají zobrazit v živém kanálu
 - Sportovní výsledky nebo informace o herních hodinách
 - Interaktivní reklamní balíčky, které se mají zobrazit vedle videa v prohlížeči
 - Kvízy a cyklické dotazování
  
Azure Media Services živé události a balírna je schopná přijímat tyto časované signály metadat a převádět je do datového proudu metadat, který může kontaktovat klientské aplikace pomocí protokolů založených na standardech, jako je HLS a POMLČKa.


## <a name="21-rtmp-timed-metadata"></a>2,1 metadata RTMP – vypršela

Protokol [RTMP] umožňuje odeslat časované signály metadat pro různé scénáře, jako jsou vlastní metadata a signály AD SCTE-35. 

Reklamní signály (startovací zprávy) se odesílají jako startovací zprávy [AMF0] vložené do datového proudu [RTMP]. Zprávy s oznámením mohou být odeslány na nějakou dobu před samotným signálem pro spojení AD události nebo [SCTE35] AD. Pro podporu tohoto scénáře se v rámci zprávy hromádky pošle skutečné časové razítko události. Další informace najdete v tématu [AMF0].

Azure Media Services podporuje následující příkazy [AMF0] pro ingestování RTMP:

- **onUserDataEvent** – používá se pro vlastní metadata nebo pro [ID3v2] časované metadata.
- **onAdCue** – používá se primárně k signalizaci příležitosti umístění reklamy v živém streamu. Podporují se dva formy hromádky, jednoduchý režim a režim "SCTE-35". 
- **onCuePoint** – podporováno některými místními hardwarovými kodéry, jako je například element živého kodéru, k signalizaci zpráv [SCTE35]. 
  

Následující tabulka popisuje formát datové části zprávy AMF, kterou Media Services ingestuje jak pro režimy zpráv "jednoduché", tak i [SCTE35].

Název zprávy [AMF0] lze použít k odlišení více proudů událostí stejného typu.  V případě zpráv [SCTE-35] i "jednoduchého" musí být název zprávy AMF "onAdCue" podle požadavku ve specifikaci [Adobe-primetime].  Všechna pole, která nejsou uvedená níže, se Azure Media Services při ingestování ignorují.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP s vlastními metadaty s využitím "onUserDataEvent"

Pokud chcete poskytnout vlastní kanály metadat z nadřazeného kodéru, kamery IP, pomocí dronů nebo zařízení pomocí protokolu RTMP, použijte typ příkazu "onUserDataEvent" [AMF0] data Message.

Příkaz **"onUserDataEvent"** data Message musí obsahovat datovou část zprávy s následující definicí, kterou má zachytit Media Services a zabalené do formátu HLS, pomlčka a Smooth Streaming.
Doporučuje se odesílat zprávy s vypršenou platností – zprávy s metadaty nejsou častěji než jednou za 0,5 sekund (500 ms) nebo mohou nastat problémy se stabilitou živého datového proudu. Každá zpráva by mohla agregovat metadata z více snímků, pokud potřebujete zadat metadata na úrovni rámce. Pokud posíláte datové proudy s více přenosovými rychlostmi, doporučujeme, abyste zároveň poskytovali metadata jenom pro jednu přenosovou rychlost, abyste snížili šířku pásma a nedocházelo k rušivému zpracování videa nebo zvuku. 

Datová část pro **"onUserDataEvent"** by měla být zpráva formátu [MPEGDASH] EventStream XML. Díky tomu je snadné předat vlastní definovaná schémata, která se dají přenášet v EMSG datových vytíženích pro CMAF [MPEGCMAF] obsah, který se doručuje přes protokoly HLS nebo POMLČKy. Každá zpráva streamování událostí obsahuje schemeIdUri, který funguje jako identifikátor schématu zprávy URN a definuje datovou část zprávy. Některá schémata, jako je například " https://aomedia.org/emsg/ID3 " pro [ID3v2] nebo **urn: scte: scte35:2013: bin** pro [scte-35], jsou standardizovány v oboru pro interoperabilitu. Libovolný poskytovatel aplikace může definovat vlastní schéma pomocí adresy URL, kterou řídí (vlastní doména), a v případě potřeby může zadat specifikaci na této adrese URL. Pokud hráč má obslužnou rutinu pro definované schéma, pak to je jediná součást, která potřebuje pochopit datovou část a protokol.

Schéma pro datovou část XML EventStream [MPEG-SPOJOVNÍK] je definováno jako (výňatek z POMLČKy ISO-IEC-23009-1-3 Edition). Všimněte si, že v tuto chvíli je podporovaná jenom jedna "EventType" na "EventStream". Pokud je v **EventStream** k dispozici více událostí, bude zpracován pouze první prvek **události** .

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


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Ukázkový datový proud události XML s ID schématu ID3 a datovou datovou částí s kódováním base64.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Příklad datového proudu událostí s vlastním ID schématu a binárními daty s kódováním base64  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Příklad datového proudu událostí s vlastním ID schématu a vlastním kódem JSON  
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

### <a name="built-in-supported-scheme-id-uris"></a>Předdefinované identifikátory URI IDENTIFIKÁTORů podporovaných schémat
| Identifikátor URI ID schématu                 | Description                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https: \/ /aomedia.org/EMSG/ID3 | Popisuje, jak lze metadata [ID3v2] přenést jako časované metadata v rámci fragmentované MP4 kompatibilního s CMAF [MPEGCMAF]. Další informace najdete v tématu [časované metadata ve formátu Common Media Application Format (CMAF)](https://github.com/AOMediaCodec/id3-emsg) . |

### <a name="event-processing-and-manifest-signaling"></a>Zpracování událostí a signalizace manifestu

Po přijetí platné události **onUserDataEvent** bude Azure Media Services Hledat platnou datovou část XML, která odpovídá EventStreamType (definované v [MPEGDASH]), analyzovat datovou část XML a převést ji na pole fragmentu [MPEGCMAF] MP4 "EMSG" verze 1 pro úložiště v archivu Live a přenos do nástroje Media Services Package.   Balíček zjistí v živém streamu pole ' EMSG ' a:

- (a) "dynamické balení" do segmentů TS pro doručování klientům HLS v souladu se specifikací HLS Time metadata Specification [HLS-TMD] nebo
- (b) předat za doručení v CMAF fragmentech prostřednictvím HLS nebo POMLČKy nebo 
- (c) převeďte je na signál pro zhuštěnou stopu pro doručení prostřednictvím Smooth Streaming [MS-SSTR].

Kromě EMSG ve formátu CMAF nebo TS pro HLS, manifesty pro POMLČKu (MPD) a Smooth Streaming budou obsahovat odkaz na proudy událostí v pásmu (označované také jako stopa v podobě zhuštěného streamu v Smooth Streaming).

Jednotlivé události nebo jejich datová data se neúčtují přímo v manifestech HLS, SPOJOVNÍKu nebo hladce. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Další informativní omezení a výchozí hodnoty pro události onUserDataEvent

- Pokud v elementu EventStream není nastavená časová osa, ve výchozím nastavení se použije časová osa RTMP 1 kHz.
- Doručení zprávy onUserDataEvent je omezeno na každý 500 ms max. Pokud události odesíláte častěji, může to mít vliv na šířku pásma a stabilitu živého kanálu.

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 upozornění na hromádku služby RTMP pro AD pomocí "onAdCue"

Azure Media Services může naslouchat a reagovat na několik typů zpráv [AMF0], které se dají použít k signalizaci různých synchronizovaných metadat v reálném čase v živém streamu.  Specifikace [Adobe-primetime] definuje dva typy hromádky s názvem "jednoduchý" a "SCTE-35" režim. U "jednoduchého" režimu Media Services podporuje jedinou zprávu AMF upozornění nazvanou "onAdCue" s použitím datové části, která odpovídá tabulce níže definované pro signál "jednoduchý režim".  

V následující části se zobrazuje "jednoduchý" režim "v" režimu RTMP, který se dá použít k signalizaci základního signálu "spliceOut", který se bude přenášet do klientského manifestu pro HLS, POMLČKu a Smooth Streaming Microsoftu. To je velmi užitečné ve scénářích, kdy zákazník nemá komplexní systém pro nasazení nebo vkládání signálů SCTE s využitím služby AD-35 a používá k posílání zprávy startovacích zpráv základní kodér prostřednictvím rozhraní API. Místní kodér obvykle podporuje rozhraní API založené na REST pro aktivaci tohoto signálu, který bude také "spojením" s datovým proudem videa vložením IDR snímku do videa a spuštěním nového skupinu GOP.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 pro upozornění na hromádku RTMP AD pomocí "onAdCue" – jednoduchý režim

| Název pole | Typ pole | Povinné? | Označení                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| typ       | Řetězec     | Vyžadováno  | Zpráva události  Musí být "SpliceOut" k určení spojení jednoduchého režimu.                                                                                                                                                                                                         |
| id         | Řetězec     | Vyžadováno  | Jedinečný identifikátor popisující připletení nebo segment. Identifikuje tuto instanci zprávy.                                                                                                                                                                                       |
| doba trvání   | Číslo     | Vyžadováno  | Doba trvání připletení. Jednotky jsou zlomky sekund.                                                                                                                                                                                                                           |
| elapsed    | Číslo     | Volitelné  | V případě, že se signál opakuje za účelem podpory navýšení služby, toto pole je množství času prezentace, které uplynulo od zahájení spojení. Jednotky jsou zlomky sekund. Při použití jednoduchého režimu by tato hodnota neměla přesáhnout původní dobu trvání zapletení. |
| time       | Číslo     | Vyžadováno  | Musí být čas zapletení v době prezentace. Jednotky jsou zlomky sekund.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Ukázkový výstup manifestu s POMLČKou MPEG při použití jednoduchého režimu Adobe RTMP

Viz příklad [3.3.2.1 MPEG pomlčka. MPD EventStream pomocí jednoduchého režimu Adobe](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Viz příklad [3.3.3.1 pomlčky manifest s jednou periodou a Adobe Simple Mode ](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Příklad výstupu manifestu HLS při použití jednoduchého režimu Adobe RTMP

Viz ukázkový [manifest 3.2.2 HLS s použitím značky Adobe Simple Mode a EXT-X-Cue](#322-apple-hls-with-adobe-primetime-ext-x-cue) .

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 upozornění na hromádku v rámci služby AD v onAdCue – režim SCTE-35

Když pracujete s pokročilejším pracovním postupem pro provozování, který vyžaduje, aby se do manifestu HLS nebo POMLČKy převedla úplná 35 SCTE zpráva datové části, je nejlepší použít režim SCTE-35 specifikace [Adobe-primetime].  Tento režim podporuje signály v rámci Band SCTE-35, které se odesílají přímo do místního kodéru Live Encoder. potom se signály zakódují do datového proudu RTMP pomocí režimu SCTE-35, který je zadaný ve specifikaci [Adobe-primetime]. 

Zprávy SCTE-35 se obvykle mohou objevit pouze ve vstupech datového proudu MPEG-2 (TS) na místním kodéru. Podrobnosti o tom, jak nakonfigurovat přenos datového proudu, který obsahuje SCTE-35 a umožnit ho pro předávací řešení do RTMP v režimu Adobe SCTE-35, vám poskytne výrobce kodéru.

V tomto scénáři je nutné odeslat z místního kodéru následující datovou část pomocí typu zprávy **"onAdCue"** [AMF0].

| Název pole | Typ pole | Povinné? | Označení                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| informovat        | Řetězec     | Vyžadováno  | Zpráva události  U zpráv [SCTE-35] musí být toto pole binární splice_info_section () kódovaný v kódování Base64 (), aby bylo možné zprávy odesílat do HLS, hladkého a přerušovaného klienta.                                                                                                                                                                                                                               |
| typ       | Řetězec     | Vyžadováno  | Název URN nebo adresa URL, které identifikují schéma zprávy. U zpráv [SCTE-35] **by měl** být **"scte35"** , aby bylo možné zprávy odesílat do HLS, hladkého a přerušovaného klienta v souladu s [Adobe-primetime]. V případě potřeby může být k signalizaci zprávy [SCTE-35] použito také URN "urn: scte: scte35:2013: bin".                                                                                                        |
| id         | Řetězec     | Vyžadováno  | Jedinečný identifikátor popisující připletení nebo segment. Identifikuje tuto instanci zprávy.  Zprávy s ekvivalentní sémantikou musí mít stejnou hodnotu.                                                                                                                                                                                                                                                       |
| doba trvání   | Číslo     | Vyžadováno  | Doba, po kterou je segment přihlášení k události nebo AD, je-li znám. Pokud je tato hodnota neznámá, **měla by** být 0.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Číslo     | Volitelné  | Pokud se pro účely navýšení signálu [SCTE-35] AD opakuje, toto pole je množství času prezentace, které uplynulo od začátku zaznamenání. Jednotky jsou zlomky sekund. V režimu [SCTE-35] může tato hodnota přesáhnout původní určenou dobu trvání zapletení nebo segmentu.                                                                                                                   |
| time       | Číslo     | Vyžadováno  | Čas prezentace události nebo reklamy.  Čas a trvání prezentace **by měly být** v souladu s přístupovými body streamu (SAP) typu 1 nebo 2, jak je definováno v [ISO-14496-12] příloze I. Pro odchozí HLS **by měl být** čas a trvání zarovnaný s hranicemi segmentů. Prezentace a doba trvání různých zpráv událostí v rámci stejného datového proudu událostí se nesmí překrývat. Jednotky jsou zlomky sekund. |

---

<!---
#### Example MPEG DASH .mpd manifest with SCTE-35 mode
See [Section 3.3.3.2 example DASH manifest with SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)
--->

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Příklad HLS manifestu. m3u8 s signálem režimu SCTE-35
Viz [část 3.2.1.1 příklad MANIFESTU HLS s SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35)



## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 RTMP AD signalizace s "onCuePoint" pro živé prvky

On-premises on-premises Encoder podporuje v signálu RTMP značky AD. Azure Media Services v současné době podporuje pouze typ značky "onCuePoint" pro RTMP.  To může být povoleno v nastavení skupiny Adobe RTMP v nastaveních sady Media Live Encoder nebo rozhraní API nastavení "**ad_markers**" na "onCuePoint".  Podrobnosti najdete v dokumentaci k elementu Live. Povolení této funkce ve skupině RTMP projde signály SCTE-35 do výstupů Adobe RTMP, aby je bylo možné zpracovat pomocí Azure Media Services.

Typ zprávy "onCuePoint" je definován v [Adobe-Flash-AS] a má následující strukturu datové části při odeslání z výstupu elementu Live RTMP.


| Vlastnost   | Popis                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | Název by měl být "**scte35**" prostřednictvím elementu Live.                                                                                                                                                                              |
| time       | Čas v sekundách, kdy během časové osy došlo k startovacímu bodu v souboru videa                                                                                                                                           |
| typ       | Typ startovacího bodu by měl být nastaven na "**Event**".                                                                                                                                                                             |
| parameters | Asociativní pole řetězců dvojice název/hodnota obsahující informace ze zprávy SCTE-35, včetně ID a doby trvání. Tyto hodnoty jsou analyzovány Azure Media Services a zahrnuty do tagu dekorace manifestu. |


Když se použije tento režim značky AD, výstup manifestu HLS je podobný režimu Adobe "Simple".


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Příklad MPEG POMLČKy MPD, jedno období, signály Adobe Simple Mode

```xml
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
```

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Příklad HLS playlist, Adobe Simple Mode signales s použitím značky EXT-X-CUE (zkrácený znak "..." pro zkrácení)

Následující příklad ukazuje výstup z Media Services dynamického balíčku pro datový proud služby RTMP ingestování pomocí signálů v režimu Adobe "jednoduchá" a jako starší značku [Adobe-primetime] EXT-X-CUE.  

```
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
```

### <a name="216-cancellation-and-updates"></a>2.1.6 zrušení a aktualizace

Zprávy můžete zrušit nebo aktualizovat odesláním více zpráv se stejným časem a ID prezentace. Čas a ID prezentace jedinečně identifikují událost a poslední přijatá zpráva pro konkrétní dobu prezentace, která splňuje podmínky předběžného zavedení, je zpráva, na které se aplikace používá. Aktualizovaná událost nahrazuje všechny dříve přijaté zprávy. Omezení předběžného zavedení je čtyři sekundy. Oznámení obdržené nejméně čtyři sekundy před dobou trvání prezentace se budou zpracovávat.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2,2 fragmentovaný ingestování MP4 (Smooth Streaming)

Požadavky na ingestování živého streamu najdete v tématu [MS-SSTR-ingestování]. Následující části obsahují podrobné informace o ingestování časovanéch metadat prezentace.  Vyčasované metadata prezentace jsou ingestovaná jako zhuštěná stopa, která je definovaná v poli manifest živého serveru (viz MS-SSTR) a v poli video (' Moov ').  

Každý zhuštěný fragment se skládá z pole fragmentu videa (' Moof ') a mediálního Data Box (' mdat '), kde je pole ' mdat ' binární zprávou.

Aby bylo možné dosáhnout přesného vkládání reklam, musí kodér rozdělit fragment v době prezentace, kde je nutné přidat hromádku.  Je nutné vytvořit nový fragment, který začíná nově vytvořeným IDR snímkem nebo přístupovým bodem (SAP) typu 1 nebo 2, jak je definováno v [ISO-14496-12] příloze I.
<!--- This allows the Azure Media Packager to properly generate an HLS manifest and a DASH multi-period manifest where the new Period begins at the frame-accurate splice conditioned presentation time. --->

### <a name="221-live-server-manifest-box"></a>2.2.1 dynamický Server manifest serveru

Zhuštěná stopa **musí** být deklarována v poli manifestu živého serveru s **\<textstream\>** položkou a **musí** mít následující sady atributů:

| **Název atributu** | **Typ pole** | **Požadováno?** | **Popis**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Číslo         | Vyžadováno      | **Musí** být "0", což znamená, že záznam má neznámou proměnlivou přenosovou rychlost.                                                                                                                                                          |
| parentTrackName    | Řetězec         | Vyžadováno      | **Musí** být název nadřazeného záznamu, na který jsou kódy času zhuštěného stopy zarovnané na časovou osu. Nadřazená stopa nemůže být zhuštěná stopa.                                                                             |
| manifestOutput     | Logická hodnota        | Vyžadováno      | **Musí** být "true", aby bylo patrné, že zhuštěné stopy budou vloženy do hladkého manifestu klienta.                                                                                                                        |
| Podtyp            | Řetězec         | Vyžadováno      | **Musí** se jednat o čtyři kódy znaků "data".                                                                                                                                                                                  |
| Schéma             | Řetězec         | Vyžadováno      | **Musí** to být název URN nebo adresa URL identifikující schéma zprávy. U zpráv [SCTE-35] **musí** být název urn: SCTE: scte35:2013: bin, aby bylo možné zprávy odeslat HLS, hladkému a přerušovanému klientovi v souladu s [SCTE-35]. |
| stop          | Řetězec         | Vyžadováno      | **Musí** se jednat o název zhuštěného záznamu. Stop lze použít k odlišení více datových proudů událostí se stejným schématem. Každý datový proud událostí **musí** mít jedinečný název stopy.                                |
| měřítk          | Číslo         | Volitelné      | **Musí** se jednat o časovou osu nadřazené stopy.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 video box

Pole video (' Moov ') se řídí polem manifest pro živý Server jako součást záhlaví Stream pro zhuštěnou stopu.

Pole Moov **by mělo** obsahovat pole **TrackHeaderBox (' tkhd ')** , jak je definováno v [ISO-14496-12] s následujícími omezeními:

| **Název pole** | **Typ pole**          | **Požadováno?** | **Popis**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| doba trvání       | 64 – bitová unsigned integer | Vyžadováno      | Hodnota **by měla** být 0, protože pole Track má nula vzorků a celková doba trvání vzorků v poli stop je 0. |

---

Pole Moov **by mělo** obsahovat **HandlerBox (' HDLR ')** , jak je definováno v [ISO-14496-12] s těmito omezeními:

| **Název pole** | **Typ pole**          | **Požadováno?** | **Popis**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32 – bitová unsigned integer | Vyžadováno      | **Měla by** být meta. |

---

Pole stsd **by mělo** obsahovat MetaDataSampleEntry pole s názvem kódování definovaným v [ISO-14496-12].  Například pro zprávy SCTE-35 **musí** být název kódování "SCTE".

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 – pole fragmentu videa a Data Box multimédií

Fragmenty zhuštěného sledování se skládají z pole fragment videa (' Moof ') a mediálního Data Box (' mdat ').

> [!NOTE]
> Aby bylo možné dosáhnout přesného vkládání reklam, musí kodér rozdělit fragment v době prezentace, kde je nutné přidat hromádku.  Je nutné vytvořit nový fragment, který začíná nově vytvořeným IDR snímkem nebo přístupovým bodem (SAP) typu 1 nebo 2, jak je definováno v [ISO-14496-12] příloze I.
> 

Pole MovieFragmentBox (' Moof ') **musí** obsahovat pole **TrackFragmentExtendedHeaderBox (' UUID ')** , jak je definováno v [MS-SSTR] pomocí následujících polí:

| **Název pole**         | **Typ pole**          | **Požadováno?** | **Popis**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64 – bitová unsigned integer | Vyžadováno      | **Musí** se jednat o dobu příjezdu události. Tato hodnota Zarovná zprávu s nadřazenou stopou.           |
| fragment_duration      | 64 – bitová unsigned integer | Vyžadováno      | **Musí** se jednat o dobu trvání události. Doba trvání může být nulová, aby označovala, že doba trvání je neznámá. |

---


Pole MediaDataBox (' mdat ') **musí** mít následující formát:

| **Název pole**          | **Typ pole**                   | **Požadováno?** | **Popis**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| verze                 | 32-bit unsigned integer (uimsbf) | Vyžadováno      | Určuje formát obsahu v poli ' mdat '. Nerozpoznané verze budou ignorovány. V současné době je jediná podporovaná verze 1.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-bit unsigned integer (uimsbf) | Vyžadováno      | Identifikuje tuto instanci zprávy. Zprávy s ekvivalentní sémantikou musí mít stejnou hodnotu. To znamená, že zpracování libovolného okna se zprávou o události se stejným ID je dostatečné.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bit unsigned integer (uimsbf) | Vyžadováno      | Součet fragment_absolute_time určený v TrackFragmentExtendedHeaderBox a presentation_time_delta **musí** být časem prezentace události. Čas a trvání prezentace **by měly být** v souladu s přístupovými body streamu (SAP) typu 1 nebo 2, jak je definováno v [ISO-14496-12] příloze I. Pro odchozí HLS **by měl být** čas a trvání zarovnaný s hranicemi segmentů. Prezentace a doba trvání různých zpráv událostí v rámci **stejného datového proudu událostí se nesmí** překrývat. |
| zpráva                 | pole bajtů                       | Vyžadováno      | Zpráva události U zpráv [SCTE-35] je zpráva binární splice_info_section (). U zpráv [SCTE-35] **musí** být toto splice_info_section (), aby bylo možné ODESÍLAT zprávy HLS, hladkému a přerušovanému klientovi v souladu s [SCTE-35]. Pro zprávy [SCTE-35] je binární splice_info_section () datovou částí pole mdat a není kódována pomocí **kódování Base64.**                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 zrušení a aktualizace

Zprávy můžete zrušit nebo aktualizovat odesláním více zpráv se stejným časem a ID prezentace.  Čas a ID prezentace jednoznačně identifikují událost. Poslední zpráva přijatá pro určitou dobu prezentace, která splňuje podmínky předběžného zavedení, je zpráva, na které se aplikace provádí. Aktualizovaná zpráva nahrazuje všechny dříve přijaté zprávy.  Omezení předběžného zavedení je čtyři sekundy. Oznámení obdržené nejméně čtyři sekundy před dobou trvání prezentace se budou zpracovávat. 


## <a name="3-timed-metadata-delivery"></a>3 časované doručování metadat

Data datového proudu událostí jsou neprůhledná, aby Media Services. Media Services pouze předává tři části informací mezi koncovým bodem ingesta a koncovým bodem klienta. Následující vlastnosti jsou doručeny klientovi, v souladu s [SCTE-35] a/nebo protokolem streamování klienta:

1.  Schéma – název URN nebo adresa URL identifikující schéma zprávy.
2.  Prezentační čas – čas prezentace události na časové ose média.
3.  Duration – doba trvání události.
4.  ID – volitelný jedinečný identifikátor události.
5.  Zpráva – data události.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3,1 Smooth Streaming manifestu Microsoftu  

Podrobnosti o tom, jak naformátovat zhuštěné zprávy, najdete v článku zpracování řídkých stop [MS-SSTR]. U zpráv [SCTE35] Smooth Streaming bude výstupem splice_info_section () kódovaných ve formátu base64 do zhuštěného fragmentu.
StreamIndex **musí** mít PODTYP "data" a CustomAttributes – **musí** obsahovat atribut s názvem = "Schema" a hodnotou = "urn: scte: scte35:2013: bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Vyhlazený manifest klienta příklad ukazující na kódování Base64 [SCTE35] splice_info_section ()
```xml
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
```

## <a name="32-apple-hls-manifest-decoration"></a>3,2. dekorace manifestu HLS Apple

Azure Media Services podporuje následující značky manifestu HLS pro signalizaci informací o vyřízení služby AD během živé události nebo události na vyžádání. 

<!--- EXT-X-DATERANGE as defined in Apple HLS [RFC8216] --->
- EXT-X-CUE, jak je definováno v [Adobe-primetime]
<!--- this mode is considered "legacy".  Customers should adopt the EXT-X-DATERANGE tag when possible. --->

Výstup dat do jednotlivých značek se bude lišit v závislosti na použitém režimu pro příjem signálu. Například RTMP ingestování s Adobe Simple Mode neobsahuje úplnou datovou část s kódováním base64 SCTE-35.

<!---
## 3.2.1 Apple HLS with EXT-X-DATERANGE (recommended)

The Apple HTTP Live Streaming [RFC8216] specification allows for signaling of [SCTE-35] messages. The messages are inserted into the segment playlist in an EXT-X-DATERANGE tag per [RFC8216] section titled "Mapping SCTE-35 into EXT-X-DATERANGE".  The client application layer can parse the M3U playlist and process M3U tags, or receive the events through the Apple player framework.  

The **RECOMMENDED** approach in Azure Media Services (version 3 API) is to follow [RFC8216] and use the EXT-X_DATERANGE tag for [SCTE35] ad avail decoration in the manifest.
--->


## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35"></a>3.2.1.1 příklad HLS manifest. m3u8 zobrazující signalizaci EXT-X-CUE pro SCTE-35

V následujícím příkladu je výstup manifestu HLS z dynamického balíčku Media Services zobrazuje značku EXT-X-CUE pro [Adobe-primetime] v režimu SCTE35. 

```
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
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

```


<!---
THIS VERSION HAS THE HLSv8 DATERANGE Tags in it
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

--->

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue"></a>3.2.2 Apple HLS s Adobe primetime EXT-X-CUE

Media Services (verze 2 a 3 API) podporuje výstup značky EXT-X-CUE, jak je definovaný v [Adobe-primetime] "SCTE-35 režimu". V tomto režimu Azure Media Services vloží do značky EXT-X-CUE [SCTE-35] splice_info_section () s kódováním base64.  

Označení "starší" verze "EXT-X-HROMÁDKy je definováno níže a také může být normativní odkaz ve specifikaci [Adobe-primetime]. To by mělo být použito pouze pro SCTE35, pokud je to potřeba, v opačném případě je doporučená značka definována v [RFC8216] jako EXT-X-DATERANGE. 

| **Název atributu** | **Typ**                      | **Požadováno?**                             | **Popis**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| INFORMOVAT                | řetězec v uvozovkách                 | Vyžadováno                                  | Zpráva zakódovaná jako řetězec kódovaný v kódování Base64, jak je popsáno v [RFC4648]. U zpráv [SCTE-35] se jedná o splice_info_section kódovaný v kódování Base64 ().                                                                                                                                      |
| TYP               | řetězec v uvozovkách                 | Vyžadováno                                  | Název URN nebo adresa URL, které identifikují schéma zprávy. U zpráv [SCTE-35] má typ speciální hodnotu "scte35".                                                                                                                                                                          |
| ID                 | řetězec v uvozovkách                 | Vyžadováno                                  | Jedinečný identifikátor události Pokud ID není zadáno při ingestování zprávy, vygeneruje Azure Media Services jedinečný identifikátor.                                                                                                                                              |
| DURATION           | desítkové číslo s plovoucí desetinnou čárkou | Vyžadováno                                  | Doba trvání události. Pokud je tato hodnota neznámá, **měla by** být 0. Jednotky jsou factional sekund.                                                                                                                                                                                           |
| UPLYNULÝ            | desítkové číslo s plovoucí desetinnou čárkou | Volitelné, ale vyžadované pro posuvné okno | Když se signál opakuje pro podporu posuvných oken prezentace, toto pole **musí** být množství času prezentace, které uplynulo od začátku události. Jednotky jsou zlomky sekund. Tato hodnota může přesáhnout původní určenou dobu trvání připletení nebo segmentu. |
| ČAS               | desítkové číslo s plovoucí desetinnou čárkou | Vyžadováno                                  | Čas prezentace události Jednotky jsou zlomky sekund.                                                                                                                                                                                                                        |

Vrstva aplikace přehrávače HLS použije typ k identifikaci formátu zprávy, dekódování zprávy, použití potřebných převodů času a zpracování události.  Události jsou v závislosti na časovém razítku události synchronizovány v seznamu stop segmentu nadřazené stopy.  Jsou vloženy před nejbližší segment (#EXTINF značka).


### <a name="323-hls-m3u8-manifest-example-using-adobe-primetime-ext-x-cue"></a>3.2.3 Příklad manifestu HLS. m3u8 pomocí Adobe primetime EXT-X-CUE

Následující příklad ukazuje dekoraci manifestu HLS pomocí značky Adobe primetime EXT-X-CUE.  Parametr "CUE" obsahuje pouze vlastnosti typu a trvání, což znamená, že se jedná o zdroj RTMP pomocí signalizace režimu Adobe "Simple".  
<!---If this was a SCTE-35 mode signal, the tag would include the base64 encoded binary SCTE-35 payload as seen in the [3.2.1.1 example](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).
--->


```
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

```

### <a name="324-hls-message-handling-for-adobe-primetime-ext-x-cue"></a>HLS zpracování zpráv pro Adobe primetime EXT-X-CUE

Události jsou oznámeny v seznamu stop segmentu každého videa a zvukové stopy. Pozice značky EXT-X-CUE **musí** vždy být buď bezprostředně před prvním segmentem HLS (pro zahlašování nebo spouštění segmentu), nebo hned za posledním segmentem HLS (pro spojení v nebo na konci segmentu), na který se vztahují atributy času a doby trvání, jak požaduje [Adobe-primetime].

Když je povoleno posuvné okno prezentace, **musí** být značka EXT-X-Cue opakovaně dostatečně popsána v seznamu stop segmentů a uplynulý atribut **musí** být použit k označení doby, po kterou je spojení nebo segment aktivní, podle požadavku [Adobe-primetime].

Když je zapnuté posuvné okno prezentace, značky EXT-X-CUE se odeberou ze seznamu skladeb segmentů, když se v době, kdy se na ně odkazuje, vyřadí z posuvných oken prezentace.

## <a name="33-dash-manifest-decoration-mpd"></a>3,3 PŘERUŠOVANého manifestu – dekorace (MPD)

[MPEGDASH] poskytuje tři způsoby, jak signalizovat události:

1.  Události s signálem MPD EventStream
2.  Události signalizace v rámci pásma pomocí pole zpráva o události (EMSG)
3.  Kombinace obou hodnot 1 a 2

Události s signálem MPD EventStream jsou užitečné pro VOD streaming, protože klienti mají přístup ke všem událostem hned po stažení MPD. Je to také užitečné pro signalizaci SSAI, kde musí SSAI dodavatel pro příjem dat analyzovat signály z manifestu MPD a dynamicky vkládat obsah služby AD.  Řešení in-band (' EMSG ') je užitečné pro živé streamování, kde klienti nepotřebují stahovat MPD znovu, nebo neprobíhá žádná manipulace s SSAI manifestem mezi klientem a počátkem. 

Azure Media Services výchozím chováním pro POMLČKu je signalizovat v EventStream MPD i v pásmu pomocí pole zpráva o události (EMSG).

Zprávy hromádky ingestované přes [RTMP] nebo [MS-SSTR-ingestování] jsou namapované na POMLČKové události, a to pomocí integrovaných polí EMSG a/nebo v EventStreams. 

Signalizace SCTE-35 pro POMLČKu se řídí definicemi a požadavky definovanými v [SCTE-214-3] a také v [SPOJOVNÍK-IF-IOP] oddílu 13.12.2 (' SCTE35 Events '). 

V případě služby SCTE-Band [-35] se v poli zpráva o události (EMSG) používá schemeId = "urn: SCTE: scte35:2013: bin". V případě dekorace manifestu MPD EventStream schemeId používá "urn: scte: scte35:2014: XML + bin".  Tento formát je reprezentace XML události, která zahrnuje binární výstup s kódováním base64 úplné zprávy SCTE-35, která byla doručena při příjmu. 

Normativní referenční definice zástupných zpráv [SCTE-35] v SPOJOVNÍKu jsou k dispozici v [SCTE-214-1] sec 6.7.4 (MPD) a [SCTE-214-3] SEC 7.3.2 (přeprava zpráv s 35 startovacími zprávami SCTE).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 EventStream signalizace (MPD) MPEG POMLČKa

Manifest (MPD) dekorace událostí bude signalizována v MPD pomocí elementu EventStream, který se zobrazí v rámci elementu period. Používá se schemeId "urn: scte: scte35:2014: XML + bin".


> [!NOTE]
> Pro účely zkrácení [SCTE-35] umožňuje použití oddílu zakódovaného ve formátu base64 v signálu. Binary element (místo prvku Signal. SpliceInfoSection) jako alternativu k přepravení celé analyzované zprávy upozornění.
> Azure Media Services používá tento přístup k signalizaci v manifestu MPD pomocí tohoto přístupu ke službě XML + bin.
> To je také doporučená metoda, která se používá v [SPOJOVNÍK-IF-IOP] – Viz část s názvem [streamování událostí vkládání reklam na straně pomlčky, pokud se IOP pokyn](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 


Element EventStream má následující atributy:

| **Název atributu** | **Typ**                | **Požadováno?** | **Popis**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | řetězec                  | Vyžadováno      | Určuje schéma zprávy. Schéma je nastaveno na hodnotu atributu schématu v poli manifest živého serveru. Hodnota **by měla** být název URN nebo adresa URL identifikující schéma zprávy; Podporovaná výstupní schemeId by měla být "urn: scte: scte35:2014: XML + bin" na [SCTE-214-1] SEK 6.7.4 (MPD), protože služba v tuto chvíli podporuje pouze "XML + bin" pro zkrácení v MPD. |
| hodnota              | řetězec                  | Volitelné      | Další řetězcovou hodnotu, kterou vlastníci schématu používají k přizpůsobení sémantiky zprávy. Aby bylo možné odlišit více datových proudů událostí se stejným schématem, **musí** být hodnota nastavena na název datového proudu událostí (stopovat pro [MS-SSTR-ingesta] nebo AMF název zprávy pro [RTMP] ingestovat).                                                                         |
| Timescale          | 32 – bitová unsigned integer | Vyžadováno      | Časová osa v taktech za sekundu.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 příklad streamů událostí pro MPEG POMLČKu

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 příklady manifestu MPEG POMLČKa. MPD pro streamování RTMP pomocí Adobe Simple Mode

Následující příklad ukazuje výňatek EventStream z nástroje Media Services Dynamic Package pro datový proud RTMP pomocí signalizace režimu Adobe "Simple".

```xml
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
```


#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 příklady manifestu MPEG POMLČKa. MPD pro datový proud RTMP pomocí režimu Adobe SCTE-35

Následující příklad ukazuje výňatek EventStream z nástroje Media Services Dynamic Package pro datový proud RTMP pomocí signalizace režimu Adobe SCTE-35.

Příklad EventStream elementu pomocí XML + signalizace stylu přihrádky na [SCTE-214-1]

```xml

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
```



> [!IMPORTANT]
> Všimněte si, že presentationTime je doba prezentace přeložené události [SCTE-35], která bude relativní vzhledem k času spuštění období, nikoli času doručení zprávy.
> [MPEGDASH] definuje Event@presentationTime jako určuje čas prezentace události relativně k začátku období.
> Hodnota doby prezentace v sekundách je rozdělení hodnoty tohoto atributu a hodnoty EventStream@timescale atributu.
> Pokud není zadán, hodnota času prezentace je 0.


#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 ukázkový manifest MPEG POMLČKy (MPD) s jednou periodou, EventStream, s využitím signálů Adobe Simple Mode

Následující příklad ukazuje výstup z nástroje Media Services Dynamic packageer pro zdrojový Stream RTMP pomocí metody služby AD "Simple" Mode Signal. Výstupem je manifest s jedním tečkou ukazující EventStream pomocí identifikátoru URI schemeId nastaveného na "urn: com: Adobe: dpi: Simple: 2015" a vlastnost Value nastavenou na "simplesignal".
Jednotlivé jednoduché signály jsou k dispozici v prvku události s @presentationTime @duration vlastnostmi, a @id vyplněny na základě příchozích jednoduchých signálů.

```xml
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

```

<!---
#### 3.3.3.2 Example MPEG DASH manifest (MPD) with multi-period, EventStream, using Adobe SCTE35 mode signaling

The following example shows the output from the Media Services dynamic packager for a source RTMP stream using the Adobe SCTE35 mode signaling.
In this case, the output manifest is a multi-period DASH .mpd with an EventStream element, and @schemeIdUri property set to "urn:scte:scte35:2014:xml+bin" and a @value property set to "scte35". Each Event element in the EventStream contains the full base64 encoded binary SCTE35 signal 

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

--->

### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 PŘERUŠOVANé signalizace v okně zpráv o probandu události

Proud událostí v rámci pásma vyžaduje, aby MPD měl element InbandEventStream na úrovni adaptační sady.  Tento prvek má povinný atribut schemeIdUri a volitelný atribut časové osy, který se také zobrazí v poli zpráva události (' EMSG ').  Okna zpráv událostí s identifikátory schémat, které nejsou definovány v rámci MPD, **by neměla** být k dispozici.

V případě přepravování na SCTE-35] **musí** signály používat schemeId = "urn: SCTE: scte35:2013: bin".
Normativní definice přepravosti [SCTE-35] ve zprávách ve bandu jsou definované v [SCTE-214-3] SEC 7.3.2 (přeprava SCTE 35 zprávy upozornění).

Následující podrobnosti popisují konkrétní hodnoty, které by měl klient očekávat v ' EMSG ' v souladu s [SCTE-214-3]:

| **Název pole**          | **Typ pole**          | **Požadováno?** | **Popis**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | řetězec                  | Vyžadováno      | Určuje schéma zprávy. Schéma je nastaveno na hodnotu atributu schématu v poli manifest živého serveru. Hodnota **musí** být název URN identifikující schéma zprávy. Pro zprávy [SCTE-35] **musí** být "urn: SCTE: scte35:2013: bin" v souladu s [SCTE-214-3].          |
| Hodnota                   | řetězec                  | Vyžadováno      | Další řetězcovou hodnotu, kterou vlastníci schématu používají k přizpůsobení sémantiky zprávy. Aby bylo možné odlišit více datových proudů událostí se stejným schématem, bude hodnota nastavena na název datového proudu událostí (název stopy pro hladkou zprávu ingesta nebo název zprávy AMF pro ingestování RTMP). |
| Timescale               | 32 – bitová unsigned integer | Vyžadováno      | Časová osa (v taktech za sekundu) polí časy a trvání v rámci pole ' EMSG '.                                                                                                                                                                                                            |
| Presentation_time_delta | 32 – bitová unsigned integer | Vyžadováno      | Časový rozdíl v době prezentace médií v době a nejbližší době prezentace v tomto segmentu. Čas a trvání prezentace **by měly být** v souladu s přístupovými body streamu (SAP) typu 1 nebo 2, jak je definováno v [ISO-14496-12] příloze I.                                  |
| event_duration          | 32 – bitová unsigned integer | Vyžadováno      | Doba trvání události nebo 0xFFFFFFFF, která označuje neznámou dobu trvání.                                                                                                                                                                                                                              |
| Id                      | 32 – bitová unsigned integer | Vyžadováno      | Identifikuje tuto instanci zprávy. Zprávy s ekvivalentní sémantikou musí mít stejnou hodnotu. Pokud ID není zadáno při ingestování zprávy, vygeneruje Azure Media Services jedinečný identifikátor.                                                                                        |
| Message_data            | pole bajtů              | Vyžadováno      | Zpráva události Pro zprávy [SCTE-35] jsou data zprávy binární splice_info_section () v souladu s [SCTE-214-3].                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Příklad entity InBandEvenStream pro Adobe Simple Mode
```xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
```

### <a name="335-dash-message-handling"></a>Zpracování PŘERUŠOVANých zpráv 3.3.5

Události jsou v rámci pole EMSG pro video i zvukové stopy signalizace v rámci pásma.  K signalizaci dochází u všech požadavků segmentů, pro které je presentation_time_delta 15 sekund nebo méně. 

Když je zapnuté posuvné okno prezentace, odeberou se zprávy o událostech ze systému MPD, pokud je součet času a doby trvání zprávy události menší než čas mediálních dat v manifestu.  Jinými slovy, zprávy o událostech jsou odebrány z manifestu, pokud čas média, na který odkazují, byl zahrnut z posuvných oken prezentace.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 ingestující pokyny k implementaci pro dodavatele kodéru

Následující pokyny jsou běžné problémy, které mohou mít vliv na implementaci této specifikace od dodavatele kodéru.  Níže uvedené pokyny byly shromážděny na základě názoru reálného partnera, který usnadňuje implementaci této specifikace pro ostatní. 

[SCTE-35] zprávy se ingestují v binárním formátu pomocí schématu **"urn: SCTE: scte35:2013: bin"** pro [MS-SSTR-ingestování] a typu **"scte35"** pro [RTMP] ingestování. Aby se usnadnil převod typu [SCTE-35], který vychází z časových razítek prezentace streamování MPEG-2 (PTS), je k dispozici mapování mezi PTS (pts_time + pts_adjustment splice_time ()) a časovou osou médií, která je k dispozici v době prezentace události (pole fragment_absolute_time pro položku plynule přijmout a v poli čas pro RTMP ingestování). Mapování je nezbytné, protože hodnota 33-bit PTS se překročí přibližně každých 26,5 hodin.

Smooth Streaming ingestování [MS-SSTR-ingestování] vyžaduje, aby mediální Data Box (' mdat ') **musí** obsahovat **splice_info_section ()** definované v [SCTE-35]. 

V případě služby RTMP ingesta se atribut Cue zprávy AMF nastaví na splice_info_section kódovaný v kódování Base64 **()** definované v [SCTE-35].  


Když zprávy mají výše popsaný formát, odesílají se do HLS, hladkého a PŘERUŠOVANého klienta, jak je definováno výše.  

Při testování vaší implementace s Azure Media Services platformou Nejdřív spusťte testování před přechodem na testování v Livestream pro kódování před přechodem na testování pomocí Livestream Pass-through.

---

## <a name="change-history"></a>Historie změn

| Date (Datum)     | Změny                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Revidovaná podpora pro ingestování RTMP, přidání RTMP "onCuePoint" pro živé prvky                                            |
| 08/22/19 | Aktualizováno, aby se přidala OnUserDataEvent do RTMP pro vlastní metadata                                                          |
| 1/08/20  | Opravená chyba pro jednoduchý a SCTE35 režim RTMP. Změněno z "onCuePoint" na "onAdCue". Byla aktualizována tabulka jednoduchého režimu. |
| 08/4/20  | Odebrala se podpora pro značku DATERANGE, která bude odpovídat implementaci v produkční službě.    |

## <a name="next-steps"></a>Další kroky
Zobrazení Media Servicesch cest výuky.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
