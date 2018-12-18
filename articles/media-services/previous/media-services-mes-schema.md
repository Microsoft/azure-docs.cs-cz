---
title: Schéma Media Encoderu Standard | Dokumentace Microsoftu
description: Tento článek poskytuje přehled schéma Media Encoderu Standard.
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
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: 95f7d5cafa39daccccbd35c44510038d28601aed
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241748"
---
# <a name="media-encoder-standard-schema"></a>Schéma Media Encoderu Standard
Tento článek popisuje některé elementů a typů schématu XML, na kterém [kodéru Media Encoder Standard přednastavení](media-services-mes-presets-overview.md) jsou založeny. Tento článek poskytuje vysvětlení prvků a jejich platné hodnoty.  

## <a name="Preset"></a> Přednastavení (kořenový element)
Definuje přednastavení kódování.  

### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **Kódování** |[Kódování](media-services-mes-schema.md#Encoding) |Kořenový element, označuje, že kódovaný vstupních zdrojů. |
| **Výstupy** |[Výstupy](media-services-mes-schema.md#Output) |Kolekce požadovaný výstupní soubory. |
| **StretchMode**<br/>minOccurs="0"<br/>Výchozí = "AutoSize|xs:String|Ovládací prvek velikost výstup videa rámce, odsazení, pixelů nebo zobrazit poměr stran. **StretchMode** můžou mít jednu z následujících hodnot: **žádný**, **AutoSize** (výchozí), nebo **přizpůsobit**.<br/><br/>**Žádný**: striktně řídit výstupní rozlišení (například **šířka** a **výška** v přednastavení) bez zohlednění poměr stran nebo zobrazení poměru stran u vstupního videa. Doporučujeme ve scénářích, jako [oříznutí](media-services-crop-video.md), kde má odlišný poměr stran porovná se vstupem výstup videa. <br/><br/>**Automaticky přizpůsobit velikost**: výstupní rozlišení, se vejde do okna (šířka * výška) určené přednastavený kontext. Kodér však vytváří výstupní video, který má poměr stran Čtvereček pixel (1:1). Proto, buď výstupní šířku nebo výšku výstupní by mohla být přepsána, aby odpovídala poměr stran vstupu bez odsazení. Pokud vstup je 1920 × 1080 a použitá předvolba kódování vyzve k zadání 1280 x 1280, pak je hodnota výšky v nastavení přepsat a výstup se bude účtovat s 1280 × 720, což zajišťuje vstupní poměr 16:9. <br/><br/>**AutoFit**: v případě potřeby odsadí výstup videa (s letterbox nebo pillarbox) požadovaný výstupní rozlišení přijmout přitom zajistit, že aktivní oblast videa ve výstupu nemá stejný poměr stran jako vstup. Předpokládejme například, vstup je 1920 × 1080 a vyzve k zadání 1280 x 1280 předvolbu kódování. Bude výstup videa na 1 280 x 1280, ale bude obsahovat vnitřní obdélník 1280 × 720 "active videa' s poměrem 16:9 a letterbox oblastech 280 pixelů na výšku v horní a dolní části. Další příklad Pokud vstup je 1440 × 1080 a použitá předvolba kódování vyzve k zadání 1280 × 720 výstup budou v 1280 × 720, která obsahuje vnitřní obdélník 960 × 720 na poměr stran 4:3 a pillar pole oblasti 160 pixelů na šířku na levou a pravou. 

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Verze**<br/><br/> Požaduje se |**xs: decimal** |Předvolby verze. Platí následující omezení: hodnota xs:fractionDigits = "1" a xs:minInclusive value = "1", například **verze = "1.0"**. |

## <a name="Encoding"></a> Kódování
Obsahuje řadu následující prvky:  

### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Nastavení kódování videa H.264. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Nastavení kódování AAC zvuku. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Nastavení obrázku Bpm na obrázek. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Nastavení pro obrázek Png. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Nastavení pro obrázek Jpg. |

## <a name="H264Video"></a> H264Video
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:Boolean** |V současné době se podporuje jenom jednofázové kódování. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**: Time** |Určuje pevné mezery mezi IDR rámce v jednotkách sady sekund. Také označuje jako doba trvání GOP. Zobrazit **SceneChangeDetection** pro řízení, zda kodér může lišit od tuto hodnotu. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "false" |**xs: boolean** |Pokud nastavenou na hodnotu true, kodér pokusí rozpoznat změnu scény ve videu a vloží IDR rámce. |
| **Složitost**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "Rovnováha" |**xs:String** |Ovládací prvky rovnováhu mezi kódování rychlost a video kvality. Můžou mít jednu z následujících hodnot: **rychlost**, **Rovnováha**, nebo **kvality**<br/><br/> Výchozí hodnota: **Balanced** |
| **SyncMode**<br/><br/> minOccurs="0" | |Funkce se zveřejní v budoucí verzi. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Kolekce vrstev výstup videa. |

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs:String** | Pokud vstup má žádná videa, můžete vynutit kodéru pro vložení monochromatický sledovat videa. K tomuto účelu použít podmínku = "InsertBlackIfNoVideoBottomLayerOnly" (pro vložení videa v pouze nejnižší přenosové rychlosti) nebo stav = "InsertBlackIfNoVideo" (pro vložení videa vůbec výstup přenosových rychlostí). Další informace najdete v [tomto](media-services-advanced-encoding-with-mes.md#no_video) článku.|

## <a name="H264Layers"></a> H264Layers

Ve výchozím nastavení Pokud odesíláte vstupní hodnota pro kodér, který obsahuje pouze zvuku a žádné video výstupního prostředku obsahuje soubory s pouze zvuková data. Některé přehrávače nemusí být schopna zpracovávat takové výstupní datové proudy. Můžete použít H264Video **InsertBlackIfNoVideo** atribut nastavení vynucení kodér přidat stopu videa do výstupu v tomto scénáři. Další informace najdete v [tomto](media-services-advanced-encoding-with-mes.md#no_video) článku.
              
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs = "0" maxOccurs = "bez vazby" |[H264Layer](media-services-mes-schema.md#H264Layer) |Kolekce vrstev H264. |

## <a name="H264Layer"></a> H264Layer
> [!NOTE]
> Omezení videa jsou založené na hodnotách je popsáno v [úrovně H264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) tabulky.  
> 
> 

### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "Auto" |**xs: řetězec** |Může být jedna z následujících **xs: řetězec** hodnoty: **automaticky**, **směrného plánu**, **hlavní**, **vysoké**. |
| **Úroveň**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "Auto" |**xs: řetězec** | |
| **S přenosovou rychlostí**<br/><br/> minOccurs="0" |**xs:int** |Přenosové rychlosti pro toto video vrstvy zadané v kb/s. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |Maximální přenosové rychlosti pro toto video vrstvy zadané v kb/s. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "00: 00:05" |**xs: čas** |Délka videa vyrovnávací paměti. |
| **Šířka**<br/><br/> minOccurs="0" |**xs: int** |Šířka rámce výstup videa, v pixelech.<br/><br/> V současné době musíte zadat šířku a výšku. Šířka a výška musí být sudá čísla. |
| **Výška**<br/><br/> minOccurs="0" |**xs:int** |Výška rámečku výstup videa, v pixelech.<br/><br/> V současné době musíte zadat šířku a výšku. Šířka a výška musí být sudá čísla.|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |Počet snímků B mezi snímky odkaz. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "3" |**xs:int** |Počet snímků odkaz v GOP. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "Cabac" |**xs: řetězec** |Můžou mít jednu z následujících hodnot: **Cabac** a **Cavlc**. |
| **Snímkovou**<br/><br/> minOccurs="0" |racionální číslo |Určuje frekvenci snímků výstup videa. Kodér použít stejnou frekvenci snímků jako vstup videa pomocí výchozí hodnota je "0/1". Povolené hodnoty jsou očekávané běžné sazby za snímků. Libovolný platný racionální je však povoleno. Například 1/1 bude 1 snímků za sekundu a je platný.<br/><br/> -12/1 (12 snímků za sekundu)<br/><br/> – 15 nebo 1 (15 snímků za sekundu)<br/><br/> -24/1 (24 snímků za sekundu)<br/><br/> 24000/1001 (23.976 snímků za sekundu)<br/><br/> -25/1 (25 snímků za sekundu)<br/><br/>  -30/1 (30 snímků za sekundu)<br/><br/> 30000/1001 (29,97 snímků za sekundu) <br/> <br/>**Poznámka:** Pokud vytváříte vlastní předvolba kódování s více přenosovými rychlostmi, pak všechny vrstvy Předvolby **musí** použijte stejnou hodnotu Snímkovou.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: boolean** |Zkopírujte z Azure media encoderu |
| **Kolekce obsahuje nějaké řezy**<br/><br/> minOccurs="0"<br/><br/> Výchozí = "0" |**xs:int** |Určuje, kolik řezů se dělí rámec. Doporučujeme používat výchozí. |

## <a name="AACAudio"></a> AACAudio
 Obsahuje řadu následující prvky a skupiny.  

 Další informace o AAC, naleznete v tématu [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0 "<br/><br/> Výchozí = "AACLC" |**xs: řetězec** |Můžou mít jednu z následujících hodnot: **AACLC**, **HEAACV1**, nebo **HEAACV2**. |

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs: řetězec** |K vynucení kodér vytvořit asset, který obsahuje tiché zvukové stopy po žádný zvukový vstup, zadejte hodnotu "InsertSilenceIfNoAudio".<br/><br/> Ve výchozím nastavení Pokud odesíláte vstupní hodnota pro kodér, který obsahuje pouze video a zvuk, pak výstupního prostředku obsahuje soubory, které obsahují pouze videa data. Některé přehrávače nemusí být schopna zpracovávat takové výstupní datové proudy. Toto nastavení slouží k vynucení kodéru pro přidání do výstupu v tomto scénáři tiché zvuková stopa. |

### <a name="groups"></a>Skupiny
| Referenční informace | Popis |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Zobrazit popis [AudioGroup](media-services-mes-schema.md#AudioGroup) znát odpovídající počet kanálů, vzorkovací frekvenci a přenosové rychlosti, která se dají nastavit pro jednotlivé profily. |

## <a name="AudioGroup"></a> AudioGroup
Podrobnosti o tom, jaké hodnoty jsou platné pro každý profil najdete v tématu "Zvukový kodek details" v následující tabulce.  

### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **kanály**<br/><br/> minOccurs="0" |**xs: int** |Počet zvukových kanálů kódování. Platné možnosti jsou následující: 1, 2, 5, 6, 8.<br/><br/> Výchozí: 2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**xs: int** |Zvukový vzorkovací frekvenci podle Hz. |
| **S přenosovou rychlostí**<br/><br/> minOccurs="0" |**xs: int** |Přenosové rychlosti při kódování zvuk, zadané v kb/s. |

### <a name="audio-codec-details"></a>Podrobnosti o zvukový kodek
Zvukový kodek|Podrobnosti  
-----------------|---  
**AACLC**|1:<br/><br/> -11025: 8 &lt;= s přenosovou rychlostí &lt; 16<br/><br/> -12000: 8 &lt;= s přenosovou rychlostí &lt; 16<br/><br/> -16000: 8 &lt;= s přenosovou rychlostí &lt;32<br/><br/>-22050: 24 &lt;= s přenosovou rychlostí &lt; 32<br/><br/> -24000: 24 &lt;= s přenosovou rychlostí &lt; 32<br/><br/> -32000: 32 &lt;= s přenosovou rychlostí &lt;= 192<br/><br/> -44100: 56 &lt;= s přenosovou rychlostí &lt;= 288<br/><br/> -48000: 56 &lt;= s přenosovou rychlostí &lt;= 288<br/><br/> -88200: 128 &lt;= s přenosovou rychlostí &lt;= 288<br/><br/> -96000: 128 &lt;= s přenosovou rychlostí &lt;= 288<br/><br/> 2:<br/><br/> -11025: 16 &lt;= s přenosovou rychlostí &lt; 24<br/><br/> -12000: 16 &lt;= s přenosovou rychlostí &lt; 24<br/><br/> -16000: 16 &lt;= s přenosovou rychlostí &lt; 40<br/><br/> -22050: 32 &lt;= s přenosovou rychlostí &lt; 40<br/><br/> -24000: 32 &lt;= s přenosovou rychlostí &lt; 40<br/><br/> -32000: 40 &lt;= s přenosovou rychlostí &lt;= 384<br/><br/> -44100: 96 &lt;= s přenosovou rychlostí &lt;= 576<br/><br/> -48000: 96 &lt;= s přenosovou rychlostí &lt;= 576<br/><br/> -88200: 256 &lt;= s přenosovou rychlostí &lt;= 576<br/><br/> -96000: 256 &lt;= s přenosovou rychlostí &lt;= 576<br/><br/> 5/6:<br/><br/> -32000: 160 &lt;= s přenosovou rychlostí &lt;= 896<br/><br/> -44100: 240 &lt;= s přenosovou rychlostí &lt;= 1024<br/><br/> -48000: 240 &lt;= s přenosovou rychlostí &lt;= 1024<br/><br/> -88200: 640 &lt;= s přenosovou rychlostí &lt;= 1024<br/><br/> -96000: 640 &lt;= s přenosovou rychlostí &lt;= 1024<br/><br/> 8:<br/><br/> -32000: 224 &lt;= s přenosovou rychlostí &lt;= 1024<br/><br/> -44100: 384 &lt;= s přenosovou rychlostí &lt;= 1024<br/><br/> -48000: 384 &lt;= s přenosovou rychlostí &lt;= 1024<br/><br/> -88200: 896 &lt;= s přenosovou rychlostí &lt;= 1024<br/><br/> -96000: 896 &lt;= s přenosovou rychlostí &lt;= 1024  
**HEAACV1**|1:<br/><br/> -22050: s přenosovou rychlostí = 8<br/><br/> -24000: 8 &lt;= s přenosovou rychlostí &lt;= 10<br/><br/> -32000: 12 &lt;= s přenosovou rychlostí &lt;= 64<br/><br/> -44100: 20 &lt;= s přenosovou rychlostí &lt;= 64<br/><br/> -48000: 20 &lt;= s přenosovou rychlostí &lt;= 64<br/><br/> -88200: s přenosovou rychlostí = 64<br/><br/> 2:<br/><br/> -32000: 16 &lt;= s přenosovou rychlostí &lt;= 128<br/><br/> -44100: 16 &lt;= s přenosovou rychlostí &lt;= 128<br/><br/> -48000: 16 &lt;= s přenosovou rychlostí &lt;= 128<br/><br/> -88200: 96 &lt;= s přenosovou rychlostí &lt;= 128<br/><br/> -96000: 96 &lt;= s přenosovou rychlostí &lt;= 128<br/><br/> 5/6:<br/><br/> -32000: 64 &lt;= s přenosovou rychlostí &lt;= 320<br/><br/> -44100: 64 &lt;= s přenosovou rychlostí &lt;= 320<br/><br/> -48000: 64 &lt;= s přenosovou rychlostí &lt;= 320<br/><br/> -88200: 256 &lt;= s přenosovou rychlostí &lt;= 320<br/><br/> -96000: 256 &lt;= s přenosovou rychlostí &lt;= 320<br/><br/> 8:<br/><br/> -32000: 96 &lt;= s přenosovou rychlostí &lt;= 448<br/><br/> -44100: 96 &lt;= s přenosovou rychlostí &lt;= 448<br/><br/> -48000: 96 &lt;= s přenosovou rychlostí &lt;= 448<br/><br/> -88200: 384 &lt;= s přenosovou rychlostí &lt;= 448<br/><br/> -96000: 384 &lt;= s přenosovou rychlostí &lt;= 448  
**HEAACV2**|2:<br/><br/> -22050: 8 &lt;= s přenosovou rychlostí &lt;= 10<br/><br/> -24000: 8 &lt;= s přenosovou rychlostí &lt;= 10<br/><br/> -32000: 12 &lt;= s přenosovou rychlostí &lt;= 64<br/><br/> -44100: 20 &lt;= s přenosovou rychlostí &lt;= 64<br/><br/> -48000: 20 &lt;= s přenosovou rychlostí &lt;= 64<br/><br/> -88200: 64 &lt;= s přenosovou rychlostí &lt;= 64  
  
## <a name="Clip"></a> Galerie
### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **startTime** |**značku xs: Duration** |Určuje počáteční čas prezentace. Hodnota StartTime musí odpovídat absolutní časové razítko vstupního videa. Například pokud první snímek vstupní video má časové razítko 12:00:10.000, pak StartTime by měl být alespoň 12:00:10.000 nebo vyšší. |
| **Doba trvání** |**značku xs: Duration** |Určuje dobu, po prezentace (například vzhled překrytí ve videu). |

## <a name="Output"></a> Výstup
### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **FileName** |**xs:String** |Název výstupního souboru.<br/><br/> Makra popsané v následující tabulce můžete použít k vytváření názvů výstupních souborů. Příklad:<br/><br/> **"Výstupy": [{"Název_souboru": "{Basename} *{rozlišení}* MP4 {s přenosovou rychlostí}", "Formátu": {"Type": "MP4Format"}}]** |

### <a name="macros"></a>Makra
| – Makro | Popis |
| --- | --- |
| **{Basename}** |Pokud provádíte kódování videa na vyžádání, {Basename} je prvních 32 znaků vlastnost AssetFile.Name primárního souboru ve vstupní asset.<br/><br/> Pokud vstupní asset živý archív se {Basename} pochází z trackName atributy v manifestu serveru. Pokud jste odeslali úlohu dílčí klip pomocí TopBitrate, stejně jako v: "< VideoStream\>TopBitrate < / VideoStream\>" a výstupní soubor obsahuje video a pak {Basename} je prvních 32 znaků trackName vrstvu videa s nejvyšší s přenosovou rychlostí.<br/><br/> Pokud místo toho jsou odeslání úlohy dílčí klip pomocí všechny vstupní přenosových rychlostí, například "< VideoStream\>* < / VideoStream\>" a výstupní soubor obsahuje video a pak {Basename} je prvních 32 znaků trackName z odpovídající vrstvy videa. |
| **{Kodek}** |Mapuje pro video "H264" a "AAC" pro zvuk. |
| **{S přenosovou rychlostí}** |Video bitrate cíl výstupního souboru obsahuje video a zvuk nebo zvukový s přenosovou rychlostí cíl výstupního souboru obsahuje pouze zvuku. Použít hodnotu s přenosovou rychlostí v kb/s. |
| **{Kanálu}** |Pokud soubor obsahuje audio zvuku kanálu počet. |
| **{Šířka}** |Šířka videa v pixelech, výstupní soubor, pokud soubor obsahuje video. |
| **{Výška}** |Výška videa v pixelech, výstupní soubor, pokud soubor obsahuje video. |
| **{Extension}** |Dědí z vlastnosti "Type" pro výstupní soubor. Název výstupního souboru má příponu, což je jedna z: "mp4", "ts", "jpg", "png" nebo "bmp". |
| **{Index}** |Povinné pro miniaturu. By měl být pouze přítomen jednou. |

## <a name="Video"></a> Video (komplexní typ dědí z kodek)
### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Start** |**xs:String** | |
| **Krok** |**xs:String** | |
| **rozsah** |**xs:String** | |
| **PreserveResolutionAfterRotation** |**xs:Boolean** |Podrobné vysvětlení najdete v následující části: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Doporučuje se použít **PreserveResolutionAfterRotation** příznak v kombinaci s hodnotami rozlišení vyjádřené v procentech (Width = "100 %", Height = "100 %").  

Ve výchozím nastavení kódovat rozlišení (šířka, výška) v předvolbách Media Encoder Standard (MES) cílí na videa s využitím otočení kolem osy 0 stupňů. Například pokud vaše vstupní video je 1280 × 720 s otočení kolem osy nula stupňů, pak výchozí přednastavení Ujistěte se, že výstup obsahuje stejné řešení.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Pokud byla zachycena vstupního videa s nenulovou otočení (například chytrém telefonu nebo tabletu uchovávat svisle), pak MES ve výchozím nastavení nastavení překladu kódovat (šířka, výška) se vztahuje na vstupním videu a pak jako kompenzaci za otočení. Podívejte se například na následujícím obrázku. Přednastavení používá Width = "100 %" Height = "100 %", který MES interpretuje jako vyžadující výstupem bude 1 280 pixelů na šířku a 720 pixelů na výšku. Po rotaci videa, pak ho zmenšuje obrázek, který se vejde do tohoto okna, což vede k oblastem pillar-box vlevo a vpravo.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Alternativně můžete provést využívání **PreserveResolutionAfterRotation** příznak a nastavte ho na "true" (výchozí hodnota je "false"). Proto pokud vaše předvolby má šířku = "100 %", Height = "100 %" a PreserveResolutionAfterRotation nastavena na "true", vstupního videa, což je 1 280 pixelů na šířku a 720 pixelů na výšku s 90stupňových otočení vytváří výstupní otočení kolem osy stupňů nula, ale 720 pixelů široký a 1280 pixelů na výšku. Viz následující obrázek:  

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
| **Šířka**<br/><br/> minOccurs="0" |**xs:int** | |
| **Výška**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs:String** | |

## <a name="PngLayer"></a> PngLayer
### <a name="element"></a>Element
| Název | Typ | Popis |
| --- | --- | --- |
| **Šířka**<br/><br/> minOccurs="0" |**xs:int** | |
| **Výška**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs:String** | |

## <a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Element
| Název | Typ | Popis |
| --- | --- | --- |
| **Šířka**<br/><br/> minOccurs="0" |**xs:int** | |
| **Výška**<br/><br/> minOccurs="0" |**xs:int** | |
| **Kvalita**<br/><br/> minOccurs="0" |**xs:int** |Platné hodnoty: 1(worst)-100(best) |

### <a name="attributes"></a>Atributy
| Název | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs:String** | |

## <a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs = "0" maxOccurs = "bez vazby" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs = "0" maxOccurs = "bez vazby" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs = "0" maxOccurs = "bez vazby" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="BmpImage"></a> BmpImage (komplexní typ dědí z Video)
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Vrstvy PNG |

## <a name="JpgImage"></a> JpgImage (komplexní typ dědí z Video)
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Vrstvy PNG |

## <a name="PngImage"></a> PngImage (komplexní typ dědí z Video)
### <a name="elements"></a>Elementy
| Název | Typ | Popis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Vrstvy PNG |

## <a name="examples"></a>Příklady
Příklady najdete v tématu předvolby XML, které jsou vytvořeny na základě tohoto schématu naleznete v tématu [předvolby úloh pro MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

