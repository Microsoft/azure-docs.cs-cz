---
title: "Azure Media Services – funkce Smooth Streaming Ammendment Protocol (MS-SSTR) pro HEVC | Microsoft Docs"
description: "Tato specifikace popisuje protokol a formát pro fragmentovaných na základě MP4 živé streamování s HEVC ve službě Azure Media Services. Jedná se ammendment documentaiton technologie Smooth Streaming protocol (MS-SSTR) k zahrnují podporu pro HEVC ingestování a vysílání datového proudu. Pouze změny, které jsou potřeba k poskytování HEVC jsou určené v tomto článku, s výjimkou byly \"(žádná změna)\" označuje text je zkopírován pouze informace."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: johndeu; cenkd
ms.openlocfilehash: 5a8cdf4187c1b751e0c61e5c750646c5819b5c2b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="smooth-streaming-protocol-ms-sstr-ammendment-for-hevc"></a>Technologie Smooth Streaming Ammendment Protocol (MS-SSTR) pro HEVC

## <a name="1-introduction"></a>1 Úvod 

Tento článek obsahuje podrobné změny má být použita pro protokol Smooth Streaming specifikace [MS-SSTR] k povolení technologie Smooth Streaming z HEVC kódovaný video. V téhle specifikaci jsme popisují pouze změny, které jsou potřeba k poskytování HEVC kodek videa. Článek dodržuje stejné číslování schéma jako specifikace [MS-SSTR]. Prázdný titulky uvedené v článku jsou uvedené pro orientaci čtečky do polohy ve specifikaci [MS-SSTR].  "(Žádná změna)" označuje, že je text zkopírován vysvětlení pouze pro účely.

Článek obsahuje technickou implementaci požadavky pro signalizace HEVC kodek videa v manifestu technologie Smooth Streaming a normativní odkazy jsou aktualizovány tak, aby odkazovaly aktuální MPEG standardů, které zahrnují HEVC, běžné šifrování HEVC a pole názvy pro formát souboru média základní ISO byly aktualizovány, aby byl konzistentní s nejnovější specifikace. 

Odkazovaná funkce Smooth Streaming protokol specifikace [MS-SSTR] popisuje přenosový formát použitý pro doručování živě i na vyžádání digitálního média, jako je například audio a video, z následujících způsobů: z ze serveru na jiný server a z kodéru na webový server, Server i pro HTTP klienta.
Použití MPEG-4 ([[MPEG4-RA])](http://go.microsoft.com/fwlink/?LinkId=327787)-doručení struktura dat s použitím prostřednictvím protokolu HTTP umožňuje bezproblémové přepínání skoro v reálném čase mezi různé úrovně kvality komprimované mediálního obsahu. Výsledkem je konstantní přehrávání prostředí pro koncového uživatele klienta HTTP, i když síť a vykreslování videa podmínky změnit pro klientský počítač nebo zařízení.

## <a name="11-glossary"></a>1.1 Glosář 

Následující termíny jsou definovány v *[MS-GLOS]*:

>   **identifikátor UUID globálně jedinečný identifikátor (GUID) (UUID)**

Následující termíny jsou specifické pro tento dokument:

>  **čas sestavení:** čas ukázku je uvedena na klientovi, jak jsou definovány v [[ISO/IEC-14496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

>   **Šifrování CENC**: Common Encryption, jak jsou definovány v druhé vydání [ISO/IEC 23001-7].

>   **Čas dekódování:** čas ukázku je potřeba dekódovat na straně klienta podle definice v [[ISO/IEChttp://go.microsoft.com/fwlink/?LinkId=18369514496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

**fragment:** nezávisle ke stažení jednotka **média** , obsahuje jeden nebo více **ukázky**.

>   **HEVC:** vysoké efektivitu Video kódování, jak je definována v [ISO/IEC 23008-2]

>   **manifest:** Metadata o **prezentace** , umožňuje klientům provádět požadavky **média**. **média:** komprimovaných zvuk, video a text dat používaných klientem přehrávání **prezentace**. **Formát média:** dobře definovaný formát pro představující zvuku a videa jako komprimovaný **ukázka**.

>   **prezentace:** sada všech **datové proudy** a související metadata potřebná jeden video. **požadavek:** HTTP zpráv odeslaných z klienta na server, jak jsou definovány v [[RFC2616].](http://go.microsoft.com/fwlink/?LinkId=90372) **odpověď:** HTTP zpráv odeslaných ze serveru do klienta, jak jsou definovány v [[RFC2616].](http://go.microsoft.com/fwlink/?LinkId=90372)

>   **Ukázka:** nejmenší základní jednotky (například rámečkem), ve kterém **média** uložena a zpracována.

>   **PRAVDĚPODOBNĚ, SHOULD, musí, BYSTE neměli, nesmí:** se používají tyto termíny (v velká písmena), jak je popsáno v [[RFC2119].](http://go.microsoft.com/fwlink/?LinkId=90317) Všechny příkazy použití volitelné chování buď může SHOULD nebo by neměl.

## <a name="12-references"></a>1.2 odkazy 
-----------

>   Odkazy na dokumentaci k Microsoft Open specifikace nezahrnují publikování roku, protože jsou odkazy na nejnovější verzi dokumentů, které jsou často aktualizuje. Odkazy na další dokumenty zahrnují publikování roku, pokud není k dispozici.

 ### <a name="121-normative-references"></a>1.2.1 normativní odkazy 

>  [MS-SSTR] Smooth Streaming Protocol *v20140502* [http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/[MS-SSTR].pdf](http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

>   [ISO/IEC 14496-12] Mezinárodní organizace pro normalizaci, "informačních technologií--kódování audiovizuální objektů – část 12: formát souboru média základní ISO", ISO/IEC 14496-12:2014, verze 4 a oprava 1, změny 1 a 2.
>   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>

>   [ISO/IEC 14496 15] Mezinárodní organizace pro normalizaci, "informačních technologií--kódování audiovizuální objektů – část 15: znaků CR NAL jednotky strukturovaná videa ve formátu souboru ISO základní Media", ISO 14496-15:2015, edice 3.
>   <http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>

>   [ISO/IEC 23008-2] Informace o technologii –--vysoce účinné kódování a média doručení v heterogenních prostředích – část 2: vysoce účinné video kódování: 2013 nebo nejnovější verzi   <http://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>

>   [ISO/IEC 23001-7] Informačních technologií – technologie systémů MPEG – část 7: běžné šifrování v ISO základní souborů ve formátu souboru média, 2:2015 CENC Edition <http://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>

>   [RFC-6381] Sdružení IETF RFC-6381, "'Kodeky' a 'Profily' parametry"sady"typy médií" <http://tools.ietf.org/html/rfc6381>

>   [MPEG4-RA] Registrační autorita MP4, "MP4REG" [http://www.mp4ra.org   ](http://go.microsoft.com/fwlink/?LinkId=327787)

>   [RFC2119] Bradner, S., "Key words for use in RFCs to Indicate Requirement   Levels", BCP 14, RFC 2119, March 1997,   [http://www.rfc-editor.org/rfc/rfc2119.txt   ](http://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 informativní odkazy 

>   [MS-GLOS] Microsoft Corporation "*Windows protokoly hlavní glosář*."

>   [RFC3548] Josefsson, S., Ed., "The Base16, Base32, and Base64 Data   Encodings", RFC 3548, July 2003, [http://www.ietf.org/rfc/rfc3548.txt   ](http://go.microsoft.com/fwlink/?LinkId=90432)

>   [RFC5234] Crocker, D., vydání a Overell, P., "rozšířen BNF pro syntaxe specifikace: ABNF", – STD 68, RFC 5234, leden 2008 [http://www.rfc-editor.org/rfc/rfc5234.txt   ](http://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 – přehled 
---------

>   Pouze změny specifikace technologie Smooth Streaming požadované pro doručování HEVC jsou uvedeny níže. Hlavičky beze změny části jsou uvedeny Udržovat umístění v odkazované technologie Smooth Streaming specifikaci [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1.4 vztah k ostatním protokolům 
--------------------------------

## <a name="15-prerequisitespreconditions"></a>1,5 požadavky nebo předběžných podmínek 
----------------------------

## <a name="16-applicability-statement"></a>1.6 příkaz použitelnosti 
------------------------

## <a name="17-versioning-and-capability-negotiation"></a>1.7 Správa verzí a schopností vyjednávání 
--------------------------------------

## <a name="18-vendor-extensible-fields"></a>1.8 dodavatele Extensible pole 
-------------------------

>   Následující metoda se používá identifikovat datové proudy video formátu HEVC:

>   * **Vlastní popisný kódy pro média formáty:** tato funkce poskytuje **FourCC** pole, jak je uvedeno v části *2.2.2.5*.
>   Implementátory můžete zajistit, že rozšíření nejsou v konfliktu pomocí registrace rozšíření kódy MPEG4-RA, jak je uvedeno v [[ISO/IEC-14496-12] ](http://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 standardy přiřazení 
----------------------

# <a name="2-messages"></a>2 zprávy 

## <a name="21-transport"></a>2.1 přenosu 
----------

## <a name="22-message-syntax"></a>2.2 message Syntax 
---------------

### <a name="221-manifest-request"></a>2.2.1 manifestu požadavku 

### <a name="222-manifest-response"></a>2.2.2 manifestu odpovědi 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (proměnná):** podverze zprávy s odpovědí Manifest. MUSÍ být nastaven na hodnotu 2. (Žádná změna)

>   **Časová osa (proměnná):** ose atributu doba trvání, zadaný jako počet kroků za jednu sekundu. Výchozí hodnota je
>   10000000. (Žádná změna)

>   Doporučená hodnota je 90000 pro představující přesný trvání snímky videa a fragmenty obsahující video zlomkové kmitočet snímků (například 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

ProtectionElement musí být k dispozici při běžné šifrování (CENC) byla použita v video nebo zvuk datových proudů. HEVC šifrované datové proudy musí odpovídat běžným šifrováním 2. verze [ISO/IEC 23001-7]. Pouze řez data v VCL NAL jednotky musí být šifrována.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (proměnná):** ose pro dobu trvání a čas hodnoty v tento datový proud, zadané jako počet kroků za jednu sekundu. Hodnota 90000 se doporučuje pro datové proudy HEVC. Hodnotu odpovídající vzorku frekvence zvukového průběhu (například 48000 nebo 44100) se doporučuje pro zvukové datové proudy.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (proměnná):** čtyřmístný kód, který identifikuje formát médium se používá pro jednotlivé vzorky. Následující rozsah hodnot je vyhrazena následující významy sémantického:

>  * "hev1": Video ukázky pro toto sledování použít HEVC video, ve formátu 'hev1' ukázka popis zadaný v [ISO/IEC-14496-15].

>   **CodecPrivateData (proměnná):** Data, která určuje parametry specifické pro formát média a společné pro všechny ukázky v sledovat, vyjádřené řetězec programového šestnáctkově bajtů. Formát a význam sémantického pořadí bajtů se liší s hodnotou **FourCC** pole následujícím způsobem:

>   * Když TrackElement popisuje HEVC video, **FourCC** rovná pole **"hev1"** a;

>   **CodecPrivateData** pole musí obsahovat programového šestnáctkově řetězcovou reprezentaci následující pořadí bajtů, zadaný v ABNF [[RFC5234]:](http://go.microsoft.com/fwlink/?LinkId=123096) (žádná změna MS SSTR)

>   * %x 00 %x 00 %x 00 %x 01 SPSField %x 00 %x 00 %x 00 %x 01 PPSField

>   * SPSField obsahuje pořadí parametr nastavit (SP).

>   * PPSField obsahuje řez parametr nastavit (kód PPS).

>   Poznámka: Video parametr nastavit (náměstci) není obsažen v CodecPrivateData, ale by měly být obsaženy v hlavičce souboru uložených souborů v poli 'hvcC'. Systémy Protokol Smooth Streaming musí signál další dekódování parametry (například HEVC vrstva) pomocí atributu vlastní "kodeky."

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   **Na SmoothStreamingMedia MajorVersion** pole musí být nastaven na hodnotu 2, a **MinorVersion** pole musí být nastaven na hodnotu 2. (Žádná změna)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 fragment požadavku 

>   **Poznámka:**: výchozí formát média požadované pro **MinorVersion** 2 a 'hev1' je 'iso8' brand ISO základní média formát souboru zadanou v [ISO/IEC 14496-12] ISO základní média soubor formátu čtvrté vydání a [ISO/IEC 23001-7] Běžné šifrování druhé vydání.

### <a name="224-fragment-response"></a>2.2.4 fragment odpovědi 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox** je zastaralý a jeho funkce nahrazuje sledovat Fragment dekódovat čas pole (tfdt) v oddílu [ISO/IEC 14496-12] 8.8.12 zadat.

>   **Poznámka:**: klient může vypočítat trvání fragment jako součet doby ukázka uvedenou v poli Spustit sledování (trun) nebo vynásobením počet vzorků, které krát výchozí doba vzorku. BaseMediaDecodeTime v době trvání, tfdt, plus fragment rovná parametru času adresu URL pro další fragment.

>   Proto, že odkaz čas pole (prft) je třeba vložit před film Fragment pole (moof) podle potřeby, označit čas UTC odpovídající sledovat Fragment dekódovat čas první ukázka odkazuje pole Fragment film jako v [standardem ISO/IEC 14496 zadat -12] části 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** je zastaralý a jeho funkce nahrazuje sledovat Fragment dekódovat čas pole (tfdt) v oddílu [ISO/IEC 14496-12] 8.8.12 zadat.

>   **Poznámka:**: klient může vypočítat trvání fragment jako součet doby ukázka uvedenou v poli Spustit sledování (trun) nebo vynásobením počet vzorků, které krát výchozí doba vzorku. BaseMediaDecodeTime v době trvání, tfdt, plus fragment rovná parametru času adresu URL pro další fragment. Vzhled napřed adresy jsou zastaralé, protože jejich zpoždění živé vysílání datového proudu.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** a související pole zapouzdření výchozí hodnoty pro jednotlivé ukázka metadata ve fragmentu. Syntaxe **TfhdBox** pole je podmnožinou syntaxe pole hlavičky Fragment sledovat definované v striktní [[ISO/IEC-14496-12]](http://go.microsoft.com/fwlink/?LinkId=183695) části 8.8.7.

>   **BaseDataOffset (8 bajtů):** posun v bajtech, od začátku **MdatBox** pole k poli Ukázka v **MdatBox** pole. Signál toto omezení, musí být nastaven příznak výchozí – základní – je – moof (0x020000).

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** a související pole pro zapouzdření za ukázka metadata pro požadovaný fragment. Syntaxe **TrunBox** striktní podmnožinu verze 1 sledovat Fragment pole spustit definované v [[ISO/IEC-14496-](http://go.microsoft.com/fwlink/?LinkId=183695)*12]* části 8.8.8.

>   **SampleCompositionTimeOffset (4 bajty):** posun ukázkové složení času každého vzorku nastaví tak, aby prezentace čas první vidění vzorku ve fragmentu rovná dekódování čas první dekódované vzorku. Posuny složení záporné ukázkové video má být použit,

>   jak jsou definovány v [[ISO/IEC-14496-12].](http://go.microsoft.com/fwlink/?LinkId=183695)

>   Poznámka: To zabraňuje video chybě způsobené video, zvuk rovno největší zpoždění odebrání dekódované obrázek vyrovnávací paměti vzhledem a udržuje časování prezentace mezi alternativní fragmenty, které mohou mít různé odebrání zpoždění.

>   Syntaxe pro pole definovaná v této části, zadaný v ABNF [[RFC5234]](http://go.microsoft.com/fwlink/?LinkId=123096) zůstane stejný, s výjimkou následujícím způsobem:

>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 fragmentovat běžné pole odpovědi 

### <a name="225-sparse-stream-pointer"></a>2.2.5 ukazatel zhuštěný datový proud 

### <a name="226-fragment-not-yet-available"></a>2.2.6 fragmentovat ještě nebyla k dispozici 

### <a name="227-live-ingest"></a>2.2.7 Ingestování za provozu 

#### <a name="2271-filetype"></a>2.2.7.1 typ souboru 

>   **Typ souboru (proměnná):** Určuje dílčí a zamýšleného použití MPEG-4 ([[MPEG4-RA])](http://go.microsoft.com/fwlink/?LinkId=327787) souboru a atributy vysoké úrovně.

>   **MajorBrand (proměnná):** hlavní brand mediálního souboru. MUSÍ být nastavena na "isml."

>   **MinorVersion (proměnná):** podverze systému souboru média. MUSÍ být nastavena na hodnotu 1.

>   **CompatibleBrands (proměnná):** určuje podporovaná značky MPEG-4.
>   MUSÍ zahrnovat "ccff" a "iso8."

>   Syntaxe pro pole definovaná v této části, zadaný v ABNF [[RFC5234]](http://go.microsoft.com/fwlink/?LinkId=123096) vypadá takto:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Poznámka:**: kompatibility značky 'ccff' a 'iso8' znamenat, že fragmenty odpovídá "Běžný formát souboru kontejneru" a Common Encryption [ISO/IEC 23001-7] a ISO základní média soubor formátu edice 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 Fragment sledovat rozšířené záhlaví 

### <a name="228-server-to-server-ingest"></a>2.2.8 Ingestování server-Server 

# <a name="3-protocol-details"></a>Podrobnosti o 3 protokolu 


## <a name="31-client-details"></a>3.1 podrobnosti o klienta 

### <a name="311-abstract-data-model"></a>3.1.1 abstraktní datový Model 

#### <a name="3111-presentation-description"></a>3.1.1.1 popis prezentace 

>   Popis prezentace datový prvek zapouzdřuje všechny metadata pro prezentaci.

>   Prezentace Metadata: Sada metadata, která jsou společná pro všechny datové proudy v prezentaci. Metadata prezentace se skládá z následujících polí, uvedený v oddílu *2.2.2.1*:

>   * **MajorVersion**
>   * **MinorVersion**
>   * **TimeScale**
>   * **Doba trvání**
>   * **IsLive**
>   * **LookaheadCount**
>   * **DVRWindowLength**

>   Prezentace obsahující HEVC datové proudy musí nastavit:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Poznámka: polí zastaralé)

>   Nastavte také prezentací:

    TimeScale = 90000

>   Kolekce datového proudu: Kolekci elementů popis datový proud dat, podle bodu *3.1.1.1.2*.

>   Popis Protection: Kolekci elementů popis metadat systému ochrany dat, podle bodu *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 popis Metadata ochrany systému 

>   Popis metadat systému ochrany datový prvek zapouzdří metadata specifická pro jeden systém ochrany obsahu. (Žádná změna)

>   Popis záhlaví Protection: Ochrana obsahu metadata, která se vztahují na jednom systému ochrany obsahu. Popis záhlaví ochrany zahrnuje následující pole, uvedený v oddílu *2.2.2.2*:

>   * **ID systému**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 popis datového proudu 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Popis sledování 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Popis vlastních atributů 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 popis odkazu fragment 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 popis odkazu Fragment specifické sledování 

#### <a name="3112-fragment-description"></a>3.1.1.2 popis fragment 

##### <a name="31121-sample-description"></a>3.1.1.2.1 popis ukázky 

### <a name="312-timers"></a>3.1.2 časovače 

### <a name="313-initialization"></a>3.1.3 inicializace 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 vyšší úrovně aktivované události 

#### <a name="3141-open-presentation"></a>3.1.4.1 otevřete prezentace 

#### <a name="3142-get-fragment"></a>3.1.4.2 získat Fragment 

#### <a name="3143-close-presentation"></a>3.1.4.3 zavřete prezentace 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 zpracovává události a pořadí úloh pravidla 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 manifest požadavku a odpovědi Manifest 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 fragmentovat požadavku a odpovědi fragmentovat

## <a name="32-server-details"></a>3.2 podrobnosti o serveru

## <a name="33-live-encoder-details"></a>3.3 podrobnosti o provozu kodér 

# <a name="4-protocol-examples"></a>Příklady 4 protokolu 

# <a name="5-security"></a>5 zabezpečení 

## <a name="51-security-considerations-for-implementers"></a>5.1 aspekty zabezpečení pro implementátory 
-----------------------------------------

>   Pokud obsah přenosu pomocí tento protokol má vysoký obchodní hodnoty, abyste zabránili neoprávněnému používání obsahu slouží systému ochrany obsahu. **ProtectionElement** slouží k provádění metadata týkající se použití systému ochrany obsahu. Chráněné zvuk a video obsahu se šifrovat uvedené MPEG běžné šifrování druhého edicí: 2015 [ISO/IEC 23001-7].

>   **Poznámka:**: pro HEVC video jenom řez data v VCL NALs zašifrovaná. Řez hlavičky a dalších NALs jsou přístupné prezentace aplikací před jejich dešifrování. zabezpečené video cesty šifrované informace nejsou k dispozici na prezentační aplikace.

# <a name="52-index-of-security-parameters"></a>5.2 Index parametrů zabezpečení 
-----------------------------


| **Parametr zabezpečení**  | **Část**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Běžné šifrování polí | *[ISO/IEC 23001-7]* |

# <a name="53-common-encryption-boxes"></a>5.3 běžné šifrování polí
-----------------------

Do následujících polí může nacházet ve fragmentu odpovědi při běžné šifrování se použije a jsou určené v [ISO/IEC 23001-7] nebo [ISO/IEC 14496-12]:

1.  Ochrana systému konkrétní záhlaví pole (pssh)

2.  Ukázka šifrování pole (senc)

3.  Ukázka pomocného informace posun pole (saio)

4.  Ukázka pomocného informace velikost pole (saiz)

5.  Ukázka skupiny popis pole (sgpd)

6.  Ukázka skupiny pole (sbgp)

-----------------------

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
