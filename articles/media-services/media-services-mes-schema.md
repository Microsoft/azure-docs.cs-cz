---
title: Media Encoder Standard schématu | Microsoft Docs
description: Článek nabízí přehled Media Encoder Standard schématu.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 4c060062-8ef2-41d9-834e-e81e8eafcf2e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.openlocfilehash: e936f5c47abe5bb5531f9af3be48662ea2f48c97
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard schématu
Tento článek popisuje některé prvky a typy schématu XML, na kterém [Media Encoder Standard přednastavení](media-services-mes-presets-overview.md) jsou založené. Článek obsahuje vysvětlení elementů a jejich platné hodnoty.  

## <a name="Preset"></a> Přednastavení (kořenový element)
Definuje předvolby kódování.  

### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **Kódování** |[Kódování](media-services-mes-schema.md#Encoding) |Kořenový element udává, že kódovaný vstupního zdroje. |
| **Výstupy** |[Výstupy](media-services-mes-schema.md#Output) |Kolekce požadovaný výstupní soubory. |

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Verze**<br/><br/> Požaduje se |**xs: decimal** |Přednastavené verze. Platí následující omezení: hodnota xs:fractionDigits = "1" a xs:minInclusive value = "1", například **verze = "1.0"**. |

## <a name="Encoding"></a> Kódování
Obsahuje posloupnost následující prvky:  

### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Nastavení kódování H.264 videa. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Nastavení pro AAC kódování zvukovém souboru. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Nastavení pro obrázku Bpm na obrázek. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Nastavení pro obrázek Png. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Nastavení pro bitovou kopii Jpg. |

## <a name="H264Video"></a> H264Video
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:Boolean** |V současné době je podporováno pouze jeden průchodu kódování. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:time** |Určuje pevné mezery mezi IDR rámce v jednotkách sekund. Také označuje jako GOP doba trvání. V tématu **SceneChangeDetection** řízení, zda kodér může lišit od tuto hodnotu. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "false" |**xs: logická hodnota** |Pokud nastaven na hodnotu true, kodér pokouší rozpoznat změnu scény ve videu a vloží již IDR rámečku. |
| **Složitost**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "Rovnováha" |**xs:String** |Ovládací prvky o kompromisu mezi kódování kvality rychlost a video. Může mít jednu z následujících hodnot: **rychlost**, **Rovnováha**, nebo **kvality**<br/><br/> Výchozí hodnota: **vyrovnáváním** |
| **SyncMode**<br/><br/> minOccurs="0" | |Funkce se zveřejní v budoucí verzi. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Kolekce vrstev výstupu videa. |

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs:String** | Pokud je vstupní žádné video, můžete vynutit kodér vložit černobílý video sledovat. K tomu použít podmínku = "InsertBlackIfNoVideoBottomLayerOnly" (Chcete-li vložit video na pouze nejnižší přenosovou rychlostí) nebo podmínku = "InsertBlackIfNoVideo" (Vložit video vůbec výstup přenosových rychlostí). Další informace najdete v [tomto](media-services-advanced-encoding-with-mes.md#no_video) článku.|

## <a name="H264Layers"></a> H264Layers

Ve výchozím nastavení Pokud odesíláte vstup kodéru, který obsahuje pouze zvuk a obraz, výstupní asset obsahuje soubory s pouze zvuk data. Některé přehrávače nemusí být schopná zpracovat takové výstupní datové proudy. Můžete použít H264Video **InsertBlackIfNoVideo** atribut nastavení vynucení kodér přidat video sledovat ve výstupu v tomto scénáři. Další informace najdete v [tomto](media-services-advanced-encoding-with-mes.md#no_video) článku.
              
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Kolekce H264 vrstvy. |

## <a name="H264Layer"></a> H264Layer
> [!NOTE]
> Video omezení jsou založené na hodnotách, které jsou popsané v [H264 úrovně](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) tabulky.  
> 
> 

### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "Auto" |**xs: řetězec** |Může být jeden z následujících **xs: řetězec** hodnoty: **automaticky**, **směrného plánu**, **hlavní**, **vysokou**. |
| **Úroveň**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "Auto" |**xs: řetězec** | |
| **Přenosovou rychlostí**<br/><br/> minOccurs="0" |**xs:int** |Přenosovou rychlostí, použít pro tuto vrstvu videa zadaný v kb/s. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |Maximální přenosová použít pro tuto vrstvu videa zadaný v kb/s. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "00: 00:05" |**xs: čas** |Délka video vyrovnávací paměti. |
| **Width**<br/><br/> minOccurs="0" |**xs: int** |Šířka rámečku video výstup v pixelech.<br/><br/> V současné době musíte zadat, šířku a výšku. Šířka a výška muset být sudé číslo. |
| **Výška**<br/><br/> minOccurs="0" |**xs:int** |Výška rámečku video výstup v pixelech.<br/><br/> V současné době musíte zadat, šířku a výšku. Šířka a výška muset být sudé číslo.|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |Počet snímků B mezi odkaz snímky. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "3" |**xs:int** |Počet snímků odkaz v GOP. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "Cabac" |**xs: řetězec** |Může mít jednu z následujících hodnot: **Cabac** a **Cavlc**. |
| **FrameRate**<br/><br/> minOccurs="0" |racionální číslo |Určuje obnovovací frekvence výstupu videa. Použijte výchozí nastavení 0 nebo 1"Pokud chcete, aby služba kodér použít stejné obnovovací frekvence jako vstup videa. Povolené hodnoty jsou očekávány běžné frekvenci snímků videa. Libovolný platný rozumné je však povolena. Například 1 nebo 1 by 1 fps a je platný.<br/><br/> -12/1 (12 fps)<br/><br/> -15/1 (15 fps)<br/><br/> -24/1 (24 fps)<br/><br/> 24000/1001 (23.976 fps)<br/><br/> -25/1 (25 fps)<br/><br/>  -30/1 (30 fps)<br/><br/> 30000/1001 (29,97 fps) <br/> <br/>**Poznámka:** při vytváření vlastní předvolbu pro více přenosovými rychlostmi kódování, pak všechny vrstvy přednastavení **musí** použít stejnou hodnotu kmitočet snímků.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: logická hodnota** |Zkopírujte z Azure media encoder |
| **Řezy**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "0" |**xs:int** |Určuje, kolik řezy rámeček je rozdělené do. Doporučujeme používat výchozí. |

## <a name="AACAudio"></a> AACAudio
 Obsahuje posloupnost následující prvky a skupiny.  

 Další informace o AAC najdete v tématu [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0 "<br/><br/> Výchozí = "AACLC" |**xs: řetězec** |Může mít jednu z následujících hodnot: **AACLC**, **HEAACV1**, nebo **HEAACV2**. |

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs: řetězec** |Chcete-li vynutit kodér k vytvoření asset, který obsahuje tichou zvuk sledovat, kdy se vstup neobsahuje žádný zvuk, zadejte hodnotu "InsertSilenceIfNoAudio".<br/><br/> Ve výchozím nastavení Pokud odesíláte vstup kodéru, který obsahuje pouze video a žádné zvuk, pak výstupní asset obsahuje soubory, které obsahují pouze video data. Některé přehrávače nemusí být schopná zpracovat takové výstupní datové proudy. Toto nastavení slouží k vynucení kodér přidat tichou zvuk sledovat ve výstupu v tomto scénáři. |

### <a name="groups"></a>Skupiny
| Referenční informace | Popis |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Popis [AudioGroup](media-services-mes-schema.md#AudioGroup) vědět odpovídající počet kanálů, vzorkovací frekvenci a přenosovou rychlost, která může být nastavena pro každý profil. |

## <a name="AudioGroup"></a> AudioGroup
Podrobnosti o tom, jaké hodnoty jsou platné pro každý profil najdete v tabulce "Zvuk kodeků podrobnosti", který následuje.  

### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **Kanály**<br/><br/> minOccurs="0" |**xs: int** |Počet zvukových kanálů kódování. Platné možnosti jsou následující: 1, 2, 5, 6, 8.<br/><br/> Výchozí: 2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**xs: int** |Zvuk vzorkovací frekvenci zadaný v Hz. |
| **Přenosovou rychlostí**<br/><br/> minOccurs="0" |**xs: int** |Přenosovou rychlostí, používá při kódování zvuk, zadané v kb/s. |

### <a name="audio-codec-details"></a>Podrobnosti o zvukových kodeků
Zvukových kodeků|Podrobnosti  
-----------------|---  
**AACLC**|1:<br/><br/> -11025: 8 &lt;= přenosovou rychlostí &lt; 16<br/><br/> -12000: 8 &lt;= přenosovou rychlostí &lt; 16<br/><br/> -16000: 8 &lt;= přenosovou rychlostí &lt;32.<br/><br/>-22050: 24 &lt;= přenosovou rychlostí &lt; 32.<br/><br/> -24000: 24 &lt;= přenosovou rychlostí &lt; 32.<br/><br/> -32000: 32 &lt;= přenosovou rychlostí &lt;= 192<br/><br/> -44100: 56 &lt;= přenosovou rychlostí &lt;= 288<br/><br/> -48000: 56 &lt;= přenosovou rychlostí &lt;= 288<br/><br/> -88200: 128 &lt;= přenosovou rychlostí &lt;= 288<br/><br/> -96000: 128 &lt;= přenosovou rychlostí &lt;= 288<br/><br/> 2:<br/><br/> -11025: 16 &lt;= přenosovou rychlostí &lt; 24<br/><br/> -12000: 16 &lt;= přenosovou rychlostí &lt; 24<br/><br/> -16000: 16 &lt;= přenosovou rychlostí &lt; 40<br/><br/> -22050: 32 &lt;= přenosovou rychlostí &lt; 40<br/><br/> -24000: 32 &lt;= přenosovou rychlostí &lt; 40<br/><br/> -32000: 40 &lt;= přenosovou rychlostí &lt;= 384<br/><br/> -44100: 96 &lt;= přenosovou rychlostí &lt;= 576<br/><br/> -48000: 96 &lt;= přenosovou rychlostí &lt;= 576<br/><br/> -88200: 256 &lt;= přenosovou rychlostí &lt;= 576<br/><br/> -96000: 256 &lt;= přenosovou rychlostí &lt;= 576<br/><br/> 5/6:<br/><br/> -32000: 160 &lt;= přenosovou rychlostí &lt;= 896<br/><br/> -44100: 240 &lt;= přenosovou rychlostí &lt;= 1024<br/><br/> -48000: 240 &lt;= přenosovou rychlostí &lt;= 1024<br/><br/> -88200: 640 &lt;= přenosovou rychlostí &lt;= 1024<br/><br/> -96000: 640 &lt;= přenosovou rychlostí &lt;= 1024<br/><br/> 8:<br/><br/> -32000: 224 &lt;= přenosovou rychlostí &lt;= 1024<br/><br/> -44100: 384 &lt;= přenosovou rychlostí &lt;= 1024<br/><br/> -48000: 384 &lt;= přenosovou rychlostí &lt;= 1024<br/><br/> -88200: 896 &lt;= přenosovou rychlostí &lt;= 1024<br/><br/> -96000: 896 &lt;= přenosovou rychlostí &lt;= 1024  
**HEAACV1**|1:<br/><br/> -22050: přenosovou rychlostí = 8<br/><br/> -24000: 8 &lt;= přenosovou rychlostí &lt;= 10<br/><br/> -32000: 12 &lt;= přenosovou rychlostí &lt;= 64<br/><br/> -44100: 20 &lt;= přenosovou rychlostí &lt;= 64<br/><br/> -48000: 20 &lt;= přenosovou rychlostí &lt;= 64<br/><br/> -88200: přenosovou rychlostí = 64<br/><br/> 2:<br/><br/> -32000: 16 &lt;= přenosovou rychlostí &lt;= 128<br/><br/> -44100: 16 &lt;= přenosovou rychlostí &lt;= 128<br/><br/> -48000: 16 &lt;= přenosovou rychlostí &lt;= 128<br/><br/> -88200: 96 &lt;= přenosovou rychlostí &lt;= 128<br/><br/> -96000: 96 &lt;= přenosovou rychlostí &lt;= 128<br/><br/> 5/6:<br/><br/> -32000: 64 &lt;= přenosovou rychlostí &lt;= 320<br/><br/> -44100: 64 &lt;= přenosovou rychlostí &lt;= 320<br/><br/> -48000: 64 &lt;= přenosovou rychlostí &lt;= 320<br/><br/> -88200: 256 &lt;= přenosovou rychlostí &lt;= 320<br/><br/> -96000: 256 &lt;= přenosovou rychlostí &lt;= 320<br/><br/> 8:<br/><br/> -32000: 96 &lt;= přenosovou rychlostí &lt;= 448<br/><br/> -44100: 96 &lt;= přenosovou rychlostí &lt;= 448<br/><br/> -48000: 96 &lt;= přenosovou rychlostí &lt;= 448<br/><br/> -88200: 384 &lt;= přenosovou rychlostí &lt;= 448<br/><br/> -96000: 384 &lt;= přenosovou rychlostí &lt;= 448  
**HEAACV2**|2:<br/><br/> -22050: 8 &lt;= přenosovou rychlostí &lt;= 10<br/><br/> -24000: 8 &lt;= přenosovou rychlostí &lt;= 10<br/><br/> -32000: 12 &lt;= přenosovou rychlostí &lt;= 64<br/><br/> -44100: 20 &lt;= přenosovou rychlostí &lt;= 64<br/><br/> -48000: 20 &lt;= přenosovou rychlostí &lt;= 64<br/><br/> -88200: 64 &lt;= přenosovou rychlostí &lt;= 64  
  

## <a name="Clip"></a> Clip
### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **StartTime** |**xs** |Určuje počáteční čas prezentace. Hodnota čas spuštění musí odpovídat absolutní časová razítka vstupní videa. Například pokud první snímek vstupní video má časovým razítkem 12:00:10.000, pak čas spuštění by měl alespoň 12:00:10.000 nebo vyšší. |
| **Doba trvání** |**xs** |Určuje dobu trvání prezentace (například vzhled překrytí v video). |

## <a name="Output"></a> Výstup
### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **FileName** |**xs:String** |Název výstupního souboru.<br/><br/> Makra popsané v následující tabulce můžete použít k vytvoření názvy výstupního souboru. Příklad:<br/><br/> **"Výstupy": [{"Název souboru": "{Basename}*{řešení}*.mp4 {přenosovou rychlostí}", "Format": {"Typ": "MP4Format"}}]** |

### <a name="macros"></a>Makra
| Macro | Popis |
| --- | --- |
| **{Basename}** |Pokud byste VoD kódování, {Basename} je nejdřív 32 znaků AssetFile.Name vlastnosti primárního souboru v vstupní prostředku.<br/><br/> Pokud vstupní asset za provozu archivu, {Basename} je odvozená od trackName atributy v manifestu serveru. Pokud jste odeslali úlohu subclip pomocí TopBitrate, jako v: "< VideoStream\>TopBitrate < / VideoStream\>" a výstupní soubor obsahuje video a potom {Basename} je prvních 32 znaků trackName video vrstvy s nejvyšší přenosovou rychlostí.<br/><br/> Pokud místo toho jsou odesílání úlohu subclip pomocí všechny vstupní přenosových rychlostí, například "< VideoStream\>* < / VideoStream\>" a výstupní soubor obsahuje video a potom {Basename} je prvních 32 znaků trackName odpovídající video vrstvy. |
| **{Kodeků}** |Mapuje pro video "H264" a "AAC" pro zvukovém souboru. |
| **{Bitrate}** |Video přenosovou cíl Pokud výstupní soubor obsahuje video a zvuk nebo zvuk přenosovou rychlostí cíl, pokud výstupní soubor obsahuje pouze zvuk. Hodnota použitá je přenosovou rychlostí v kb/s. |
| **{Channel}** |Pokud soubor obsahuje zvuk zvuk kanál počet. |
| **{Šířka}** |Šířka videa v pixelech, ve výstupním souboru, pokud soubor obsahuje video. |
| **{Výška}** |Výška videa v pixelech, ve výstupním souboru, pokud soubor obsahuje video. |
| **{Rozšíření}** |Dědí z vlastnosti "Type" pro výstupní soubor. Název výstupního souboru má příponu, což je jedna z: "mp4", "ts", "jpg", "png" nebo "bmp". |
| **{Index}** |Povinné pro miniaturu. Mají jenom přítomen jednou. |

## <a name="Video"></a> Video (komplexní typ dědí od kodeků)
### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Start** |**xs:String** | |
| **Krok** |**xs:String** | |
| **rozsah** |**xs:String** | |
| **PreserveResolutionAfterRotation** |**xs:Boolean** |Podrobné vysvětlení, najdete v následující části: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Doporučuje se použít **PreserveResolutionAfterRotation** příznak v kombinaci s hodnotami řešení vyjádřené v procentech (šířka = "100 %", výška = "100 %").  

Ve výchozím nastavení řešení kódovat (šířka a výška) v přednastavení Media Encoder Standard (MES) cílí na videa s otočení 0 stupňů. Například pokud vaše vstupní video 1280 × 720 s otočení nula stupňů, pak jsou výchozí přednastavení zajistěte, aby výstup stejné rozlišení.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Pokud vstupní video má zaznamenala s nenulovou hodnotou otočení (například smartphone nebo tabletu uchovávat svisle), pak MES ve výchozím nastavení platí nastavení překladu kódovat (šířka a výška) pro vstup videa a pak kompenzovat otočení. Například najdete na obrázku, který následuje dále. Přednastavení používá Width = "100 %", výška = "100 %", který MES interpretuje jako vyžadování výstup 1 280 pixelů na šířku a výšku 720 pixelů. Po rotaci videa, pak zaplňování na obrázku a nevejde se do okna, což pillar-box oblasti vlevo a vpravo.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Alternativně můžete použít použití **PreserveResolutionAfterRotation** příznak a nastavte na hodnotu "PRAVDA" (výchozí hodnota je "false"). Takže pokud má vaše přednastavených šířku = "100 %", výška = "100 %" a PreserveResolutionAfterRotation nastavena na hodnotu true, vstupní video, což je 1 280 pixelů a 720 pixelů výšku s otočení 90 stupňů vytváří výstup s otočení stupeň nula, ale 720 pixelů široké a 1280 Vysoký pixelů. Viz následující obrázek:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="FormatGroup"></a> FormatGroup (skupiny)
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Element
| Název | Typ | Popis |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Výška**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs:String** | |

## <a name="PngLayer"></a> PngLayer
### <a name="element"></a>Element
| Název | Typ | Popis |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Výška**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs:String** | |

## <a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Element
| Název | Typ | Popis |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Výška**<br/><br/> minOccurs="0" |**xs:int** | |
| **Kvality**<br/><br/> minOccurs="0" |**xs:int** |Platné hodnoty: 1(worst)-100(best) |

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs:String** | |

## <a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="BmpImage"></a> BmpImage (komplexní typ dědí od Video)
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG vrstev |

## <a name="JpgImage"></a> JpgImage (komplexní typ dědí od Video)
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG vrstev |

## <a name="PngImage"></a> PngImage (komplexní typ dědí od Video)
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG vrstev |

## <a name="examples"></a>Příklady
Najdete příklady přednastavení XML, které jsou vytvořené na základě této schématu, najdete v části [přednastavení úloh pro MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

