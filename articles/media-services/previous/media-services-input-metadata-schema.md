---
title: Azure Media Services schéma metadat vstupu | Microsoft Docs
description: Tento článek obsahuje přehled Azure Media Services schématu vstupních metadat.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: eebe1c77c9af6791be94c16f0f792a9e7deb9a6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017405"
---
# <a name="input-metadata"></a>Vstupní metadata

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Úloha kódování je přidružená ke vstupnímu assetu (nebo assetům), na kterém chcete provést některé úlohy kódování.  Po dokončení úlohy se vytvoří výstupní Asset.  Výstupní Asset obsahuje video, zvuk, miniatury, manifest atd. Výstupní Asset obsahuje také soubor s metadaty o vstupním prostředku. Název souboru XML s metadaty má následující formát: &lt; asset_id &gt;_metadata.xml (například 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), kde &lt; Asset_id &gt; je hodnota AssetId vstupního assetu.  

Media Services nesálu kontrolu vstupních assetů za účelem generování metadat. Vstupní metadata se generují jako artefakt, když se v úloze zpracuje vstupní Asset. Proto je tento artefakt zapsán do výstupního prostředku. Pro generování metadat pro vstupní prostředky a výstupní prostředky se používají různé nástroje. Vstupní metadata proto mají trochu jiné schéma než výstupní metadata.

Pokud chcete prošetřit soubor metadat, můžete vytvořit Lokátor **SAS** a stáhnout soubor do místního počítače. Můžete najít příklad, jak vytvořit Lokátor SAS a stáhnout soubor  [pomocí rozšíření Media Services .NET SDK](media-services-dotnet-get-started.md).  

Tento článek popisuje prvky a typy schématu XML, na kterých je vstupní metada ( &lt; asset_id &gt;_metadata.xml) založen.  Informace o souboru, který obsahuje metadata o výstupním prostředku, najdete v tématu [výstupní metadata](media-services-output-metadata-schema.md).  

[Kód schématu](media-services-input-metadata-schema.md#code) na konci tohoto článku najdete jako [příklad XML](media-services-input-metadata-schema.md#xml) .  
 

## <a name="assetfiles-element-root-element"></a><a name="AssetFiles"></a> AssetFiles – element (kořenový element)
Obsahuje kolekci [AssetFile element](media-services-input-metadata-schema.md#AssetFile)s pro úlohu kódování.  

Podívejte se na příklad XML na konci tohoto článku: [příklad XML](media-services-input-metadata-schema.md#xml).  

| Název | Description |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs = "1" maxOccurs = "Unbounded" |Jeden podřízený element. Další informace naleznete v tématu [AssetFile element](media-services-input-metadata-schema.md#AssetFile). |

## <a name="assetfile-element"></a><a name="AssetFile"></a> Element AssetFile
 Obsahuje atributy a prvky, které popisují soubor assetu.  

 Podívejte se na příklad XML na konci tohoto článku: [příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Název**<br /><br /> Vyžadováno |**xs: String** |Název souboru assetu. |
| **Velikost**<br /><br /> Vyžadováno |**xs: Long** |Velikost souboru prostředků v bajtech |
| **Doba trvání**<br /><br /> Vyžadováno |**xs: Duration** |Doba přehrávání obsahu Příklad: Duration = "PT25M 37.757 S". |
| **NumberOfStreams**<br /><br /> Vyžadováno |**xs: int** |Počet datových proudů v souboru prostředků. |
| **FormatNames**<br /><br /> Vyžadováno |**xs: String** |Názvy formátů. |
| **FormatVerboseNames**<br /><br /> Vyžadováno |**xs: String** |Formátování podrobných názvů. |
| **Spuštění** |**xs: Duration** |Čas zahájení obsahu Příklad: Čas_spuštění = "PT 2.669 S". |
| **OverallBitRate** |**xs: int** |Průměrná přenosová rychlost souboru prostředků v KB/s. |

> [!NOTE]
> Následující čtyři podřízené elementy se musí objevit v sekvenci.  
> 
> 

### <a name="child-elements"></a>Podřízené prvky
| Název | Typ | Description |
| --- | --- | --- |
| **Programy**<br /><br /> minOccurs = "0" | |Kolekce prvků všechny [programy](media-services-input-metadata-schema.md#Programs) , pokud je soubor prostředků ve formátu MPEG-TS. |
| **VideoTracks**<br /><br /> minOccurs = "0" | |Každý soubor fyzického majetku může obsahovat nula nebo více videí, které se pronechají v příslušném formátu kontejneru. Tento prvek obsahuje kolekci všech [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) , které jsou součástí souboru prostředků. |
| **AudioTracks**<br /><br /> minOccurs = "0" | |Každý fyzický soubor prostředků může obsahovat nula nebo více zvukových stop, které se pronechají v příslušném formátu kontejneru. Tento prvek obsahuje kolekci všech [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) , které jsou součástí souboru prostředků. |
| **Metadata**<br /><br /> minOccurs = "0" maxOccurs = "Unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Metadata souboru prostředků reprezentovaná jako key\value řetězce. Například:<br /><br /> **&lt;Klíč metadat = "jazyk" value = "ENG"/&gt;** |

## <a name="tracktype"></a><a name="TrackType"></a> TrackType
Podívejte se na příklad XML na konci tohoto článku: [příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Description |
| --- | --- | --- |
| **Účet**<br /><br /> Vyžadováno |**xs: int** |Index s nulovým základem tohoto zvukového zvuku nebo stopy videa<br /><br /> To neznamená, že TrackID, jak se používá v souboru MP4. |
| **Kodek** |**xs: String** |Řetězec kodeku pro sledování videa |
| **CodecLongName** |**xs: String** |Dlouhý název kodeku zvuk nebo video stop |
| **Porno**<br /><br /> Vyžadováno |**xs: String** |Časová základna Příklad: základ času = "1/48000" |
| **NumberOfFrames** |**xs: int** |Počet snímků (přítomných pro stopy videa). |
| **Spuštění** |**xs: Duration** |Čas zahájení sledování Příklad: Čas_spuštění = "PT 2.669 S" |
| **Doba trvání** |**xs: Duration** |Doba trvání sledování Příklad: Duration = "PTSampleFormat M 37.757 S". |

> [!NOTE]
> Následující dva podřízené elementy se musí objevit v sekvenci.  
> 
> 

### <a name="child-elements"></a>Podřízené prvky
| Název | Typ | Description |
| --- | --- | --- |
| **Akcí**<br /><br /> minOccurs = "0" maxOccurs = "1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Obsahuje informace o prezentaci (například o tom, zda je konkrétní zvuková stopa určena pro vizuálně postižené diváky). |
| **Metadata**<br /><br /> minOccurs = "0" maxOccurs = "Unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Obecné řetězce klíč/hodnota, které lze použít k uložení nejrůznějších informací. Například Key = "jazyk", a value = "ENG". |

## <a name="audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a> AudioTrackType (dědí z TrackType)
 **AudioTrackType** je globální komplexní typ, který dědí z [TrackType](media-services-input-metadata-schema.md#TrackType).  

 Typ představuje konkrétní zvukovou stopu v souboru prostředků.  

 Podívejte se na příklad XML na konci tohoto článku: [příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Description |
| --- | --- | --- |
| **SampleFormat** |**xs: String** |Vzorový formát |
| **ChannelLayout** |**xs: String** |Rozložení kanálu. |
| **Kanály**<br /><br /> Vyžadováno |**xs: int** |Číslo (0 nebo více) zvukových kanálů. |
| **SamplingRate**<br /><br /> Vyžadováno |**xs: int** |Vzorkovací frekvence zvuku v ukázkách/s nebo Hz. |
| **Rychlostí** |**xs: int** |Průměrná rychlost zvukového bitu v bitech za sekundu, jak je vypočítána ze souboru assetu. Počítá se pouze část základního datového proudu a režie balení není v tomto počtu zahrnutá. |
| **BitsPerSample** |**xs: int** |Bity na vzorek pro typ formátu wFormatTag |

## <a name="videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a> VideoTrackType (dědí z TrackType)
**VideoTrackType** je globální komplexní typ, který dědí z [TrackType](media-services-input-metadata-schema.md#TrackType).  

Typ představuje konkrétní stopu videa v souboru prostředků.  

Podívejte se na příklad XML na konci tohoto článku: [příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Description |
| --- | --- | --- |
| **FourCC**<br /><br /> Vyžadováno |**xs: String** |FourCCový kodek pro video. |
| **Profil** |**xs: String** |Profil stopy videa. |
| **Obsah** |**xs: String** |Úroveň stopy videa |
| **PixelFormat** |**xs: String** |Formát pixelu stopy videa |
| **Width (Šířka)**<br /><br /> Vyžadováno |**xs: int** |Zakódovaná Šířka videa v pixelech |
| **Height (Výška)**<br /><br /> Vyžadováno |**xs: int** |Výška kódovaného videa v pixelech |
| **DisplayAspectRatioNumerator**<br /><br /> Vyžadováno |**xs: Double** |Čítač zobrazení poměru stran videa |
| **DisplayAspectRatioDenominator**<br /><br /> Vyžadováno |**xs: Double** |Jmenovatel poměru stran zobrazení videa |
| **DisplayAspectRatioDenominator**<br /><br /> Vyžadováno |**xs: Double** |Ukázka poměru stran videa – čitatel. |
| **SampleAspectRatioNumerator** |**xs: Double** |Ukázka poměru stran videa – čitatel. |
| **SampleAspectRatioNumerator** |**xs: Double** |Jmenovatel poměru stran vzorku videa |
| **Snímků**<br /><br /> Vyžadováno |**xs: Decimal** |Měřená snímková frekvence videa ve formátu. 3F. |
| **Rychlostí** |**xs: int** |Průměrná přenosová rychlost videa v kilobitech za sekundu, jak je vypočítána ze souboru assetu Počítá se pouze část základního datového proudu a režijní náklady na balení nejsou zahrnuty. |
| **MaxGOPBitrate** |**xs: int** |Maximální přenosová rychlost skupinu GOP pro tuto stopu videa v kilobitech za sekundu |
| **HasBFrames** |**xs: int** |Video sleduje počet snímků B. |

## <a name="metadatatype"></a><a name="MetadataType"></a> MetadataType
**MetadataType** je globální komplexní typ, který popisuje metadata souboru assetu jako řetězce klíč/hodnota. Například Key = "jazyk", a value = "ENG".  

Podívejte se na příklad XML na konci tohoto článku: [příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Description |
| --- | --- | --- |
| **zkrat**<br /><br /> Vyžadováno |**xs: String** |Klíč v páru klíč/hodnota. |
| **value**<br /><br /> Vyžadováno |**xs: String** |Hodnota v páru klíč/hodnota. |

## <a name="programtype"></a><a name="ProgramType"></a> ProgramType
**ProgramType** je globální komplexní typ, který popisuje program.  

### <a name="attributes"></a>Atributy
| Název | Typ | Description |
| --- | --- | --- |
| **ProgramId**<br /><br /> Vyžadováno |**xs: int** |ID programu |
| **NumberOfPrograms**<br /><br /> Vyžadováno |**xs: int** |Počet programů. |
| **PmtPid**<br /><br /> Vyžadováno |**xs: int** |Tabulky map programu (PMTs) obsahují informace o programech.  Další informace najdete v tématu [splátka](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Vyžadováno |**xs: int** |Používá se dekodérem. Další informace najdete v tématu [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) . |
| **StartPTS** |**xs: Long** |Spouští se časové razítko prezentace. |
| **EndPTS** |**xs: Long** |Časové razítko ukončení prezentace. |

## <a name="streamdispositiontype"></a><a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** je globální komplexní typ, který popisuje datový proud.  

Podívejte se na příklad XML na konci tohoto článku: [příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Description |
| --- | --- | --- |
| **Výchozí**<br /><br /> Vyžadováno |**xs: int** |Nastavením tohoto atributu na hodnotu 1 označíte, že se jedná o výchozí prezentaci. |
| **Dub**<br /><br /> Vyžadováno |**xs: int** |Nastavením tohoto atributu na hodnotu 1 označíte, že se jedná o dubbed prezentaci. |
| **Původní**<br /><br /> Vyžadováno |**xs: int** |Nastavením tohoto atributu na hodnotu 1 označíte, že se jedná o původní prezentaci. |
| **Komentář**<br /><br /> Vyžadováno |**xs: int** |Nastavením tohoto atributu na hodnotu 1 označíte, že stopa obsahuje komentář. |
| **Texty**<br /><br /> Vyžadováno |**xs: int** |Nastavením tohoto atributu na hodnotu 1 označíte, že stopa obsahuje texty. |
| **Karaoke**<br /><br /> Vyžadováno |**xs: int** |Nastavte tento atribut na hodnotu 1, chcete-li indikovat, že představuje stopu karaoke (hudba na pozadí, ne vocals). |
| **Korek**<br /><br /> Vyžadováno |**xs: int** |Nastavením tohoto atributu na hodnotu 1 označíte, že se jedná o vynucenou prezentaci. |
| **HearingImpaired**<br /><br /> Vyžadováno |**xs: int** |Nastavte tento atribut na hodnotu 1, pokud chcete, aby Tato stopa byla pro lidi, kteří jsou sluchově postiženi. |
| **VisualImpaired**<br /><br /> Vyžadováno |**xs: int** |Nastavte tento atribut na hodnotu 1, pokud chcete, aby Tato stopa byla označena jako vizuálně poškozená. |
| **CleanEffects**<br /><br /> Vyžadováno |**xs: int** |Nastavením tohoto atributu na hodnotu 1 označíte, že tato stopa má čisté efekty. |
| **AttachedPic**<br /><br /> Vyžadováno |**xs: int** |Nastavte tento atribut na hodnotu 1, pokud chcete, aby Tato stopa měla obrázky. |

## <a name="programs-element"></a><a name="Programs"></a> Program – element
Prvek obálky, který uchovává více prvků **programu** .  

### <a name="child-elements"></a>Podřízené prvky
| Název | Typ | Description |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs = "0" maxOccurs = "Unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Pro soubory prostředků, které jsou ve formátu MPEG-TS, obsahují informace o programech v souboru prostředků. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> Element VideoTracks
 Element obálky, který uchovává více elementů **VideoTrack** .  

 Podívejte se na příklad XML na konci tohoto článku: [příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Podřízené prvky
| Název | Typ | Description |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs = "0" maxOccurs = "Unbounded" |[VideoTrackType (dědí z TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Obsahuje informace o stopách videa v souboru prostředků. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> Element AudioTracks
 Element obálky, který uchovává více elementů **AudioTrack** .  

 Podívejte se na příklad XML na konci tohoto článku: [příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>elementy
| Název | Typ | Description |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs = "0" maxOccurs = "Unbounded" |[AudioTrackType (dědí z TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Obsahuje informace o zvukových stopách v souboru prostředků. |

## <a name="schema-code"></a><a name="code"></a> Kód schématu
```xml
<?xml version="1.0" encoding="utf-8"?>  
<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            elementFormDefault="qualified">  

  <xs:complexType name="MetadataType">  
    <xs:attribute name="key"   type="xs:string" use="required"/>  
    <xs:attribute name="value" type="xs:string" use="required"/>  
  </xs:complexType>  

  <xs:complexType name="ProgramType">  
    <xs:attribute name="ProgramId" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Program Id</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Number of programs</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PmtPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pmt pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PcrPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pcr pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="StartPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>start pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="EndPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>end pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="StreamDispositionType">  
    <xs:attribute name="Default"          type="xs:int" use="required" />  
    <xs:attribute name="Dub"              type="xs:int" use="required" />  
    <xs:attribute name="Original"         type="xs:int" use="required" />  
    <xs:attribute name="Comment"          type="xs:int" use="required" />  
    <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
    <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
    <xs:attribute name="Forced"           type="xs:int" use="required" />  
    <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
    <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
    <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
    <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
  </xs:complexType>  

  <xs:complexType name="TrackType" abstract="true">  
    <xs:sequence>  
      <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
      <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
    </xs:sequence>  
    <xs:attribute name="Id" use="required">  
      <xs:annotation>  
        <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="Codec" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec string</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="CodecLongName" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec long name</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="TimeBase"  type="xs:string" use="required">  
      <xs:annotation>  
        <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfFrames">  
      <xs:annotation>  
        <xs:documentation>number of frames</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="StartTime" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="Duration" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="VideoTrackType">  
    <xs:annotation>  
      <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="FourCC" type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>video codec FourCC code</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Profile" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>profile</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Level" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>level</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PixelFormat" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>Video track's pixel format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Width" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video width in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Height" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video height in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioNumerator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioDenominator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="FrameRate" use="required">  
          <xs:annotation>  
            <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:decimal">  
              <xs:minInclusive value="0"/>  
              <xs:fractionDigits value="3"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="MaxGOPBitrate">  
          <xs:annotation>  
            <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="HasBFrames" type="xs:int">  
          <xs:annotation>  
            <xs:documentation>video track number of B frames</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:complexType name="AudioTrackType">  
    <xs:annotation>  
      <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="SampleFormat"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>sample format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="ChannelLayout"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>channel layout</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Channels" use="required">  
          <xs:annotation>  
            <xs:documentation>number of audio channels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SamplingRate" use="required">  
          <xs:annotation>  
            <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="BitsPerSample">  
          <xs:annotation>  
            <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:element name="AssetFiles">  
    <xs:annotation>  
      <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
    </xs:annotation>  
    <xs:complexType>  
      <xs:sequence>  
        <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
          <xs:annotation>  
            <xs:documentation>asset file</xs:documentation>  
          </xs:annotation>  
          <xs:complexType>  
            <xs:sequence>  
              <xs:element name="Programs" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="VideoTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="AudioTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
            </xs:sequence>  
            <xs:attribute name="Name" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>the media asset file name</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Size" use="required">  
              <xs:annotation>  
                <xs:documentation>size of file in bytes</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:long">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Duration" type="xs:duration" use="required">  
              <xs:annotation>  
                <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
              <xs:annotation>  
                <xs:documentation>number of streams in asset file</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatNames" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format verbose names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="StartTime" type="xs:duration">  
              <xs:annotation>  
                <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="OverallBitRate">  
              <xs:annotation>  
                <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:complexType>  
        </xs:element>  
      </xs:sequence>  
    </xs:complexType>  
  </xs:element>  
</xs:schema>  
```


## <a name="xml-example"></a><a name="xml"></a> Příklad XML
Následuje příklad souboru s metadaty vstupu.  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
  <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
    <VideoTracks>  
      <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
      </VideoTrack>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
      </AudioTrack>  
    </AudioTracks>  
    <Metadata key="major_brand" value="mp42" />  
    <Metadata key="minor_version" value="0" />  
    <Metadata key="compatible_brands" value="mp42mp41" />  
    <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
    <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
  </AssetFile>  
</AssetFiles>  
```

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

