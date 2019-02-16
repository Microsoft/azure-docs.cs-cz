---
title: Schéma metadat výstupu pro Azure Media Services | Dokumentace Microsoftu
description: Téma s přehledem Azure Media Services výstupní metadata schématu.
author: Juliako
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: cdcdcff8a5c016cfd3074a950ad6060e55b8b0fe
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312578"
---
# <a name="output-metadata"></a>Výstupní Metadata
## <a name="overview"></a>Přehled
Kódovací úlohy jsou přiřazeny k vstupní prostředek (nebo prostředky) na kterém chcete provést některé úlohy kódování. Například kódování souboru MP4 do sad H.264 MP4 s adaptivní přenosovou rychlostí; vytvořit miniaturu. Vytvořte překrytí. Po dokončení úlohy je vytvořen výstupního prostředku.  Výstupní asset obsahuje video, zvuk, miniatury, atd. Výstupní asset obsahuje také soubor s metadaty o výstupního prostředku. Název souboru XML metadat má následující formát: &lt;source_file_name&gt;_manifest.xml (například BigBuckBunny_manifest.xml).  

Služba Media Services nekontroluje preventivně vstupní prostředky ke generování metadat. Vstupní metadata se vygeneruje pouze jako artefakt při vstupní Asset se zpracovává v rámci úlohy. Proto tento artefakt se zapisují do výstupu Asset. Jiné nástroje se používají pro generování metadat pro prostředky vstupní a výstupní Assety. Vstupní metadata proto má mírně odlišné schéma než výstup metadat.

Pokud chcete zkontrolovat soubor metadat, můžete vytvořit **SAS** Lokátor a stahování souborů do místního počítače.  

Tento článek popisuje elementů a typů schématu XML, na kterém metada výstup (&lt;source_file_name&gt;_manifest.xml) je založena. Informace o souboru, který obsahuje metadata o vstupní asset najdete v tématu vstupní Metadata.  

Můžete najít kód úplné schéma a ukázkový kód XML na konci tohoto článku.  

## <a name="AssetFiles "></a> AssetFiles kořenový element
Kolekce položek AssetFile pro úlohy kódování.  

### <a name="child-elements"></a>Podřízené prvky
| Název | Popis |
| --- | --- |
| **AssetFile**<br/><br/> minOccurs="0" maxOccurs="1" |AssetFile element, který je součástí kolekce AssetFiles. |

## <a name="AssetFile "></a> AssetFile – element
Příklad XML můžete nalézt [ukázkový kód XML](#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Název**<br/><br/> Požaduje se |**xs:string** |Název souboru asset média. |
| **Velikost**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:long** |Velikost souboru prostředku v bajtech. |
| **Doba trvání**<br/><br/> Požaduje se |**xs:duration** |Přehrávání obsahu back doby trvání. |

### <a name="child-elements"></a>Podřízené prvky
| Název | Popis |
| --- | --- |
| **Zdroje** |Kolekce vstupu/zdrojových multimediálních souborů, který byl zpracován cílem vytvořit tento AssetFile. Další informace najdete v tématu Source element. |
| **VideoTracks**<br/><br/> minOccurs="0" maxOccurs="1" |Každý fyzický AssetFile může obsahovat v něm nula nebo více videa sleduje prokládané do formátu odpovídajícího kontejneru. Další informace najdete v tématu VideoTracks elementu. |
| **AudioTracks**<br/><br/> minOccurs="0" maxOccurs="1" |Každý fyzický AssetFile může obsahovat v něm nula nebo více zvukové stopy prokládané do formátu odpovídajícího kontejneru. Jedná se o kolekci tyto zvukové stopy. Další informace najdete v tématu AudioTracks elementu. |

## <a name="Sources "></a> Zdrojový element
Kolekce vstupu/zdrojových multimediálních souborů, který byl zpracován cílem vytvořit tento AssetFile.  

Příklad XML můžete nalézt [ukázkový kód XML](#xml).  

### <a name="child-elements"></a>Podřízené prvky
| Název | Popis |
| --- | --- |
| **Zdroj**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Vstup/zdrojového souboru, které používá při generování tohoto prostředku. Další informace najdete v tématu Source element. |

## <a name="Source "></a> Zdrojový element
Vstup/zdrojového souboru, které používá při generování tohoto prostředku.  

Příklad XML můžete nalézt [ukázkový kód XML](#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Název**<br/><br/> Požaduje se |**xs:string** |Název souboru vstupní zdroj. |

## <a name="VideoTracks "></a> VideoTracks – element
Každý fyzický AssetFile může obsahovat v něm nula nebo více videa sleduje prokládané do formátu odpovídajícího kontejneru. **VideoTracks** element představuje kolekci videa stopy.  

Příklad XML můžete nalézt [ukázkový kód XML](#xml).  

### <a name="child-elements"></a>Podřízené prvky
| Název | Popis |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |V nadřazené AssetFile sledovat konkrétní videa. Další informace najdete v tématu VideoTrack elementu. |

## <a name="VideoTrack"></a> VideoTrack – element
V nadřazené AssetFile sledovat konkrétní videa.  

Příklad XML můžete nalézt [ukázkový kód XML](#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **ID**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Index založený na nule sledovat toto video. **Poznámka:**  To **Id** není nutně TrackID v souboru MP4. |
| **FourCC**<br/><br/> Požaduje se |**xs:string** |Kodek videa FourCC kódu. |
| **Profil** |**xs:string** |Profil H264 (platí jenom pro H264 kodek). |
| **Úroveň** |**xs:string** |Předvolby H264 úrovně (platí jenom pro H264 kodek). |
| **Šířka**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Kódování videa šířka v pixelech. |
| **Výška**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Kódování videa výšku v pixelech. |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:Double** |Čítač poměr stran obrazovky. |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:Double** |Jmenovatel poměr stran obrazovky. |
| **snímkovou**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:decimal** |Měří videa snímkovou frekvenci ve formátu .3f. |
| **TargetFramerate**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:decimal** |Přednastavení videa Snímková frekvence cíl ve formátu .3f. |
| **Bitrate**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Průměrná přenosová rychlost videa v počítané od AssetFile kilobity za sekundu. Vrátí pouze datové části Základní datový proud a nezahrnuje nároky na balení. |
| **TargetBitrate**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Cíl průměrná přenosové rychlosti pro toto video stopu podle požadavku prostřednictvím kódovací předvolby, v kilobity za sekundu. |
| **MaxGOPBitrate**<br/><br/> minInclusive = "0" |**xs:int** |Maximální počet GOP průměrná přenosové rychlosti pro tato videa sledovat v kilobity za sekundu. |

## <a name="AudioTracks "></a> AudioTracks – element
Každý fyzický AssetFile může obsahovat v něm nula nebo více zvukové stopy prokládané do formátu odpovídajícího kontejneru. **AudioTracks** element představuje kolekci tyto zvukové stopy.  

Příklad XML můžete nalézt [ukázkový kód XML](#xml).  

### <a name="child-elements"></a>Podřízené prvky
| Název | Popis |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Konkrétní zvukové stopy v nadřazeném prvku AssetFile. Další informace najdete v tématu AudioTrack elementu. |

## <a name="AudioTrack "></a> AudioTrack – element
Konkrétní zvukové stopy v nadřazeném prvku AssetFile.  

Příklad XML můžete nalézt [ukázkový kód XML](#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **ID**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Index založený na nule této zvuková stopa. **Poznámka:**  To není nutně TrackID v souboru MP4. |
| **Kodek** |**xs:string** |Řetězec kodek zvuková stopa. |
| **EncoderVersion** |**xs:string** |Řetězec verze volitelné kodér, vyžaduje se pro EAC3. |
| **kanály**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Počet zvukových kanálů. |
| **SamplingRate**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Zvukový vzorkovací frekvenci vzorků/s nebo Hz. |
| **Bitrate**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Průměrná zvuku přenosová rychlost v bitech za sekundu, počítané od AssetFile. Vrátí pouze datové části Základní datový proud a nezahrnuje nároky na balení. |
| **BitsPerSample**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Bitů na vzorek formátu wFormatTag typu. |

### <a name="child-elements"></a>Podřízené prvky
| Název | Popis |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs="0" maxOccurs="1" |Parametry výsledek měření rytmu. Další informace najdete v tématu LoudnessMeteringResultParameters elementu. |

## <a name="LoudnessMeteringResultParameters "></a> LoudnessMeteringResultParameters – element
Parametry výsledek měření rytmu.  

Příklad XML můžete nalézt [ukázkový kód XML](#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs:string** |**Dolby** profesionální hlasitost měření verzi development kit. |
| **DialogNormalization**<br/><br/> minInclusive = maxInclusive "-31" = "-1"<br/><br/> Požaduje se |**xs:int** |DialogNormalization vygenerovaných prostřednictvím DPLM vyžadována, je-li nastavit LoudnessMetering |
| **IntegratedLoudness**<br/><br/> minInclusive = "-70" maxInclusive = "10"<br/><br/> Požaduje se |**xs:float** |Integrované hlasitosti |
| **IntegratedLoudnessUnit**<br/><br/> Požaduje se |**xs:string** |Jednotka integrované rytmu. |
| **IntegratedLoudnessGatingMethod**<br/><br/> Požaduje se |**xs:string** |Ověřování věku identifikátor |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive = "0" maxInclusive = "100" |**xs:float** |Mluveného obsahu prostřednictvím programu jako procentuální hodnotu. |
| **SamplePeak**<br/><br/> Požaduje se |**xs:float** |Absolutní Ukázková hodnota ve špičce, od resetování nebo od jejího posledního vymazat, jeden kanál.  Jednotky jsou dBFS. |
| **SamplePeakUnit**<br/><br/> fixed="dBFS"<br/><br/> Požaduje se |**xs:anySimpleType** |Ukázka jednotek ve špičce. |
| **TruePeak**<br/><br/> Požaduje se |**xs:float** |True maximální hodnota, podle BS.1770 ITU-R-2, od resetování nebo od jejího posledního vymazána, jeden kanál. Jednotky jsou dBTP. |
| **TruePeakUnit**<br/><br/> fixed="dBTP"<br/><br/> Požaduje se |**xs:anySimpleType** |Jednotka true ve špičce. |

## <a name="schema-code"></a>Kód schématu
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
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



## <a name="xml"></a> Ukázkový kód XML

Následující kód XML je příkladem výstupního souboru metadat.  

    <AssetFiles xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema"   
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

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
