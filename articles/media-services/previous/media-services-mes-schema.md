---
title: Media Encoder Standard schéma | Microsoft Docs
description: Tento článek popisuje některé prvky a typy schématu XML, na kterém jsou založené Media Encoder Standard předvolby.
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
ms.openlocfilehash: f82e0c3f76dba05c3404b11e07c7130119ce0b9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015654"
---
# <a name="media-encoder-standard-schema"></a>Schéma Media Encoderu Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Tento článek popisuje některé prvky a typy schématu XML, na kterém jsou založené [Media Encoder Standard předvolby](media-services-mes-presets-overview.md) . Článek obsahuje vysvětlení prvků a jejich platných hodnot.  

## <a name="preset-root-element"></a><a name="Preset"></a> Předvolba (kořenový element)
Definuje přednastavení kódování.  

### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **Kódování** |[Kódování](media-services-mes-schema.md#Encoding) |Kořenový element označuje, že vstupní zdroje mají být kódovány. |
| **Výstupy** |[Výstupy](media-services-mes-schema.md#Output) |Kolekce požadovaných výstupních souborů. |
| **StretchMode**<br/>minOccurs = "0"<br/>default = "AutoSize|xs: String|Řízení velikosti snímku, odsazení, pixelů nebo zobrazení poměru stran výstupní videa. **StretchMode** může být jedna z následujících hodnot: **none**, **AutoSize** (default) nebo **autopřizpůsob**.<br/><br/>**Žádné**: striktní postupovat podle rozlišení výstupu (například **šířky** a **výšky** v předvolbě) bez zvážení poměru stran obrazových bodů nebo zobrazení poměru stran pro vstupní video. Doporučuje se ve scénářích, jako je například [ořezávání](media-services-crop-video.md), kde výstupní video má v porovnání se vstupem jiný poměr stran. <br/><br/>**AutoSize**: rozlišení výstupu se vejde do okna (šířka × výška) zadané v přednastaveném. Kodér však vytvoří výstupní video, které má čtvercový poměr stran (1:1) pixelů. Proto je možné přepsat šířku výstupu nebo výstupní výšku, aby se shodoval poměr stran zobrazení vstupu, bez odsazení. Například pokud je vstup 1080 a předvolby kódování požádá o 1280x1280, pak hodnota výšky v předdefinovaném je přepsána a výstup bude na 1280 × 720, který bude udržovat poměr stran vstupních hodnot 16:9. <br/><br/>**Automatické přizpůsobení**: Pokud je to potřeba, nasadíte výstupní video (s Letterbox nebo pillarbox), aby se zajistilo požadované výstupní řešení, zatímco aktivní oblast videa ve výstupu má stejný poměr stran jako vstup. Předpokládejme například, že vstup je 1080 a předvolby kódování požádá o 1280x1280. Výstupní video se pak bude nacházet na 1280x1280, ale bude obsahovat vnitřní 1280 × 720 obdélník aktivního videa s poměrem stran 16:9 a Letterbox oblasti 280 pixelů v horní a dolní části. Další příklad, pokud je vstup 1440x1080 a předvolby kódování požádá o 1280 × 720, pak bude výstup na 1280 × 720, který obsahuje vnitřní obdélník 960x720 v poměru stran 4:3 a oblasti pole pilíře 160 pixelů ve světě vlevo a vpravo. 

### <a name="attributes"></a>Atributy

| Název | Typ | Description |
| --- | --- | --- |
| **Verze**<br/><br/> Vyžadováno |**xs: Decimal** |Přednastavená verze Platí následující omezení: xs: fractionDigits value = "1" a xs: minInclusive value = "1", například **Version = "1.0"**. |

## <a name="encoding"></a><a name="Encoding"></a> Kódování
Obsahuje sekvenci následujících prvků:  

### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Nastavení pro H. 264 kódování videa |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Nastavení pro kódování AAC zvuk. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Nastavení pro obrázek bmp |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Nastavení pro obrázek PNG. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Nastavení pro obrázek jpg |

## <a name="h264video"></a><a name="H264Video"></a> H264Video
### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs = "0" |**xs: Boolean** |V současné době je podporováno pouze kódování s jedním průchodem. |
| **KeyFrameInterval**<br/><br/> minOccurs = "0"<br/><br/> **Výchozí = "00:00:02"** |**xs: Time** |Určuje pevné mezery mezi IDR snímky v jednotkách sekund. Také se označuje jako skupinu GOP doba trvání. Informace o tom, jestli se kodér může odchylovat od této hodnoty, najdete v tématu **SceneChangeDetection** . |
| **SceneChangeDetection**<br/><br/> minOccurs = "0"<br/><br/> výchozí nastavení = false |**xs: Boolean** |Pokud je nastavená hodnota true, kodér se pokusí zjistit změnu scény ve videu a vloží IDR snímek. |
| **Hesla**<br/><br/> minOccurs = "0"<br/><br/> výchozí nastavení = "vyváženo" |**xs: String** |Řídí kompromis mezi rychlostí kódování a kvalitou videa. Může to být jedna z následujících hodnot: **rychlost**, **rovnováha** nebo **kvalita** .<br/><br/> Výchozí: **vyvážené** |
| **SyncMode**<br/><br/> minOccurs = "0" | |Funkce bude vystavena v budoucí verzi. |
| **H264Layers**<br/><br/> minOccurs = "0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Kolekce výstupních vrstev videa |

### <a name="attributes"></a>Atributy

| Název | Typ | Description |
| --- | --- | --- |
| **Condition** (Podmínka) |**xs: String** | Pokud vstup nemá žádné video, možná budete chtít, aby kodér vynutil vložení monochromatického videa. Uděláte to tak, že použijete Condition = "InsertBlackIfNoVideoBottomLayerOnly" (pro vložení videa pouze s nejnižší přenosovou rychlostí) nebo Condition = "InsertBlackIfNoVideo" (pro vložení videa do všech výstupních přenosů). Další informace najdete v [tomto](media-services-advanced-encoding-with-mes.md#no_video) článku.|

## <a name="h264layers"></a><a name="H264Layers"></a> H264Layers

Ve výchozím nastavení platí, že pokud odešlete vstup do kodéru, který obsahuje pouze zvuk, a žádné video, výstupní Asset obsahuje soubory pouze se zvukovými daty. Někteří hráči nemusí být schopni tyto výstupní proudy zpracovat. Nastavení atributu H264Video's **InsertBlackIfNoVideo** můžete použít k vynucení, aby kodér Přidal video stopu do výstupu v tomto scénáři. Další informace najdete v [tomto](media-services-advanced-encoding-with-mes.md#no_video) článku.
              
### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs = "0" maxOccurs = "Unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Kolekce H264 vrstev. |

## <a name="h264layer"></a><a name="H264Layer"></a> H264Layer
> [!NOTE]
> Omezení videa jsou založena na hodnotách popsaných v tabulce [úrovně H264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) .  
> 
> 

### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> výchozí nastavení = "auto" |**xs: String** |Může to být jedna z následujících hodnot typu **xs:** **auto**, **směrný plán**, **hlavní**, **Vysoká**. |
| **Obsah**<br/><br/> minOccurs = "0"<br/><br/> výchozí nastavení = "auto" |**xs: String** | |
| **Rychlostí**<br/><br/> minOccurs = "0" |**xs: int** |Přenosová rychlost použitá pro tuto vrstvu videa zadaná v KB/s. |
| **MaxBitrate**<br/><br/> minOccurs = "0" |**xs: int** |Maximální přenosová rychlost použitá pro tuto vrstvu videa zadaná v KB/s. |
| **BufferWindow**<br/><br/> minOccurs = "0"<br/><br/> Výchozí = "00:00:05" |**xs: Time** |Délka vyrovnávací paměti videa |
| **Width (Šířka)**<br/><br/> minOccurs = "0" |**xs: int** |Šířka výstupního snímku videa (v pixelech)<br/><br/> V současné době je nutné zadat šířku i výšku. Šířka a výška musí být sudými čísly. |
| **Height (Výška)**<br/><br/> minOccurs = "0" |**xs: int** |Výška výstupního snímku videa v pixelech<br/><br/> V současné době je nutné zadat šířku i výšku. Šířka a výška musí být sudými čísly.|
| **BFrames**<br/><br/> minOccurs = "0" |**xs: int** |Počet snímků B mezi referenčními snímky |
| **ReferenceFrames**<br/><br/> minOccurs = "0"<br/><br/> výchozí nastavení = "3" |**xs: int** |Počet referenčních rámců v skupinu GOP. |
| **EntropyMode**<br/><br/> minOccurs = "0"<br/><br/> default = "CABAC" |**xs: String** |Může to být jedna z následujících hodnot: **CABAC** a **Cavlc**. |
| **Snímků**<br/><br/> minOccurs = "0" |Racionální číslo |Určuje kmitočet snímků výstupního videa. Použijte výchozí hodnotu "0/1", pokud chcete, aby kodér používal stejný kmitočet snímků jako vstupní video. U povolených hodnot se očekává běžné sazby za video snímků. Nicméně jakékoli platné rozumné je povolené. Například 1/1 by byl 1 fps a je platný.<br/><br/> -12/1 (12 fps)<br/><br/> -15/1 (15 snímků za sekundu)<br/><br/> -24/1 (24 snímků za sekundu)<br/><br/> -24000/1001 (23,976 fps)<br/><br/> -25/1 (25 snímků za sekundu)<br/><br/>  -30/1 (30 snímků/s)<br/><br/> -30000/1001 (29,97 fps) <br/> <br/>**Poznámka:** Pokud vytváříte vlastní přednastavení pro kódování s více přenosovými rychlostmi, pak všechny vrstvy přednastavení **musí** používat stejnou hodnotu snímkování.|
| **AdaptiveBFrame**<br/><br/> minOccurs = "0" |**xs: Boolean** |Kopírování z Azure Media Encoderu |
| **Řezy**<br/><br/> minOccurs = "0"<br/><br/> Výchozí = "0" |**xs: int** |Určuje, kolik řezů je snímek rozdělen. Doporučujeme použít výchozí. |

## <a name="aacaudio"></a><a name="AACAudio"></a> AACAudio
 Obsahuje posloupnost následujících prvků a skupin.  

 Další informace o AAC naleznete v tématu [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> default = "AACLC" |**xs: String** |Může to být jedna z následujících hodnot: **AACLC**, **HEAACV1** nebo **HEAACV2**. |

### <a name="attributes"></a>Atributy

| Název | Typ | Description |
| --- | --- | --- |
| **Condition** (Podmínka) |**xs: String** |Chcete-li vynutit, aby kodér vytvořil Asset, který obsahuje tichou zvukovou stopu, když vstup nemá žádný zvuk, zadejte hodnotu "InsertSilenceIfNoAudio".<br/><br/> Ve výchozím nastavení platí, že pokud odešlete vstup do kodéru, který obsahuje pouze video, a žádný zvuk, pak výstupní prostředek obsahuje soubory, které obsahují pouze data videa. Někteří hráči nemusí být schopni tyto výstupní proudy zpracovat. Toto nastavení můžete použít k vynucení, aby kodér mohl do výstupu v tomto scénáři přidat tichou zvukovou stopu. |

### <a name="groups"></a>Skupiny

| Reference | Description |
| --- | --- |
| [Zvuk](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs = "0" |Podívejte se na popis skupiny [zvuků](media-services-mes-schema.md#AudioGroup) a zjistěte odpovídající počet kanálů, vzorkovací frekvenci a přenosovou rychlost, kterou je možné nastavit pro jednotlivé profily. |

## <a name="audiogroup"></a><a name="AudioGroup"></a> Zvuk
Podrobnosti o tom, jaké hodnoty jsou pro každý profil platné, najdete v tabulce "Podrobnosti o zvukovém kodeku".  

### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **Kanály**<br/><br/> minOccurs = "0" |**xs: int** |Počet zakódovaných zvukových kanálů. Níže jsou uvedené platné možnosti: 1, 2, 5, 6, 8.<br/><br/> Výchozí hodnota: 2. |
| **SamplingRate**<br/><br/> minOccurs = "0" |**xs: int** |Vzorkovací frekvence zvuku zadaná v Hz. |
| **Rychlostí**<br/><br/> minOccurs = "0" |**xs: int** |Přenosová rychlost použitá při kódování zvuku zadaná v KB/s. |

### <a name="audio-codec-details"></a>Podrobnosti zvukového kodeku

Zvukový kodek|Podrobnosti  
-----------------|---  
**AACLC** |1:<br/><br/> -11025:8 &lt; = přenosová rychlost &lt; 16<br/><br/> -12000:8 &lt; = přenosová rychlost &lt; 16<br/><br/> -16000:8 &lt; = přenosová rychlost &lt; 32<br/><br/>-22050:24 &lt; = přenosová rychlost &lt; 32<br/><br/> -24000:24 &lt; = přenosová rychlost &lt; 32<br/><br/> -32000:32 &lt; = přenosová rychlost &lt; = 192<br/><br/> -44100:56 &lt; = přenosová rychlost &lt; = 288<br/><br/> -48000:56 &lt; = přenosová rychlost &lt; = 288<br/><br/> -88200:128 &lt; = přenosová rychlost &lt; = 288<br/><br/> -96000:128 &lt; = přenosová rychlost &lt; = 288<br/><br/> 2:<br/><br/> -11025:16 &lt; = přenosová rychlost &lt; 24<br/><br/> -12000:16 &lt; = přenosová rychlost &lt; 24<br/><br/> -16000:16 &lt; = přenosová rychlost &lt; 40<br/><br/> -22050:32 &lt; = přenosová rychlost &lt; 40<br/><br/> -24000:32 &lt; = přenosová rychlost &lt; 40<br/><br/> -32000:40 &lt; = přenosová rychlost &lt; = 384<br/><br/> -44100:96 &lt; = přenosová rychlost &lt; = 576<br/><br/> -48000:96 &lt; = přenosová rychlost &lt; = 576<br/><br/> -88200:256 &lt; = přenosová rychlost &lt; = 576<br/><br/> -96000:256 &lt; = přenosová rychlost &lt; = 576<br/><br/> 5/6:<br/><br/> -32000:160 &lt; = přenosová rychlost &lt; = 896<br/><br/> -44100:240 &lt; = přenosová rychlost &lt; = 1024<br/><br/> -48000:240 &lt; = přenosová rychlost &lt; = 1024<br/><br/> -88200:640 &lt; = přenosová rychlost &lt; = 1024<br/><br/> -96000:640 &lt; = přenosová rychlost &lt; = 1024<br/><br/> 8:<br/><br/> -32000:224 &lt; = přenosová rychlost &lt; = 1024<br/><br/> -44100:384 &lt; = přenosová rychlost &lt; = 1024<br/><br/> -48000:384 &lt; = přenosová rychlost &lt; = 1024<br/><br/> -88200:896 &lt; = přenosová rychlost &lt; = 1024<br/><br/> -96000:896 &lt; = přenosová rychlost &lt; = 1024  
**HEAACV1** |1:<br/><br/> -22050: přenosová rychlost = 8<br/><br/> -24000:8 &lt; = přenosová rychlost &lt; = 10<br/><br/> -32000:12 &lt; = přenosová rychlost &lt; = 64<br/><br/> -44100:20 &lt; = přenosová rychlost &lt; = 64<br/><br/> -48000:20 &lt; = přenosová rychlost &lt; = 64<br/><br/> -88200: přenosová rychlost = 64<br/><br/> 2:<br/><br/> -32000:16 &lt; = přenosová rychlost &lt; = 128<br/><br/> -44100:16 &lt; = přenosová rychlost &lt; = 128<br/><br/> -48000:16 &lt; = přenosová rychlost &lt; = 128<br/><br/> -88200:96 &lt; = přenosová rychlost &lt; = 128<br/><br/> -96000:96 &lt; = přenosová rychlost &lt; = 128<br/><br/> 5/6:<br/><br/> -32000:64 &lt; = přenosová rychlost &lt; = 320<br/><br/> -44100:64 &lt; = přenosová rychlost &lt; = 320<br/><br/> -48000:64 &lt; = přenosová rychlost &lt; = 320<br/><br/> -88200:256 &lt; = přenosová rychlost &lt; = 320<br/><br/> -96000:256 &lt; = přenosová rychlost &lt; = 320<br/><br/> 8:<br/><br/> -32000:96 &lt; = přenosová rychlost &lt; = 448<br/><br/> -44100:96 &lt; = přenosová rychlost &lt; = 448<br/><br/> -48000:96 &lt; = přenosová rychlost &lt; = 448<br/><br/> -88200:384 &lt; = přenosová rychlost &lt; = 448<br/><br/> -96000:384 &lt; = přenosová rychlost &lt; = 448  
**HEAACV2** |2:<br/><br/> -22050:8 &lt; = přenosová rychlost &lt; = 10<br/><br/> -24000:8 &lt; = přenosová rychlost &lt; = 10<br/><br/> -32000:12 &lt; = přenosová rychlost &lt; = 64<br/><br/> -44100:20 &lt; = přenosová rychlost &lt; = 64<br/><br/> -48000:20 &lt; = přenosová rychlost &lt; = 64<br/><br/> -88200:64 &lt; = přenosová rychlost &lt; = 64  
  
## <a name="clip"></a><a name="Clip"></a> Sady
### <a name="attributes"></a>Atributy

| Název | Typ | Description |
| --- | --- | --- |
| **Spuštění** |**xs: Duration** |Určuje počáteční čas prezentace. Hodnota StartTime musí odpovídat absolutním časovým razítkem vstupního videa. Například pokud má první snímek vstupního videa časové razítko 12:00:10.000, pak StartTime by měl být aspoň 12:00:10.000 nebo vyšší. |
| **Doba trvání** |**xs: Duration** |Určuje dobu trvání prezentace (například vzhled překrytí ve videu). |

## <a name="output"></a><a name="Output"></a> Výkonem
### <a name="attributes"></a>Atributy

| Název | Typ | Description |
| --- | --- | --- |
| **Bitmap** |**xs: String** |Název výstupního souboru<br/><br/> Pomocí maker popsaných v následující tabulce můžete sestavovat názvy výstupních souborů. Například:<br/><br/> **"Výstupy": [{"FileName": "{Base.}*{Solution}*{přenos}. mp4", "Format": {"Type": "MP4Format"}}]** |

### <a name="macros"></a>Makra

| Podokně | Description |
| --- | --- |
| **BaseName** |Pokud provádíte kódování VoD, je {Baseer} první 32 znaky vlastnosti AssetFile.Name primárního souboru ve vstupním prostředku.<br/><br/> Pokud je vstupním prostředkem živý archiv, pak je {Baseer} odvozený od atributů stop v manifestu serveru. Pokud odesíláte úlohu dílčího klipu pomocí TopBitrate, jako v: "<VideoStream \> TopBitrate</videostream \> " a výstupní soubor obsahuje video, pak je {Base} první 32 znaků stopy vrstvy videa s nejvyšší přenosovou rychlostí.<br/><br/> Pokud místo toho odesíláte úlohu dílčího klipu pomocí všech vstupních přenosů, jako je například "<VideoStream \> * </videostream \> " a výstupní soubor obsahuje video, je {Base} první 32 znaků stopy odpovídající vrstvy videa. |
| **Kodek** |Mapuje se na video a AAC pro zvuk na "H264". |
| **Rychlostí** |Cílová přenosová rychlost videa, pokud výstupní soubor obsahuje video a zvuk nebo cílovou přenosovou rychlostí, pokud výstupní soubor obsahuje jenom zvuk. Použitá hodnota je přenosová rychlost v KB/s. |
| **Kanál** |Počet zvukových kanálů, pokud soubor obsahuje zvuk. |
| **Délk** |Šířka videa (v pixelech) ve výstupním souboru, pokud soubor obsahuje video. |
| **Výška** |Výška videa v pixelech ve výstupním souboru, pokud soubor obsahuje video. |
| **Klapk** |Dědí z vlastnosti "Type" pro výstupní soubor. Název výstupního souboru má příponu, která je jedna z těchto: "MP4", "TS", "jpg", "png" nebo "BMP". |
| **Indexovacím** |Povinné pro miniaturu. By měla být přítomna pouze jednou. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a> Video (komplexní typ dědí z kodeku)
### <a name="attributes"></a>Atributy

| Název | Typ | Description |
| --- | --- | --- |
| **Zahájení** |**xs: String** | |
| **Krok** |**xs: String** | |
| **Rozsah** |**xs: String** | |
| **PreserveResolutionAfterRotation** |**xs: Boolean** |Podrobné vysvětlení najdete v následující části: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Doporučuje se použít příznak **PreserveResolutionAfterRotation** v kombinaci s hodnotami rozlišení vyjádřenými v procentech (Width = "100%", Height = "100%").  

Ve výchozím nastavení jsou nastavení rozlišení kódování (šířka, výška) v předvolbách Media Encoder Standard (na úrovni) cílena na videa s otočením na 0 stupňů. Pokud je například vstupní video 1280 × 720 s otočením nulové úrovně, pak výchozí přednastavení zajistí, že výstup má stejné rozlišení.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Pokud se vstupní video zachycuje s nenulovým otočením (například zařízení smartphone nebo tabletu se podrží svisle), pak se ve výchozím nastavení standardně aplikuje nastavení rozlišení kódování (šířka, výška) na vstupní video a pak se tato rotace kompenzuje. Podívejte se například na následující obrázek. Přednastavení používá Width = "100%", Height = "100%", který roz1280 kládá dolů, protože vyžaduje, aby výstup byl větší než pixelů široké a 720 pixelů. Po otočení videa se obrázek zmenší tak, aby se vešel do tohoto okna, což vede k oblastem pilíře vlevo a vpravo.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Alternativně můžete použít příznak **PreserveResolutionAfterRotation** a nastavit ho na hodnotu "pravda" (výchozí hodnota je false). Takže pokud má vaše předvolba šířku = "100%", Height = "100%" a PreserveResolutionAfterRotation nastavenou na "true", vstupní video, které 1280 je vyšší 90 než pixelů na šířku a 720 pixelů s vysokým otočením na úrovni. Podívejte se na následující obrázek:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a> Format Group (skupina)
### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Prvek

| Název | Typ | Description |
| --- | --- | --- |
| **Width (Šířka)**<br/><br/> minOccurs = "0" |**xs: int** | |
| **Height (Výška)**<br/><br/> minOccurs = "0" |**xs: int** | |

### <a name="attributes"></a>Atributy

| Název | Typ | Description |
| --- | --- | --- |
| **Condition** (Podmínka) |**xs: String** | |

## <a name="pnglayer"></a><a name="PngLayer"></a> PngLayer
### <a name="element"></a>Prvek

| Název | Typ | Description |
| --- | --- | --- |
| **Width (Šířka)**<br/><br/> minOccurs = "0" |**xs: int** | |
| **Height (Výška)**<br/><br/> minOccurs = "0" |**xs: int** | |

### <a name="attributes"></a>Atributy

| Název | Typ | Description |
| --- | --- | --- |
| **Condition** (Podmínka) |**xs: String** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Prvek

| Název | Typ | Description |
| --- | --- | --- |
| **Width (Šířka)**<br/><br/> minOccurs = "0" |**xs: int** | |
| **Height (Výška)**<br/><br/> minOccurs = "0" |**xs: int** | |
| **Kvalita**<br/><br/> minOccurs = "0" |**xs: int** |Platné hodnoty: 1 (nejhorší)-100 (nejlepší) |

### <a name="attributes"></a>Atributy

| Název | Typ | Description |
| --- | --- | --- |
| **Condition** (Podmínka) |**xs: String** | |

## <a name="pnglayers"></a><a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs = "0" maxOccurs = "Unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs = "0" maxOccurs = "Unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs = "0" maxOccurs = "Unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a> BmpImage (komplexní typ dědí z videa)
### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Vrstvy png |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a> JpgImage (komplexní typ dědí z videa)
### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Vrstvy png |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a> PngImage (komplexní typ dědí z videa)
### <a name="elements"></a>Elementy

| Název | Typ | Description |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Vrstvy png |

## <a name="examples"></a>Příklady
Podívejte se na příklady přednastavení XML, která jsou založená na tomto schématu, v tématu [Předvolby úloh Media Encoder standard pro](media-services-mes-presets-overview.md)soubor.

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

