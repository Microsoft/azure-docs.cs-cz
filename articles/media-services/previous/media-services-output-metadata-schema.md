---
title: Azure Media Services výstup metadat schématu | Microsoft Docs
description: Téma nabízí přehled Azure Media Services výstup metadat schématu.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.openlocfilehash: 4babceb9454a229903c54aab7083c5e5ed138b8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="output-metadata"></a>Výstup metadat
## <a name="overview"></a>Přehled
Kódování úlohy jsou přiřazeny prostředek vstupní (nebo prostředky) na který chcete provést některé úlohy kódování. Můžete například kódovat soubor MP4 do sad H.264 MP4 s adaptivní přenosovou rychlostí; Vytvořte na miniaturu; Vytvořte překryvy. Po dokončení úlohy se vytvářejí výstupní asset.  Výstupní asset obsahuje video, zvuk, miniatur, atd. Výstupní asset obsahuje také soubor s metadata o výstupní asset. Název souboru XML metadat má následující formát: &lt;source_file_name&gt;_manifest.xml (například BigBuckBunny_manifest.xml).  

Pokud chcete zkontrolovat soubor metadat, můžete vytvořit **SAS** Lokátor a stahování souborů do místního počítače.  

Tento článek popisuje elementy a typy schématu XML, na kterém metada výstup (&lt;source_file_name&gt;_manifest.xml) je založena. Informace o souboru, který obsahuje metadata o vstupní asset najdete v tématu [vstupní Metadata](media-services-input-metadata-schema.md).  

> [!NOTE]
> Můžete najít kód úplné schéma a ukázkový kód XML na konci tohoto článku.  
>
>

## <a name="AssetFiles "></a> Kořenový element AssetFiles
Kolekce položek AssetFile pro úlohy kódování.  

### <a name="child-elements"></a>Podřízené elementy
| Název | Popis |
| --- | --- |
| **AssetFile**<br/><br/> Hodnota minOccurs = maxOccurs "0" = "1" |[AssetFile element](media-services-output-metadata-schema.md) který je součástí AssetFiles kolekce. |

## <a name="AssetFile "></a> AssetFile element
Příklad XML můžete nalézt [ukázkový kód XML](media-services-output-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Název**<br/><br/> Požaduje se |**xs:String** |Název souboru asset média. |
| **Velikost**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:Long** |Velikost souboru assetu v bajtech. |
| **Doba trvání**<br/><br/> Požaduje se |**xs** |Obsahu play back doba trvání. |

### <a name="child-elements"></a>Podřízené elementy
| Název | Popis |
| --- | --- |
| **Zdroje** |Kolekce vstup nebo zdrojové soubory médií, který byl zpracován, aby bylo možné vytvořit tento AssetFile. Další informace najdete v tématu [Source element](media-services-output-metadata-schema.md). |
| **VideoTracks**<br/><br/> Hodnota minOccurs = maxOccurs "0" = "1" |Každý fyzický AssetFile může obsahovat v ní nula nebo více videa sleduje prokládaný do formátu odpovídajícího kontejneru. Další informace najdete v tématu [VideoTracks element](media-services-output-metadata-schema.md). |
| **AudioTracks**<br/><br/> Hodnota minOccurs = maxOccurs "0" = "1" |Každý fyzický AssetFile může obsahovat v ní nula nebo více zvukových stop prokládaný do formátu odpovídajícího kontejneru. Toto je kolekce těchto zvukových stop. Další informace najdete v tématu [AudioTracks element](media-services-output-metadata-schema.md). |

## <a name="Sources "></a> Zdrojový element
Kolekce vstup nebo zdrojové soubory médií, který byl zpracován, aby bylo možné vytvořit tento AssetFile.  

Příklad XML můžete nalézt [ukázkový kód XML](media-services-output-metadata-schema.md#xml).  

### <a name="child-elements"></a>Podřízené elementy
| Název | Popis |
| --- | --- |
| **Zdroj**<br/><br/> Hodnota minOccurs = "1" maxOccurs = "bez vazby" |Vstup/zdrojový soubor, používá při generování tento prostředek. Další informace najdete v tématu [Source element](media-services-output-metadata-schema.md). |

## <a name="Source "></a> Source element
Vstup/zdrojový soubor, používá při generování tento prostředek.  

Příklad XML můžete nalézt [ukázkový kód XML](media-services-output-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Název**<br/><br/> Požaduje se |**xs:String** |Název souboru vstupního zdroje. |

## <a name="VideoTracks "></a> VideoTracks element
Každý fyzický AssetFile může obsahovat v ní nula nebo více videa sleduje prokládaný do formátu odpovídajícího kontejneru. **VideoTracks** element představuje kolekci video sleduje.  

Příklad XML můžete nalézt [ukázkový kód XML](media-services-output-metadata-schema.md#xml).  

### <a name="child-elements"></a>Podřízené elementy
| Název | Popis |
| --- | --- |
| **VideoTrack**<br/><br/> Hodnota minOccurs = "1" maxOccurs = "bez vazby" |V nadřazené AssetFile sledovat konkrétní video. Další informace najdete v tématu [VideoTrack element](media-services-output-metadata-schema.md#VideoTrack). |

## <a name="VideoTrack"></a> VideoTrack element
V nadřazené AssetFile sledovat konkrétní video.  

Příklad XML můžete nalézt [ukázkový kód XML](media-services-output-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **ID**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Index počítaný od nuly toto video sledovat. **Poznámka:** to **Id** není nutně TrackID jako použít v souboru MP4. |
| **FourCC**<br/><br/> Požaduje se |**xs:String** |Video kodeků FourCC kódu. |
| **Profil** |**xs:String** |Profil H264 (platí jenom pro H264 kodeků). |
| **Úroveň** |**xs:String** |Úroveň H264 (platí jenom pro H264 kodeků). |
| **Šířka**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Kódovaný video šířku v pixelech. |
| **Výška**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Kódovaný Výška videa v pixelech. |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:Double** |Zobrazení videa čítači poměr stran. |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:Double** |Jmenovatel grafického poměr stran. |
| **kmitočet snímků**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:decimal** |Měří video obnovovací frekvence ve formátu .3f. |
| **TargetFramerate**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:decimal** |Video obnovovací frekvence přednastavené cíl ve formátu .3f. |
| **Přenosovou rychlostí**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Průměrná přenosová rychlost videa v počítané od AssetFile kilobity za sekundu. Počty jenom datové části Základní datový proud a nezahrnuje balení režii. |
| **TargetBitrate**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Cíle průměrná přenosovou rychlostí pro toto video sledovat, jak si vyžádal prostřednictvím kódování přednastavené v kilobity za sekundu. |
| **MaxGOPBitrate**<br/><br/> minInclusive = "0" |**xs:int** |Maximální počet GOP průměrná přenosovou rychlostí pro tento stopy videa, v kB. |

## <a name="AudioTracks "></a> AudioTracks element
Každý fyzický AssetFile může obsahovat v ní nula nebo více zvukových stop prokládaný do formátu odpovídajícího kontejneru. **AudioTracks** element představuje kolekci těchto zvukových stop.  

Příklad XML můžete nalézt [ukázkový kód XML](media-services-output-metadata-schema.md#xml).  

### <a name="child-elements"></a>Podřízené elementy
| Název | Popis |
| --- | --- |
| **AudioTrack**<br/><br/> Hodnota minOccurs = "1" maxOccurs = "bez vazby" |V nadřazené AssetFile sledovat konkrétní zvukovém souboru. Další informace najdete v tématu [AudioTrack element](media-services-output-metadata-schema.md). |

## <a name="AudioTrack "></a> AudioTrack element
V nadřazené AssetFile sledovat konkrétní zvukovém souboru.  

Příklad XML můžete nalézt [ukázkový kód XML](media-services-output-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **ID**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Index tento zvuk dráhy nule. **Poznámka:** to není nezbytně TrackID jako použít v souboru MP4. |
| **Kodeků** |**xs:String** |Zvuk sledovat kodeků řetězec. |
| **EncoderVersion** |**xs:String** |Řetězec verze volitelné kodér požadované pro EAC3. |
| **Kanály**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Počet zvukových kanálů. |
| **SamplingRate**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Zvuk vzorkovací frekvenci v ukázky za sekundu nebo Hz. |
| **Přenosovou rychlostí**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Průměrná přenosová rychlost zvuku v bitech za sekundu, počítané od AssetFile. Počty jenom datové části Základní datový proud a nezahrnuje balení režii. |
| **BitsPerSample**<br/><br/> minInclusive = "0"<br/><br/> Požaduje se |**xs:int** |Bitů na vzorek pro formát wFormatTag typu. |

### <a name="child-elements"></a>Podřízené elementy
| Název | Popis |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> Hodnota minOccurs = maxOccurs "0" = "1" |Parametry výsledek měření hlasitosti. Další informace najdete v tématu [LoudnessMeteringResultParameters element](media-services-output-metadata-schema.md). |

## <a name="LoudnessMeteringResultParameters "></a> LoudnessMeteringResultParameters element
Parametry výsledek měření hlasitosti.  

Příklad XML můžete nalézt [ukázkový kód XML](media-services-output-metadata-schema.md#xml).  

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs:String** |**Dolby** professional hlasitosti měření verzi development kit. |
| **DialogNormalization**<br/><br/> minInclusive = "-31" maxInclusive = "-1"<br/><br/> Požaduje se |**xs:int** |DialogNormalization vygenerované pomocí DPLM, požadováno pro nastavení LoudnessMetering |
| **IntegratedLoudness**<br/><br/> minInclusive = "-70" maxInclusive = "10"<br/><br/> Požaduje se |**xs:float** |Integrované hlasitosti |
| **IntegratedLoudnessUnit**<br/><br/> Požaduje se |**xs:String** |Integrované hlasitosti jednotky. |
| **IntegratedLoudnessGatingMethod**<br/><br/> Požaduje se |**xs:String** |Identifikátor prostřednictvím brány |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive = "0" maxInclusive = "100" |**xs:float** |Rozpoznávání řeči obsah prostřednictvím programu, jako procentuální hodnotu. |
| **SamplePeak**<br/><br/> Požaduje se |**xs:float** |Ukázka absolutní hodnota ve špičce, od resetování nebo od posledního vymazat, na kanál.  Jednotky jsou dBFS. |
| **SamplePeakUnit**<br/><br/> pevné = "dBFS"<br/><br/> Požaduje se |**xs:anySimpleType** |Ukázka jednotky ve špičce. |
| **TruePeak**<br/><br/> Požaduje se |**xs:float** |Maximální hodnota true ve špičce hodnotu, podle BS.1770 ITU-R-2, od resetování nebo od posledního vymazat na kanál. Jednotky jsou dBTP. |
| **TruePeakUnit**<br/><br/> pevné = "dBTP"<br/><br/> Požaduje se |**xs:anySimpleType** |Jednotka true ve špičce. |

## <a name="schema-code"></a>Schéma kódu
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

Následující kód XML je příklad výstupního souboru metadat.  

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
