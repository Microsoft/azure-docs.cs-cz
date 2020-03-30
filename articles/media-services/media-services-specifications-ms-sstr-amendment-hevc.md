---
title: Změna protokolu hladkého streamování (MS-SSTR) pro HEVC – Azure
description: Tato specifikace popisuje protokol a formát pro fragmentované živé vysílání založené na MP4 pomocí HEVC ve službě Azure Media Services. V tomto článku jsou uvedeny pouze změny potřebné k dodání HEVC, s výjimkou případů, kdy byly "(No Change)" označuje, že text je zkopírován pouze pro objasnění.
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
ms.openlocfilehash: be4009d418f2f8f3dff755e2e990efee593f070b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514217"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Změna protokolu o hladkém streamování (MS-SSTR) pro HEVC 

## <a name="1-introduction"></a>1 Úvod 

Tento článek obsahuje podrobné změny, které mají být použity pro specifikaci protokolu Smooth Streaming Protocol [MS-SSTR], aby bylo možné plynulé streamování videa kódovaného HEVC. V této specifikaci uvádíme pouze změny potřebné k dodání videokodeku HEVC. Článek následuje za stejným schématem číslování jako specifikace [MS-SSTR]. Prázdné titulky prezentované v celém článku jsou k dispozici pro orientaci čtenáře na jejich pozici ve specifikaci [MS-SSTR].  "(No Change)" označuje, že text je zkopírován pouze pro účely objasnění.

Článek obsahuje požadavky na technickou implementaci pro signalizaci video kodeku HEVC (pomocí stop ve formátu "hev1" nebo "hvc1") v manifestu Plynulého streamování a normativní odkazy jsou aktualizovány tak, aby odkazovaly na aktuální standardy MPEG, které zahrnuty HEVC, společné šifrování HEVC a názvy polí pro formát iso základního mediálního souboru byly aktualizovány, aby byly v souladu s nejnovějšími specifikacemi. 

Odkazovaná specifikace protokolu Smooth Streaming Protocol [MS-SSTR] popisuje formát drátu používaný k doručování živých a na vyžádání digitálních médií, jako je zvuk a video, následujícími způsoby: od kodéru k webovému serveru, ze serveru na jiný server a z serveru klientovi HTTP.
Použití datové struktury založené na MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787)přes HTTP umožňuje bezproblémové přepínání téměř v reálném čase mezi různými úrovněmi kvality komprimovaného mediálního obsahu. Výsledkem je neustálé přehrávání pro koncového uživatele klienta HTTP, i v případě, že se podmínky síťového a vykreslování videa změní pro klientský počítač nebo zařízení.

## <a name="11-glossary"></a>1.1 Slovníček pojmů 

Následující termíny jsou definovány v *[MS-GLOS]*:

>   **globálně jedinečný identifikátor (GUID) univerzálně jedinečný identifikátor (UUID)**

Pro tento dokument jsou specifické následující termíny:

>  **doba kompozice:** Čas, kdy je vzorek prezentován u klienta, jak je definováno v [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695).
> 
>   **CENC**: Společné šifrování, jak je definováno v [ISO/IEC 23001-7] Druhé vydání.
> 
>   **dekódovat čas:** Čas, po který má být vzorek dekódován na straně klienta, jak je definováno v [[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695).

**fragment:** Jednotka **médií,** kterou lze stáhnout, která obsahuje jeden nebo více **vzorků**.

>   **HEVC:** Vysoce účinné video kódování, jak je definováno v [ISO/IEC 23008-2]
> 
>   **manifest:** Metadata o **prezentaci,** která umožňuje klientovi posuzovat žádosti o **média**. **média:** Komprimovaná zvuková, obrazová a textová data používaná klientem k přehrání **prezentace**. **formát média:** Dobře definovaný formát pro reprezentaci zvuku nebo videa jako **komprimovanéukázky**.
> 
>   **prezentace:** Sada všech **datových proudů** a souvisejících metadat potřebných k přehrání jednoho filmu. **žádost:** Zpráva HTTP odeslaná z klienta na server, jak je definována v odpovědi [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) **:** Zpráva HTTP odeslaná ze serveru klientovi, jak je definováno v [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **vzorek:** Nejmenší základní jednotka (například rámeček), ve které je **médium** uloženo a zpracováno.
> 
>   **MŮŽE, MUSÍ, NESMÍ, NESMÍ, NESMÍ:** Tyto termíny (ve všech písmenech a) se používají tak, jak je popsáno v [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) Všechny příkazy volitelné chování použití buď května, by nebo by neměla.

## <a name="12-references"></a>1.2 Reference

>   Odkazy na dokumentaci k otevřeným specifikacím společnosti Microsoft neobsahují rok publikování, protože odkazy jsou na nejnovější verzi dokumentů, které jsou často aktualizovány. Odkazy na jiné dokumenty zahrnují rok publikování, kdy je k dispozici.

### <a name="121-normative-references"></a>1.2.1 Normativní reference 

>  [MS-SSTR] Protokol plynulého streamování *v20140502*[https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496-12] Mezinárodní organizace pro normalizaci, "Informační technologie -- Kódování audiovizuálních objektů -- Část 12: ISO Base Media File Format", ISO/IEC 14496-12:2014, Edition 4, Plus Corrigendum 1, Změny 1 & 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] Mezinárodní organizace pro normalizaci, "Informační technologie -- Kódování audiovizuálních objektů -- Část 15: Přeprava strukturovaného videa jednotky NAL ve formátu ISO Base Media File Format", ISO 14496-15:2015, Edition 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Informační technologie - Vysoce účinné kódování a doručování médií v heterogenních prostředích - Část 2: Kódování videa s vysokou účinností: 2013 nebo nejnovější vydání<https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Informační technologie – technologie systémů MPEG – Část 7: Společné šifrování v souborech formátu základního mediálního souboru ISO, CENC Edition 2:2015<https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, "Kodeky' a 'Profily' Parametry pro "Bucket" Typy médií"<https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] Registrační úřad MP4, "MP4REG",[http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "Klíčová slova pro použití v RFC k označení úrovní požadavků", BCP 14, RFC 2119, březen 1997,[https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 Informativní reference 

>   [MS-GLOS] Microsoft Corporation, "*Windows Protocols Master Glossary*."
> 
>   [RFC3548] Josefsson, S., Ed., "Kódování dat Base16, Base32 a Base64", RFC 3548, červenec 2003,[https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., a Overell, P., "Rozšířené BNF pro specifikace syntaxe: ABNF", STD 68, RFC 5234, leden 2008,[https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 Přehled 

>   Níže jsou uvedeny pouze změny specifikace Plynulého streamování požadované pro dodání HEVC. Záhlaví oddílů beze změny jsou uvedena pro zachování umístění ve specifikaci sledovaného datového proudu [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1.4 Vztah k jiným protokolům 

## <a name="15-prerequisitespreconditions"></a>1.5 Předpoklady/předpoklady 

## <a name="16-applicability-statement"></a>1.6 Prohlášení o použitelnosti 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 Vyjednávání verzí a schopností 

## <a name="18-vendor-extensible-fields"></a>1.8 Pole dodavatele-rozšiřitelná pole 

>   Pomocí videoformátu HEVC se použije následující metoda:
> 
>   * **Vlastní popisné kódy pro formáty médií:** Tato schopnost je poskytována polem **FourCC,** jak je uvedeno v bodě *2.2.2.5*.
>   Implementátoři mohou zajistit, že rozšíření nebudou v konfliktu registrací kódů rozšíření s MPEG4-RA, jak je uvedeno v [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 Přiřazení standardů 

## <a name="2-messages"></a>2 Zprávy 

## <a name="21-transport"></a>2.1 Doprava

## <a name="22-message-syntax"></a>2.2 Syntaxe zprávy 

### <a name="221-manifest-request"></a>2.2.1 Manifestní žádost 

### <a name="222-manifest-response"></a>2.2.2 Zjevná odpověď 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (proměnná):** Dílčí verze zprávy manifestodpověď. Musí být nastavena na 2. (Beze změny)
> 
>   **TimeScale (proměnná):** Časové měřítko duration atribut, určené jako počet přírůstků za jednu sekundu. Výchozí hodnotou je .
> 1. (Beze změny)
> 
>    Doporučená hodnota je 90000 pro reprezentaci přesné doby trvání snímků videa a fragmentů obsahujících video fractional framerate (například 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 Ochranný prvek 

ProtectionElement musí být přítomen, pokud bylo na video nebo audio streamy použito společné šifrování (CENC). Šifrované proudy HEVC musí odpovídat společnému šifrování 2nd Edition [ISO/IEC 23001-7]. Šifrovat se budou pouze údaje o řezech v jednotkách VCL NAL.

#### <a name="2223-streamelement"></a>2.2.2.3 Prvek streamu 

>   **StreamTimeScale (proměnná):** Časové měřítko pro hodnoty doby trvání a času v tomto datovém proudu zadané jako počet přírůstků za jednu sekundu. Pro proudy HEVC se doporučuje hodnota 90000. Pro zvukové proudy se doporučuje hodnota odpovídající frekvenci vzorkování průběhů (například 48000 nebo 44100).

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 Prvek ochrany proudu

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (proměnná):** Čtyřmístný kód, který určuje, který formát média se používá pro každou ukázku. Následující rozsah hodnot je vyhrazen s následujícími sémantickými významy:
> 
> * "hev1": Video ukázky pro tuto stopu používají video HEVC pomocí ukázkového formátu hev1 uvedeného v [ISO/IEC-14496-15].
>
> * "hvc1": Video ukázky pro tuto stopu používají video HEVC pomocí ukázkového formátu "hvc1" uvedeného v [ISO/IEC-14496-15].
> 
>   **CodecPrivateData (proměnná):** Data, která určují parametry specifické pro formát média a jsou společná pro všechny vzorky ve stopě, reprezentovaná jako řetězec šestnáctkově kódovaných bajtů. Formát a sémantický význam sekvenci bajtů se liší v závislosti na hodnotě pole **FourCC** následujícím způsobem:
> 
>   * Když TrackElement popisuje video HEVC, pole **FourCC** se rovná **"hev1"** nebo **"hvc1"**
> 
>   Pole **CodecPrivateData** musí obsahovat šestnáctkovou řetězcovou reprezentaci následující posloupnosti bajtů zadanou v ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (žádná změna z MS-SSTR)
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField obsahuje sekvenční parametr (SPS).
> 
>   * PPSField obsahuje sadu parametrů řezu (PPS).
> 
>   Poznámka: Sada parametrů videa (VPS) není obsažena v CodecPrivateData, ale měla by být obsažena v záhlaví souboru uložených souborů v poli 'hvcC'. Systémy používající protokol Smooth Streaming Protocol musí signalizovat další dekódovací parametry (například úroveň HEVC) pomocí kodeků vlastníatributy.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   Pole **MajorVersion programu SmoothStreamingMedia** musí být nastaveno na 2 a pole **MinorVersion** musí být nastaveno na 2. (Beze změny)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 Požadavek na fragment 

>   **Poznámka:** Výchozí formát média požadovaný pro **minorversion** 2 a "hev1" nebo "hvc1" je 'iso8' značka ISO Base Media File Format zadaná v [ISO/IEC 14496-12] ISO Base Media File Format Fourth Edition a [ISO/IEC 23001-7] Common Encryption Second Edition.

### <a name="224-fragment-response"></a>2.2.4 Fragmentní odezva 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox** je zastaralé a jeho funkce nahrazena časovým rámečkem odkódování fragmentu stopy ('tfdt') zadaným v oddílu 8.8.12 [ISO/IEC 14496-12].
> 
>   **Poznámka:** Klient může vypočítat dobu trvání fragmentu sečtením doby trvání vzorku uvedených v poli Track Run ("not run") nebo vynásobením počtu vzorků krát výchozí dobu trvání vzorku. BaseMediaDecodeTime v 'tfdt' plus délka fragmentu se rovná parametru času URL pro další fragment.
> 
>   Referenční časové pole producenta ("prft") by mělo být podle potřeby vloženo před krabici fragmentů filmu ("moof"), aby se označil čas UTC odpovídající času dekódování fragmentu stopy prvního vzorku, na který odkazuje rámeček fragmentu filmu, jak je uvedeno v oddíle 8.16.5 [ISO/IEC 14496-12].

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** je zastaralé a jeho funkce nahrazena časovým rámečkem dekódování fragmentu stopy ('tfdt') zadaným v oddílu 8.8.12 [ISO/IEC 14496-12].
> 
>   **Poznámka:** Klient může vypočítat dobu trvání fragmentu sečtením doby trvání vzorku uvedených v poli Track Run ("not run") nebo vynásobením počtu vzorků krát výchozí dobu trvání vzorku. BaseMediaDecodeTime v 'tfdt' plus délka fragmentu se rovná parametru času URL pro další fragment. Podívejte se dopředu adresy jsou zastaralé, protože zpoždění živého vysílání.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** a související pole zapouzdřit výchozí hodnoty pro na vzorku metadat v fragmentu. Syntaxe pole **TfhdBox** je striktní podmnožinou syntaxe pole hlavičky fragmentu stopy definovaného v oddílu 8.8.7 [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   **BaseDataOffset (8 bajtů):** Posun v bajtech od začátku pole **MdatBox** k ukázkovému poli v poli **MdatBox.** Chcete-li signalizovat toto omezení, musí být nastaven výchozí příznak base-is-moof (0x020000).

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** a související pole zapouzdřeny na ukázková metadata pro požadovaný fragment. Syntaxe **TrunBoxu** je striktní podmnožinou pole spuštění fragmentu stopy verze 1 definovanév oddílu 8.8.8 [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12].*
> 
>   **SampleCompositionTimeOffset (4 bajty):** Posun doby složení vzorku každého vzorku se upraví tak, aby doba prezentace prvního prezentovaného vzorku v fragmentu byla rovna času dekódování prvního dekódovaného vzorku. Použije se negativní kompenzace složení vzorku videa,
> 
>   jak je definováno v [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   Poznámka: Tím se zabrání chybě synchronizace videa způsobené video zaostává zvuk rovná největší dekódované poškození vyrovnávací paměti obrazu odstranění zpoždění a udržuje časování prezentace mezi alternativní fragmenty, které mohou mít různé zpoždění odebrání.
> 
>   Syntaxe polí definovaných v této části, zadaná v ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) zůstává stejná, s výjimkou následujících případů:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 Společná pole fragmentační odezvy 

### <a name="225-sparse-stream-pointer"></a>2.2.5 Řídké streamu je 

### <a name="226-fragment-not-yet-available"></a>2.2.6 Fragment zatím není k dispozici 

### <a name="227-live-ingest"></a>2.2.7 Živé ingestování 

#### <a name="2271-filetype"></a>2.2.7.1 Typ souboru 

>   **FileType (proměnná):** určuje podtyp a zamýšlené použití souboru MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) a atributy vysoké úrovně.
> 
>   **MajorBrand (proměnná):** Hlavní značka mediálního souboru. Musí být nastavena na "isml."
> 
>   **MinorVersion (proměnná):** Dílčí verze mediálního souboru. Musí být nastavena na 1.
> 
>   **CompatibleBrands (proměnná):** Určuje podporované značky MPEG-4.
>   Musí obsahovat "ccff" a "iso8."
> 
>   Syntaxe polí definovaných v této části, zadaná v ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) je následující:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Poznámka:** Značky kompatibility "ccff" a "iso8" označují, že fragmenty odpovídají "společnému formátu souboru kontejneru" a společnému šifrování [ISO/IEC 23001-7] a ISO Base Media File Format Edition 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 ProudsMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 Fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 Rozšířené záhlaví fragmentu stopy 

### <a name="228-server-to-server-ingest"></a>2.2.8 Ingestování mezi servery 

## <a name="3-protocol-details"></a>3 Podrobnosti protokolu 


## <a name="31-client-details"></a>3.1 Podrobnosti o klientovi 

### <a name="311-abstract-data-model"></a>3.1.1 Abstraktní datový model 

#### <a name="3111-presentation-description"></a>3.1.1.1 Popis prezentace 

>   Datový prvek Popis prezentace zapouzdřuje všechna metadata pro prezentaci.
> 
>   Metadata prezentace: Sada metadat, která je společná pro všechny datové proudy v prezentaci. Metadata prezentace se skládají z následujících polí uvedených v bodu *2.2.2.1*:
> 
> * **Hlavní verze**
> * **Minorversion**
> * **Časové osy**
> * **Doba trvání**
> * **IsLive**
> * **Počet výhledů**
> * **Délka okna DVR**
> 
>   Prezentace obsahující proudy HEVC musí stanovit:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Poznámka: Zastaralá pole)
> 
>   Prezentace by měly také nastavit:

    TimeScale = 90000

>   Stream Collection: Kolekce datových prvků Stream Description, jak je uvedeno v části *3.1.1.1.2*.
> 
>   Popis ochrany: Kolekce prvků metadat popisu metadat systému ochrany, jak je uvedeno v oddíle *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>Popis metadat systému ochrany 3.1.1.1 

>   Datový prvek popisu metadat systému ochrany zapouzdřuje metadata specifická pro jeden systém ochrany obsahu. (Beze změny)
> 
>   Popis záhlaví ochrany: Metadata ochrany obsahu, která se vztahuje k jedinému systému ochrany obsahu. Popis hlavičky ochrany obsahuje následující pole uvedená v bodu *2.2.2.2*:
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Popis streamu 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Popis trasy 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Popis vlastního atributu 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 Popis odkazu na fragmenty 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Referenční popis fragmentu specificképro daný trať 

#### <a name="3112-fragment-description"></a>Popis fragmentu 3.1.1.2 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Popis vzorku 

### <a name="312-timers"></a>3.1.2 Časovače 

### <a name="313-initialization"></a>3.1.3 Inicializace 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Události spouštění vyšší vrstvou 

#### <a name="3141-open-presentation"></a>3.1.4.1 Otevřená prezentace 

#### <a name="3142-get-fragment"></a>3.1.4.2 Získat fragment 

#### <a name="3143-close-presentation"></a>3.1.4.3 Zavřít prezentaci 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 Zpracování událostí a pravidla řazení 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 Manifest žádost a manifest odpověď 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 Požadavek na fragment a odpověď na fragmenty

## <a name="32-server-details"></a>3.2 Podrobnosti o serveru

## <a name="33-live-encoder-details"></a>3.3 Podrobnosti o živém kodéru 

## <a name="4-protocol-examples"></a>4 Příklady protokolu 

## <a name="5-security"></a>5 Bezpečnost 

## <a name="51-security-considerations-for-implementers"></a>5.1 Důležité informace o zabezpečení pro implementátory

>   Pokud má obsah přepravovaný pomocí tohoto protokolu vysokou obchodní hodnotu, měl by být použit systém ochrany obsahu, aby se zabránilo neoprávněnému použití obsahu. **ProtectionElement** lze použít k přenosu metadat souvisejících s použitím systému ochrany obsahu. Chráněný audio a video obsah musí být šifrován podle specifikace MPEG Common Encryption Druhé vydání: 2015 [ISO/IEC 23001-7].
> 
>   **Poznámka:** U videa HEVC jsou šifrována pouze data řezu v nalach VCL. Záhlaví řezů a další nala jsou přístupné prezentačním aplikacím před dešifrováním. v zabezpečené cestě k videu nejsou šifrované informace pro prezentační aplikace k dispozici.

## <a name="52-index-of-security-parameters"></a>5.2 Index parametrů zabezpečení 


| **Parametr zabezpečení**  | **Sekce**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Společná šifrovací pole | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 Společné šifrovací boxy

Při použití společného šifrování mohou být v odpovědích fragmentů přítomna následující pole, která jsou uvedena v [ISO/IEC 23001-7] nebo [ISO/IEC 14496-12]:

1.  Pole záhlaví specifického pro systém ochrany ('pssh')

2.  Vzorová šifrovací skříňka ("senc")

3.  Vzorová pomocná informační odsazená skříň ('saio')

4.  Vzorová pomocná informační velikostní skříň ('saiz')

5.  Pole s popisem vzorové skupiny ('sgpd')

6.  Ukázka do skupinového pole ('sbgp')

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
