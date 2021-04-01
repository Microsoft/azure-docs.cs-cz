---
title: SSTR Protocol (MS-) – změna pro HEVC – Azure Smooth Streaming
description: Tato specifikace popisuje protokol a formát pro fragmentování živého streamování založeného na MP4 pomocí HEVC v Azure Media Services. V tomto článku jsou zadány pouze změny, které jsou požadovány pro doručení HEVC, s výjimkou "(beze změny)" značí, že je text zkopírován pouze pro objasnění.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: johndeu
ms.openlocfilehash: 6454bc863cb5fd628d581fff380c5ab61354f762
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87053050"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Změna protokolu SSTR (MS-) pro HEVC Smooth Streaming 

## <a name="1-introduction"></a>1 Úvod 

Tento článek poskytuje podrobné změny pro použití specifikace protokolu Smooth Streaming [MS-SSTR], aby bylo možné Smooth Streaming zakódovaném videu HEVC. V této specifikaci vytvoříme pouze změny, které jsou potřeba k dodávání kodeku HEVC video. Tento článek se shoduje se stejným schématem číslování jako specifikace [MS-SSTR]. K dispozici jsou prázdné nadpisy, které jsou uvedeny v celém článku, aby se čtenář orientovat na pozici ve specifikaci [MS-SSTR].  "(Žádná změna)" označuje, že text se kopíruje jenom pro účely objasnění.

Tento článek obsahuje požadavky na technickou implementaci pro signalizaci HEVCého kodeku (pomocí stop ve formátu hev1 nebo hvc1) v manifestu Smooth Streaming a normativní odkazy jsou aktualizované tak, aby odkazovaly na aktuální standardy MPEG, které zahrnují HEVC, Common Encryption HEVC a názvy polí pro formát základního mediálního souboru ISO byly aktualizovány tak, aby byly konzistentní s nejnovějšími specifikacemi. 

Odkazovaná Smooth Streaming protokolu [MS-SSTR] popisuje formát, který se používá k doručování a digitálního média na vyžádání, jako je například zvuk a video, v následujících způsobech: z kodéru na webový server, ze serveru na jiný server a ze serveru do klienta HTTP.
Použití nástroje pro doručování datových struktur MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787)prostřednictvím protokolu HTTP umožňuje bezproblémové přepínání téměř v reálném čase mezi různými úrovněmi kvality komprimovaného mediálního obsahu. Výsledkem je stálé prostředí přehrávání pro koncového uživatele klienta HTTP, a to i v případě, že se v klientském počítači nebo zařízení mění podmínky vykreslování sítě a videa.

## <a name="11-glossary"></a>Glosář 1,1 

V *[MS-Glos]* jsou definované následující výrazy:

>   **globálně jedinečný identifikátor (GUID) univerzálně jedinečný identifikátor (UUID)**

Následující výrazy jsou specifické pro tento dokument:

>  **čas kompozice:** Čas, kdy se ukázka zobrazí na klientovi, jak je definován v   [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695).
> 
>   **CENC**: Common Encryption, jak je definováno v [ISO/IEC 23001-7] Second Edition.
> 
>   **čas dekódování:** Čas, kdy je nutné dekódovat ukázku na klientovi, jak je definováno v   [[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695).

**fragment:** Nezávislá jednotka ke stažení **médií** , která zahrnuje jednu nebo více **vzorků**.

>   **HEVC:** Kódování videa s vysokým efektivitou, jak je definováno v [ISO/IEC 23008-2]
> 
>   **manifest:** Metadata o **prezentaci** , která klientovi umožňuje vytvářet požadavky na **média** **média:** Komprimovaná zvuk, video a textová data, která klient používá k přehrání **prezentace**. **formát média:** Dobře definovaný formát, který reprezentuje zvuk nebo video jako komprimovaný **vzorek**.
> 
>   **prezentace:** Sada všech **datových proudů** a související metadata potřebná k přehrání jediného filmu. **požadavek:** Zpráva HTTP odeslaná z klienta na server, jak je definováno v odpovědi [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) **Odpověď:** zpráva HTTP odeslaná ze serveru klientovi, jak je definováno v [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **Ukázka:** Nejmenší základní jednotka (například rámec), ve které se   **médium** ukládá a zpracovává.
> 
>   **květen** by neměl, nesmí: Tyto výrazy (ve všech verzálky) se používají tak, jak je popsáno v   [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) všechny příkazy volitelného chování, které by mohly být nebo by neměly.

## <a name="12-references"></a>odkazy 1,2

>   Odkazy na dokumentaci ke službě Microsoft Open Specification neobsahují rok publikování, protože jsou odkazy na nejnovější verzi dokumentů, které se často aktualizují. Odkazy na jiné dokumenty zahrnují rok publikování, když je jeden dostupný.

### <a name="121-normative-references"></a>1.2.1 normativní odkazy 

>  [MS-SSTR] *V20140502* protokolu Smooth Streaming [https://msdn.microsoft.com/library/ff469518.aspx](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)
> 
>   [ISO/IEC 14496-12] Mezinárodní organizace pro normalizaci, "informační technologie – kódování zvuku-vizuálních objektů – část 12: formát souboru ISO Base Media", ISO/IEC 14496-12:2014, edice 4, plus Corrigendum 1, změny 1 & 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] Mezinárodní organizace pro normalizaci, "informační technologie--kódování zvuku-vizuálních objektů – část 15: přeprava strukturovaného videa jednotky NAL ve formátu ISO Base Media File Format", ISO 14496-15:2015, edice 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Informační technologie – kódování s vysokými efektivitami a doručování médií v heterogenních prostředích – část 2: video s vysokým efektivitou: 2013 nebo nejnovější edice   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Informační technologie – systémové technologie MPEG – část 7: běžné šifrování ve formátu souborů ISO Base Media Format, CENC Edition 2:2015 <https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, "kodeky" a "profiles" typy médií "interval" <https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] Registrační autorita MP4 "MP4REG", [http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "klíčová slova pro použití v dokumentech RFC k označení úrovní požadavků", BCP 14, RFC 2119, březen 1997,   [https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 informativní odkazy 

>   [MS-GLOS] Microsoft Corporation, "*hlavní Glosář protokolů systému Windows*"
> 
>   [RFC3548] Josefsson, S., Ed., "Base16, Base32 a kódování dat Base64", RFC 3548, červenec 2003, [https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., Ed., a Overell, P., "Rozšířená BNF pro specifikace syntaxe: ABNF", STD 68, RFC 5234, leden 2008,   [https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>Přehled 1,3 

>   Níže jsou uvedeny pouze změny specifikace Smooth Streaming požadované pro doručení HEVC. Do odkazovaného Smooth Streaming specifikace [MS-SSTR] se v seznamu zachovají nezměněná záhlaví oddílů.

## <a name="14-relationship-to-other-protocols"></a>vztah 1,4 k ostatním protokolům 

## <a name="15-prerequisitespreconditions"></a>1,5 požadavky/předběžné podmínky 

## <a name="16-applicability-statement"></a>1,6 – příkaz použitelnosti 

## <a name="17-versioning-and-capability-negotiation"></a>1,7 Správa verzí a vyjednávání schopností 

## <a name="18-vendor-extensible-fields"></a>1,8 dodavatelé – rozšiřitelná pole 

>   K identifikaci datových proudů ve formátu videa HEVC je třeba použít následující metodu:
> 
>   * **Vlastní popisné kódy pro formáty médií:** Tato funkce je poskytována polem **FourCC** , jak je uvedeno v části *2.2.2.5*.
>   Implementátori můžou zajistit, aby rozšíření nedocházelo ke konfliktu registrací kódů rozšíření s MPEG4-RA, jak je uvedeno v [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) .

## <a name="19-standards-assignments"></a>1,9 přiřazení standardů 

## <a name="2-messages"></a>2 zprávy 

## <a name="21-transport"></a>2,1 přenos

## <a name="22-message-syntax"></a>Syntaxe zprávy 2,2 

### <a name="221-manifest-request"></a>2.2.1 – požadavek manifestu 

### <a name="222-manifest-response"></a>2.2.2 odpověď manifestu 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   Podverze **(proměnná):** Vedlejší verze zprávy s odpovědí manifestu. MUSÍ být nastavené na 2. (Beze změny)
> 
>   **Časová osa (proměnná):** Časové měřítko atributu trvání, které je zadáno jako počet přírůstků v jedné sekundě. Výchozí hodnotou je .
> 1. (Beze změny)
> 
>    Doporučená hodnota je 90000, aby představovala přesnou dobu trvání snímků videa a fragmentů obsahující video s zlomkovým snímkem (například 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

ProtectionElement se musí vyskytovat, když se použije Common Encryption (CENC) na datové proudy videa nebo zvuku. HEVC šifrované proudy musí splňovat Common Encryption Druhá edice [ISO/IEC 23001-7]. V jednotkách VCL NAL se šifrují jenom data řezu.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (proměnná):** Časové měřítko pro hodnoty doby trvání a času v tomto datovém proudu, které jsou zadány jako počet přírůstků v jedné sekundě. Pro datové proudy HEVC se doporučuje hodnota 90000. Pro zvukové proudy se doporučuje hodnota, která se shoduje s frekvencí vzorku signálu (například 48000 nebo 44100).

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (proměnná):** Kód se čtyřmi znaky, který určuje, který formát média se používá pro každou ukázku. Následující rozsah hodnot je vyhrazený s následujícími sémantickými významy:
> 
> * "hev1": ukázky videa pro tuto stopu používají HEVC video s použitím formátu "hev1" ukázkového popisu určeného v [ISO/IEC-14496-15].
>
> * "hvc1": ukázky videa pro tuto stopu používají HEVC video s použitím formátu "hvc1" ukázkového popisu určeného v [ISO/IEC-14496-15].
> 
>   **CodecPrivateData (proměnná):** Data, která určují parametry specifické pro formát média a společná pro všechny vzorky v rámci stop, reprezentované jako řetězec šestnáctkově kódovaných bajtů. Formát a sémantický význam sekvence bajtů se liší hodnotou pole **FourCC** následujícím způsobem:
> 
>   * Pokud TrackElement popisuje video HEVC, pole **FourCC** se rovná **"hev1"** nebo **"hvc1"** .
> 
>   Pole **CODECPRIVATEDATA** musí obsahovat šestnáctkovou řetězcovou reprezentaci následující posloupnosti bajtů určenou v ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (žádná změna z MS-SSTR).
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField obsahuje sadu parametrů Sequence (SPS).
> 
>   * PPSField obsahuje sadu parametrů řezu (PPS).
> 
>   Poznámka: sada parametrů videa (VPS) není obsažena v CodecPrivateData, ale měla by být obsažena v hlavičce souboru uložených souborů v poli ' hvcC '. Systémy používající Smooth Streaming protokol musí signalizovat další parametry dekódování (například HEVC) pomocí vlastního atributu "kodeky".

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   Pole **MajorVersion SMOOTHSTREAMINGMEDIA** musí být nastavené na hodnotu 2 **a pole s** podverzemi musí být nastavené na hodnotu 2. (Beze změny)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 – požadavek na fragment 

>   **Poznámka**: výchozí formát média **požadovaný pro** podverze 2 a ' hev1 ' nebo ' hvc1 ' je ' Iso8 ' se základní formát mediálního souboru ISO, který je určen v [ISO/IEC 14496-12] ISO Base Media Format File Format ČTVRTÉ edice a [ISO/IEC 23001-7] Common Encryption Second Edition.

### <a name="224-fragment-response"></a>2.2.4 odezva na fragment 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox** je zastaralá a její funkce je nahrazená časovým polem pro sledování fragmentu (tfdt), které je uvedené v části [ISO/IEC 14496-12] 8.8.12.
> 
>   **Poznámka**: klient může vypočítat dobu trvání fragmentu tak, že sečte dobu trvání vzorků uvedenou v poli sledovat běh (' Trun ') nebo vynásobí počet vzorků vynásobený výchozí dobu trvání vzorku. BaseMediaDecodeTime v ' tfdt ' plus doba trvání fragmentu odpovídá parametru času adresy URL pro další fragment.
> 
>   V případě potřeby by měl být do pole fragmentu videa (' prft ') vloženo pole s referenčním časem producenta (' '), které určuje čas UTC odpovídající času dekódování prvního vzorku, na který odkazuje pole fragment videa, jak je uvedeno v části [ISO/IEC 14496-12] 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** je zastaralá a její funkce je nahrazená časovým polem pro sledování fragmentu (tfdt), které je uvedené v části [ISO/IEC 14496-12] 8.8.12.
> 
>   **Poznámka**: klient může vypočítat dobu trvání fragmentu tak, že sečte dobu trvání vzorků uvedenou v poli sledovat běh (' Trun ') nebo vynásobí počet vzorků vynásobený výchozí dobu trvání vzorku. BaseMediaDecodeTime v ' tfdt ' plus doba trvání fragmentu odpovídá parametru času adresy URL pro další fragment. Adresy hledání jsou zastaralé, protože jsou zpožděné živé streamování.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** a související pole zapouzdřují výchozí hodnoty pro každou ukázková metadata v fragmentu. Syntaxe pole **TfhdBox** je striktní podmnožinou syntaxe pole sledovat fragment hlavičky definované v [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) oddíl 8.8.7.
> 
>   **BaseDataOffset (8 bajtů):** Posun v bajtech od začátku pole **MdatBox** k ukázkovému poli v poli **MdatBox** . K signalizaci tohoto omezení musí být nastavená příznak default-Base-is-Moof (0x020000).

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** a související pole jsou zapouzdřená na vzorová metadata pro požadovaný fragment. Syntaxe **TrunBox** je striktní podmnožinou pole pro sledování fragmentu verze 1 definovaného v [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* oddíl 8.8.8.
> 
>   **SampleCompositionTimeOffset (4 bajty):** Časový posun vzorku pro každý vzorek upravený tak, aby čas prezentace první prezentované ukázky v fragmentu byl stejný jako čas dekódování prvního dekódovanýho vzorku. Použijí se záporné posuny ukázek videa,
> 
>   Jak je definováno v   [[ISO/IEC – 14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   Poznámka: zabrání se tak Chyba synchronizace videa způsobené videem, která se rovná největšímu zpoždění při odstraňování vyrovnávací paměti obrázků, a udržuje časování prezentace mezi alternativními fragmenty, které mohou mít různou prodlevu při odebírání.
> 
>   Syntaxe polí definovaných v této části uvedená v ABNF   [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) zůstává stejná, s výjimkou následujících:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>Společná pole odpovědi na fragment 2.2.4.9 

### <a name="225-sparse-stream-pointer"></a>Ukazatel 2.2.5 zhuštěného streamu 

### <a name="226-fragment-not-yet-available"></a>Fragment 2.2.6 ještě není dostupný. 

### <a name="227-live-ingest"></a>2.2.7 živé ingestování 

#### <a name="2271-filetype"></a>Typ 2.2.7.1 

>   **Typ souboru (proměnná):** určuje podtyp a zamýšlené použití souboru MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) a atributů na nejvyšší úrovni.
> 
>   **MajorBrand (proměnná):** Hlavní značka mediálního souboru. MUSÍ být nastavené na "ISML".
> 
>   Podverze **(proměnná):** Vedlejší verze mediálního souboru. MUSÍ být nastavené na hodnotu 1.
> 
>   **CompatibleBrands (proměnná):** Určuje podporované značky MPEG-4.
>   MUSÍ zahrnovat "ccff" a "ISO8".
> 
>   Syntaxe polí definovaných v této části, určená v ABNF   [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) je následující:

```properties
FileType = MajorBrand MinorVersion CompatibleBrands
MajorBrand = STRING_UINT32
MinorVersion = STRING_UINT32
CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)
```

**Poznámka**: značky kompatibility ' ccff ' a ' ISO8 ' označují, že fragmenty odpovídají "Common File Format" a Common Encryption [ISO/IEC 23001-7] a ISO Base Media Format File Format Edition 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>Fragment 2.2.7.5 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 sledovat rozšířenou hlavičku fragmentu 

### <a name="228-server-to-server-ingest"></a>ingestování mezi servery 2.2.8 

## <a name="3-protocol-details"></a>3 podrobnosti protokolu 


## <a name="31-client-details"></a>Podrobnosti o klientovi 3,1 

### <a name="311-abstract-data-model"></a>abstraktní datový model 3.1.1 

#### <a name="3111-presentation-description"></a>Popis prezentace 3.1.1.1 

>   Datový prvek popis prezentace zapouzdřuje všechna metadata pro prezentaci.
> 
>   Metadata prezentace: sada metadat, která je společná pro všechny datové proudy v prezentaci. Metadata prezentace obsahují následující pole, která jsou uvedena v části *2.2.2.1*:
> 
> * **MajorVersion**
> * **Podverze**
> * **Měřítk**
> * **Doba trvání**
> * **V reálném čase**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   Prezentace obsahující proudy HEVC musí být nastavené na:

```properties
MajorVersion = 2
MinorVersion = 2
```

>   LookaheadCount = 0 (Poznámka: nepoužívané rámečky)
> 
>   JE také potřeba nastavit prezentace:

```properties
TimeScale = 90000
```

>   Kolekce datových proudů: kolekce datových elementů s popisem datového proudu, jak je uvedeno v oddílu *3.1.1.1.2*.
> 
>   Popis ochrany: kolekce datových prvků s popisem metadat systému ochrany, jak je uvedeno v oddílu *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>Popis metadat systému 3.1.1.1.1 Protection 

>   Datový prvek popis metadat systému ochrany zapouzdřuje metadata specifická pro jeden Content Protection systém. (Beze změny)
> 
>   Popis hlavičky ochrany: metadata Content Protection, která se vztahují k jednomu Content Protection systému. Popis hlavičky ochrany obsahuje následující pole, která jsou uvedená v části *2.2.2.2*:
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>Popis 3.1.1.1.2 streamu 

###### <a name="311121-track-description"></a>Popis 3.1.1.1.2.1 stopy 

###### <a name="3111211-custom-attribute-description"></a>Popis vlastního atributu 3.1.1.1.2.1.1 

##### <a name="3113-fragment-reference-description"></a>Popis odkazu na fragment 3.1.1.3 

###### <a name="31131-track-specific-fragment-reference-description"></a>Popis odkazu na fragment Track-Specific 3.1.1.3.1 

#### <a name="3112-fragment-description"></a>Popis fragmentu 3.1.1.2 

##### <a name="31121-sample-description"></a>Popis ukázky 3.1.1.2.1 

### <a name="312-timers"></a>časovače 3.1.2 

### <a name="313-initialization"></a>Inicializace 3.1.3 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Higher-Layer aktivované události 

#### <a name="3141-open-presentation"></a>otevřená prezentace 3.1.4.1 

#### <a name="3142-get-fragment"></a>3.1.4.2 získat fragment 

#### <a name="3143-close-presentation"></a>Zavřít prezentaci 3.1.4.3 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 zpracování událostí a pravidel sekvencování 

#### <a name="3151-manifest-request-and-manifest-response"></a>Požadavek manifestu 3.1.5.1 a odpověď manifestu 

#### <a name="3152-fragment-request-and-fragment-response"></a>Požadavek na fragment 3.1.5.2 a odpověď na fragment

## <a name="32-server-details"></a>Podrobnosti o serveru 3,2

## <a name="33-live-encoder-details"></a>3,3 Live Encoder – podrobnosti 

## <a name="4-protocol-examples"></a>4 Příklady protokolu 

## <a name="5-security"></a>5 zabezpečení 

## <a name="51-security-considerations-for-implementers"></a>5,1 požadavky na zabezpečení pro implementátory

>   Pokud obsah přepravovaný pomocí tohoto protokolu má vysokou komerční hodnotu, je třeba použít systém Content Protection, aby se zabránilo neoprávněnému použití obsahu. **ProtectionElement** se dá použít k přenosu metadat souvisejících s používáním Content Protectionho systému. Chráněný audio a video obsah se zašifrují podle specifikace MPEG Common Encryption Second Edition: 2015 [ISO/IEC 23001-7].
> 
>   **Poznámka**: pro video HEVC se šifrují jenom data řezu v VCL NALs. Záhlaví řezů a další NALs jsou přístupné pro prezentace aplikací před jejich dešifrováním. v zabezpečené cestě k videu nejsou k dispozici šifrované informace pro aplikace pro prezentace.

## <a name="52-index-of-security-parameters"></a>5,2 index parametrů zabezpečení 


| **Parametr zabezpečení**  | **Sekce**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Common Encryptionová pole | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5,3 Common Encryption kolonkách

Následující pole mohou být přítomna v odpovědích fragmentů při použití Common Encryption a jsou uvedena v [ISO/IEC 23001-7] nebo [ISO/IEC 14496-12]:

1.  Pole hlavičky specifické pro systém ochrany (' pssh ')

2.  Ukázkový šifrovací rámeček (' senc ')

3.  Ukázka pomocného pomocného údaje (' saio ')

4.  Ukázka pomocné informace o velikosti pole (' Saiz ')

5.  Pole s popisem skupiny vzorků (' sgpd ')

6.  Vzorek do skupinového pole (' sbgp ')

---

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
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
