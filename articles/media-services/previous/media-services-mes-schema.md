---
title: Schéma Standard mediálního kodéru | Dokumenty společnosti Microsoft
description: Tento článek popisuje některé prvky a typy schématu XML, na kterých jsou založena přednastavení Standard kodéru médií.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 622f14beabb1f2f109dff5d28c1591ffdd5aa000
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901440"
---
# <a name="media-encoder-standard-schema"></a>Schéma Media Encoderu Standard
Tento článek popisuje některé prvky a typy schématu XML, na kterých jsou založena [přednastavení Standard kodéru médií.](media-services-mes-presets-overview.md) Článek podává vysvětlení prvků a jejich platných hodnot.  

## <a name="preset-root-element"></a><a name="Preset"></a>Přednastavení (kořenový prvek)
Definuje přednastavení kódování.  

### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Kódování** |[Kódování](media-services-mes-schema.md#Encoding) |Kořenový prvek označuje, že vstupní zdroje mají být kódovány. |
| **Výstupy** |[Výstupy](media-services-mes-schema.md#Output) |Kolekce požadovaných výstupních souborů. |
| **StretchMode**<br/>minOccurs="0"<br/>default="Automatická velikost|xs:řetězec|Ovládejte velikost výstupního snímku videa, odsazení, obrazový bod nebo poměr stran zobrazení. **StretchMode** může být jedna z následujících hodnot: **None**, **AutoSize** (výchozí) nebo **AutoFit**.<br/><br/>**Žádné**: Přísně dodržujte výstupní rozlišení (například **šířku** a **výšku** v přednastavení) bez ohledu na poměr stran obrazových bodů nebo poměr stran zobrazení vstupního videa. Doporučeno ve scénářích, jako je [oříznutí](media-services-crop-video.md), kde má výstupní video jiný poměr stran ve srovnání se vstupem. <br/><br/>**Automatická velikost**: Výstupní rozlišení se vejde do okna (Šířka * Výška) určeného přednastavením. Kodér však vytvoří výstupní video, které má čtvercový poměr stran obrazových bodů (1:1). Proto může být výstupní šířka nebo výstupní výška přepsána tak, aby odpovídala poměru stran zobrazení vstupu bez odsazení. Například pokud je vstup 1920x1080 a přednastavení kódování požádá o 1280x1280, pak výška hodnota v přednastavení je přepsána a výstup bude na 1280x720, který udržuje poměr vstupních stran 16:9. <br/><br/>**Automatické přizpůsobení**: V případě potřeby vysuňte výstupní video (s poštovní schránkou nebo sloupkem pilíře) tak, aby bylo uspokojeno požadované výstupní rozlišení, a zároveň zajistěte, aby aktivní oblast videa ve výstupu získala stejný poměr stran jako vstup. Předpokládejme například, že vstup je 1920x1080 a přednastavení kódování požádá o 1280x1280. Pak výstupní video bude na 1280x1280, ale bude obsahovat vnitřní obdélník 1280x720 "aktivního videa" s poměrem stran 16: 9 a oblasti letterbox 280 pixelů vysoké v horní a dolní části. Pro jiný příklad, pokud je vstup 1440x1080 a přednastavení kódování požádá o 1280x720, pak výstup bude na 1280x720, který obsahuje vnitřní obdélník 960x720 při poměru stran 4: 3 a oblasti sloupku pole 160 pixelů široké vlevo a vpravo. 

### <a name="attributes"></a>Atributy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Verze**<br/><br/> Požaduje se |**xs: desetinné číslo** |Přednastavená verze. Platí následující omezení: xs:fractionDigits value="1" a xs:minInclusive value="1" Například **version="1.0"**. |

## <a name="encoding"></a><a name="Encoding"></a>Kódování
Obsahuje posloupnost následujících prvků:  

### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Nastavení kódování videa H.264. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Nastavení kódování zvuku AAC. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Nastavení pro obraz Bmp. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Nastavení obrázku Png. |
| **Obrázek jpg** |[Obrázek jpg](media-services-mes-schema.md#JpgImage) |Nastavení obrázku Jpg. |

## <a name="h264video"></a><a name="H264Video"></a>H264Video
### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:logická hodnota** |V současné době je podporováno pouze jednoprůchodové kódování. |
| **Interval keyframe**<br/><br/> minOccurs="0"<br/><br/> **výchozí="00:00:02"** |**xs:čas** |Určuje pevné mezery mezi snímky IDR v jednotkách sekund. Označuje se také jako doba trvání gop. Viz **SceneChangeDetection** pro řízení, zda kodér může odchýlit od této hodnoty. |
| **Detekce změn scény**<br/><br/> minOccurs="0"<br/><br/> default="false" |**xs: logická** |Pokud je kodér nastaven na hodnotu true, pokusí se zjistit změnu scény ve videu a vloží snímek IDR. |
| **Složitost**<br/><br/> minOccurs="0"<br/><br/> default="Vyvážený" |**xs:řetězec** |Řídí kompromis mezi rychlostí kódování a kvalitou videa. Může se na nich **jed.** **Balanced** **Quality**<br/><br/> Výchozí: **Vyvážený** |
| **Režim synchronizace**<br/><br/> minOccurs="0" | |Funkce bude vystavena v budoucí verzi. |
| **H264Vrstvy**<br/><br/> minOccurs="0" |[H264Vrstvy](media-services-mes-schema.md#H264Layers) |Kolekce výstupních vrstev videa. |

### <a name="attributes"></a>Atributy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs:řetězec** | Pokud vstup nemá žádné video, můžete vynutit, aby kodér vložil monochromatickou stopu videa. Chcete-li to provést, použijte Condition="InsertBlackIfNoVideoBottomLayerOnly" (vložit video pouze s nejnižším přenosovým tokem) nebo Condition="InsertBlackIfNoVideo" (pro vložení videa při všech výstupních přenosových rychlostech). Další informace naleznete v [tomto](media-services-advanced-encoding-with-mes.md#no_video) článku.|

## <a name="h264layers"></a><a name="H264Layers"></a>H264Vrstvy

Ve výchozím nastavení, pokud odešlete vstup do kodéru, který obsahuje pouze zvuk a žádné video, výstupní datový zdroj obsahuje pouze soubory se zvukovými daty. Někteří hráči nemusí být schopni zpracovat tyto výstupní datové proudy. Pomocí nastavení atributu **InsertBlackIfNoVideo** videa h264Video můžete vynutit přidání stopy videa do výstupu v tomto scénáři. Další informace naleznete v [tomto](media-services-advanced-encoding-with-mes.md#no_video) článku.
              
### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Vrstva H264**<br/><br/> minOccurs="0" maxOccurs="bez meze" |[Vrstva H264](media-services-mes-schema.md#H264Layer) |Kolekce vrstev H264. |

## <a name="h264layer"></a><a name="H264Layer"></a>Vrstva H264
> [!NOTE]
> Omezení videa jsou založena na hodnotách popsaných v tabulce [Úrovně H264.](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels)  
> 
> 

### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0"<br/><br/> default="Auto" |**xs: řetězec** |Může se napojit na jednu z následujících hodnot x: hodnot **y řetězce:** **Auto**, **Baseline**, **Main**, **High**. |
| **Úroveň**<br/><br/> minOccurs="0"<br/><br/> default="Auto" |**xs: řetězec** | |
| **Datový tok**<br/><br/> minOccurs="0" |**xs:int** |Datová rychlost použitá pro tuto vrstvu videa určená v kbps. |
| **Maximální bitová rychlost**<br/><br/> minOccurs="0" |**xs: int** |Maximální přenosová rychlost použitá pro tuto vrstvu videa určená v kbps. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> výchozí="00:00:05" |**xs: čas** |Délka vyrovnávací paměti videa. |
| **impulzu**<br/><br/> minOccurs="0" |**xs: int** |Šířka výstupního snímku videa v obrazových bodech.<br/><br/> V současné době je nutné zadat šířku i výšku. Šířka a výška musí být sudá čísla. |
| **Height**<br/><br/> minOccurs="0" |**xs:int** |Výška výstupního snímku videa v obrazových bodech.<br/><br/> V současné době je nutné zadat šířku i výšku. Šířka a výška musí být sudá čísla.|
| **BFrame**<br/><br/> minOccurs="0" |**xs: int** |Počet snímků B mezi referenčními snímky. |
| **Referenční rámce**<br/><br/> minOccurs="0"<br/><br/> default="3" |**xs:int** |Počet referenčních rámců v gop. |
| **EntropieMode**<br/><br/> minOccurs="0"<br/><br/> default="Cabac" |**xs: řetězec** |Může to být jedna z následujících hodnot: **Cabac** a **Cavlc**. |
| **Framerate**<br/><br/> minOccurs="0" |racionální číslo |Určuje kmitočet snímků výstupního videa. Pomocí výchozího nastavení "0/1" můžete kodér použít stejnou kmitočet snímků jako vstupní video. Očekává se, že povolené hodnoty budou běžné kmitočet snímků videa. Je však povoleno jakékoli platné rational. Například 1/1 by bylo 1 fps a je platný.<br/><br/> - 12/1 (12 fps)<br/><br/> - 15/1 (15 fps)<br/><br/> - 24/1 (24 fps)<br/><br/> - 24000/1001 (23.976 fps)<br/><br/> - 25/1 (25 fps)<br/><br/>  - 30/1 (30 fps)<br/><br/> - 30000/1001 (29,97 fps) <br/> <br/>**POZNÁMKA:** Pokud vytváříte vlastní přednastavení pro kódování s více přenosovými rychlostmi, **musí** všechny vrstvy přednastavení používat stejnou hodnotu FrameRate.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: logická** |Kopírování z kodéru médií Azure |
| **Řezy**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |Určuje, na kolik řezů je rámeček rozdělen. Doporučujeme používat výchozí nastavení. |

## <a name="aacaudio"></a><a name="AACAudio"></a>AACAudio
 Obsahuje posloupnost následujících prvků a skupin.  

 Další informace o AAC naleznete v tématu [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs: řetězec** |Může se na nich vyváznout jedna z následujících hodnot: **AACLC**, **HEAACV1**nebo **HEAACV2**. |

### <a name="attributes"></a>Atributy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs: řetězec** |Chcete-li vynutit kodér k vytvoření datového zdroje, který obsahuje tichou zvukovou stopu, když vstup nemá žádný zvuk, zadejte hodnotu InsertSilenceIfNoAudio.<br/><br/> Ve výchozím nastavení, pokud odešlete vstup do kodéru, který obsahuje pouze video a žádný zvuk, pak výstupní datový zdroj obsahuje soubory, které obsahují pouze data videa. Někteří hráči nemusí být schopni zpracovat tyto výstupní datové proudy. Toto nastavení můžete použít k vynucení kodéru přidat tichou zvukovou stopu do výstupu v tomto scénáři. |

### <a name="groups"></a>Skupiny

| Odkaz | Popis |
| --- | --- |
| [Audioskupina](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Viz popis [AudioGroup](media-services-mes-schema.md#AudioGroup) znát příslušný počet kanálů, vzorkovací frekvence a přenosová rychlost, které by mohly být nastaveny pro každý profil. |

## <a name="audiogroup"></a><a name="AudioGroup"></a>Audioskupina
Podrobnosti o tom, jaké hodnoty jsou platné pro každý profil, naleznete v tabulce "Podrobnosti zvukového kodeku", která následuje.  

### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Kanály**<br/><br/> minOccurs="0" |**xs: int** |Počet zakódovaných zvukových kanálů. Platné jsou následující možnosti: 1, 2, 5, 6, 8.<br/><br/> Výchozí hodnota: 2. |
| **Vzorkovací míra**<br/><br/> minOccurs="0" |**xs: int** |Vzorkovací frekvence zvuku uvedená v Hz. |
| **Datový tok**<br/><br/> minOccurs="0" |**xs: int** |Přenosová rychlost použitá při kódování zvuku, určená v kbps. |

### <a name="audio-codec-details"></a>Podrobnosti o zvukovém kodeku

Zvukový kodek|Podrobnosti  
-----------------|---  
**AACLC** |1:<br/><br/> - 11025: &lt;8 &lt; = přenosový tok 16<br/><br/> - 12000: &lt;8 &lt; = přenosový tok 16<br/><br/> - 16000: &lt;8 &lt;= přenosový tok 32<br/><br/>- 22050: &lt;24 &lt; = přenosový tok 32<br/><br/> - 24000: &lt;24 &lt; = přenosový tok 32<br/><br/> - 32000: &lt;32 &lt;= přenosový tok = 192<br/><br/> - 44100: &lt;56 &lt;= přenosový tok = 288<br/><br/> - 48000: &lt;56 &lt;= přenosová rychlost = 288<br/><br/> - 88200 : &lt;128 &lt;= přenosový tok = 288<br/><br/> - 96000 : &lt;128 &lt;= přenosový tok = 288<br/><br/> 2:<br/><br/> - 11025: &lt;16 &lt; = přenosový tok 24<br/><br/> - 12000: &lt;16 &lt; = přenosový tok 24<br/><br/> - 16000: &lt;16 &lt; = přenosový tok 40<br/><br/> - 22050: &lt;32 &lt; = přenosový tok 40<br/><br/> - 24000 : &lt;32 &lt; = přenosový tok 40<br/><br/> - 32000: &lt;40 &lt;= přenosová rychlost = 384<br/><br/> - 44100: &lt;96 &lt;= přenosová rychlost = 576<br/><br/> - 48000 : &lt;96 &lt;= přenosový tok = 576<br/><br/> - 88200: 256 &lt; &lt;= přenosová rychlost = 576<br/><br/> - 96000: 256 &lt; &lt;= přenosová rychlost = 576<br/><br/> 5/6:<br/><br/> - 32000: 160 &lt; &lt;= přenosová rychlost = 896<br/><br/> - 44100: 240 &lt; &lt;= přenosová rychlost = 1024<br/><br/> - 48000: 240 &lt; &lt;= přenosová rychlost = 1024<br/><br/> - 88200: 640 &lt; &lt;= přenosová rychlost = 1024<br/><br/> - 96000: 640 &lt; &lt;= přenosová rychlost = 1024<br/><br/> 8:<br/><br/> - 32000 : &lt;224 &lt;= přenosový tok = 1024<br/><br/> - 44100 : &lt;384 &lt;= přenosová rychlost = 1024<br/><br/> - 48000: 384 &lt; &lt;= přenosová rychlost = 1024<br/><br/> - 88200: 896 &lt; &lt;= přenosová rychlost = 1024<br/><br/> - 96000: 896 &lt; &lt;= přenosová rychlost = 1024  
**HEAACV1** |1:<br/><br/> - 22050: přenosová rychlost = 8<br/><br/> - 24000: &lt;8 &lt;= přenosový tok = 10<br/><br/> - 32000: &lt;12 &lt;= přenosový tok = 64<br/><br/> - 44100: &lt;20 &lt;= přenosová rychlost = 64<br/><br/> - 48000: &lt;20 &lt;= přenosová rychlost = 64<br/><br/> - 88200: přenosová rychlost = 64<br/><br/> 2:<br/><br/> - 32000: &lt;16 &lt;= přenosový tok = 128<br/><br/> - 44100: &lt;16 &lt;= přenosový tok = 128<br/><br/> - 48000: &lt;16 &lt;= přenosový tok = 128<br/><br/> - 88200 : &lt;96 &lt;= přenosový tok = 128<br/><br/> - 96000: &lt;96 &lt;= přenosový tok = 128<br/><br/> 5/6:<br/><br/> - 32000 : &lt;64 &lt;= přenosový tok = 320<br/><br/> - 44100: &lt;64 &lt;= přenosová rychlost = 320<br/><br/> - 48000: &lt;64 &lt;= přenosová rychlost = 320<br/><br/> - 88200 : &lt;256 &lt;= přenosová rychlost = 320<br/><br/> - 96000: 256 &lt; &lt;= přenosová rychlost = 320<br/><br/> 8:<br/><br/> - 32000: &lt;96 &lt;= přenosová rychlost = 448<br/><br/> - 44100: &lt;96 &lt;= přenosová rychlost = 448<br/><br/> - 48000: &lt;96 &lt;= přenosová rychlost = 448<br/><br/> - 88200: 384 &lt; &lt;= přenosová rychlost = 448<br/><br/> - 96000: 384 &lt; &lt;= přenosová rychlost = 448  
**HEAACV2** |2:<br/><br/> - 22050: &lt;8 &lt;= přenosový tok = 10<br/><br/> - 24000: &lt;8 &lt;= přenosový tok = 10<br/><br/> - 32000: &lt;12 &lt;= přenosový tok = 64<br/><br/> - 44100: &lt;20 &lt;= přenosová rychlost = 64<br/><br/> - 48000: &lt;20 &lt;= přenosová rychlost = 64<br/><br/> - 88200: &lt;64 &lt;= přenosová rychlost = 64  
  
## <a name="clip"></a><a name="Clip"></a>Klip
### <a name="attributes"></a>Atributy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Starttime** |**xs:doba trvání** |Určuje čas zahájení prezentace. Hodnota StartTime musí odpovídat absolutní časová razítka vstupní video. Pokud má například první snímek vstupního videa časové razítko 12:00:10.000, měl by být StartTime alespoň 12:00:10.000 nebo vyšší. |
| **Doba trvání** |**xs:doba trvání** |Určuje dobu trvání prezentace (například vzhled překrytí ve videu). |

## <a name="output"></a><a name="Output"></a>Výstup
### <a name="attributes"></a>Atributy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Název_souboru** |**xs:řetězec** |Název výstupního souboru<br/><br/> K vytvoření názvů výstupních souborů můžete použít makra popsaná v následující tabulce. Například:<br/><br/> **"Výstupy": [ { "FileName": "{Basename}*{Resolution}*{Bitrate}.mp4", "Format": { "Type": "MP4Format" } } ]** |

### <a name="macros"></a>Makra

| Makro | Popis |
| --- | --- |
| **{Název_base_}** |Pokud provádíte kódování VoD, {Basename} je prvních 32 znaků vlastnosti AssetFile.Name primárního souboru ve vstupním datovém zdroji.<br/><br/> Pokud je vstupní matyní živý archiv, je {Basename} odvozen z atributů trackName v manifestu serveru. Pokud odesíláte úlohu podklipu pomocí TopBitrate, jako v: "<\>VideoStream\>TopBitrate</VideoStream ", a výstupní soubor obsahuje video, pak {Basename} je prvních 32 znaků trackName vrstvy videa s nejvyšším datovým tokem.<br/><br/> Pokud místo toho odesíláte úlohu podklipu pomocí všech vstupních datových toků, například "<VideoStream\>*</VideoStream\>", a výstupní soubor obsahuje video, pak {Basename} je prvních 32 znaků trackName odpovídající vrstvy videa. |
| **{Kodek}** |Mapy na "H264" pro video a "AAC" pro audio. |
| **{Bitrate}** |Cílový přenosový tok videa, pokud výstupní soubor obsahuje video a zvuk, nebo cílový zvukový přenosový tok, pokud výstupní soubor obsahuje pouze zvuk. Použitá hodnota je přenosová rychlost v kbps. |
| **{Kanál}** |Počet zvukových kanálů, pokud soubor obsahuje zvuk. |
| **{Šířka}** |Šířka videa v obrazových bodech ve výstupním souboru, pokud soubor obsahuje video. |
| **{Výška}** |Výška videa v obrazových bodech ve výstupním souboru, pokud soubor obsahuje video. |
| **{Rozšíření}** |Dědí z vlastnosti "Typ" pro výstupní soubor. Název výstupního souboru má příponu, která je jednou z: "mp4", "ts", "jpg", "png" nebo "bmp". |
| **{Index}** |Povinné pro miniaturu. Měl by být přítomen pouze jednou. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a>Video (komplexní typ dědí z kodeku)
### <a name="attributes"></a>Atributy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Zahájení** |**xs:řetězec** | |
| **Krok** |**xs:řetězec** | |
| **Rozsah** |**xs:řetězec** | |
| **Zachovat rezoluciAfterRotation** |**xs:logická hodnota** |Podrobné vysvětlení naleznete v následující části: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a>Zachovat rezoluciAfterRotation
Doporučuje se použít příznak **PreserveResolutionAfterRotation** v kombinaci s hodnotami rozlišení vyjádřenými v procentech (Width="100%" , Height="100%").  

Ve výchozím nastavení jsou nastavení rozlišení kódování (Šířka, Výška) v přednastavení standardu kodéru médií (MES) zaměřena na videa s otočením o 0 stupňů. Pokud je například vstupní video 1280 × 720 s otočením nulového stupně, výchozí přednastavení zajistí, že výstup má stejné rozlišení.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Pokud bylo vstupní video zachyceno s nenulovou rotací (například smartphone nebo tablet držený svisle), pak MES ve výchozím nastavení aplikuje nastavení rozlišení kódování (Šířka, Výška) na vstupní video a pak kompenzuje otočení. Podívejte se například na následující obrázek. Přednastavení používá Width = "100%", Height = "100%", které MES interpretuje jako vyžadující výstup, který má být 1280 pixelů široký a 720 pixelů vysoký. Po otočení videa se obrázek zmenší tak, aby se vešel do tohoto okna, což vede k oblastem sloupového rámečku vlevo a vpravo.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Alternativně můžete použít zachovat **preserveresolutionafterrotation** příznak a nastavte jej na "true" (výchozí je "false"). Takže pokud vaše přednastavení má šířku = "100%", Výška = "100%" a PreserveResolutionAfterRotation nastavena na "true", vstupní video, které je 1280 pixelů široký a 720 pixelů vysoký s 90 stupňovým otočením vytváří výstup s otočením nulového stupně, ale 720 pixelů široké a 1280 pixelů vysoký. Viz následující obrázek:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a>FormatGroup (skupina)
### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Formát Bmp** |**Formát Bmp** | |
| **PngFormat** |**PngFormat** | |
| **Formát Jpg** |**Formát Jpg** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a>Vrstva Bmp
### <a name="element"></a>Element

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **impulzu**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs:řetězec** | |

## <a name="pnglayer"></a><a name="PngLayer"></a>PngLayer
### <a name="element"></a>Element

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **impulzu**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Atributy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs:řetězec** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a>JpgLayer
### <a name="element"></a>Element

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **impulzu**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |
| **Kvalita**<br/><br/> minOccurs="0" |**xs:int** |Platné hodnoty: 1 (nejhorší)-100(nejlepší) |

### <a name="attributes"></a>Atributy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Podmínka** |**xs:řetězec** | |

## <a name="pnglayers"></a><a name="PngLayers"></a>PngVrstvy
### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="bez meze" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a>Vrstvy Bmp
### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **Vrstva Bmp**<br/><br/> minOccurs="0" maxOccurs="bez meze" |[Vrstva Bmp](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a>Vrstvy Jpg
### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="bez meze" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a>BmpImage (komplexní typ dědí z Video)
### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **PngVrstvy**<br/><br/> minOccurs="0" |[PngVrstvy](media-services-mes-schema.md#PngLayers) |Png vrstvy |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a>JpgImage (komplexní typ dědí z Video)
### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **PngVrstvy**<br/><br/> minOccurs="0" |[PngVrstvy](media-services-mes-schema.md#PngLayers) |Png vrstvy |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a>PngImage (komplexní typ dědí z Videa)
### <a name="elements"></a>Elementy

| Name (Název) | Typ | Popis |
| --- | --- | --- |
| **PngVrstvy**<br/><br/> minOccurs="0" |[PngVrstvy](media-services-mes-schema.md#PngLayers) |Png vrstvy |

## <a name="examples"></a>Příklady
Viz příklady přednastavení XML, které jsou vytvořeny na základě tohoto schématu, viz [Přednastavení úloh pro MES (Standard kodéru médií).](media-services-mes-presets-overview.md)

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

