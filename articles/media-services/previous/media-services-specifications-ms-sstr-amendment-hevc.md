---
title: Azure Media Services – Smooth Streaming změny protokolu (MS-SSTR) pro HEVC | Dokumentace Microsoftu
description: Tato specifikace popisuje protokol a formáty fragmentovaného MP4 živé streamování s HEVC ve službě Azure Media Services. Jedná se o změnu do dokumentace technologie Smooth Streaming protokolu (MS-SSTR) zahrnující podporu pro ingestování HEVC a streamování. V tomto článku jsou uvedeny pouze změny, které jsou nutné k poskytování HEVC s výjimkou byly "(žádná změna)" označuje text bude zkopírován pro objasnění pouze.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: johndeu;
ms.openlocfilehash: e13995ccdc609951f908b4a1779eaefcfdc596bb
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005212"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Technologie Smooth Streaming protokolu (MS-SSTR) změně pro HEVC 

## <a name="1-introduction"></a>1 Úvod 

Tento článek obsahuje podrobné změny použít na protokol Smooth Streaming specifikace [MS-SSTR] k povolení technologie Smooth Streaming z HEVC kódování videa. V téhle specifikaci uvádíme pouze změny, které jsou potřebné k zajištění kodek HEVC videa. Tento článek se řídí číslování stejné schéma jako specifikaci [MS-SSTR]. K orientaci čtenáře na jejich umístění ve specifikaci [MS-SSTR] jsou k dispozici prázdnou nadpisy uvedené v celém článku.  "(Žádná změna)" označuje, že text bude zkopírován vyjasnění pouze pro účely.

Článek obsahuje požadavky na technickou implementaci pro signalizaci HEVC kodek videa v manifestu technologie Smooth Streaming a normativní odkazy jsou aktualizovány tak, aby odkazovaly na aktuální standardy MPEG, které zahrnují HEVC Common Encryption HEVC a pole názvy pro formát mediálního souboru ISO Base byla aktualizována, aby bylo v souladu s nejnovější specifikace. 

Odkazované protokol Smooth Streaming specifikace [MS-SSTR] popisuje přenosový formát použitý k doručování živě i na vyžádání digitální média, jako je například zvuku a videa, z následujících způsobů: z kodéru na webový server, ze serveru na jiný server a od Server HTTP klientovi.
Použití MPEG-4 ([[MPEG4 RA])](https://go.microsoft.com/fwlink/?LinkId=327787)– struktura doručování na základě dat prostřednictvím protokolu HTTP umožňuje bezproblémové přepínání téměř v reálném čase mezi různé úrovně kvality komprimované mediálního obsahu. Výsledkem je konstantní přehrávání pro koncového uživatele klienta protokolu HTTP, i v případě, že síť a vykreslování videa podmínky změnit pro klientský počítač nebo zařízení.

## <a name="11-glossary"></a>1.1 Glosář 

Následující termíny jsou definovány v *[MS-GLOS]*:

>   **globálně jedinečný identifikátor (GUID) univerzálně jedinečným identifikátorem (UUID)**

Následující termíny jsou specifické pro tento dokument:

>  **čas sestavení:** Čas ukázku je uvedena na klientovi, jak jsou definovány v [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)

>   **CENC**: Běžné šifrování jako definovaný v [ISO/IEC 23001-7] druhé vydání.

>   **Čas dekódování:** Čas ukázku je potřeba se dekódovat na klientovi, jak jsou definovány v [[ISO/IEC http://go.microsoft.com/fwlink/?LinkId=18369514496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)

**fragment:** Nezávisle na sobě ke stažení jednotka **média** , která zahrnuje jednu nebo více **ukázky**.

>   **HEVC:** Vysoké efektivity Video psaní kódu, jak jsou definovány v [ISO/IEC 23008 2]

>   **manifest:** Metadata o **prezentace** umožňuje získat klientským k podání žádostí o **média**. **médium:** Komprimovaných dat zvuk, video a text klient používá k přehrávání **prezentace**. **Formát médií:** Dobře definovaný formát pro uvádění zvuku nebo videa jako komprimovaný **ukázka**.

>   **prezentace:** Sada všech **datové proudy** a související metadata potřebná k přehrání videa jeden. **Žádost:** Zpráva HTTP odeslaných z klienta na server, jak jsou definovány v [[RFC2616].](https://go.microsoft.com/fwlink/?LinkId=90372) **Odpověď:** Zpráva HTTP odeslaných ze serveru klientovi, jak jsou definovány v [[RFC2616].](https://go.microsoft.com/fwlink/?LinkId=90372)

>   **Ukázka:** Nejmenší základní jednotku (jako je například rámec), ve kterém **média** jsou uložena a zpracována.

>   **MŮŽE BY MĚL, MUSÍ, BY NEMĚLA, NENÍ NUTNÉ:** Tyto podmínky (v všechna písmena velká) se používají, jak je popsáno v [[RFC2119].](https://go.microsoft.com/fwlink/?LinkId=90317) Všechny příkazy volitelné chování použijte buď může SHOULD nebo by neměl.

## <a name="12-references"></a>1.2 odkazy

>   Odkazy na dokumentaci k Microsoft Open specifikace nezahrnují publikování roku, protože odkazy vedou k dokumentům, což se často aktualizují na nejnovější verzi. Odkazy na další dokumenty, které zahrnují publikování rok, kdy je k dispozici.

### <a name="121-normative-references"></a>1.2.1 normativní odkazy 

>  [MS-SSTR] Smooth Streaming protokol *v20140502* [https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)

>   [ISO/IEC 14496 12] Mezinárodní organizace pro normalizaci, "informačních technologií – kódování audiovizuální objektů – část 12: ISO základní formát mediálního souboru", ISO/IEC 14496-12:2014, verze 4 a oprava 1, změny 1 a 2.
>   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>

>   [ISO/IEC 14496-15] Mezinárodní organizace pro normalizaci, "informačních technologií – kódování audiovizuální objektů – část 15: Návrat na začátek řádku NAL jednotky strukturované videa ve formátu souboru ISO Base Media", ISO 14496-15:2015 Edition 3.
>   <http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>

>   [ISO/IEC 23008 2] Informace o technologii – – vysoké účinnosti a kódování médií doručování v heterogenních prostředích – část 2: Vysoké účinnosti videa kódování: 2013 nebo nejnovější vydání   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>

>   [ISO/IEC 23001-7] Informace o technologii – technologie systémy MPEG – část 7: V ISO média základní formát souborů, 2:2015 CENC edice Standard Common encryption <http://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>

>   [RFC 6381] Sdružení IETF RFC 6381, "" Kodeky"a 'Profily' parametry"kontejneru"typy médií" <http://tools.ietf.org/html/rfc6381>

>   [MPEG4 RA] Registrační autorita MP4 "MP4REG" [http://www.mp4ra.org   ](https://go.microsoft.com/fwlink/?LinkId=327787)

>   [RFC2119] Bradner S., "klíčových slov pro použití v dokumentech RFC do úrovně požadavků určete", BCP 14, RFC 2119, březen 1997   [http://www.rfc-editor.org/rfc/rfc2119.txt   ](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 informativních odkazů 

>   [MS-GLOS] Microsoft Corporation "*Windows protokoly hlavní glosář*."

>   [RFC3548] Josefsson S., vydání "Base16 Base32 a kódování Base64 Data", RFC 3548, červenec 2003 [http://www.ietf.org/rfc/rfc3548.txt   ](https://go.microsoft.com/fwlink/?LinkId=90432)

>   [RFC5234] Crocker, d, vydání a Overell P., "Rozšířená BNF specifikace syntaxe: ABNF", STD 68, RFC 5234, January 2008,   [http://www.rfc-editor.org/rfc/rfc5234.txt   ](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 přehled 

>   Níže jsou uvedené pouze změny potřebné pro dodání HEVC specifikaci technologie Smooth Streaming. Hlavičky beze změny části patří k údržbě umístění v odkazované technologie Smooth Streaming specifikace [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1.4 vztah k ostatním protokolům 

## <a name="15-prerequisitespreconditions"></a>1.5 požadavky a předpoklady 

## <a name="16-applicability-statement"></a>1.6 příkaz použitelnosti 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 Správa verzí a schopností vyjednávání 

## <a name="18-vendor-extensible-fields"></a>1.8 dodavatele – Extensible pole 

>   Následující metoda se používá identifikovat datové proudy použití formátu videa HEVC:

>   * **Vlastní popisný kódy pro formáty multimédií:** Tato funkce poskytuje **FourCC** pole, jak je uvedeno v části *2.2.2.5*.
>   Implementátoři můžete zajistit, že rozšíření nejsou v konfliktu pomocí registrace rozšíření kódy MPEG4-RA, jak je uvedeno v [[ISO/IEC-14496-12] ](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 přiřazení standardy 

## <a name="2-messages"></a>2 zprávy 

## <a name="21-transport"></a>2.1 přenos

## <a name="22-message-syntax"></a>2.2 syntaxe zpráv 

### <a name="221-manifest-request"></a>2.2.1 žádost o manifestu 

### <a name="222-manifest-response"></a>2.2.2 manifestu odpovědi 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **Podverze (proměnnou):** Dílčí verze manifestu odpověď. MUSÍ být nastavena na 2. (Žádné změny)

>   **Časová osa (proměnnou):** Časové měřítko atributu doby trvání, zadaný jako počet kroků v jedné sekundy. Výchozí hodnota je
>   10000000. (Žádné změny)

>   Doporučená hodnota je 90000 představující přesná doba trvání snímky videí a fragmenty obsahující desetinná snímkovou video (například 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

ProtectionElement musí být k dispozici při použití Common Encryption (CENC) videa nebo zvukový stream. HEVC šifrované datové proudy musí odpovídat používat standard Common Encryption 2. verze [ISO/IEC 23001-7]. Pouze datový řez v VCL NAL jednotky se zašifrovat.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (proměnnou):** Časové měřítko pro dobu trvání a času hodnoty v tomto datovém proudu zadané jako počet kroků v jedné sekundy. Hodnota 90000 se doporučuje pro datové proudy HEVC. Hodnotu, která odpovídá vzorku frekvence zvukového průběhu (například 48000 nebo 44100) se doporučuje pro zvukové datové proudy.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (proměnnou):** Čtyřmístný kód, který identifikuje formát médium se používá pro každý vzorek. Následující rozsah hodnot je vyhrazena pro následující sémantický význam:

>  * "hev1": Ukázky videa pro toto sledování použití HEVC videa pomocí "hev1" Ukázkový popis formátu určeného v [ISO/IEC-14496-15].

>   **CodecPrivateData (proměnnou):** Data, která určuje parametry specifické pro formát média a společné pro všechny ukázky v sledovat, reprezentovaný jako řetězec hex pevně zakódované bajtů. Formát a sémantický význam sekvence bajtů se liší podle hodnoty **FourCC** pole následujícím způsobem:

>   * Když TrackElement popisuje HEVC video, **FourCC** pole musí být roven **"hev1"** a;

>   **CodecPrivateData** pole musí obsahovat hex pevně zakódované řetězcové reprezentace následující sekvence bajtů podle ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (žádná změna MS SSTR)

>   * %x 00 %x 00 %x 00 %x 01 SPSField %x 00 %x 00 %x 00 %x 01 PPSField

>   * SPSField obsahuje v pořadí parametr nastavte (SPS).

>   * PPSField obsahuje řez parametr nastavte (PPS).

>   Poznámka: Video parametr nastavte (náměstci) není obsažena v CodecPrivateData, ale by měly být obsaženy v záhlaví souboru uložených souborů v poli "hvcC". Systémy s využitím technologie Smooth Streaming protokol musí signalizuje, že další dekódování parametry (například HEVC vrstva) používá vlastní atribut "kodeky."

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   **MajorVersion pro SmoothStreamingMedia** pole musí být nastavené na 2, a **MinorVersion** pole musí být nastavené na 2. (Žádné změny)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 fragment žádosti 

>   **Poznámka:** Výchozí formát média požadovaným pro **MinorVersion** 2 a "hev1" je značka "iso8" ISO Base média zadaný formát souboru v [ISO/IEC 14496 12] ISO Base média souboru formátu čtvrtým vydáním a běžné šifrování druhého [ISO/IEC 23001-7] Edice.

### <a name="224-fragment-response"></a>2.2.4 fragment odpovědi 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox** je zastaralá a jeho funkci nahrazuje sledování Fragment dekódování poli (tfdt) zadané v části [ISO/IEC 14496 12] 8.8.12.

>   **Poznámka:** Klient může vypočítat trvání fragment jako součet dob trvání vzorku, uvedenou v poli Spustit sledování (trun) nebo součinu počet vzorků, které vyprší výchozí doba trvání vzorku. BaseMediaDecodeTime "tfdt" plus fragment doby rovná čas parametr adresy URL pro další fragment.

>   Výrobce odkaz čas pole (prft) být vložena před (moof) film Fragment zadejte podle potřeby, označuje čas UTC odpovídající čas dekódování sledování Fragment první vzorku odkazuje Fragment zadejte film, jako zadané v [ISO/IEC 14496 -12] části 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** je zastaralá a jeho funkci nahrazuje sledování Fragment dekódování poli (tfdt) zadané v části [ISO/IEC 14496 12] 8.8.12.

>   **Poznámka:** Klient může vypočítat trvání fragment jako součet dob trvání vzorku, uvedenou v poli Spustit sledování (trun) nebo součinu počet vzorků, které vyprší výchozí doba trvání vzorku. BaseMediaDecodeTime "tfdt" plus fragment doby rovná čas parametr adresy URL pro další fragment. Dívejte se, že adresy jsou zastaralé, protože jejich zpoždění živého streamování.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** a související pole zapouzdření výchozí hodnoty pro každý vzorek metadata ve fragmentu. Syntaxe **TfhdBox** pole je striktní podmnožinou syntaxe pole záhlaví Fragment sledování definované v [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) části 8.8.7.

>   **BaseDataOffset (8 bajtů):** Posun v bajtech od začátku **MdatBox** pole na pole v **MdatBox** pole. Který signalizuje, že toto omezení, musí být nastaven příznak výchozí base – je moof (0x020000).

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** a související pole zapouzdřit každý vzorek metadat pro požadovaný fragment. Syntaxe **TrunBox** jsou striktní podmnožinou verze 1 sledování Fragment spustit pole definované v [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* části 8.8.8.

>   **SampleCompositionTimeOffset (4 bajty):** Ukázka složení časovým posunem každého vzorku upravit tak, aby prezentace čas první uvedený příklad ve fragmentu je roven času dekódování první dekódovaný vzorku. MUSÍ být použit složení posunutí záporné ukázkové video

>   jak jsou definovány v [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)

>   Poznámka: To zabraňuje videa synchronizace chyby způsobené videa, zvuku se rovná největší zpoždění dekódovaný obrázek vyrovnávací paměti odstranění vzhledem a udržuje prezentace časování mezi alternativní fragmenty, které můžou mít různé odebrání zpoždění.

>   Syntaxe polí definovaných v této části podle ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) zůstává stejné, s výjimkou následujícím způsobem:

>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 fragment společných polí odpovědi 

### <a name="225-sparse-stream-pointer"></a>2.2.5 ukazatel na sadu zhuštěných Stream 

### <a name="226-fragment-not-yet-available"></a>2.2.6 fragment ještě není k dispozici 

### <a name="227-live-ingest"></a>2.2.7 živé Ingestování 

#### <a name="2271-filetype"></a>2.2.7.1 FileType 

>   **Typ souboru (proměnnou):** určuje podtyp a zamýšlené použití MPEG-4 ([[MPEG4 RA])](https://go.microsoft.com/fwlink/?LinkId=327787) souboru a základní atributy.

>   **MajorBrand (proměnnou):** Hlavní značky do souboru média. MUSÍ být nastavena na "isml."

>   **Podverze (proměnnou):** Podverze mediální soubor. MUSÍ být nastavena na hodnotu 1.

>   **CompatibleBrands (proměnnou):** Určuje podporované značky MPEG-4.
>   MUSÍ zahrnovat "ccff" a "iso8."

>   Syntaxe polí definovaných v této části podle ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) vypadá takto:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Poznámka:** Kompatibility značek "ccff" a "iso8" označují, že fragmenty odpovídají "Běžný formát souborů kontejneru" a používat standard Common Encryption [ISO/IEC 23001-7] a ISO Base média souboru formátu vydání 4 [ISO/IEC 14496 12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 sledování Fragment rozšířené hlavičky 

### <a name="228-server-to-server-ingest"></a>2.2.8 Ingestování server-to-Server 

## <a name="3-protocol-details"></a>3 podrobnosti protokolu 


## <a name="31-client-details"></a>3.1 podrobnosti o klienta 

### <a name="311-abstract-data-model"></a>3.1.1 abstraktní datový Model 

#### <a name="3111-presentation-description"></a>3.1.1.1 popis prezentace 

>   Zapouzdřuje datový element prezentace popis všechna metadata pro prezentaci.

>   Prezentace metadat: Sada metadata, která jsou společná pro všechny datové proudy v prezentaci. Prezentace metadat obsahuje následující pole jsou uvedeny v části *2.2.2.1*:

>   * **MajorVersion**
>   * **MinorVersion**
>   * **TimeScale**
>   * **Doba trvání**
>   * **IsLive**
>   * **LookaheadCount**
>   * **DVRWindowLength**

>   Prezentace obsahující HEVC streamů se nastavit:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Poznámka: Pole zastaralé)

>   Nastavte také prezentace:

    TimeScale = 90000

>   Stream kolekce: Kolekce Stream popis datové prvky, jako v zadaném oddílu *3.1.1.1.2*.

>   Popis ochrany: Kolekce Popis ochrany systému metadat datové prvky, jako v zadaném oddílu *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Popis ochrany systému metadat 

>   Popis ochrany systému metadat datový element zapouzdřuje metadata specifická pro jeden systém ochrany obsahu. (Žádné změny)

>   Popis záhlaví ochrany: Ochrana obsahu metadata, která se vztahuje na jednom systému ochrany obsahu. Popis záhlaví ochrany zahrnuje následující pole jsou uvedeny v části *2.2.2.2*:

>   * **ID systému**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 popis Stream 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Popis sledování 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 popis vlastního atributu 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 popis odkazu fragmentu 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 popis odkazu sledování konkrétní fragmentu 

#### <a name="3112-fragment-description"></a>3.1.1.2 popis fragmentu 

##### <a name="31121-sample-description"></a>3.1.1.2.1 popis ukázky 

### <a name="312-timers"></a>3.1.2 časovače 

### <a name="313-initialization"></a>3.1.3 inicializace 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 vyšší vrstvy aktivované události 

#### <a name="3141-open-presentation"></a>3.1.4.1 otevřete prezentaci 

#### <a name="3142-get-fragment"></a>3.1.4.2 získat fragmentu 

#### <a name="3143-close-presentation"></a>3.1.4.3 zavřete prezentace 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 zpracovává události a pravidel klasifikace 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 manifest žádost a odpověď manifestu 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 fragment žádost a odpověď Fragment

## <a name="32-server-details"></a>3.2 podrobnosti o serveru

## <a name="33-live-encoder-details"></a>3.3 live Encoder podrobnosti 

## <a name="4-protocol-examples"></a>Příklady 4 protokolu 

## <a name="5-security"></a>5 zabezpečení 

## <a name="51-security-considerations-for-implementers"></a>5.1 důležité informace o zabezpečení pro implementátory

>   Pokud má vysoký obchodní hodnoty obsah přenosu pomocí tohoto protokolu, abyste zabránili neoprávněnému používání obsahu by měla sloužit systém ochrany obsahu. **ProtectionElement** je možné provádět metadata vztahující se k využívání obsahu ochrany systému. Chráněné zvuk a video obsahu jsou šifrované podle MPEG Common Encryption druhé vydání: 2015 [ISO/IEC 23001-7].

>   **Poznámka:** Pouze datový řez v VCL NALs HEVC videa, se šifrují. Záhlaví řezu a ostatní NALs jsou přístupné prezentace aplikací před jejich dešifrování. zabezpečené video cesty není k dispozici pro aplikace pro prezentaci šifrované informace.

## <a name="52-index-of-security-parameters"></a>5.2 Index parametrů zabezpečení 


| **Parametr zabezpečení**  | **Section**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Běžné šifrování polí | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 common Encryption polí

Následující pole mohou být přítomny v odpovědi fragment při použití používat standard Common Encryption a jsou určené v [ISO/IEC 23001-7] nebo [ISO/IEC 14496 12]:

1.  Ochrana systému konkrétní záhlaví pole (pssh)

2.  Ukázka šifrování pole (senc)

3.  Ukázka pomocné informace o posun pole (saio)

4.  Ukázka pomocné informace o velikosti pole (saiz)

5.  Okno ukázkové skupiny popis (sgpd)

6.  Ukázka skupinovém rámečku (sbgp)

-----------------------

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
