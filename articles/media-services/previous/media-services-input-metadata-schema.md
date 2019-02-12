---
title: Vstupní metadata schématu Azure Media Services | Dokumentace Microsoftu
description: Téma s přehledem Azure Media Services vstupní metadata schématu.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: 42e3464a190f296675b544e0087b664ff256f2fa
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003240"
---
# <a name="input-metadata-legacy"></a>Vstupní Metadata (starší verze)

Kódovací úlohy jsou přiřazeny k vstupní prostředek (nebo prostředky) na kterém chcete provést některé úlohy kódování.  Po dokončení úlohy je vytvořen výstupního prostředku.  Výstupní asset obsahuje video, zvuk, miniatury, manifest atd. Výstupní asset obsahuje také soubor s metadaty o vstupní asset. Název souboru XML metadat má následující formát: &lt;asset_id&gt;_metadata.xml (například d 57 8 41114ad3 eb5e - 4c 92-5354e2b7d4a4_metadata.xml), kde &lt;asset_id&gt; je hodnota AssetId vstupní asset.  

Služba Media Services nekontroluje preventivně vstupní prostředky ke generování metadat. Vstupní metadata se vygeneruje pouze jako artefakt při vstupní Asset se zpracovává v rámci úlohy. Proto tento artefakt se zapisují do výstupu Asset. Jiné nástroje se používají pro generování metadat pro prostředky vstupní a výstupní Assety. Vstupní metadata proto má mírně odlišné schéma než výstup metadat.

Pokud chcete zkontrolovat soubor metadat, můžete vytvořit **SAS** Lokátor a stahování souborů do místního počítače. Příklad najdete na tom, jak vytvořit lokátor SAS a stáhněte si soubor [pomocí rozšíření Media Services .NET SDK](media-services-dotnet-get-started.md).  

Tento článek popisuje elementů a typů schématu XML, na kterém vstupní metada (&lt;asset_id&gt;_metadata.xml) je založena.  Informace o souboru, který obsahuje metadata o prostředku výstupu najdete v tématu [výstup metadat](media-services-output-metadata-schema.md).  

Můžete najít [kód schématu](media-services-input-metadata-schema.md#code) [ukázkový kód XML](media-services-input-metadata-schema.md#xml) na konci tohoto článku.  
 

## <a name="AssetFiles"></a> Element AssetFiles (kořenový element)
Obsahuje kolekci [AssetFile element](media-services-input-metadata-schema.md#AssetFile)s pro úlohy kódování.  

Podívejte se příklad XML na konci tohoto článku: [Ukázkový kód XML](media-services-input-metadata-schema.md#xml).  

| Název | Popis |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs="1" maxOccurs="unbounded" |Jeden podřízený prvek. Další informace najdete v tématu [AssetFile element](media-services-input-metadata-schema.md#AssetFile). |

## <a name="AssetFile"></a> AssetFile – element
 Obsahuje atributy a prvky, které popisují souboru prostředků.  

 Podívejte se příklad XML na konci tohoto článku: [Ukázkový kód XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Název**<br /><br /> Požaduje se |**xs:string** |Název souboru prostředku. |
| **Velikost**<br /><br /> Požaduje se |**xs:long** |Velikost souboru prostředku v bajtech. |
| **Doba trvání**<br /><br /> Požaduje se |**xs:duration** |Přehrávání obsahu back doby trvání. Příklad: Duration="PT25M37.757S". |
| **NumberOfStreams**<br /><br /> Požaduje se |**xs:int** |Počet datových proudů souboru prostředku. |
| **FormatNames**<br /><br /> Požaduje se |**xs: řetězec** |Názvy ve formátu. |
| **FormatVerboseNames**<br /><br /> Požaduje se |**xs: řetězec** |Podrobné názvy ve formátu. |
| **startTime** |**xs:duration** |Čas zahájení obsahu. Příklad: StartTime = "PT2.669S". |
| **OverallBitRate** |**xs: int** |Průměrná s přenosovou rychlostí souboru prostředku v kb/s. |

> [!NOTE]
> Následující čtyři podřízené elementy musí být uvedena v pořadí.  
> 
> 

### <a name="child-elements"></a>Podřízené prvky
| Název | Typ | Popis |
| --- | --- | --- |
| **Programy**<br /><br /> minOccurs="0" | |Kolekce všech [programy element](media-services-input-metadata-schema.md#Programs) po souboru prostředku ve formátu MPEG-TS. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Každý soubor fyzický prostředek může obsahovat nula nebo více sleduje videa prokládané do formátu odpovídajícího kontejneru. Tento prvek obsahuje kolekci všech [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) , které jsou součástí souboru prostředku. |
| **AudioTracks**<br /><br /> minOccurs="0" | |Každý soubor fyzický prostředek může obsahovat nula nebo více zvukové stopy prokládané do formátu odpovídajícího kontejneru. Tento prvek obsahuje kolekci všech [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) , které jsou součástí souboru prostředku. |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Soubor assetu metadat reprezentována jako key\value řetězce. Příklad:<br /><br /> **&lt;Metadata key="language" value="eng" /&gt;** |

## <a name="TrackType"></a> TrackType
Podívejte se příklad XML na konci tohoto článku: [Ukázkový kód XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **ID**<br /><br /> Požaduje se |**xs:int** |Index založený na nule tento záznam zvuku nebo videa.<br /><br /> Toto není nutně, že TrackID jako použití v souboru MP4. |
| **Kodek** |**xs:string** |Sledovat videa kodek řetězec. |
| **CodecLongName** |**xs: řetězec** |Sledovat videa nebo zvukový kodek dlouhý název. |
| **TimeBase**<br /><br /> Požaduje se |**xs:string** |Základ pro dobu. Příklad: TimeBase="1/48000" |
| **NumberOfFrames** |**xs:int** |Počet rámců (k dispozici pro videa stopy). |
| **startTime** |**xs: duration** |Čas spuštění sledování. Příklad: StartTime = "PT2.669S" |
| **Doba trvání** |**xs:duration** |Sledování doby trvání. Příklad: Doba trvání = "PTSampleFormat M37.757S". |

> [!NOTE]
> Následující dva podřízené elementy musí být uvedena v pořadí.  
> 
> 

### <a name="child-elements"></a>Podřízené prvky
| Název | Typ | Popis |
| --- | --- | --- |
| **Dispozice**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Obsahuje informace o prezentace (například, jestli konkrétní zvukové stopy je pro slabozraké uživatele). |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Obecný klíč/hodnota řetězce, které lze použít pro uložení různých informací. Například klíč = "jazyk" a hodnota = "eng". |

## <a name="AudioTrackType"></a> AudioTrackType (dědí nastavení z TrackType)
 **AudioTrackType** je globální komplexní typ, který dědí z [TrackType](media-services-input-metadata-schema.md#TrackType).  

 Typ představuje konkrétní zvukové stopy v souboru prostředku.  

 Podívejte se příklad XML na konci tohoto článku: [Ukázkový kód XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **SampleFormat** |**xs:string** |Vzor formátu. |
| **ChannelLayout** |**xs: řetězec** |Kanál rozložení. |
| **kanály**<br /><br /> Požaduje se |**xs:int** |Číslo (0 nebo více) zvuku kanálů. |
| **SamplingRate**<br /><br /> Požaduje se |**xs:int** |Zvukový vzorkovací frekvenci vzorků/s nebo Hz. |
| **Bitrate** |**xs:int** |Průměrná zvuku přenosová rychlost v bitech za sekundu, počítané od souboru prostředku. Pouze datové části Základní datový proud se počítá a nároky na balení nepatří do tohoto počtu. |
| **BitsPerSample** |**xs:int** |Bitů na vzorek formátu wFormatTag typu. |

## <a name="VideoTrackType"></a> VideoTrackType (dědí nastavení z TrackType)
**VideoTrackType** je globální komplexní typ, který dědí z [TrackType](media-services-input-metadata-schema.md#TrackType).  

Typ představuje konkrétní videa sledovat v souboru prostředku.  

Podívejte se příklad XML na konci tohoto článku: [Ukázkový kód XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **FourCC**<br /><br /> Požaduje se |**xs:string** |Kodek videa FourCC kódu. |
| **Profil** |**xs: řetězec** |Sledovat video profilu. |
| **Úroveň** |**xs: řetězec** |Sledovat video úroveň. |
| **PixelFormat** |**xs: řetězec** |Sledovat video Pixelový formát. |
| **Šířka**<br /><br /> Požaduje se |**xs:int** |Kódování videa šířka v pixelech. |
| **Výška**<br /><br /> Požaduje se |**xs:int** |Kódování videa výšku v pixelech. |
| **DisplayAspectRatioNumerator**<br /><br /> Požaduje se |**xs: double** |Čítač poměr stran obrazovky. |
| **DisplayAspectRatioDenominator**<br /><br /> Požaduje se |**xs:Double** |Jmenovatel poměr stran obrazovky. |
| **DisplayAspectRatioDenominator**<br /><br /> Požaduje se |**xs: double** |Čítač poměr stran videa vzorku. |
| **SampleAspectRatioNumerator** |**xs: double** |Čítač poměr stran videa vzorku. |
| **SampleAspectRatioNumerator** |**xs:Double** |Jmenovatel poměr stran videa vzorku. |
| **Snímkovou**<br /><br /> Požaduje se |**xs:decimal** |Měří videa snímkovou frekvenci ve formátu .3f. |
| **Bitrate** |**xs:int** |Průměrná přenosová rychlost videa v kilobity za sekundu, počítané od souboru prostředku. Pouze datové části Základní datový proud se počítá a balení zatížení není zahrnutý. |
| **MaxGOPBitrate** |**xs: int** |Maximální počet GOP průměrná přenosové rychlosti pro tato videa sledovat v kilobity za sekundu. |
| **HasBFrames** |**xs:int** |Videa sledovat počet rámců B. |

## <a name="MetadataType"></a> MetadataType
**MetadataType** je globální komplexní typ, který popisuje metadata souboru prostředku jako klíč/hodnota řetězce. Například klíč = "jazyk" a hodnota = "eng".  

Podívejte se příklad XML na konci tohoto článku: [Ukázkový kód XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **key**<br /><br /> Požaduje se |**xs:string** |Klíč v páru klíč/hodnota. |
| **value**<br /><br /> Požaduje se |**xs:string** |Hodnota ve dvojici klíč/hodnota. |

## <a name="ProgramType"></a> ProgramType
**ProgramType** je globální komplexní typ, který popisuje programu.  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **ProgramId**<br /><br /> Požaduje se |**xs:int** |Id programu |
| **NumberOfPrograms**<br /><br /> Požaduje se |**xs:int** |Počet aplikací. |
| **PmtPid**<br /><br /> Požaduje se |**xs:int** |Program Mapa tabulky (PMTs) obsahují informace o programech.  Další informace najdete v tématu [platba](http://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Požaduje se |**xs: int** |Dekodér používá. Další informace najdete v tématu [nejvyššího počtu buněk](http://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: long** |Spouští se prezentace časové razítko. |
| **EndPTS** |**xs: long** |Časové razítko ukončení prezentace. |

## <a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** je globální komplexní typ, který popisuje datového proudu.  

Podívejte se příklad XML na konci tohoto článku: [Ukázkový kód XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Výchozí**<br /><br /> Požaduje se |**xs: int** |Tento atribut nastavte na 1. to znamená, že toto je výchozí prezentace. |
| **Dub**<br /><br /> Požaduje se |**xs:int** |Tento atribut nastavte na 1. to znamená, že jde o dubbed prezentace. |
| **Původní**<br /><br /> Požaduje se |**xs: int** |Tento atribut nastavte na 1. to znamená, že jde o původní prezentace. |
| **Komentář**<br /><br /> Požaduje se |**xs:int** |Tento atribut nastavte na 1. to znamená, že toto sledování obsahuje komentáře. |
| **Text**<br /><br /> Požaduje se |**xs:int** |Tento atribut nastavte na 1. to znamená, že toto sledování obsahuje text. |
| **Karaoke**<br /><br /> Požaduje se |**xs:int** |Tento atribut nastavte na 1. to znamená, že to představuje jeden směr karaoke (hudby na pozadí, žádné hlasy zpěváků). |
| **Vynucené**<br /><br /> Požaduje se |**xs:int** |Tento atribut nastavte na 1. to znamená, že jde o vynucené prezentace. |
| **HearingImpaired**<br /><br /> Požaduje se |**xs:int** |Tento atribut nastavte na 1. to znamená, že tato směr určený pro sluchu narušena. |
| **VisualImpaired**<br /><br /> Požaduje se |**xs:int** |Tento atribut nastavte na 1. to znamená, že tato směr určený pro uživatele se zrakovým postižením. |
| **CleanEffects**<br /><br /> Požaduje se |**xs: int** |Tento atribut nastavte na 1. to znamená, že tento blok má čisté účinky. |
| **AttachedPic**<br /><br /> Požaduje se |**xs: int** |Tento atribut nastavte na 1. to znamená, že tento blok má obrázky. |

## <a name="Programs"></a> Element programy
Element obálky, která uchovává více **Program** elementy.  

### <a name="child-elements"></a>Podřízené prvky
| Název | Typ | Popis |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Soubory prostředků, které jsou ve formátu MPEG-TS obsahuje informace o aplikacích v souboru prostředku. |

## <a name="VideoTracks"></a> VideoTracks – element
 Element obálky, která uchovává více **VideoTrack** elementy.  

 Podívejte se příklad XML na konci tohoto článku: [Ukázkový kód XML](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Podřízené prvky
| Název | Typ | Popis |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType (dědí nastavení z TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Obsahuje informace o videu sleduje v souboru prostředku. |

## <a name="AudioTracks"></a> AudioTracks – element
 Element obálky, která uchovává více **AudioTrack** elementy.  

 Podívejte se příklad XML na konci tohoto článku: [Ukázkový kód XML](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType (dědí nastavení z TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Obsahuje informace o zvukové stopy v souboru prostředku. |

## <a name="code"></a> Kód schématu
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
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


## <a name="xml"></a> Ukázkový kód XML
Následuje příklad souboru metadat vstupu.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
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

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

