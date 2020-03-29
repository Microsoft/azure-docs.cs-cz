---
title: Schéma vstupních metadat služby Azure Media Services | Dokumenty společnosti Microsoft
description: Tento článek poskytuje přehled schématu vstupních metadat Služby Azure Media Services.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a81d6edfd887dc935a53742b7bc1492651c9bda5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887114"
---
# <a name="input-metadata"></a>Vstupní metadata 

Úloha kódování je přidružena ke vstupnímu prostředku (nebo datovým zdrojům), na kterém chcete provést některé úlohy kódování.  Po dokončení úkolu je vytvořen výstupní majetek.  Výstupní aktivum obsahuje video, zvuk, miniatury, manifest atd. Výstupní datový zdroj také obsahuje soubor s metadaty o vstupním datovém zdroji. Název souboru XML metadat má následující &lt;formát: asset_id&gt;_metadata.xml (například 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), kde &lt;asset_id&gt; je hodnota AssetId vstupního datového zdroje.  

Služba Media Services nepreventivně neprohledá vstupní datové zdroje a nevygeneruje metadata. Vstupní metadata jsou generována pouze jako artefakt při zpracování vstupního datového zdroje v úloze. Proto je tento artefakt zapsán do výstupního assetu. Různé nástroje se používají ke generování metadat pro vstupní datové zdroje a výstupní datové zdroje. Vstupní metadata proto má mírně odlišné schéma než výstupní metadata.

Chcete-li soubor metadat prozkoumat, můžete vytvořit lokátor **SAS** a stáhnout soubor do místního počítače. Najdete příklad, jak vytvořit lokátor SAS a stáhnout soubor [pomocí rozšíření Media Services .NET SDK Extensions](media-services-dotnet-get-started.md).  

Tento článek popisuje prvky a typy schématu XML, na kterém&lt;je&gt;založena vstupní metada ( asset_id _metadata.xml).  Informace o souboru, který obsahuje metadata o výstupním datovém zdroji, naleznete v [tématu Výstupní metadata](media-services-output-metadata-schema.md).  

Na konci tohoto článku můžete najít [příklad schema](media-services-input-metadata-schema.md#code) [xml.](media-services-input-metadata-schema.md#xml)  
 

## <a name="assetfiles-element-root-element"></a><a name="AssetFiles"></a>AssetFiles element (kořenový prvek)
Obsahuje kolekci [AssetFile element](media-services-input-metadata-schema.md#AssetFile)s pro úlohu kódování.  

Viz příklad XML na konci tohoto článku: [Příklad XML](media-services-input-metadata-schema.md#xml).  

| Name (Název) | Popis |
| --- | --- |
| **Soubor majetku**<br /><br /> minOccurs="1" maxOccurs="bez meze" |Jeden podřízený prvek. Další informace naleznete v [tématu AssetFile element](media-services-input-metadata-schema.md#AssetFile). |

## <a name="assetfile-element"></a><a name="AssetFile"></a>Element AssetFile
 Obsahuje atributy a prvky, které popisují soubor majetku.  

 Viz příklad XML na konci tohoto článku: [Příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Název**<br /><br /> Požaduje se |**xs:řetězec** |Název souboru datového zdroje. |
| **Velikost**<br /><br /> Požaduje se |**xs:dlouhé** |Velikost souboru datového zdroje v bajtech. |
| **Doba trvání**<br /><br /> Požaduje se |**xs:doba trvání** |Doba přehrávání obsahu. Příklad: duration="PT25M37.757S". |
| **Početofstreamů**<br /><br /> Požaduje se |**xs:int** |Počet datových proudů v souboru datového zdroje. |
| **Formátnázvů**<br /><br /> Požaduje se |**xs: řetězec** |Formátovat názvy. |
| **FormatVerboseNames**<br /><br /> Požaduje se |**xs: řetězec** |Naformátujte podrobné názvy. |
| **Starttime** |**xs:doba trvání** |Čas zahájení obsahu. Příklad: StartTime="PT2.669S". |
| **Celková bitová míra** |**xs: int** |Průměrná přenosová rychlost souboru datového zdroje v kbps. |

> [!NOTE]
> Následující čtyři podřízené prvky se musí objevit v pořadí.  
> 
> 

### <a name="child-elements"></a>Podřízené prvky
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Programy**<br /><br /> minOccurs="0" | |Kolekce všech [programů prvek,](media-services-input-metadata-schema.md#Programs) když soubor datových zdrojů je ve formátu MPEG-TS. |
| **VideoSkladby**<br /><br /> minOccurs="0" | |Každý soubor fyzického majetku může obsahovat nula nebo více videí prokládáných do příslušného formátu kontejneru. Tento prvek obsahuje kolekci všech [VideoTracks,](media-services-input-metadata-schema.md#VideoTracks) které jsou součástí souboru majetku. |
| **Zvukové stopy**<br /><br /> minOccurs="0" | |Každý soubor fyzického majetku může obsahovat nula nebo více zvukových stop prokládáných do příslušného formátu kontejneru. Tento prvek obsahuje kolekci všech [AudioTracks,](media-services-input-metadata-schema.md#AudioTracks) které jsou součástí souboru datového zdroje. |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="bez meze" |[Typ metadat](media-services-input-metadata-schema.md#MetadataType) |Metadata souboru majetku reprezentovaná jako řetězce key\value. Například:<br /><br /> **&lt;Metadata key="language" value="eng" /&gt;** |

## <a name="tracktype"></a><a name="TrackType"></a>TrackType
Viz příklad XML na konci tohoto článku: [Příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **ID**<br /><br /> Požaduje se |**xs:int** |Nulový index této zvukové stopy nebo videostopy.<br /><br /> To není nutně, že TrackID, jak je použito v souboru MP4. |
| **Kodek** |**xs:řetězec** |Řetězec kodeku sledování videa. |
| **Název CodecLongName** |**xs: řetězec** |Dlouhý název kodeku zvukové stopy nebo videa. |
| **Časová základna**<br /><br /> Požaduje se |**xs:řetězec** |Časová základna. Příklad: TimeBase="1/48000" |
| **Počet rámců** |**xs:int** |Počet snímků (k dispozici pro video stopy). |
| **Starttime** |**xs: doba trvání** |Čas zahájení sledování. Příklad: StartTime="PT2.669S" |
| **Doba trvání** |**xs:doba trvání** |Doba trvání sledování. Příklad: duration="PTSampleFormat M37.757S". |

> [!NOTE]
> Následující dva podřízené prvky se musí objevit v pořadí.  
> 
> 

### <a name="child-elements"></a>Podřízené prvky
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Dispozice**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Obsahuje informace o prezentaci (například zda je určitá zvuková stopa pro zrakově postižené prohlížeče). |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="bez meze" |[Typ metadat](media-services-input-metadata-schema.md#MetadataType) |Obecné řetězce klíč/hodnota, které lze použít k uložení různých informací. Například key="language" a value="eng". |

## <a name="audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a>AudioTrackType (dědí z TrackType)
 **AudioTrackType** je globální komplexní typ, který dědí z [TrackType](media-services-input-metadata-schema.md#TrackType).  

 Typ představuje konkrétní zvukovou stopu v souboru datového zdroje.  

 Viz příklad XML na konci tohoto článku: [Příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Ukázkový formát** |**xs:řetězec** |Ukázkový formát. |
| **Rozložení kanálu** |**xs: řetězec** |Rozložení kanálu. |
| **Kanály**<br /><br /> Požaduje se |**xs:int** |Číslo (0 nebo více) zvukových kanálů. |
| **Vzorkovací míra**<br /><br /> Požaduje se |**xs:int** |Vzorkovací frekvence zvuku ve vzorcích/s nebo Hz. |
| **Datový tok** |**xs:int** |Průměrná přenosová rychlost zvuku v bitech za sekundu vypočtená ze souboru datových zdrojů. Počítá se pouze datová část elementárního datového proudu a režie balení není zahrnuta v tomto počtu. |
| **BitsPerSample** |**xs:int** |Bity na ukázku pro typ formátu wFormatTag. |

## <a name="videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a>VideoTrackType (dědí z TrackType)
**VideoTrackType** je globální komplexní typ, který dědí z [TrackType](media-services-input-metadata-schema.md#TrackType).  

Typ představuje konkrétní stopu videa v souboru datového zdroje.  

Viz příklad XML na konci tohoto článku: [Příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Fourcc**<br /><br /> Požaduje se |**xs:řetězec** |Video kodek FourCC kód. |
| **Profil** |**xs: řetězec** |Profil videostopy. |
| **Úroveň** |**xs: řetězec** |Úroveň videostopy. |
| **Pixelformat** |**xs: řetězec** |Formát obrazových bodů videostopy. |
| **impulzu**<br /><br /> Požaduje se |**xs:int** |Zakódovaná šířka videa v pixelech. |
| **Height**<br /><br /> Požaduje se |**xs:int** |Zakódovaná výška videa v pixelech. |
| **Čítač DisplayAspectRatioNumerator**<br /><br /> Požaduje se |**xs: dvojité** |Čitateč poměru stran zobrazení videa. |
| **ZobrazitRatioRatioJmenovatel**<br /><br /> Požaduje se |**xs:dvojitá** |Jmenovatel poměru stran zobrazení videa. |
| **ZobrazitRatioRatioJmenovatel**<br /><br /> Požaduje se |**xs: dvojité** |Video ukázkový poměr stran čitatel. |
| **SampleAspectRatioNumerator** |**xs: dvojité** |Video ukázkový poměr stran čitatel. |
| **SampleAspectRatioNumerator** |**xs:dvojitá** |Jmenovatel poměru stran vzorku videa. |
| **Framerate**<br /><br /> Požaduje se |**xs:desetinné** |Naměřená kmitočet snímků videa ve formátu .3f. |
| **Datový tok** |**xs:int** |Průměrná přenosová rychlost videa v kilobitech za sekundu, jak je vypočtena ze souboru majetku. Počítá se pouze datová část elementárního datového proudu a režie balení není zahrnuta. |
| **MaxGOPBitrate** |**xs: int** |Maximální gop průměrný přenosový tok pro tuto video stopu v kilobitech za sekundu. |
| **Rámce HasBFrames** |**xs:int** |Počet snímků B sledování videa. |

## <a name="metadatatype"></a><a name="MetadataType"></a>Typ metadat
**MetadataType** je globální komplexní typ, který popisuje metadata souboru datového zdroje jako řetězce klíč/hodnota. Například key="language" a value="eng".  

Viz příklad XML na konci tohoto článku: [Příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **key**<br /><br /> Požaduje se |**xs:řetězec** |Klíč v páru klíč/hodnota. |
| **value**<br /><br /> Požaduje se |**xs:řetězec** |Hodnota v páru klíč/hodnota. |

## <a name="programtype"></a><a name="ProgramType"></a>Typ programu
**ProgramType** je globální komplexní typ, který popisuje program.  

### <a name="attributes"></a>Atributy
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Id programu**<br /><br /> Požaduje se |**xs:int** |ID programu |
| **Počet programů**<br /><br /> Požaduje se |**xs:int** |Počet programů. |
| **PmtPid**<br /><br /> Požaduje se |**xs:int** |Tabulky map programů (PMT) obsahují informace o programech.  Další informace naleznete v tématu [PMt](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Požaduje se |**xs: int** |Používá se dekodérem. Další informace naleznete v tématu [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: dlouhé** |Počáteční časové razítko prezentace. |
| **EndPTS** |**xs: dlouhé** |Ukončení časového razítka prezentace. |

## <a name="streamdispositiontype"></a><a name="StreamDispositionType"></a>StreamDispositionType
**StreamDispositionType** je globální komplexní typ, který popisuje datový proud.  

Viz příklad XML na konci tohoto článku: [Příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Výchozí**<br /><br /> Požaduje se |**xs: int** |Nastavte tento atribut na hodnotu 1, která označuje, že se jedná o výchozí prezentaci. |
| **Dub**<br /><br /> Požaduje se |**xs:int** |Nastavte tento atribut na 1, který označuje, že se jedná o dubbovní prezentaci. |
| **Původní**<br /><br /> Požaduje se |**xs: int** |Nastavte tento atribut na 1, který označuje, že se jedná o původní prezentaci. |
| **Komentář**<br /><br /> Požaduje se |**xs:int** |Nastavte tento atribut na 1, který označuje, že tato stopa obsahuje komentář. |
| **Texty**<br /><br /> Požaduje se |**xs:int** |Nastavte tento atribut na 1, který označuje, že tato stopa obsahuje texty. |
| **Karaoke**<br /><br /> Požaduje se |**xs:int** |Nastavte tento atribut na 1, který označuje, že se jedná o karaoke skladbu (hudba na pozadí, bez vokálů). |
| **Nucené**<br /><br /> Požaduje se |**xs:int** |Nastavte tento atribut na 1, který označuje, že se jedná o vynucenou prezentaci. |
| **Sluchově postižené**<br /><br /> Požaduje se |**xs:int** |Nastavte tento atribut na 1, který označuje, že tato stopa je pro uživatele, kteří jsou nedoslýchaví. |
| **Zrakově postižené**<br /><br /> Požaduje se |**xs:int** |Nastavte tento atribut na 1, který označuje, že tato stopa je pro zrakově postižené. |
| **Čisté efekty**<br /><br /> Požaduje se |**xs: int** |Nastavte tento atribut na hodnotu 1, která označuje, že tato stopa má čisté účinky. |
| **AttachedPic**<br /><br /> Požaduje se |**xs: int** |Nastavte tento atribut na 1, který označuje, že tato stopa má obrázky. |

## <a name="programs-element"></a><a name="Programs"></a>Prvek Programy
Prvek obálky, který obsahuje více prvků **programu.**  

### <a name="child-elements"></a>Podřízené prvky
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" maxOccurs="bez meze" |[Typ programu](media-services-input-metadata-schema.md#ProgramType) |Pro soubory datových zdrojů, které jsou ve formátu MPEG-TS, obsahuje informace o programech v souboru datových zdrojů. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a>VideoTracks prvek
 Element obálky, který obsahuje více prvků **VideoTrack.**  

 Viz příklad XML na konci tohoto článku: [Příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Podřízené prvky
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="bez meze" |[VideoTrackType (dědí z TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Obsahuje informace o stopách videa v souboru datových zdrojů. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a>Prvek AudioTracks
 Element obálky, který obsahuje více prvků **AudioTrack.**  

 Viz příklad XML na konci tohoto článku: [Příklad XML](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>elementy
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="bez meze" |[AudioTrackType (dědí z TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Obsahuje informace o zvukových stopách v souboru datových zdrojů. |

## <a name="schema-code"></a><a name="code"></a>Kód schématu
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


## <a name="xml-example"></a><a name="xml"></a>Příklad XML
Následuje příklad souboru vstupních metadat.  

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

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

