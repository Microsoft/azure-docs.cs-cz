---
title: Schéma výstupních metadat Azure Media Services | Dokumenty společnosti Microsoft
description: Tento článek poskytuje přehled schématu výstupních metadat Azure Media Services.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 3f0c6b60e2be625d1f869c3eda4acb9dfd3c6e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74886808"
---
# <a name="output-metadata"></a>Výstupní metadata
## <a name="overview"></a>Přehled
Úloha kódování je přidružena ke vstupnímu prostředku (nebo datovým zdrojům), na kterém chcete provést některé úlohy kódování. Můžete například zakódovat soubor MP4 do adaptivních datových tokových sad H.264 MP4; vytvořit miniaturu; vytvářet překryvy. Po dokončení úkolu je vytvořen výstupní majetek.  Výstupní datový zdroj obsahuje video, zvuk, miniatury atd. Výstupní datový zdroj také obsahuje soubor s metadaty o výstupním datovém zdroji. Název souboru XML metadat má následující &lt;formát: source_file_name&gt;_manifest.xml (například BigBuckBunny_manifest.xml).  

Služba Media Services nepreventivně neprohledá vstupní datové zdroje a nevygeneruje metadata. Vstupní metadata jsou generována pouze jako artefakt při zpracování vstupního datového zdroje v úloze. Proto je tento artefakt zapsán do výstupního assetu. Různé nástroje se používají ke generování metadat pro vstupní datové zdroje a výstupní datové zdroje. Vstupní metadata proto má mírně odlišné schéma než výstupní metadata.

Chcete-li soubor metadat prozkoumat, můžete vytvořit lokátor **SAS** a stáhnout soubor do místního počítače.  

Tento článek popisuje prvky a typy schématu XML, na kterém&lt;je&gt;založen výstup metada ( source_file_name _manifest.xml). Informace o souboru, který obsahuje metadata o vstupním datovém zdroji, naleznete v tématu Vstupní metadata.  

Úplný kód schématu a příklad XML najdete na konci tohoto článku.  

## <a name="assetfiles-root-element"></a><a name="AssetFiles"></a>Kořenový prvek AssetFiles
Kolekce položek AssetFile pro úlohu kódování.  

### <a name="child-elements"></a>Podřízené prvky
| Name (Název) | Popis |
| --- | --- |
| **Soubor majetku**<br/><br/> minOccurs="0" maxOccurs="1" |AssetFile prvek, který je součástí AssetFiles kolekce. |

## <a name="assetfile-element"></a><a name="AssetFile"></a>Element AssetFile
Můžete najít příklad [XML XML příklad](#xml).  

### <a name="attributes"></a>Atributy
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Název**<br/><br/> Požaduje se |**xs:řetězec** |Název souboru mediálního datového zdroje. |
| **Velikost**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:dlouhé** |Velikost souboru datového zdroje v bajtech. |
| **Doba trvání**<br/><br/> Požaduje se |**xs:doba trvání** |Doba přehrávání obsahu. |

### <a name="child-elements"></a>Podřízené prvky
| Name (Název) | Popis |
| --- | --- |
| **Zdrojů** |Kolekce vstupních a zdrojových mediálních souborů, které byly zpracovány za účelem vytvoření tohoto souboru AssetFile. Další informace naleznete v tématu Source element. |
| **VideoSkladby**<br/><br/> minOccurs="0" maxOccurs="1" |Každý fyzický AssetFile může obsahovat v něm nula nebo více videí sleduje prokládány do příslušného formátu kontejneru. Další informace naleznete v tématu VideoTracks element. |
| **Zvukové stopy**<br/><br/> minOccurs="0" maxOccurs="1" |Každý fyzický AssetFile může obsahovat v něm nula nebo více zvukových stop prokládány do příslušného formátu kontejneru. Tohle je sbírka všech těch zvukových stop. Další informace naleznete v tématu AudioTracks element. |

## <a name="sources-element"></a><a name="Sources"></a>Zdrojový prvek
Kolekce vstupních a zdrojových mediálních souborů, které byly zpracovány za účelem vytvoření tohoto souboru AssetFile.  

Můžete najít příklad [XML XML příklad](#xml).  

### <a name="child-elements"></a>Podřízené prvky
| Name (Název) | Popis |
| --- | --- |
| **Zdroj**<br/><br/> minOccurs="1" maxOccurs="bez meze" |Vstupní/zdrojový soubor použitý při generování tohoto datového zdroje. Další informace naleznete v tématu Source element. |

## <a name="source-element"></a><a name="Source"></a>Zdrojový prvek
Vstupní/zdrojový soubor použitý při generování tohoto datového zdroje.  

Můžete najít příklad [XML XML příklad](#xml).  

### <a name="attributes"></a>Atributy
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Název**<br/><br/> Požaduje se |**xs:řetězec** |Název vstupního zdrojového souboru. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a>VideoTracks prvek
Každý fyzický AssetFile může obsahovat v něm nula nebo více videí sleduje prokládány do příslušného formátu kontejneru. **VideoTracks** Prvek představuje kolekci všech stop videa.  

Můžete najít příklad [XML XML příklad](#xml).  

### <a name="child-elements"></a>Podřízené prvky
| Name (Název) | Popis |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs="1" maxOccurs="bez meze" |Konkrétní stopa videa v nadřazeném souboru AssetFile. Další informace naleznete v tématu VideoTrack element. |

## <a name="videotrack-element"></a><a name="VideoTrack"></a>VideoTrack prvek
Konkrétní stopa videa v nadřazeném souboru AssetFile.  

Můžete najít příklad [XML XML příklad](#xml).  

### <a name="attributes"></a>Atributy
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **ID**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:int** |Nulový index této video stopy. **Poznámka:**  Toto **ID** není nutně TrackID, jak je použito v souboru MP4. |
| **Fourcc**<br/><br/> Požaduje se |**xs:řetězec** |Video kodek FourCC kód. |
| **Profil** |**xs:řetězec** |Profil H264 (vztahuje se pouze na kodek H264). |
| **Úroveň** |**xs:řetězec** |Úroveň H264 (vztahuje se pouze na kodek H264). |
| **impulzu**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:int** |Zakódovaná šířka videa v pixelech. |
| **Height**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:int** |Zakódovaná výška videa v pixelech. |
| **Čítač DisplayAspectRatioNumerator**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:dvojitá** |Čitateč poměru stran zobrazení videa. |
| **ZobrazitRatioRatioJmenovatel**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:dvojitá** |Jmenovatel poměru stran zobrazení videa. |
| **Framerate**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:desetinné** |Naměřená kmitočet snímků videa ve formátu .3f. |
| **Cílová míra rychlosti snímků**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:desetinné** |Přednastavená cílová kmitočet snímků videa ve formátu .3f. |
| **Datový tok**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:int** |Průměrná přenosová rychlost videa v kilobitech za sekundu, jak je vypočítáno z AssetFile. Počítá pouze datové části elementárnídatový proud a nezahrnuje zatížení obalu. |
| **Cílová bitová rychlost**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:int** |Cílový průměrný přenosový tok pro tuto stopu videa, jak je požadováno prostřednictvím přednastavení kódování, v kilobitech za sekundu. |
| **MaxGOPBitrate**<br/><br/> minInclusive ="0" |**xs:int** |Maximální gop průměrný přenosový tok pro tuto video stopu v kilobitech za sekundu. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a>Prvek AudioTracks
Každý fyzický AssetFile může obsahovat v něm nula nebo více zvukových stop prokládány do příslušného formátu kontejneru. Prvek **AudioTracks** představuje kolekci všech těchto zvukových stop.  

Můžete najít příklad [XML XML příklad](#xml).  

### <a name="child-elements"></a>Podřízené prvky
| Name (Název) | Popis |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs="1" maxOccurs="bez meze" |Konkrétní zvuková stopa v nadřazeném souboru AssetFile. Další informace naleznete v tématu AudioTrack element. |

## <a name="audiotrack-element"></a><a name="AudioTrack"></a>Prvek AudioTrack
Konkrétní zvuková stopa v nadřazeném souboru AssetFile.  

Můžete najít příklad [XML XML příklad](#xml).  

### <a name="attributes"></a>Atributy
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **ID**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:int** |Nulový index této zvukové stopy. **Poznámka:**  To to není nutně TrackID, jak je použito v souboru MP4. |
| **Kodek** |**xs:řetězec** |Zvukový kód trackového kodeku. |
| **Verze kodéru** |**xs:řetězec** |Volitelný řetězec verze kodéru, povinný pro EAC3. |
| **Kanály**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:int** |Počet zvukových kanálů. |
| **Vzorkovací míra**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:int** |Vzorkovací frekvence zvuku ve vzorcích/s nebo Hz. |
| **Datový tok**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:int** |Průměrná přenosová rychlost zvuku v bitech za sekundu, jak je vypočtena z AssetFile. Počítá pouze datové části elementárnídatový proud a nezahrnuje zatížení obalu. |
| **BitsPerSample**<br/><br/> minInclusive ="0"<br/><br/> Požaduje se |**xs:int** |Bity na ukázku pro typ formátu wFormatTag. |

### <a name="child-elements"></a>Podřízené prvky
| Name (Název) | Popis |
| --- | --- |
| **Parametry Parametry Citostiměřiče Výsledků**<br/><br/> minOccurs="0" maxOccurs="1" |Parametry výsledku měření hlasitosti. Další informace naleznete v tématu LoudnessMeteringResultParameters element. |

## <a name="loudnessmeteringresultparameters-element"></a><a name="LoudnessMeteringResultParameters"></a>Prvek LoudnessMeteringResultParameters
Parametry výsledku měření hlasitosti.  

Můžete najít příklad [XML XML příklad](#xml).  

### <a name="attributes"></a>Atributy
| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **DPLMVersionInformace** |**xs:řetězec** |**Dolby** profesionální hlasitost měření vývoj kit verze. |
| **DialogNormalizace**<br/><br/> minInclusive="-31" maxInclusive="-1"<br/><br/> Požaduje se |**xs:int** |DialogNormalizace generované prostřednictvím DPLM, vyžadováno při nastavení hlasitostiMěření |
| **Integrovaná hlasitost**<br/><br/> minInclusive="-70" maxInclusive="10"<br/><br/> Požaduje se |**xs:plovoucí** |Integrovaná hlasitost |
| **IntegratedLoudnessUnit**<br/><br/> Požaduje se |**xs:řetězec** |Integrovaná jednotka hlasitosti. |
| **Integrovaná metoda volání**<br/><br/> Požaduje se |**xs:řetězec** |Identifikátor gatingu |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive ="0" maxInclusive="100" |**xs:plovoucí** |Obsah řeči nad programem v procentech. |
| **SamplePeak**<br/><br/> Požaduje se |**xs:plovoucí** |Maximální absolutní hodnota vzorku od resetování nebo od posledního vymazání na kanál.  Jednotky jsou dBFS. |
| **SamplePeakUnit**<br/><br/> fixed="dBFS"<br/><br/> Požaduje se |**xs:anySimpleType** |Jednotka píku vzorku. |
| **TruePeak**<br/><br/> Požaduje se |**xs:plovoucí** |Maximální hodnota skutečného vrcholu podle ITU-R BS.1770-2 od resetování nebo od posledního vymazání na kanál. Jednotky jsou dBTP. |
| **Jednotka TruePeak**<br/><br/> fixed="dBTP"<br/><br/> Požaduje se |**xs:anySimpleType** |Skutečná špičková jednotka. |

## <a name="schema-code"></a>Kód schématu
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



## <a name="xml-example"></a><a name="xml"></a>Příklad XML

Následující kód XML je příkladem souboru výstupních metadat.  

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

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
