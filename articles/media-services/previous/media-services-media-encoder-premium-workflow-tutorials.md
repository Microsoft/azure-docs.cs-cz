---
title: Kurzy Avanced Media Encoder Premium pracovního postupu
description: Tento dokument obsahuje postupů, které ukazují, jak provádět pokročilé úlohy s pracovním postupem Premium kodér médií a také jak vytvořit komplexní pracovní postupy pomocí Návrháře pracovního postupu.
services: media-services
documentationcenter: ''
author: xstof
manager: cfowler
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: 3cba7a6a8bf6fbe199664b738b68ceca3a7746f8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33789030"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Pokročilé kurzy Media Encoder Premium pracovního postupu
## <a name="overview"></a>Přehled
Tento dokument obsahuje návody, které ukazují, jak přizpůsobit pracovní postupy s **Návrhář postupu provádění**. Můžete najít soubory skutečné pracovního postupu [zde](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>TOC
Jsou pokryta následující témata:

* [Kódování MXF do MP4 s jednou přenosovou rychlostí](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Spuštění nového pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Pomocí vstupní soubor média](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Probíhá kontrola mediální datové proudy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Přidání video kodéru pro. Generování souboru MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Kódování zvukový datový proud](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexní Audio a Video datových proudů do kontejner MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Zapisování souboru MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Vytváření Asset Media Services z výstupního souboru](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testování místně dokončení pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Kódování MXF do multibitrate soubory MP4 s rychlostmi – dynamické balení povoleno](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Přidání další jeden nebo více MP4 výstupy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Konfigurace názvů výstupní soubor](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Přidání samostatných sledovat zvuk](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Přidání souboru SMIL "ISM"](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Kódování MXF do multibitrate MP4 - rozšířené plán, podle kterého](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * [Přehled pracovního postupu k vylepšení](#workflow-overview-to-enhance)
  * [Konvence pro pojmenování souborů](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Publikování vlastnosti komponenty do kořenové pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Vygenerování výstupního souboru, jejichž názvy na hodnoty publikovaných vlastností](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Přidání miniatury do multibitrate MP4 výstup](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * [Přehled pracovního postupu přidat miniatury pro](#workflow-overview-to-add-thumbnails-to)
  * [Přidání JPG kódování](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Práci s barevný prostor převod](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Zápis miniatur](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Zjištění chyb v pracovním postupu](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Dokončení pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Oříznutí založené na čase multibitrate MP4 výstupu](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Přehled pracovního postupu, pokud chcete začít přidávat oříznutí na](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Použití oříznutí datového proudu](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Dokončení pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Představení komponentu pomocí skriptu](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Skriptování v rámci pracovního postupu: hello, world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Na základě rámce oříznutí multibitrate MP4 výstupu](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Přehled plán, podle kterého chcete začít přidávat oříznutí na](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Pomocí seznamu klip XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Úprava seznamu klip z součásti skriptování](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Přidání vlastnosti ClippingEnabled usnadnění práce](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Kódování MXF do MP4 s jednou přenosovou rychlostí
V této části ukazuje, jak vytvořit s jednou přenosovou rychlostí. Soubor MP4 s AAC-HE kódovaný zvuk ze. MXF vstupní soubor.

### <a id="MXF_to_MP4_start_new"></a>Spuštění nového pracovního postupu
Otevřete návrháře pracovních postupů a vyberte soubor > Nový pracovní prostor > plán, podle kterého převod

Nový pracovní postup ukazuje tři prvky:

* Primární zdrojový soubor
* Seznam klip ve formátu XML
* Výstupní soubor nebo prostředek  

![Nový pracovní postup kódování](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nový pracovní postup kódování*

### <a id="MXF_to_MP4_with_file_input"></a>Pomocí vstupní soubor média
Aby bylo možné přijímat souboru vstupní média, jeden začíná přidání komponentu vstupní soubor média. Chcete-li přidejte součást do pracovního postupu, podívejte se do vyhledávacího pole úložiště a přetáhněte na požadovanou položku na podokně návrháře. Opakujte akci pro vstupní soubor média a připojte komponentu primární zdrojový soubor vstupní připnete Filename ze vstupní soubor média.

![Připojených mediálních souborů vstup](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Připojených mediálních souborů vstup*

Na začátku Identifikujte příslušné ukázkový soubor pro použití při navrhování vlastní pracovní postup. Uděláte to tak, klepněte na pozadí návrháře podokně a pro vlastnost primární zdrojový soubor v podokně pravém vlastnost. Klikněte na ikonu složky a vyberte požadovaný soubor pro testování pracovního postupu. Vstupní soubor média součást zkontroluje soubor a naplní jeho výstup PIN tak, aby odrážela podrobnosti ukázkový soubor, který ho prověřovány.

![Vstupní soubor vyplněná média](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Vstupní soubor vyplněná média*

Teď, když je vyplněný vstupu, dalším krokem je nastavit výstupní nastavení kódování. Podobným způsobem primární zdrojový soubor byl nakonfigurován, teď nakonfigurovat vlastnost výstupní složky proměnné pod ním.

![Nakonfigurovaný vstupní a výstupní vlastnosti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Nakonfigurovaný vstupní a výstupní vlastnosti*

### <a id="MXF_to_MP4_streams"></a>Probíhá kontrola mediální datové proudy
Často je žádoucí vědět, jak datového proudu vypadá jako procházejících pracovního postupu. Chcete-li prověřit datový proud v libovolném bodě v pracovním postupu, stačí kliknout na výstupu nebo vstupní PIN kódu na některé z těchto komponent. V takovém případě zkuste klepnout na nekomprimované Video pin výstup ze vstupu soubor média. Zobrazí se dialogové okno otevře umožňující kontrola odchozí video.

![Probíhá kontrola pin výstup nekomprimované Video](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Probíhá kontrola pin výstup nekomprimované Video*

V tomto případě zobrazuje, že na video obsahuje 1920 × 1080 vstupu v 24 snímků za sekundu v 4:2:2 vzorkování video o téměř 2 minut.

### <a id="MXF_to_MP4_file_generation"></a>Přidání video kodéru pro. Generování souboru MP4
Teď nekomprimované Video a více výstup nekomprimované zvuk, které jsou k dispozici pro kódy PIN použít na vstupní soubor média. Chcete-li kódování příchozí video, komponentu kódování musí být přidán do pracovního postupu – v takovém případě pro generování. Soubory MP4.

Ke kódování datový proud videa na H.264, přidejte komponentu AVC kodér videa na plochu návrháře. Tato součást využívá uncompress datový proud videa jako vstup a poskytuje AVC komprimovaný datový proud videa na jeho výstupní kód pin.

![Bez připojení kodér AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Bez připojení kodér AVC*

Jeho vlastnosti určují, jak přesně kódování se stane. Pojďme Podíváme se na některých důležitější nastavení:

* Výstupní šířky a výšky výstup: Určuje rozlišení kódovaného videa. V takovém případě 640 x 360 je dobré nastavení.
* Obnovovací frekvence: Pokud nastavíte hodnotu průchozí je právě zavede obnovovací frekvence zdroje, je možné, když ji přepsat. Takový převod kmitočet snímků není pohybu vyrovnanou.
* Profil a úroveň: Určuje profil AVC a úroveň. Pohodlně získat další informace o různých úrovních a profily, klikněte na ikonu otazníku na komponentu AVC kodér videa a na stránce nápovědy se zobrazit více podrobností o jednotlivých úrovní. V tomto příkladu použijte profil hlavní na úrovni 3.2 (výchozí).
* Míra režimu řízení a přenosovou rychlostí (kb/s): v tomto scénáři zvolit konstantní přenosovou (CBR) výstup rychlostí 1 200 kb/s
* Video formát: poskytuje informace o VUI (Video použitelnost informace), který získá zápisu do datového proudu H.264 (straně informace, které můžou být použité ve decoder k vylepšení zobrazení, ale není nutná správně dekódovat):
* NTSC (typická pro USA nebo Japonsko, pomocí 30 fps)
* PAL (typická pro Evropu, pomocí 25 fps)
* GOP velikost režim: nastavení pevné velikosti GOP pro naše účely s intervalu klíč 2 sekund s GOPs uzavřen. Nastavení 2 sekundy zajišťuje kompatibilitu s dynamické balení Azure Media Services poskytuje.

Ke kanálu kodér AVC, připojte k vstupní pin nekomprimované Video z kodéru AVC pin výstup nekomprimované Video z média souboru vstupní komponenty.

![Kodér připojené AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Kodér připojené hlavní AVC*

### <a id="MXF_to_MP4_audio"></a>Kódování zvukový datový proud
V tomto okamžiku původní nekomprimované zvukový datový proud ještě potřeba komprimovat. Pro kompresi zvuk datového proudu přidejte komponentu kodér AAC (Dolby) do pracovního postupu.

![Bez připojení kodér AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Bez připojení AAC kodér*

Teď je nekompatibility: při více než pravděpodobně vstupní soubor média bude mít dva různé nekomprimované zvukové datové proudy k dispozici je pouze jeden nekomprimované zvuk vstupní pin z kodéru AAC: jeden pro levý zvuk kanál a jeden pro vpravo. (V případě, že pracujete s prostorový zvuk, který je šest kanálů.) Proto není možné k přímému připojení zvukovém souboru ze zdroje vstupní soubor média do zvuk kodér AAC. Součást AAC očekává takzvané "prokládaná" zvuk datový proud: jeden datový proud, který má vlevo a vpravo kanály prokládaný mezi sebou. Jednou víme z našich zdrojový soubor média, jsou zvukové sleduje na jaké pozici ve zdrojovém jsme může generovat takové prokládaná zvuk datový proud s správně přiřazené mluvčího pozice pro doleva a doprava.

Nejdřív jednu chce generovat prokládaná datového proudu z požadovaných zdrojových zvukové kanály. Zvuk Interleaver datového proudu součást zpracovává to pro nás. Přidejte ji do pracovního postupu a připojte zvuk výstupy ze vstupu soubor média do ní.

![Připojení Interleaver zvukový datový proud](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Připojení Interleaver zvukový datový proud*

Teď, když máme prokládaná zvukový datový proud, jsme ještě nebyla určete, kam pozice mluvčího doleva nebo doprava, které chcete přiřadit. Aby bylo možné tuto verzi uveďte, můžeme využít přidělující mluvčího pozice uživatel.

![Přidání přidělující uživatel mluvčího pozice](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Přidání přidělující uživatel mluvčího pozice*

Nakonfigurujte přidělující uživatel mluvčího pozice pro použití s stereo vstupního datového proudu prostřednictvím filtr přednastavení kodér "Vlastní" a předvolby Channel, které se nazývá "2.0 (L, R)." (To přiřadí pozice levého mluvčího do kanálu 1 a pozice správné mluvčího kanál 2.)

Připojte ke vstupu kodéru AAC výstup přidělující mluvčího pozice uživatel. Potom je nutné určit AAC kodéru pro práci s "2.0 (L, R)" kanál předvolby, aby věděli, jak nakládat s stereofonní zvuk jako vstup.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexní Audio a Video datových proudů do kontejner MP4
Zadaný naše AVC kódovaného datový proud videa a naše AAC kódovaný zvukový datový proud, jsme můžete zaznamenat do. Kontejner MP4. Kombinování různých datových proudů v jeden proces se nazývá "multiplexní" (nebo "muxing"). V takovém případě jsme se prokládání zvuk a video datové proudy v jediném souvislé. Balíček MP4. Komponenty, která koordinuje to pro. Kontejner MP4 se nazývá multiplexor ISO MPEG-4. Přidat na plochu návrháře a připojte se k jeho vstupů kodér videa AVC i AAC kodér.

![Připojené MPEG4 multiplexor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Připojené MPEG4 multiplexor*

### <a id="MXF_to_MP4_writing_mp4"></a>Zapisování souboru MP4
Při zápisu výstupního souboru, komponentu výstupního souboru se používá. Jsme můžete připojit to k výstupu modulu multiplexor ISO MPEG-4, aby získá jeho výstup zapsán na disk. K tomu, připojte k zápisu kódu pin vstupní soubor výstupu pin výstup kontejneru (MPEG-4).

![Připojení výstupního souboru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Připojení výstupního souboru*

Název souboru, který se používá, je určen vlastností souboru. Tuto vlastnost mohou být pevně zakódované zadanou hodnotu, pravděpodobně jeden chce nastavit pomocí výrazu.

Pracovní postup automaticky určit výstup souboru název vlastnosti z výrazu, klikněte na tlačítko vedle názvu souboru (vedle ikony složky). Z rozevírací nabídky vyberte "Výraz". Otevře editor výrazů. Nejprve vymažte obsah editoru.

![Prázdný výraz editoru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Prázdný výraz editoru*

Výraz editor umožňuje zadat všechny literálovou hodnotou, smíšený s jedním nebo více proměnných. Proměnné začínat znak dolaru. Protože jste dosáhl klíči $ editoru zobrazí rozevírací seznam s volbou proměnných k dispozici. V našem případě použijeme kombinaci proměnnou directory výstupní a vstupní soubor základní název proměnné:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Naplní se Editor výrazů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Naplní se Editor výrazů*

> [!NOTE]
> Chcete-li zobrazit výstupního souboru kódování úlohy v Azure, musíte zadat hodnotu v editoru výraz.
>
>

Jakmile potvrdíte výraz zasažení ok, zobrazí v okně Vlastnosti náhled co v tuto chvíli hodnota vyřeší vlastnosti souboru.

![Výraz souboru přeloží dir výstup](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Výraz souboru přeloží dir výstup*

### <a id="MXF_to_MP4_asset_from_output"></a>Vytváření Asset Media Services z výstupního souboru
Když jsme napsali výstupní soubor MP4, potřebujeme ještě znamenat, že tento soubor patří na výstupní asset, který služba media services generuje v důsledku spuštění tento pracovní postup. Za tímto účelem se používá uzlu výstupní soubor nebo Asset na plátně pracovního postupu. Všechny příchozí soubory do tohoto uzlu zařazení výsledné prostředku Azure Media Services.

Komponentu výstupního souboru se připojte k výstupu. soubor/Asset součást dokončení pracovního postupu.

![Dokončení pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Dokončení pracovního postupu*

### <a id="MXF_to_MP4_test"></a>Testování místně dokončení pracovního postupu
K testování pracovního postupu místně, stiskněte tlačítko Přehrát akci na panelu nástrojů v horní části. Po dokončení provádění pracovního postupu zkontrolujte výstup generovaný do nakonfigurované výstupní složky. Uvidíte dokončení výstupního souboru MP4, který kódované ze souboru MXF vstupní zdroj.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Kódování MXF do MP4 - multibitrate povolené dynamické balení
Tento návod vytvoří sadu souborů MP4 s více přenosovou rychlostí s kódováním AAC zvuk z jedné. MXF vstupní soubor.

Pokud výstupní asset více přenosovými rychlostmi požadované pro použití v kombinaci s funkcemi dynamické balení nabízené službou Azure Media Services, více souborů MP4 zarovnaný GOP jednotlivých potřebné pro různé přenosovou rychlostí a řešení má být vygenerován. Uděláte to tak, [kódování MXF do MP4 s jednou přenosovou rychlostí](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) návod poskytuje nám to dobrý výchozí bod.

![Spuštění pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Spuštění pracovního postupu*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Přidání další jeden nebo více MP4 výstupy
Každý soubor MP4 v našem výsledné asset Azure Media Services podporuje různé přenosové rychlosti a řešení. Umožňuje přidat jeden nebo více souborů MP4 výstup do pracovního postupu.

Pokud chcete mít jistotu, že jsme všechny naše video kodéry vytvořené pomocí stejné nastavení, je nejvhodnější duplicitní stávající kodér videa AVC a nakonfigurovat jiné kombinace překlad IP adres a přenosovou rychlostí (přidejme mezi 960 x 540 na 25 snímků za sekundu při 2,5 MB/s ). Duplicitní stávajícího kodéru, kopírování vložte jej na plochu návrháře.

Připojte pin výstup nekomprimované Video vstupní soubor média do naší nové AVC komponenty.

![Druhý AVC kodér připojení](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Druhý AVC kodér připojení*

Nyní přizpůsobíte konfiguraci pro naše nové kodér AVC výstup 960 x 540 2,5 MB/s. (Použijte jeho vlastnosti "výstup šířka", "Výstup výšky" a "Přenosovou rychlostí (kbps)" pro tuto.)

Zadané chceme používat výsledné asset společně s Azure Media Services dynamické balení, koncový bod streamování musí být schopný vytvářet z těchto souborů MP4 s fragmenty HLS nebo fragmentovaný soubor MP4/DASH, které přesně odpovídají navzájem způsobem, který klienti, kteří jsou přepínání mezi různých přenosových rychlostí získat jediného smooth průběžné videa a zvuku rozhraní. Chcete-li toho docílit, je potřeba ujistěte, že ve vlastnostech obou AVC kodéry GOP ("skupina obrázky") je velikost pro oba soubory MP4 nastavena na 2 sekund, což lze provést:

* nastavení režimu velikost GOP GOP pevnou velikost a
* Interval rámce klíč, který má dvě sekundy.
* také nastavit řízení IDR GOP na uzavřený GOP zajistit všechny GOPs stojící na své vlastní bez závislosti

Chcete-li tento pracovní postup čtení snadněji pochopit, přejmenujte první kodér AVC k "kodér videa AVC 640 x 360 1200 kb/s" a druhý kodér AVC "kodér videa AVC 960 x 540 2 500 kb/s."

Přidáte druhý multiplexor ISO MPEG-4 a druhý výstupního souboru. Připojení k nové kodér AVC multiplexor a ujistěte se, že její výstup se přesměruje do výstupního souboru. Pak také připojte AAC zvuk kodér výstup do nového multiplexor na vstup. Výstupní soubor pak můžete pak být připojen k uzlu výstupní soubor nebo Asset tím ho přidáte do služby Asset média, který bude vytvořen.

![Druhý multiplexor a výstup souboru připojení](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Druhý multiplexor a výstup souboru připojení*

Zajištění kompatibility se službou Azure Media Services dynamické balení nakonfigurujte multiplexor na režimu bloku na hodnotu GOP count nebo doba trvání a nastavte GOPs za bloku na hodnotu 1. (To by měl být výchozí.)

![Režimy multiplexor bloku](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Režimy multiplexor bloku*

Poznámka: můžete tento postup opakujte pro další přenosovou rychlostí a rozlišení kombinace, které chcete přidali do výstupní asset.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfigurace názvů výstupní soubor
Máme více než jeden jeden soubor přidán na výstupní asset. To poskytuje potřeba Ujistěte se, že názvy souborů pro každou výstupní soubory se liší od sebe navzájem a možná i použít zadávání názvů tak, že se z názvu souboru se zabývajících se.

Názvy výstupního souboru se dá řídit přes výrazy v návrháři. Otevřete podokno vlastností pro jednu ze součástí výstup souboru a otevřete editor výraz pro vlastnost souboru. Naše první výstupní soubor byl nakonfigurovaný pomocí následující výraz (projděte si kurz pro přecházející z [MXF k výstupu MP4 s jednou přenosovou rychlostí](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

To znamená, že naše filename je dáno dvou proměnných: se zapisovat do výstupního adresáře a základní název zdrojového souboru. První je zpřístupněná jako vlastnost v kořenovém adresáři pracovního postupu a je určen příchozích souborů. Výstupní adresář se používá pro místní testování; Tato vlastnost bude přepsáno modul pracovních postupů při spouští pracovní postup procesoru založené na cloudu média ve službě Azure Media Services.
Konzistentní výstup pojmenování poskytnout i naše výstupní soubory, změňte první soubor pojmenování výraz, který se:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

a druhou pro:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Spusťte test zprostředkující spustit a ujistěte se generují správně obou výstupní soubory MP4.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Přidání samostatných sledovat zvuk
Jako ukážeme později při se vygeneruje soubor .ism pomocí našich výstupní soubory MP4, bude také vyžadujeme pouze zvukový soubor MP4 jako zvuk sledování pro naše adaptivní streamování. K vytvoření tohoto souboru, přidejte další multiplexor do pracovního postupu (multiplexor ISO-MPEG-4) a připojit kodér AAC výstup pin s jeho vstupní PIN kódu pro sledování 1.

![Zvuk multiplexor přidán](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Zvuk multiplexor přidán*

Vytvořte třetí výstup souboru součást, kterou je výstup výstupní datový proud z multiplexor a nakonfigurovat souboru pojmenování výraz jako:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Zvuk multiplexor vytvoření výstupního souboru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Zvuk multiplexor vytvoření výstupního souboru*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Přidání. Soubor SMIL ISM
Dynamické balení pro práci v kombinaci s MP4 soubory (i pouze MP4) v našem asset Media Services, je také nutné soubor manifestu (také označovaný jako soubor "SMIL": synchronizované multimédia integrace jazyka). Tento soubor k Azure Media Services určuje, jaké soubory MP4 jsou dostupné pro dynamické balení a které z nich vzít v úvahu pro zvuk streamování. Typické souboru manifestu sady na MP4 s jeden datový proud zvuku vypadá takto:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>
```

Soubor .ism obsahuje v rámci příkazu switch, odkaz na každé jednotlivé video soubory MP4 a navíc tyto odkazy zvukový soubor také jeden (nebo více) k MP4, který obsahuje pouze zvukovém souboru.

Generování souboru manifestu pro náš sadu MP4 na lze provést prostřednictvím komponenty s názvem "Zapisovač AMS Manifest." Pokud chcete použít, přetáhněte ji na plochu a připojit "Zápis úplná" PIN výstup ze tří součástí výstup souboru Manifest zapisovače AMS vstupu. Ujistěte se, připojit výstup AMS Manifest zapisovače pro výstupní soubor nebo Asset.

Stejně jako u našich souboru výstup součásti, nakonfigurujte název výstupního souboru .ism s výrazem:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Naše dokončení pracovního postupu vypadá níže:

![Dokončení MXF multibitrate MP4 pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Dokončení MXF multibitrate MP4 pracovního postupu*

## <a id="MXF_to__multibitrate_MP4"></a>Kódování MXF do multibitrate MP4 - rozšířené plán, podle kterého
V [předchozího návodu pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) jste viděli, jak jeden vstupní asset MXF, mohou být převedeny na výstupní asset s souborů MP4 s více přenosovými rychlostmi, pouze zvukový soubor MP4 a soubor manifestu pro použití ve spojení s Azure média Dynamické balení služby.

Tento návod ukazuje, jak některé charakteristik jdou vylepšit a provedené pohodlnější.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Přehled pracovního postupu k vylepšení
![Pracovní postup Multibitrate MP4 k vylepšení](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Pracovní postup Multibitrate MP4 k vylepšení*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>Konvence pro pojmenování souborů
V předchozím postupu jsme jednoduchý výraz zadaný jako základ pro generování názvy výstupního souboru. Když máme některé duplikace: všech součástí jednotlivých výstupní soubor zadaný takové výraz.

Například naše součást výstupní soubor pro první videosoubor nakonfigurovaná s tímto výrazem:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Když pro druhý výstupu videa máme výraz jako:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Nebylo by čisticí méně chyba náchylné k chybám a pohodlnější, pokud jsme může odeberte některé z této duplikace a ujistěte se, co konfigurovatelnější místo? Naštěstí můžeme: Možnosti nástroje designer výrazu v kombinaci s možnost vytvářet vlastní vlastnosti v kořenovém adresáři naše pracovní postup bude poskytovat úroveň pohodlí.

Předpokládejme, že jsme budete jednotka filename konfigurace z přenosových rychlostí jednotlivých souborů MP4. Tyto přenosových rychlostí, které budete usilujeme o nakonfigurovat na jednom místě (v kořenovém naše grafu), kde budete mít přístup ke konfiguraci a jednotky generování názvů souborů. K tomuto účelu jsme začít publikování vlastnost přenosovou rychlostí z obou AVC kodérů do kořenového adresáře naše pracovního postupu, takže bude přístupný z obou kořene také kvůli kodéry AVC. (I když se zobrazí v dva různé body, se jenom jedna nadřazená hodnota.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Publikování vlastnosti komponenty do kořenové pracovního postupu
Otevřete první kodér AVC, přejděte na vlastnost přenosovou rychlostí (kbps) a z rozevíracího seznamu vyberte publikovat.

![Publikování vlastnost přenosovou rychlostí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publikování vlastnost přenosovou rychlostí*

Dialogové okno publikování publikovat do kořenového adresáře naše grafu pracovního postupu nakonfigurujte publikované název "video1bitrate" a "1 Video přenosovou rychlostí" čitelné zobrazovaný název. Nakonfigurovat vlastní název skupiny označuje "Streamování přenosových rychlostí" a stiskněte tlačítko Publikovat.

![Publikování vlastnost přenosovou rychlostí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Dialogové okno Vlastnosti přenosovou rychlostí*

Opakujte stejný pro vlastnost přenosovou rychlostí druhý kodér AVC s názvem "video2bitrate" s názvem "Video přenosovou rychlostí 2," a zobrazení ve stejné skupině vlastní "Streamování přenosových rychlostí".

Pokud jsme teď prověřit vlastnosti kořenové pracovního postupu, uvidíme naše vlastní skupiny s dvě vlastnosti publikovaných objeví. Obě jsou odrážející hodnotu jejich odpovídajících přenosovou rychlostí AVC kodér.

![Props publikované přenosovou rychlostí v kořenovém adresáři pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Pokaždé, když jsme potřeba přístup k tyto vlastnosti z kódu nebo z výrazu, jsme tak postupujte takto:

* z vloženého kódu z komponenty vpravo pod kořenovým adresářem: node.getPropertyAsString('.. / video1bitrate ", null)
* ve výrazu: ${ROOT_video1bitrate}

Umožňuje dokončit skupině "Streamování přenosových rychlostí" pomocí publikování naše zvuk sledovat přenosovou rychlostí na něm také. V rámci vlastností kodér AAC vyhledejte nastavení přenosovou rychlostí a publikovat vyberte z rozevíracího seznamu vedle sebe. Publikovat do kořenového adresáře grafu s názvem "audio1bitrate" a zobrazované jméno "1 zvuk přenosovou rychlostí" v rámci naší vlastní skupiny "Streamování přenosových rychlostí".

![Dialogové okno pro zvuk přenosovou rychlostí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Dialogové okno pro zvuk přenosovou rychlostí*

![Výsledný videa a zvuku props v kořenovém adresáři](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Výsledný videa a zvuku props v kořenovém adresáři*

Některé z těchto tří změna hodnoty také změní konfiguraci a změní hodnoty na příslušné komponenty jsou propojeny s (a případně pomocí publikovat).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Vygenerování výstupního souboru, jejichž názvy na hodnoty publikovaných vlastností
Místo hardcoding naše názvy generovaný soubor jsme nyní můžete změnit výraz naše filename jednotlivých součástí výstupního souboru se spoléhat na vlastnosti přenosovou rychlostí, nemůžeme zveřejněny v kořenovém adresáři grafu. Počínaje naše první výstupního souboru, najít vlastnost soubor a upravit výraz takto:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Různé parametry v tomto výrazu můžete získat přístup a zadá stiskne dolaru na klávesnici v okně výrazu. Jeden z dostupných parametrů je naše video1bitrate vlastnost, kterou jsme dříve publikované.

![Přístup k parametry v rámci výrazu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Přístup k parametry v rámci výrazu*

Proveďte stejný pro výstup souboru pro naše druhý video:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

a pro výstup pouze zvukový soubor:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Pokud nyní Změníme přenosovou rychlostí pro některý ze souborů video nebo zvuk, příslušné kodér konfigurace bude změněna a konvence název souboru na základě přenosovou rychlostí se dodržení vše probíhá automaticky.

## <a id="thumbnails_to__multibitrate_MP4"></a>Přidání miniatury do multibitrate MP4 výstup
Od pracovní postup, který generuje [multibitrate MP4 výstup z MXF vstup](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), jsme se teď vyhledávání do přidávání miniatur do výstupu.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Přehled pracovního postupu přidat miniatury pro
![Multibitrate MP4 pracovní postup spustit z](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Multibitrate MP4 pracovní postup spustit z*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Přidání JPG kódování
Srdcem naše miniatur generování bude komponentu kodér JPG, můžete provést výstup JPG soubory.

![Kodér JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Kodér JPG*

Nelze připojit ale přímo naší nekomprimované Video datového proudu ze vstupu soubor média do kodér JPG. Místo toho očekává možné předat jednotlivé snímky. To, provedeme prostřednictvím brány rámce Video komponent.

![Připojení brány rámce kodéru, JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Připojení brány rámce kodéru, JPG*

Pro bránu rámce každých mnoho sekund nebo rámce umožňuje snímek videa a předat. Intervalu a časového posunu s který tomu je možné konfigurovat ve vlastnostech.

![Vlastnosti videa rámce brány](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Vlastnosti videa rámce brány*

Umožňuje vytvořit miniaturu každou minutu nastavení režimu (v sekundách) a Interval, který má 60.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Práci s barevný prostor převod
Když se jeví logické, že oba PIN kódy nekomprimované Video rámce brány a vstupní soubor média lze nyní připojit, jsme by by tak v takovém případě se zobrazí upozornění.

![Vstupní barva místo chyby](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Vstupní barva místo chyby*

Je to proto, že způsob v barvu, která je reprezentována informace v našem původní nezpracovaná nekomprimované datový proud videa, pocházejících z našich MXF se liší od očekává kodér JPG. Přesněji řečeno takzvané "barvu místo" "RGB" nebo "Ve stupních šedi" se očekává v toku. To znamená, že příchozí video datový proud videa rámce brány musí mít převodu z týkající se jeho barevný prostor použije první.

Přetáhněte na pracovního postupu místo převaděč barva - Intel a připojte ho k naší rámce brány.

![Připojení úpravy místo barev](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Připojení úpravy místo barev*

V okně Vlastnosti vyberte položku BGR 24 ze seznamu přednastavení.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Zápis miniatur
Liší od našich video MP4, komponentu JPG kodér výstupy více než jeden soubor. Aby bylo možné zpracovat takové, lze použít komponentu scény vyhledávání JPG souboru zapisovače: přebírá příchozí miniatur JPG a zapíše je, každý název souboru se na konci pomocí jiné číslo. (Číslo obvykle určující počet sekund/jednotky v datový proud, který byl miniaturu čerpají z.)

![Představení zapisovač souboru JPG scény vyhledávání](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Představení zapisovač souboru JPG scény vyhledávání*

Nakonfigurovat vlastnost výstupní cesta ke složce ve výrazu: ${ROOT_outputWriteDirectory}

a vlastnost Filename předponu s:

    ${ROOT_sourceFileBaseName}_thumb_

Předpona, která určuje, jak jsou právě miniatur soubory pojmenovány. Budou se na konci číslo určující pozici v datovém proudu.

![Vlastnosti scény zapisovače souboru JPG vyhledávání](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Vlastnosti scény zapisovače souboru JPG vyhledávání*

Zapisovač souboru JPG vyhledávání scény připojte k uzlu výstupní soubor nebo Asset.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Zjištění chyb v pracovním postupu
Vstupní barva převaděč místa se připojte k nezpracované nekomprimované výstupu videa. Nyní testu místní spuštění pracovního postupu. Je velmi pravděpodobné pracovní postup bude najednou zastavit provádění a znamenat s červenou outline na komponentu došlo k chybě:

![Chyba místo převaděč barev](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Chyba místo převaděč barev*

Klepněte na malé červenou ikonu "E" v horním pravém rohu komponentu barva místo převaděč vidět co je z důvodu kódování pokus se nezdařil.

![Barva místo převaděč chybové dialogové okno](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Barva místo převaděč chybové dialogové okno*

Zjistíte, jak můžete vidět, že příchozí standard pro převaděč místo barva barevný prostor musí být rec601 pro naše požadovaný převod YUV RGB. Naše datový proud nepodporuje zjevně znamenat jeho rec601. (Dop. 601 je standard pro kódování prokládaných analogovým video signály v digitální podobě videa. Určuje oblast active pokrývajících 720 světlostí a 360 chrominance vzorků na každý řádek. Barevné kódování systému se označuje jako YCbCr 4:2:2.)

Chcete-li odstranit tento problém, jsme budete znamenat na metadata z našich datový proud, který jsme pracujete s rec601 obsah. Uděláte to tak použijeme Video datový typ aktualizační součásti, kterou jsme budete put mezi naše neformátovaná a součást převod místo barev. Tento datový typ aktualizační umožňuje ruční aktualizace určité video dat vlastnosti typu. Nakonfigurujte označíte, místo barva standardní z "Rec 601". To způsobí, že aktualizační typ dat Video k označení datový proud s barevný prostor "Rec 601", pokud se žádné barevný prostor ještě definice. (Ho nebude přepsat všechny existující metadata, pokud přepsání zaškrtnuto.)

![Aktualizace barva místo Standard na aktualizační typ dat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Aktualizace barva místo Standard na aktualizační typ dat*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Dokončení pracovního postupu
Teď, když naše pracovního postupu je dokončena, proveďte další testovací spustit zobrazíte předává.

![Dokončení pracovního postupu pro výstup více mp4 s miniaturami](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Dokončení pracovního postupu pro výstup více mp4 s miniaturami*

## <a id="time_based_trim"></a>Oříznutí založené na čase multibitrate MP4 výstupu
Od pracovní postup, který generuje [multibitrate MP4 výstup z MXF vstup](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), jsme se teď vyhledávání do ořezávání video zdroje podle časová razítka.

### <a id="time_based_trim_start"></a>Přehled pracovního postupu, pokud chcete začít přidávat oříznutí na
![Spouští pracovní postup pro přidání oříznutí na](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Spouští pracovní postup pro přidání oříznutí na*

### <a id="time_based_trim_use_stream_trimmer"></a>Použití oříznutí datového proudu
Oříznutí datového proudu součást umožňuje trim na začátek a konec vstupního datového proudu základní na časování informace (sekund, minut,...). Oříznutí nepodporuje na základě rámce oříznutí.

![Oříznutí datového proudu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Oříznutí datového proudu*

Místo propojení AVC kodéry a přidělující mluvčího pozice uživatel do vstupní soubor média přímo, jsme budete uveďte mezi ty oříznutí datového proudu. (Jeden pro video signál a jeden pro prokládaná zvukový signál.)

![V rozmezí PUT oříznutí datového proudu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*V rozmezí PUT oříznutí datového proudu*

Umožňuje nakonfigurovat oříznutí tak, aby jsme pouze zpracovat videa a zvuku mezi 15 sekund a ve videu 60 sekund.

Přejděte do vlastností oříznutí datový proud videa a nakonfigurovat i čas spuštění (15 s) a koncový čas (60 s) vlastnosti. Pokud chcete mít jistotu, že jsou obě naše audio a video oříznutí vždy nakonfigurovaná na stejné počáteční a koncová hodnota, budeme publikovat těch, které se s kořenem pracovního postupu.

![Počáteční čas vlastnost z datového proudu oříznutí publikování](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Počáteční čas vlastnost z datového proudu oříznutí publikování*

![Publikování dialogové okno vlastností pro spuštění](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publikování dialogové okno vlastností pro spuštění*

![Publikování dialogové okno vlastností pro koncový čas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publikování dialogové okno vlastností pro koncový čas*

Pokud jsme teď prověřit kořenu naše pracovního postupu, jsou obě vlastnosti zobrazit a konfigurovat přehledně odtud.

![Publikované vlastnosti, které jsou k dispozici v kořenovém adresáři](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Publikované vlastnosti, které jsou k dispozici v kořenovém adresáři*

Nyní otevřete vlastnosti oříznutí z zvuk oříznutí a nakonfigurujte počátečního a koncového času s výrazem, který odkazuje na publikovaná vlastnosti v kořenovém adresáři naše pracovního postupu.

Audio ořezávání čas spuštění:

    ${ROOT_TrimmingStartTime}

a pro její koncový čas:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Dokončení pracovního postupu
![Dokončení pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Dokončení pracovního postupu*

## <a id="scripting"></a>Představení komponentu pomocí skriptu
Skriptované součásti můžete spustit libovolný skripty během provádění fáze naše pracovního postupu. Existují čtyři jiné skripty, které mohou být provedeny, každý s konkrétními vlastnostmi a jejich vlastní místo v životním cyklu pracovního postupu:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

V dokumentaci komponentu skriptování přejde podrobněji pro každou z výše. V [v následující části](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), **realizeScript** skriptování součásti se používá pro konstrukci cliplist xml za chodu při spuštění pracovního postupu. Tento skript je volána v průběhu instalace součástí, která se dělá jenom jednou v jeho průběhu životního cyklu.

### <a id="scripting_hello_world"></a>Skriptování v rámci pracovního postupu: hello, world
Přetáhněte součást skripty na plochu návrháře a přejmenujte ji (například "SetClipListXML").

![Přidání skriptované komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Přidání skriptované komponenty*

Pokud si prohlédnout vlastnosti komponenty skriptování, čtyři typy jiný skript bude uvedené, se každý konfigurovat, a další skript.

![Skriptované vlastnosti komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Skriptované vlastnosti komponenty*

Vymažte processInputScript a otevřete editor pro realizeScript. Nyní jsme se nastavené a připravené ke spuštění skriptování.

Jazyk Groovy dynamicky kompilované skriptovací jazyk pro platformu Java, která uchovává kompatibilitu s Java. Většinu kódu v jazyce Java ve skutečnosti, je platný Groovy kód.

Můžete napsat skript groovy jednoduché hello world v kontextu naše realizeScript. V editoru zadejte následující údaje:

    node.log("hello world");

Teď spusťte místní testovací běh. Po této spustit zkontrolujte (prostřednictvím kartě systému na komponentu skripty) vlastnost protokoly.

![Výstup Hello world protokolu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Výstup Hello world protokolu*

Objekt uzlu, který jsme volat metodu protokolu, odkazuje na našich aktuální "uzel" nebo součást, kterou jsme se skriptování v rámci. Všechny komponenty jako takový má schopnost výstupní protokolování data, k dispozici prostřednictvím kartě systému. V takovém případě jsme výstup řetězcový literál "hello, world". Důležité si uvědomit, zde je, že to může být být neocenitelnou pomocí ladicí nástroj vám poskytnou přehled na skript je ve skutečnosti činnosti.

Z našich skriptovací prostředí máme také přístup k vlastnostem na dalších komponentách. Zkuste provést následující:

```java
    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);
```

Naše okno Protokol ukazuje nám následující:

![Výstup protokolu pro přístup k uzlu cesty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Výstup protokolu pro přístup k uzlu cesty*

## <a id="frame_based_trim"></a>Na základě rámce oříznutí multibitrate MP4 výstupu
Od pracovní postup, který generuje [multibitrate MP4 výstup z MXF vstup](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), jsme se teď vyhledávání do ořezávání zdroj videa na základě počtu rámce.

### <a id="frame_based_trim_start"></a>Přehled plán, podle kterého chcete začít přidávat oříznutí na
![Pracovní postup, pokud chcete začít přidávat oříznutí na](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Pracovní postup, pokud chcete začít přidávat oříznutí na*

### <a id="frame_based_trim_clip_list"></a>Pomocí seznamu klip XML
Ve všech předchozích kurzech pracovního postupu jsme použili komponentu vstupní soubor média jako naše vstupní zdroj videa. Pro tento konkrétní scénář, když budeme používat klip seznamu zdrojovou součástí místo. To by neměl být upřednostňovaný způsob práce; Zdroj seznamu klip používat jenom po skutečný důvod k tomu (jako v následujícím případě, kde provádíme za použití funkce klip seznamu oříznutí).

Přepnutí z našich vstupní soubor média ke zdroji klip seznamu, přetáhněte klip seznamu zdrojovou součástí na návrhovou plochu a PIN kód XML seznamu klip se připojit k uzlu XML seznamu klip Návrháře pracovního postupu. Zdroj seznamu klip s výstupní kód PIN, tím vyplníte podle našich vstup videa. Teď připojte nekomprimované Video a PIN kódů nekomprimované zvuk ze seznamu zdroje klip k příslušné AVC kodéry a Interleaver zvuk datového proudu. Nyní odeberte vstupní soubor média.

![Vstupní soubor média nahradí zdroji klip seznamu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Vstupní soubor média nahradí zdroji klip seznamu*

Klip seznamu zdrojovou součástí vezme jako vstupní "Klip seznam XML." Když vyberete zdrojový soubor, který testování s místně, tento klip seznamu xml je automaticky vyplněna za vás.

![Automaticky vyplněna klip seznamu XML – vlastnost](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automaticky vyplněna klip seznamu XML – vlastnost*

Vyhledávání poněkud blíže xml, to je, jak vypadá jako:

![Seznam klip dialogové okno Upravit](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Seznam klip dialogové okno Upravit*

To ale nemusí odpovídat možnosti klip seznamu xml. Jednou z možností, které máme je přidání "Trim" prvek v rámci obou videa a zvuku zdroj, například takto:

![Přidání do seznamu klip element a uvolnění dočasné paměti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Přidání do seznamu klip element a uvolnění dočasné paměti*

Je-li upravit xml seznamu klip takto výše a místní spustit test, zobrazí se na video správně byl oříznut mezi 10 a 20 sekund na videu.

Rozporu s touto co se stane, když provedete spuštění místní ale nebude tento stejný cliplist xml mají stejný účinek při použití v pracovní postup, který běží v Azure Media Services. Při spuštění služby Azure Premium kodér cliplist xml je generována pokaždé, když znovu podle vstupní soubor, který byl zadán úlohy kódování. To znamená, že by všechny změny, které jsme provést na xml bohužel přepsat.

Pro čítače xml cliplist vymazáním při kódování úloha se spustila, jsme můžete vygenerovat za chodu právě po začátku naše pracovního postupu. Tato vlastní akce můžete provést prostřednictvím co se nazývá "Skriptované komponenta". Další informace najdete v tématu [představení komponentu skripty](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Přetáhněte součást skripty na plochu návrháře a přejmenujte jej na "SetClipListXML."

![Přidání skriptované komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Přidání skriptované komponenty*

Když si prohlédnout vlastnosti komponenty skriptování, jsou čtyři typy jiný skript uvedené, se každý konfigurovat, a další skript.

![Skriptované vlastnosti komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Skriptované vlastnosti komponenty*

### <a id="frame_based_trim_modify_clip_list"></a>Úprava seznamu klip z součásti skriptování
Před jsme můžete přepsat cliplist xml, který je generován během spouštění pracovního postupu, budeme potřebovat přístup k cliplist xml vlastnosti a obsah. Jsme, postupujte takto:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Příchozí klip seznamu protokolována](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Příchozí klip seznamu protokolována*

Nejdřív potřebujeme způsob, jak určit z bod, který, dokud bod, který má být trim videa. Chcete-li to vhodné pro uživatele bez technical pracovního postupu, publikujte dvě vlastnosti do kořenového adresáře grafu. Chcete-li to provést, klikněte pravým tlačítkem na plochu návrháře a vyberte "Přidat vlastnost":

* První vlastnost: "ClippingTimeStart" typu: "Časový kód"
* Druhé vlastnosti: "ClippingTimeEnd" typu: "Časový kód"

![Přidat dialogové okno vlastností pro výstřižek počáteční čas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Přidat dialogové okno vlastností pro výstřižek počáteční čas*

![Publikovaná výstřižek props čas v kořenovém adresáři pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publikovaná výstřižek props čas v kořenovém adresáři pracovního postupu*

Nakonfigurujte obě vlastnosti vhodný hodnota:

![Nakonfigurujte výstřižek spuštění a ukončení vlastnosti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Nakonfigurujte výstřižek spuštění a ukončení vlastnosti*

Nyní z v rámci naší skriptu jsme přístup k obě vlastnosti, jako je to:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Okno Protokol zobrazující začátku a konci výstřižek](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Okno Protokol zobrazující začátku a konci výstřižek*

Umožňuje analyzovat řetězce časový kód do pohodlnější použití formuláře pomocí jednoduchého regulární výraz:

```java
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);
```

![Okno Protokol s výstupem Analyzovaná časový kód](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Okno Protokol s výstupem Analyzovaná časový kód*

Pomocí těchto informací po ruce jsme nyní můžete upravit cliplist xml tak, aby odrážela počáteční a koncový čas pro požadovanou rámce přesné výstřižek videa.

![Kód skriptu přidat trim elementy](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Kód skriptu přidat trim elementy*

K tomu bylo potřeba prostřednictvím operace manipulace s řetězci normální. Výsledný seznam xml upravené klip se nezapisují zpět na vlastnost clipListXML v kořenovém adresáři pracovního postupu prostřednictvím metody "SetProperty –". Okno Protokol po provedení jiného testu by zobrazit nám následující:

![V rozevíracím seznamu klip protokolování](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*V rozevíracím seznamu klip protokolování*

Proveďte spuštění testu chcete zobrazit, jak mají byl oříznut datové proudy videa a zvuku. Jako více než jeden testovací běh s různými hodnotami pro body oříznutí, můžete to udělat, můžete si všimnout, že ty, nebude vzít v úvahu ale! Důvodem je, že designer, na rozdíl od Azure modulu runtime nemůže přepsat cliplist xml každé spuštění. To znamená, že pouze první jste nastavili počáteční body a způsobí, že soubor xml k transformaci, všechny ostatní časy naše klauzule ochrana (pokud (clipListXML.indexOf ("<trim>") == -1)) zabrání přidávání jiný element uvolnění dočasné paměti, když je pracovní postup již jedna nachází.

Pokud chcete, aby naše pracovního postupu vhodné k testování místně, nejlepší přidáme některé údržby kód, který kontroluje, pokud element a uvolnění dočasné paměti byl již existuje. Pokud ano, jsme ho odebrat před pokračováním změnou xml s novými hodnotami. Místo použití manipulace prostý řetězec, je pravděpodobně bezpečnější to provést prostřednictvím skutečné xml objektový model analýze.

Předtím, než takový kód jsme můžete přidat, i když budeme potřebovat nejprve přidat počet příkazů pro import na začátku naše skriptu:

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;
```

Potom přidáme kód vyžaduje čištění:

```java
    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();
```

Tento kód jenom překročí bodu, na které přidáme trim elementy k cliplist xml.

V tuto chvíli jsme můžete spustit a změnit naše pracovní postup, jak tolik, jako chceme přitom má někdy použity změny času.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Přidání vlastnosti ClippingEnabled usnadnění práce
Jak je možné, že chcete, aby ořezávání provést, jsou umožňuje dokončit vypnout naše pracovního postupu přidáním pohodlný logický příznak, který označuje, zda chcete povolit ořezávání / výstřižek.

Publikujte jako předtím nové vlastnosti do kořenového adresáře naše pracovní postup volá "ClippingEnabled" typu "Logická hodnota".

![Publikovaná vlastnost pro povolení výstřižek](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publikovaná vlastnost pro povolení výstřižek*

Pomocí níže jednoduché ochrana klauzule jsme můžete zkontrolujte, zda je vyžadován oříznutí a rozhodnout, pokud naše klip seznam jako takový musí upravit, nebo ne.

```java
    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }
```

### <a id="code"></a>Kompletní kód

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }
```

## <a name="also-see"></a>Viz také
[Představení Premium kódování v Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Jak používat Premium kódování v Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Kódování obsahu na vyžádání pomocí Azure Media Service](media-services-encode-asset.md#media-encoder-premium-workflow)

[Formáty a kodeky pracovního postupu Media Encoderu Premium](media-services-premium-workflow-encoder-formats.md)

[Ukázkové soubory pracovního postupu](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Nástroj pro Azure Media Services Explorer](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
