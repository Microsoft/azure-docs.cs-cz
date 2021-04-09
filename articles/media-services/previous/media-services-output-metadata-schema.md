---
title: Azure Media Services schéma metadat pro výstup | Microsoft Docs
description: Tento článek obsahuje přehled schématu Azure Media Services výstupní metadata.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 519aa158d60eae97eb4c1b792bcecfc8a6c066e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008271"
---
# <a name="output-metadata"></a>Výstupní metadata

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Přehled
Úloha kódování je přidružená ke vstupnímu assetu (nebo assetům), na kterém chcete provést některé úlohy kódování. Například kódování souboru MP4 do H. 264 datových sad MP4 s adaptivní přenosovou rychlostí; Vytvořte miniaturu. Vytvořte překryvy. Po dokončení úlohy se vytvoří výstupní Asset.  Výstupní Asset obsahuje video, zvuk, miniatury atd. Výstupní Asset obsahuje také soubor s metadaty o výstupním prostředku. Název souboru XML s metadaty má následující formát: &lt; source_file_name &gt;_manifest.xml (například BigBuckBunny_manifest.xml).  

Media Services nesálu kontrolu vstupních assetů za účelem generování metadat. Vstupní metadata se generují jako artefakt, když se v úloze zpracuje vstupní Asset. Proto je tento artefakt zapsán do výstupního prostředku. Pro generování metadat pro vstupní prostředky a výstupní prostředky se používají různé nástroje. Vstupní metadata proto mají trochu jiné schéma než výstupní metadata.

Pokud chcete prošetřit soubor metadat, můžete vytvořit Lokátor **SAS** a stáhnout soubor do místního počítače.  

Tento článek popisuje prvky a typy schématu XML, na kterém je výstup metada ( &lt; source_file_name &gt;_manifest.xml) založen. Informace o souboru, který obsahuje metadata o vstupním assetu, naleznete v tématu Input metadata.  

Úplný kód schématu a příklad XML najdete na konci tohoto článku.  

## <a name="assetfiles-root-element"></a><a name="AssetFiles"></a> Kořenový element AssetFiles
Kolekce záznamů AssetFile pro úlohu kódování.  

### <a name="child-elements"></a>Podřízené prvky
| Název | Description |
| --- | --- |
| **AssetFile**<br/><br/> minOccurs = "0" maxOccurs = "1" |Element AssetFile, který je součástí kolekce AssetFiles. |

## <a name="assetfile-element"></a><a name="AssetFile"></a> Element AssetFile
Příklad XML příklad XML najdete v [příkladu.](#xml)  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Název**<br/><br/> Vyžadováno |**xs: String** |Název souboru mediálního prostředku |
| **Velikost**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: Long** |Velikost souboru prostředků v bajtech |
| **Doba trvání**<br/><br/> Vyžadováno |**xs: Duration** |Doba přehrávání obsahu |

### <a name="child-elements"></a>Podřízené prvky
| Název | Description |
| --- | --- |
| **zdroje** |Kolekce vstupních/zdrojových mediálních souborů, které byly zpracovány za účelem vytvoření tohoto AssetFile. Další informace naleznete v tématu Source element. |
| **VideoTracks**<br/><br/> minOccurs = "0" maxOccurs = "1" |Každý fyzický AssetFile může v něm obsahovat nula nebo více videí, které se pronechají v příslušném formátu kontejneru. Další informace naleznete v tématu VideoTracks element. |
| **AudioTracks**<br/><br/> minOccurs = "0" maxOccurs = "1" |Každý fyzický AssetFile může v něm obsahovat nula nebo více zvukových stop, které se pronechají v příslušném formátu kontejneru. Toto je kolekce všech těchto zvukových stop. Další informace naleznete v tématu AudioTracks element. |

## <a name="sources-element"></a><a name="Sources"></a> Sources – element
Kolekce vstupních/zdrojových mediálních souborů, které byly zpracovány za účelem vytvoření tohoto AssetFile.  

Příklad XML příklad XML najdete v [příkladu.](#xml)  

### <a name="child-elements"></a>Podřízené prvky
| Název | Description |
| --- | --- |
| **Zdroj**<br/><br/> minOccurs = "1" maxOccurs = "Unbounded" |Vstupní a zdrojový soubor, který se používá při generování tohoto prostředku. Další informace naleznete v tématu Source element. |

## <a name="source-element"></a><a name="Source"></a> Zdrojový element
Vstupní a zdrojový soubor, který se používá při generování tohoto prostředku.  

Příklad XML příklad XML najdete v [příkladu.](#xml)  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Název**<br/><br/> Vyžadováno |**xs: String** |Název vstupního zdrojového souboru |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> Element VideoTracks
Každý fyzický AssetFile může v něm obsahovat nula nebo více videí, které se pronechají v příslušném formátu kontejneru. Element **VideoTracks** reprezentuje kolekci všech stop videa.  

Příklad XML příklad XML najdete v [příkladu.](#xml)  

### <a name="child-elements"></a>Podřízené prvky
| Název | Description |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs = "1" maxOccurs = "Unbounded" |Konkrétní stopa videa v nadřazené AssetFile Další informace naleznete v tématu VideoTrack element. |

## <a name="videotrack-element"></a><a name="VideoTrack"></a> Element VideoTrack
Konkrétní stopa videa v nadřazené AssetFile  

Příklad XML příklad XML najdete v [příkladu.](#xml)  

### <a name="attributes"></a>Atributy
| Název | Typ | Description |
| --- | --- | --- |
| **Účet**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: int** |Index tohoto videa na základě nuly **Poznámka:**  Toto **ID** nemusí nutně být TrackID, jak se používá v souboru MP4. |
| **FourCC**<br/><br/> Vyžadováno |**xs: String** |FourCCový kodek pro video. |
| **Profil** |**xs: String** |Profil H264 (platí jenom pro kodek H264). |
| **Obsah** |**xs: String** |Úroveň H264 (platí pouze pro kodek H264). |
| **Width (Šířka)**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: int** |Zakódovaná Šířka videa v pixelech |
| **Height (Výška)**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: int** |Výška kódovaného videa v pixelech |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: Double** |Čítač zobrazení poměru stran videa |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: Double** |Jmenovatel poměru stran zobrazení videa |
| **Framerate**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: Decimal** |Měřená snímková frekvence videa ve formátu. 3F. |
| **TargetFramerate**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: Decimal** |V formátu. 3F se přednastavená míra snímků cíle videa. |
| **Rychlostí**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: int** |Průměrná přenosová rychlost videa v kilobitech za sekundu, jak se počítá z AssetFile. Počítá jenom základní datovou část datového proudu a nezahrnuje režijní náklady na balení. |
| **TargetBitrate**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: int** |Průměrná přenosová rychlost pro tuto stopu videa, jak je požadováno prostřednictvím předvolby kódování v kilobitech za sekundu. |
| **MaxGOPBitrate**<br/><br/> minInclusive = "0" |**xs: int** |Maximální přenosová rychlost skupinu GOP pro tuto stopu videa v kilobitech za sekundu |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> Element AudioTracks
Každý fyzický AssetFile může v něm obsahovat nula nebo více zvukových stop, které se pronechají v příslušném formátu kontejneru. Element **AudioTracks** reprezentuje kolekci všech těchto zvukových stop.  

Příklad XML příklad XML najdete v [příkladu.](#xml)  

### <a name="child-elements"></a>Podřízené prvky
| Název | Description |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs = "1" maxOccurs = "Unbounded" |Konkrétní záznam zvuku v nadřazené AssetFile. Další informace naleznete v tématu AudioTrack element. |

## <a name="audiotrack-element"></a><a name="AudioTrack"></a> Element AudioTrack
Konkrétní záznam zvuku v nadřazené AssetFile.  

Příklad XML příklad XML najdete v [příkladu.](#xml)  

### <a name="attributes"></a>Atributy
| Název | Typ | Description |
| --- | --- | --- |
| **Účet**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: int** |Index této zvukové stopy založený na nule. **Poznámka:**  To není nutně TrackID, jak se používá v souboru MP4. |
| **Kodek** |**xs: String** |Řetězec kodeku zvukového záznamu |
| **EncoderVersion** |**xs: String** |Volitelný řetězec verze kodéru vyžadovaný pro EAC3. |
| **Kanály**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: int** |Počet zvukových kanálů. |
| **SamplingRate**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: int** |Vzorkovací frekvence zvuku v ukázkách/s nebo Hz. |
| **Rychlostí**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: int** |Průměrná rychlost zvukového bitu v bitech za sekundu, jak se počítá z AssetFile. Počítá jenom základní datovou část datového proudu a nezahrnuje režijní náklady na balení. |
| **BitsPerSample**<br/><br/> minInclusive = "0"<br/><br/> Vyžadováno |**xs: int** |Bity na vzorek pro typ formátu wFormatTag |

### <a name="child-elements"></a>Podřízené prvky
| Název | Description |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs = "0" maxOccurs = "1" |Parametry výsledku měření nahlasu. Další informace naleznete v tématu LoudnessMeteringResultParameters element. |

## <a name="loudnessmeteringresultparameters-element"></a><a name="LoudnessMeteringResultParameters"></a> Element LoudnessMeteringResultParameters
Parametry výsledku měření nahlasu.  

Příklad XML příklad XML najdete v [příkladu.](#xml)  

### <a name="attributes"></a>Atributy
| Název | Typ | Description |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs: String** |Verze vývojové sady pro měření formátu **Dolby** Professional |
| **DialogNormalization**<br/><br/> minInclusive = "-31" maxInclusive = "-1"<br/><br/> Vyžadováno |**xs: int** |DialogNormalization generované prostřednictvím DPLM, pokud je nastavená LoudnessMetering |
| **IntegratedLoudness**<br/><br/> minInclusive = "-70" maxInclusive = "10"<br/><br/> Vyžadováno |**xs: float** |Integrovaná hlasitost |
| **IntegratedLoudnessUnit**<br/><br/> Vyžadováno |**xs: String** |Integrovaná jednotka hlasitého navýšení. |
| **IntegratedLoudnessGatingMethod**<br/><br/> Vyžadováno |**xs: String** |Identifikátor pro vyuzavírání |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive = "0" maxInclusive = "100" |**xs: float** |Nahlaste obsah přes program jako procento. |
| **SamplePeak**<br/><br/> Vyžadováno |**xs: float** |Špička absolutní ukázkové hodnoty, od resetu nebo od posledního vymazání podle kanálu.  Jednotky jsou dBFS. |
| **SamplePeakUnit**<br/><br/> fixed = "dBFS"<br/><br/> Vyžadováno |**xs: anySimpleType** |Ukázková jednotka špičky. |
| **TruePeak**<br/><br/> Vyžadováno |**xs: float** |Maximální hodnota skutečné špičky, která je podle ITU-R BS. 1770-2, od resetu nebo od posledního vymazání za kanál. Jednotky jsou dBTP. |
| **TruePeakUnit**<br/><br/> fixed = "dBTP"<br/><br/> Vyžadováno |**xs: anySimpleType** |True jednotka špičky. |

## <a name="schema-code"></a>Kód schématu
```xml
<?xml version="1.0" encoding="utf-8"?>  
<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
            targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
            elementFormDefault="qualified">  
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
              <xs:element name="Sources">  
                <xs:annotation>  
                  <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
                        <xs:attribute name="Name" type="xs:string" use="required">  
                          <xs:annotation>  
                            <xs:documentation>input source file name</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                      </xs:complexType>  
                    </xs:element>  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="VideoTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="VideoTrack" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
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
                        <xs:attribute name="FourCC" type="xs:string" use="required">  
                          <xs:annotation>  
                            <xs:documentation>video codec FourCC code</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="Profile" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="Level" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
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
                        <xs:attribute name="Framerate" use="required">  
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
                        <xs:attribute name="TargetFramerate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:decimal">  
                              <xs:minInclusive value="0"/>  
                              <xs:fractionDigits value="3"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="Bitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="TargetBitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
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
                      </xs:complexType>  
                    </xs:element>  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="AudioTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                </xs:annotation>  
                 <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="AudioTrack" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
                        <xs:sequence>  
                          <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                            <xs:annotation>  
                              <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                            </xs:annotation>  
                            <xs:complexType>  
                              <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                <xs:annotation>  
                                  <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="DialogNormalization" use="required">  
                                <xs:annotation>  
                                  <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:int">  
                                    <xs:minInclusive value="-31"/>  
                                    <xs:maxInclusive value="-1"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudness" use="required">  
                                <xs:annotation>  
                                  <xs:documentation>Integrated loudness</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:float">  
                                    <xs:minInclusive value="-70"/>  
                                    <xs:maxInclusive value="10"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                <xs:annotation>  
                                  <xs:documentation>Gating identifier</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                <xs:annotation>  
                                  <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:float">  
                                    <xs:minInclusive value="0"/>  
                                    <xs:maxInclusive value="100"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                <xs:annotation>  
                                  <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                              </xs:attribute>  
                              <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                <xs:annotation>  
                                  <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                              </xs:attribute>  
                            </xs:complexType>  
                          </xs:element>  
                        </xs:sequence>  
                        <xs:attribute name="Id" use="required">  
                          <xs:annotation>  
                            <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="Codec" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>audio track codec string</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="EncoderVersion" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
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
                        <xs:attribute name="Bitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="BitsPerSample" use="required">  
                          <xs:annotation>  
                            <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
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
            <xs:attribute name="Duration" use="required">  
              <xs:annotation>  
                <xs:documentation>content play back duration</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:duration"/>  
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

Následující kód XML je příkladem výstupního souboru s metadaty.  

```xml
<AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema"   
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
  <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
      <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
</AssetFiles>  
```

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
