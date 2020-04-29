---
title: Pokročilé kurzy k Media Encoderu Premium Workflow
description: Tento dokument obsahuje návody, které ukazují, jak provádět pokročilé úlohy pomocí Media Encoder Premium Workflow a také jak vytvářet komplexní pracovní postupy s využitím Návrhář postupu provádění.
services: media-services
documentationcenter: ''
author: xstof
manager: femila
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: christoc
ms.reviewer: xpouyat; juliako
ms.openlocfilehash: 1ab70d56bd3def58d0e814035070cf027a88cd3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79251008"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Pokročilé kurzy k Media Encoderu Premium Workflow
## <a name="overview"></a>Přehled
Tento dokument obsahuje návody, které ukazují, jak přizpůsobit pracovní postupy pomocí **Návrhář postupu provádění**. Skutečné soubory pracovního postupu najdete [tady](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>TOC
Jsou pokrytá následující témata:

* [Kódování MXF do typu MP4 s jednou přenosovou rychlostí](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Spouští se nový pracovní postup.](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Použití vstupu mediálního souboru](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Kontrola datových proudů médií](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Přidání kodéru videa pro. Generování souboru MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Kódování zvukového datového proudu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexování zvukového a obrazového streamu do kontejneru MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Zápis souboru MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Vytvoření prostředku Media Services z výstupního souboru](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Místní otestování dokončeného pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Kódování MXF na rychlostmi s více přenosovými rychlostmi – dynamické balení povoleno](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Přidání jednoho nebo více dalších výstupů MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Konfigurace názvů výstupů souborů](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Přidání samostatné zvukové stopy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Přidání souboru SMIL "ISM"](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [MXF kódování do s více přenosovými rychlostmi – rozšířený podrobný plán](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Přehled pracovního postupu pro vylepšení
  * [Zásady vytváření názvů souborů](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Publikování vlastností komponenty do kořenového adresáře pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Vygenerované názvy výstupních souborů spoléhají na hodnoty publikovaných vlastností.](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Přidávání miniatur do výstupu MP4 s více přenosovými rychlostmi](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Přehled pracovního postupu, do kterého se mají přidat miniatury
  * [Přidávání kódování JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Zvládnutí převodu barevného prostoru](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Zápis miniatur](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Zjišťování chyb v pracovním postupu](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Dokončený pracovní postup](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Oříznutí výstupu MP4 s více přenosovými na základě času](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Přehled pracovního postupu pro zahájení přidávání oříznutí do](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Použití oříznutí datového proudu](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Dokončený pracovní postup](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Úvod do skriptované komponenty](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Skriptování v rámci pracovního postupu: Hello World](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Oříznutí výstupu MP4 s více přenosovými rychlostmi na základě snímků](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Přehled podrobného plánu pro zahájení přidávání oříznutí do](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Použití souboru XML se seznamem klipů](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Úprava seznamu klipů ze skriptované komponenty](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Přidání vlastnosti pohodlí ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a name="encoding-mxf-into-a-single-bitrate-mp4"></a><a id="MXF_to_MP4"></a>Kódování MXF do typu MP4 s jednou přenosovou rychlostí
Tato část ukazuje, jak vytvořit jednu přenosovou rychlost. Soubor MP4 s formátem AAC – zakódovaný zvuk z. Vstupní soubor MXF

### <a name="starting-a-new-workflow"></a><a id="MXF_to_MP4_start_new"></a>Spouští se nový pracovní postup.
Otevřete Návrhář postupu provádění a vyberte soubor > nový pracovní prostor > přecode podrobný plán.

Nový pracovní postup ukazuje tři prvky:

* Primární zdrojový soubor
* XML seznam klipů
* Výstupní soubor/prostředek  

![Nový pracovní postup kódování](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nový pracovní postup kódování*

### <a name="using-the-media-file-input"></a><a id="MXF_to_MP4_with_file_input"></a>Použití vstupu mediálního souboru
Aby bylo možné přijmout vstupní soubor média, začíná přidání vstupní součásti mediálního souboru. Chcete-li přidat komponentu do pracovního postupu, vyhledejte ji v poli hledání úložiště a přetáhněte požadovanou položku do podokna návrháře. Opakujte akci pro vstup mediálního souboru a připojte primární součást zdrojového souboru ke vstupnímu PIN souboru pro zadání názvu souboru z média.

![Vstup propojeného mediálního souboru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Vstup propojeného mediálního souboru*

Zpočátku Identifikujte příslušný vzorový soubor, který se má použít při navrhování vlastního pracovního postupu. Provedete to tak, že kliknete na pozadí podokna návrháře a vyhledáte vlastnost primární zdrojový soubor v pravém podokně vlastností. Klikněte na ikonu složky a vyberte požadovaný soubor pro testování pracovního postupu. Komponenta pro vstup mediálního souboru zkontroluje soubor a naplní jeho výstupní spoje tak, aby odrážely podrobnosti ukázkového souboru, který zkontroloval.

![Vstup z multimediálního souboru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Vstup z multimediálního souboru*

Teď, když je vstup naplněný, je dalším krokem nastavení kódování výstupu. Podobně jako v případě, že byl primární zdrojový soubor nakonfigurovaný, teď nakonfigurujte vlastnost proměnné výstupní složky hned pod ní.

![Vlastnosti vstupu a výstupu se nakonfigurovaly.](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Vlastnosti vstupu a výstupu se nakonfigurovaly.*

### <a name="inspecting-media-streams"></a><a id="MXF_to_MP4_streams"></a>Kontrola datových proudů médií
Často je žádoucí zjistit, jak Stream vypadá při toku dat prostřednictvím pracovního postupu. Pokud chcete zkontrolovat datový proud v jakémkoli bodě pracovního postupu, stačí kliknout na výstupní nebo vstupní PIN kód na kterékoli součásti. V takovém případě se v souboru multimédia pokusíte kliknout na nekomprimovaný výstupní kód videa. Otevře se dialogové okno, které umožňuje kontrolu odchozího videa.

![Kontroluje se nekomprimovaný kód PIN výstupu videa.](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Kontroluje se nekomprimovaný kód PIN výstupu videa.*

V takovém případě se zobrazí, že video obsahuje 1080 vstup na 24 snímků za sekundu v 4:2:2 vzorkování pro video o skoro 2 minuty.

### <a name="adding-a-video-encoder-for-mp4-file-generation"></a><a id="MXF_to_MP4_file_generation"></a>Přidání kodéru videa pro. Generování souboru MP4
Teď je k dispozici nekomprimované video a více nekomprimovaných kolíků zvukového souboru, které se dají použít na vstupu do mediálního souboru. Aby bylo možné zakódovat příchozí video, je nutné do pracovního postupu přidat komponentu kódování – v tomto případě pro generování. Soubory MP4.

Ke kódování streamu videa do H. 264 přidejte komponentu Video Encoder AVC na plochu návrháře. Tato součást jako vstup převezme dekomprimovat video stream a v jeho výstupním PIN kódu doručí komprimovaný Stream videa AVC.

![Nepřipojený kodér AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Nepřipojený kodér AVC*

Jeho vlastnosti určují, jak přesně probíhá kódování. Pojďme se podívat na některá z důležitějších nastavení:

* Šířka výstupu a výška výstupu: Určuje rozlišení kódovaného videa. V tomto případě je 640x360 dobrým nastavením.
* Snímková frekvence: když se nastaví průchozí kmitočet, stačí jenom, když se nastaví frekvence zdrojových snímků, je možné ji přepsat. Takový převod snímkového kmitočtu není kompenzován pohybem.
* Profil a úroveň: Určuje profil a úroveň AVC. Pokud chcete pohodlně získat další informace o různých úrovních a profilech, klikněte na ikonu otazníku v součásti kodéru videa AVC a na stránce Nápověda se zobrazí další podrobnosti o každé z těchto úrovní. V tomto příkladu použijte hlavní profil na úrovni 3,2 (výchozí).
* Režim řízení sazeb a přenosová rychlost (KB/s): v tomto scénáři se můžete podívat na výstup konstantní přenosové rychlosti při 1200 kB/s.
* Formát videa: poskytuje informace o VUI (informace o použitelnosti videa), které se zapisují do datového proudu H. 264 (informace o straně, které může dekodér použít k vylepšení zobrazení, ale není nezbytné ho správně dekódovat):
* NTSC (typické pro USA nebo Japonsko, s použitím 30 fps)
* PAL (typický pro Evropu, s 25 fps)
* Režim velikosti skupinu GOP: nastavte pevnou skupinu GOP velikost pro naše účely s použitím intervalu klíčů 2 sekund s uzavřeným GOPs. Nastavení 2 sekund zajistí kompatibilitu s dynamickým balíčkem Azure Media Services poskytuje.

Chcete-li zamezit kodér AVC, připojte k nekomprimovanému vstupnímu PIN kódu z kodéru AVC nekomprimovaný výstupní kód videa ze součásti pro vstup mediálního souboru.

![Připojený kodér AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Hlavní kodér Spojeného AVC*

### <a name="encoding-the-audio-stream"></a><a id="MXF_to_MP4_audio"></a>Kódování zvukového datového proudu
V tuto chvíli je původní nekomprimovaný zvukový stream pořád potřeba zkomprimovat. Pro komprimaci zvukového streamu přidejte do pracovního postupu komponentu kodéru AAC (Dolby).

![Nepřipojený kodér AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Nepřipojený kodér AAC*

Došlo k nekompatibilitě: k dispozici je pouze jeden nekomprimovaný vstupní PIN kód z kodéru AAC, zatímco je více než pravděpodobný vstup mediálního souboru má dva odlišné nekomprimované zvukové datové proudy: jeden pro levý zvukový kanál a druhý pro správné. (Pokud pracujete s prostorovým zvukem, je to šest kanálů.) Proto není možné přímo připojit zvuk ze zdrojového vstupu mediálního souboru do kodéru zvuku AAC. Komponenta AAC očekává, že "" prokládaný "zvukový stream: jeden datový proud, který obsahuje levou i pravou kanál. Jakmile od našeho zdrojového mediálního souboru ví, že zvukové stopy jsou na tom, kde se nachází ve zdroji, můžeme tento datový proud prokládaných dat vygenerovat tak, že se správně přiřazené pozice mluvčího nasadí vlevo a vpravo.

Nejdřív chce jedna z požadovaných zdrojových zvukových kanálů vygenerovat prokládaný datový proud. Komponenta prokládaný modul streamování zvuku to pro nás zpracuje. Přidejte ho do pracovního postupu a propojte zvukové výstupy ze vstupního souboru média.

![Prokládaný datový proud připojeného zvukového streamu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Prokládaný datový proud připojeného zvukového streamu*

Teď, když máme prokládaný zvukový stream, ještě jsme neurčili, kam se mají přiřadit pozice pro reproduktory vlevo nebo vpravo. Aby bylo možné tuto možnost určit, můžeme využít přiřazení pozice mluvčího.

![Přidání přiřazení pozice mluvčího](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Přidání přiřazení pozice mluvčího*

Nakonfigurujte přiřazení pozice mluvčího pro použití s stereofonním vstupním datovým proudem pomocí filtru přednastavení kodéru "vlastní" a Předvolby kanálu s názvem "2,0 (L, R)". (Tím se přiřadí levá pozice mluvčího kanálu 1 a správná pozice mluvčího na kanál 2.)

Připojte výstup přiřazení pozice mluvčího ke vstupu kodéru AAC. Pak informujte kodér AAC, aby fungoval s předdefinovaným kanálem "2,0 (L, R)", takže ví, že se zaměří na stereofonní zvuk jako vstup.

### <a name="multiplexing-audio-and-video-streams-into-an-mp4-container"></a><a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexování zvukového a obrazového streamu do kontejneru MP4
Z našeho streamu s kódováním AVC a zvukového streamu s kódováním AAC můžeme zachytit obojí do. Kontejner MP4 Proces kombinování různých datových proudů do jednoho se nazývá "multiplexing" (neboli "muxing"). V takovém případě zanecháváme zvuk a streamy videa v jednom souvislém. Balíček MP4 Komponenta, která tento prvek koordinuje pro. Kontejner MP4 se nazývá multiplexor ISO MPEG-4. Přidejte ho na plochu návrháře a připojte video kodér AVC i kodér AAC ke svým vstupům.

![Připojený multiplexor MPEG4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Připojený multiplexor MPEG4*

### <a name="writing-the-mp4-file"></a><a id="MXF_to_MP4_writing_mp4"></a>Zápis souboru MP4
Při zápisu výstupního souboru je použita součást výstup souboru. Můžeme to propojit s výstupem multiplexového multiplexu ISO MPEG-4, aby jeho výstup byl zapsaný na disk. Provedete to tak, že připojíte výstupní kód kontejneru (MPEG-4) ke vstupnímu PIN kódu pro zápis výstup souboru.

![Výstup připojeného souboru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Výstup připojeného souboru*

Název souboru, který se používá, je určen vlastností soubor. I když může být tato vlastnost pevně zakódované na danou hodnotu, nejpravděpodobnější je, že ji místo toho bude možné nastavit pomocí výrazu.

Pokud chcete, aby pracovní postup automaticky určil vlastnost název výstupního souboru z výrazu, klikněte na tlačítko vedle názvu souboru (vedle ikony složky). V rozevírací nabídce vyberte "výraz". Tím se otevře Editor výrazů. Nejprve vymažte obsah editoru.

![Prázdný Editor výrazů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Prázdný Editor výrazů*

Editor výrazů umožňuje zadat libovolnou hodnotu literálu, smíšenou s jednou nebo více proměnnými. Proměnné začínají znakem dolaru. Při stisknutí klávesy $ se v editoru zobrazí rozevírací seznam s možností dostupných proměnných. V našem případě použijeme kombinaci proměnné výstupní adresář a proměnnou názvu základního vstupního souboru:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Vyplněný Editor výrazů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Vyplněný Editor výrazů*

> [!NOTE]
> Chcete-li zobrazit výstupní soubor úlohy kódování v Azure, je nutné zadat hodnotu v editoru výrazů.
>
>

Když potvrdíte výraz tak, že kliknete na OK, okno Vlastnosti zobrazí náhled toho, jakou hodnotu vlastnost souboru v tomto okamžiku vyřeší.

![Výraz File překládá výstupní adresář.](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Výraz File překládá výstupní adresář.*

### <a name="creating-a-media-services-asset-from-the-output-file"></a><a id="MXF_to_MP4_asset_from_output"></a>Vytvoření prostředku Media Services z výstupního souboru
I když jsme napsali výstupní soubor MP4, pořád musíme označit, že tento soubor patří do výstupního prostředku, který služba Media Services generuje jako výsledek spuštění tohoto pracovního postupu. K tomuto účelu se použije výstupní uzel soubor/Asset na plátně pracovního postupu. Všechny příchozí soubory do tohoto uzlu tvoří součást výsledného Azure Media Services prostředku.

Připojte součást výstupu souboru k výstupnímu souboru nebo součásti Assetu a dokončete pracovní postup.

![Dokončený pracovní postup](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Dokončený pracovní postup*

### <a name="test-the-finished-workflow-locally"></a><a id="MXF_to_MP4_test"></a>Místní otestování dokončeného pracovního postupu
Chcete-li otestovat pracovní postup místně, stiskněte tlačítko Přehrát na panelu nástrojů v horní části. Po dokončení provádění pracovního postupu zkontrolujte výstup vygenerovaný v nakonfigurované výstupní složce. Zobrazí se dokončený výstupní soubor MP4, který byl zakódovaný ze vstupního zdrojového souboru MXF.

## <a name="encoding-mxf-into-mp4---multibitrate-dynamic-packaging-enabled"></a><a id="MXF_to_MP4_with_dyn_packaging"></a>Kódování MXF do dynamického balení s mezipřenosovou rychlostí MP4
Tento názorný postup vytvoří sadu souborů MP4 s více přenosovými rychlostmi pomocí zvuku kódovaného AAC z jednoho. Vstupní soubor MXF

Pokud je výstup majetku s více přenosovými rychlostmi požadován pro použití v kombinaci s funkcemi dynamického balení nabízenými nástrojem Azure Media Services, bude nutné vygenerovat více souborů MP4 s zarovnanými skupinu GOP, které mají různé přenosové rychlosti a řešení. Pokud to chcete udělat, [MXF kódování do jediné přenosové příručky MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) poskytuje dobrý výchozí bod.

![Spouští se pracovní postup](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Spouští se pracovní postup*

### <a name="adding-one-or-more-additional-mp4-outputs"></a><a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Přidání jednoho nebo více dalších výstupů MP4
Každý soubor MP4 v naší výsledné Azure Media Services assetu podporuje různé rychlosti a řešení. Pojďme do pracovního postupu přidat jeden nebo více výstupních souborů MP4.

Aby se zajistilo, že máme všechny kodéry videa vytvořené se stejnými nastaveními, je nejužitečnější duplikovat již existující video kodéru AVC a nakonfigurovat jinou kombinaci rozlišení a přenosové rychlosti (přidejte jednu z 960 x 540 na 25 snímků za sekundu v 2,5 MB/s). Chcete-li stávající kodér duplikovat, zkopírujte jej na plochu návrháře.

Připojte k naší nové komponentě AVC nekomprimovaný kód PIN výstupního souboru média.

![Připojí se druhý kodér AVC.](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Připojí se druhý kodér AVC.*

Teď můžete přizpůsobit konfiguraci pro náš nový kodér AVC na výstup 960x540 při 2,5 MB/s. (Použijte jeho vlastnosti "Šířka výstupu", "Výstupní výška" a "přenosová rychlost (KB/s)".)

Vzhledem k tomu, že chceme použít výsledný prostředek společně s Azure Media Services dynamickým balením, musí být koncový bod streamování schopný vygenerovat z těchto souborů MP4 HLS/fragmentované fragmenty MP4/POMLČKy, které jsou přesně zarovnané na sebe tak, aby klienti, kteří přecházejí mezi různými přenosovými rychlostmi, získali jediné plynulé nepřetržité video a zvukové prostředí. K tomu je potřeba zajistit, aby ve vlastnostech skupinu GOP ("skupina obrázků") pro oba soubory MP4 byl velikost ("skupina obrázků") na 2 sekundy, kterou může udělat:

* nastavení režimu velikosti skupinu GOP na pevnou skupinu GOP velikost a
* Interval klíčových snímků na dvě sekundy.
* Nastavte také skupinu GOP IDR ovládací prvek na Closed skupinu GOP, aby se zajistilo, že všechny GOPs se sami zastavují bez závislostí.

Aby bylo možné tento pracovní postup snáze pochopit, přejmenujte první kodér AVC na "AVC Video Encoder 640x360 1200 kB/s" a druhý kodér AVC 960x540 "AVC Video Encoder 2500 kb/s".

Teď přidejte druhý multiplexor ISO MPEG-4 a druhý výstup souboru. Připojte multiplexor k novému kodéru AVC a ujistěte se, že výstup je směrován do výstupního souboru. Pak také Připojte výstup kodéru AAC k novému vstupu multiplexu. Výstup souboru je pak možné připojit k výstupnímu uzlu soubor/Asset a přidat ho k Media Services prostředku, který se vytvoří.

![Připojený druhý muxer a výstup souboru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Připojený druhý muxer a výstup souboru*

Pro zajištění kompatibility s Azure Media Services dynamického balení nakonfigurujte režim bloků dat multiplexu na skupinu GOP Count nebo Duration a nastavte GOPs na blok na 1. (Mělo by se jednat o výchozí nastavení.)

![Muxer – režimy bloků dat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer – režimy bloků dat*

Poznámka: Tento postup můžete chtít opakovat pro všechny kombinace přenosných přenosů a řešení, které chcete přidat do výstupu assetu.

### <a name="configuring-the-file-output-names"></a><a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfigurace názvů výstupů souborů
Do výstupního prostředku je přidaný více než jeden jeden soubor. To zajišťuje, aby se názvy souborů pro každý z výstupních souborů lišily od sebe navzájem a mohly dokonce použít konvenci pojmenovávání souborů, aby se z názvu souboru stalo jasné, se kterými se pracujete.

Názvy výstupů souborů lze ovládat pomocí výrazů v návrháři. Otevřete podokno vlastností pro jednu ze součástí výstupního souboru a otevřete Editor výrazů pro vlastnost soubor. Náš první výstupní soubor byl nakonfigurován pomocí následujícího výrazu (Další informace najdete v kurzu přechodu z [MXF na výstup MP4 s jednou přenosovou rychlostí](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

To znamená, že název souboru je určen dvěma proměnnými: výstupní adresář, do kterého se má zapisovat, a základní název zdrojového souboru. Bývalé je zveřejněné jako vlastnost v kořenovém adresáři pracovního postupu a druhá je určena příchozím souborem. Výstupní adresář je to, co používáte pro místní testování; Tato vlastnost bude přepsána modulem pracovních postupů v případě, že je pracovní postup spuštěn pomocí cloudového mediálního procesoru v systému Azure Media Services.
Chcete-li, aby oba výstupní soubory odpovídaly konzistentnímu výstupnímu názvu, změňte první výraz pro pojmenovávání souborů na:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

a druhý pro:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Spusťte zprostředkující testovací běh, aby se zajistilo, že oba výstupní soubory MP4 budou správně vygenerovány.

### <a name="adding-a-separate-audio-track"></a><a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Přidání samostatné zvukové stopy
Jakmile se později vygenerujeme soubor. ISM, který se dá použít pro naše výstupní soubory MP4, budeme pro náš adaptivní streamování potřebovat jenom zvukový soubor MP4. Pokud chcete tento soubor vytvořit, přidejte další muxer do pracovního postupu (ISO-MPEG-4 multiplexor) a propojte výstupní kód PIN kodéru AAC se vstupním kódem PIN pro stopu 1.

![Zvuk muxer se přidal.](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Zvuk muxer se přidal.*

Vytvořte třetí komponentu výstupu souboru pro výstup odchozího datového proudu z muxer a nakonfigurujte výraz pro pojmenovávání souborů jako:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Audio muxer vytváření výstupu souboru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Audio muxer vytváření výstupu souboru*

### <a name="adding-the-ism-smil-file"></a><a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Přidání. ISM – soubor SMIL
Aby dynamické balení fungovalo v kombinaci se soubory MP4 (a souborem MP4 pouze zvuku) v našem Media Servicesm assetu, potřebujeme také soubor manifestu (označovaný také jako "SMIL": synchronizovaný Cloud pro integraci multimédií). Tento soubor indikuje Azure Media Services, které soubory MP4 jsou k dispozici pro dynamické balení a které z nich je vhodné pro streamování zvuku. Typický soubor manifestu pro sadu MP4's s jedním zvukovým datovým proudem vypadá takto:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="https://www.w3.org/2001/SMIL20/Language">
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

Soubor. ISM obsahuje v rámci příkazu switch odkaz na každý z jednotlivých videosouborů MP4 a kromě toho také jeden (nebo více) odkazuje na zvukový soubor MP4, který obsahuje pouze zvuk.

Generování souboru manifestu pro naši sadu MP4's lze provést prostřednictvím komponenty nazvané "zapisovač manifestu AMS". Pokud ji chcete použít, přetáhněte ji na plochu a připojte výstupní kódy "zapsat kompletní" ze tří součástí výstupu souboru do vstupu modulu pro zápis manifestu AMS. Pak nezapomeňte připojit výstup zapisovače manifestu AMS k výstupnímu souboru nebo prostředku.

Stejně jako u našich dalších součástí výstupů souborů nakonfigurujte název výstupního souboru. ISM pomocí výrazu:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Náš dokončený pracovní postup vypadá následovně:

![Dokončilo se MXF pracovního postupu MP4 s více přenosovými rychlostmi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Dokončilo se MXF pracovního postupu MP4 s více přenosovými rychlostmi*

## <a name="encoding-mxf-into-multibitrate-mp4---enhanced-blueprint"></a><a id="MXF_to__multibitrate_MP4"></a>MXF kódování do s více přenosovými rychlostmi – rozšířený podrobný plán
V [předchozím návodu k pracovnímu postupu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) jsme viděli, jak se jeden vstupní prostředek MXF dá převést na výstupní Asset s použitím souborů MP4 s více přenosovými rychlostmi, jenom pomocí zvukového souboru MP4 a souboru manifestu pro použití ve spojení s Azure Media Services dynamického balení.

Tento návod ukazuje, jak lze některé aspekty rozšířit a vylepšit.

### <a name="workflow-overview-to-enhance"></a><a id="MXF_to_multibitrate_MP4_overview"></a>Přehled pracovního postupu pro vylepšení
![Pracovní postup MP4 s více přenosovými rychlostmi k vylepšení](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Pracovní postup MP4 s více přenosovými rychlostmi k vylepšení*

### <a name="file-naming-conventions"></a><a id="MXF_to__multibitrate_MP4_file_naming"></a>Zásady vytváření názvů souborů
V předchozím pracovním postupu jsme jako základ pro generování názvů výstupních souborů zadali jednoduchý výraz. Máme duplicity, i když: všechny součásti výstupních souborů, které tento výraz určily.

Například součást výstup souboru pro první videosoubor je nakonfigurována s tímto výrazem:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Pro druhé výstupní video máme výraz, jako je:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Nebylo by to čisticí, méně náchylné k chybám a pohodlnější, pokud bychom mohli některé z těchto duplicit odebrat a místo toho je dělat konfigurovatelný? Donovanovo můžeme: možnosti výrazů návrháře v kombinaci s možností vytvořit vlastní vlastnosti v našem kořenovém adresáři pracovního postupu poskytují přidanou vrstvu pohodlí.

Předpokládáme, že zadáte konfiguraci názvu souboru z přenosových jednotek jednotlivých souborů MP4. Tyto přenosové rychlosti se snažíme nakonfigurovat na jednom centrálním místě (v kořenu našeho grafu), ze kterého budou k dispozici ke konfiguraci a generování názvů souborů. Provedeme to tak, že publikujete vlastnost přenosové rychlosti z obou kodérů AVC do kořenového adresáře našeho pracovního postupu tak, aby byl přístupný z kořene i z kodérů AVC. (I když se zobrazuje na dvou různých skvrnách, existuje jenom jedna základní hodnota.)

### <a name="publishing-component-properties-onto-the-workflow-root"></a><a id="MXF_to__multibitrate_MP4_publishing"></a>Publikování vlastností komponenty do kořenového adresáře pracovního postupu
Otevřete první kodér AVC, klikněte na vlastnost přenosové rychlosti (KB/s) a z rozevíracího seznamu vyberte publikovat.

![Publikování vlastnosti přenosové rychlosti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publikování vlastnosti přenosové rychlosti*

Nakonfigurujte dialog publikovat pro publikování do kořenového adresáře vašeho grafu pracovního postupu s publikovaným názvem "video1bitrate" a čitelným zobrazovaným názvem "přenos videa 1". Nakonfigurujte vlastní název skupiny s názvem "přenosná rychlost streamování" a klikněte na publikovat.

![Publikování vlastnosti přenosové rychlosti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Dialog pro publikování pro vlastnost přenosové rychlosti*

Opakujte stejnou vlastnost přenosů druhého kodéru AVC a pojmenujte ji "video2bitrate" se zobrazovaným názvem "přenosová rychlost videa" ve stejné vlastní skupině "přenosové rychlosti streamování".

Pokud jsme teď zkontrolovali kořenové vlastnosti pracovního postupu, zobrazí se naše vlastní skupina s těmito dvěma publikovanými vlastnostmi. Obě hodnoty odpovídají hodnotě příslušných přenosů kodéru AVC.

![Publikovaná přenosová vzpěra v kořenovém adresáři pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Kdykoli chceme získat přístup k těmto vlastnostem z kódu nebo z výrazu, můžeme to udělat takto:

* z vloženého kódu z komponenty přímo pod kořen: Node. getPropertyAsString ('.. /video1bitrate ', null)
* v rámci výrazu: $ {ROOT_video1bitrate}

Pojďme vyplňovat "přenosové rychlosti streamování" tím, že publikujete i naši přenosovou rychlost zvukové stopy. V části vlastnosti kodéru AAC vyhledejte nastavení přenosové rychlosti a vyberte publikovat z rozevíracího seznamu vedle něho. Publikujte do kořenového adresáře grafu s názvem "audio1bitrate" a zobrazovaným názvem "přenosová rychlost zvuku 1" v rámci naší vlastní skupiny "přenosů streamování".

![Dialog pro publikování pro zvukovou rychlost](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Dialog pro publikování pro zvukovou rychlost*

![Výsledný obrazové a zvukové propy v kořenovém adresáři](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Výsledný obrazové a zvukové propy v kořenovém adresáři*

Změna kterékoli z těchto tří hodnot také překonfiguruje a změní hodnoty v příslušných součástech, se kterými jsou propojeny (a odkud byly publikovány).

### <a name="have-generated-output-file-names-rely-on-published-property-values"></a><a id="MXF_to__multibitrate_MP4_output_files"></a>Vygenerované názvy výstupních souborů spoléhají na hodnoty publikovaných vlastností.
Místo toho, abychom vygenerovali názvy souborů, teď můžete ve všech komponentách pro výstup souborů změnit náš název souboru tak, aby se spoléhaly na vlastnosti přenosové rychlosti publikované v kořenovém adresáři grafu. Počínaje naším prvním výstupem souboru Najděte vlastnost File a upravte výraz takto:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

K různým parametrům v tomto výrazu lze přistoupit a zadat ho stisknutím klávesy dolaru na klávesnici v okně výrazu. Jedním z dostupných parametrů je naše vlastnost video1bitrate, kterou jsme publikovali dříve.

![Přístup k parametrům v rámci výrazu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Přístup k parametrům v rámci výrazu*

Pro výstup souboru pro náš druhý videozáznam proveďte stejný postup:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

a pro výstup pouze zvukového souboru:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Pokud teď změníme přenos pro některé z videosouborů nebo zvukových souborů, příslušný kodér se překonfiguruje a konvence názvů souborů na základě přenosové rychlosti se bude akceptovat automaticky.

## <a name="adding-thumbnails-to-multibitrate-mp4-output"></a><a id="thumbnails_to__multibitrate_MP4"></a>Přidávání miniatur do výstupu MP4 s více přenosovými rychlostmi
Od pracovního postupu, který generuje [výstup MP4 s více přenosovými rychlostmi ze vstupu MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), teď budeme na výstup přihlédnout miniatury.

### <a name="workflow-overview-to-add-thumbnails-to"></a><a id="thumbnails_to__multibitrate_MP4_overview"></a>Přehled pracovního postupu, do kterého se mají přidat miniatury
![Pracovní postup MP4 s více přenosovými rychlostmi pro zahájení](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Pracovní postup MP4 s více přenosovými rychlostmi pro zahájení*

### <a name="adding-jpg-encoding"></a><a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Přidávání kódování JPG
Základem naší generace miniatur bude komponenta JPG Encoder, která dokáže vysílat výstupní soubory JPG.

![Kodér JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Kodér JPG*

Nemůžeme ale přímo připojit náš nekomprimovaný Stream videa ze vstupního souboru multimédia do kodéru JPG. Místo toho očekává, že bude nutné objednat jednotlivé snímky. To se dá udělat přes komponentu brány pro video snímků.

![Připojení brány rámců k kodéru JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Připojení brány rámců k kodéru JPG*

Brána snímku jednou, takže mnoho sekund nebo snímků umožňuje průchod obrazu. Interval a časový posun, se kterým se tato akce stane, lze konfigurovat ve vlastnostech.

![Vlastnosti brány snímků videa](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Vlastnosti brány snímků videa*

Pojďme vytvořit miniaturu každou minutu nastavením režimu na čas (sekundy) a intervalu 60.

### <a name="dealing-with-color-space-conversion"></a><a id="thumbnails_to__multibitrate_MP4_color_space"></a>Zvládnutí převodu barevného prostoru
I když by se to zdá, že by to bylo v logické formě nekomprimovaných grafických kódů brány rámců a vstup mediálního souboru se teď může připojit, zobrazilo se upozornění, pokud to uděláte.

![Chyba vstupního barevného prostoru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Chyba vstupního barevného prostoru*

Důvodem je to, že způsob, jakým jsou informace o barvách reprezentovány v původním nekomprimovaném streamu videa, se z našich MXF liší od toho, co kodér JPG očekává. V případě, že se očekává, že se bude pojmenovat "barevný prostor" RGB nebo ve stupních šedi. To znamená, že vstupní datový proud videa brány snímků videa musí nejprve použít převod na jeho barevný prostor.

Přetáhněte do pracovního postupu převaděč barev – Intel a připojte ho k bráně rámců.

![Připojování převodního prostoru barev](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Připojování převodního prostoru barev*

V okně Vlastnosti vyberte položku BGR 24 v seznamu přednastavení.

### <a name="writing-the-thumbnails"></a><a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Zápis miniatur
V případě, že se liší od našeho videa MP4, komponenta kodéru JPG vyprodukuje více než jeden soubor. Aby bylo možné s tímto způsobem pracovat, lze použít komponentu zapisovače souborů JPG pro hledání scény: přebírá příchozí miniatury a zapisuje je do každého názvu souboru, který je příponou jiného čísla. (Číslo obvykle označuje počet sekund/jednotek v proudu, ze kterého byl Miniatura vykreslena.)

![Představujeme zapisovač souborů JPG pro hledání scény](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Představujeme zapisovač souborů JPG pro hledání scény*

Nakonfigurujte vlastnost Cesta k výstupní složce s výrazem: $ {ROOT_outputWriteDirectory}.

a vlastnost Prefix názvu souboru s:

    ${ROOT_sourceFileBaseName}_thumb_

Předpona určuje, jak se pojmenují miniatury souborů. Mají příponu s číslem udávajícím pozici jezdce v datovém proudu.

![Vlastnosti zapisovače souborů JPG pro hledání scény](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Vlastnosti zapisovače souborů JPG pro hledání scény*

Připojte zapisovač souborů JPG pro hledání scény do výstupního souboru nebo na uzel Assetu.

### <a name="detecting-errors-in-a-workflow"></a><a id="thumbnails_to__multibitrate_MP4_errors"></a>Zjišťování chyb v pracovním postupu
Propojit vstup převaděče pro barevný prostor s nezpracovaným nekomprimovaným výstupem videa Nyní proveďte místní testovací běh pracovního postupu. Je velmi pravděpodobné, že se pracovní postup náhle zastaví a bude označovat červený obrys na komponentě, u které došlo k chybě:

![Chyba převaděče barevných prostorů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Chyba převaděče barevných prostorů*

Kliknutím na malou červenou ikonu "E" v pravém horním rohu součásti převaděče barevných prostorů zjistíte, proč se pokus o kódování nezdařil.

![Chybový dialog převaděče barevných prostorů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Chybový dialog převaděče barevných prostorů*

Zapíná se, jak můžete vidět, že příchozí barevný prostor pro převaděč barevných prostorů musí být rec601 pro náš požadovaný převod YUV na RGB. Zdá se, že náš Stream neindikuje jeho rec601. (REC 601 je standard pro kódování prokládaných analogových signálů videa ve formě digitálního videa. Určuje aktivní oblast, která pokrývá 720 světelnost vzorků a 360 chrominance vzorků na řádek. Systém kódování barev je známý jako YCbCr 4:2:2.)

Pokud to chcete opravit, budeme indikovat metadata našeho streamu, který pracujeme s rec601 obsahem. Provedeme to tak, že použijeme komponentu aktualizace datového typu video, kterou zadáte mezi náš nezpracovaný zdroj a komponentu pro převod barevných prostorů. Tato aktualizace datového typu umožňuje ruční aktualizaci určitých vlastností datového typu videa. Nakonfigurujte ji tak, aby označovala barevné místo standardu "REC 601". Tím dojde k tomu, že nástroj pro streamování dat zapíše datový proud na barevný prostor "REC 601", pokud ještě nebyl definován žádný barevný prostor. (Žádná existující metadata se nepřepisují, Pokud políčko přepsání nebylo zaškrtnuté.)

![Aktualizace standardního prostoru barev na základě aktualizace datového typu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Aktualizace standardního prostoru barev na základě aktualizace datového typu*

### <a name="finished-workflow"></a><a id="thumbnails_to__multibitrate_MP4_finish"></a>Dokončený pracovní postup
Teď, když je náš pracovní postup dokončený, proveďte jiný testovací běh, aby se zobrazila jeho průchod.

![Dokončený pracovní postup pro výstup ve více MP4 s miniaturami](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Dokončený pracovní postup pro výstup ve více MP4 s miniaturami*

## <a name="time-based-trimming-of-multibitrate-mp4-output"></a><a id="time_based_trim"></a>Oříznutí výstupu MP4 s více přenosovými na základě času
Od pracovního postupu, který generuje [výstup MP4 s více přenosovými rychlostmi ze vstupu MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), teď budeme v závislosti na časových razítkách vystřihovat zdrojové video.

### <a name="workflow-overview-to-start-adding-trimming-to"></a><a id="time_based_trim_start"></a>Přehled pracovního postupu pro zahájení přidávání oříznutí do
![Spouští se pracovní postup pro přidání oříznutí do](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Spouští se pracovní postup pro přidání oříznutí do*

### <a name="using-the-stream-trimmer"></a><a id="time_based_trim_use_stream_trimmer"></a>Použití oříznutí datového proudu
Komponenta streamování streamování umožňuje zkrátit začátek a konec základu vstupního streamu na informace časování (sekundy, minuty,...). Oříznutí nepodporuje oříznutí založené na snímcích.

![Přetrávník streamu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Přetrávník streamu*

Místo propojení kodérů AVC a přiřadících umístění mluvčího k přímému vstupu do mediálního souboru provedeme mezi těmito datovými proudy. (Jeden pro video signál a jeden pro vykládaný zvukový signál.)

![Vložit do datového proudu oříznutí mezi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Vložit do datového proudu oříznutí mezi*

Pojďme nakonfigurovat oříznutí tak, aby se ve videu zpracovává jenom video a zvuk ve více než 15 sekundách až 60 sekund.

Umožňuje přejít do vlastností datového proudu videa a nakonfigurovat vlastnosti počátečního času (15 s) a koncového času (60 s). Aby se zajistilo, že se vaše zvuková a obrazová trávníka vždycky nakonfigurují na stejné počáteční a koncové hodnoty, zveřejníme je do kořenového adresáře pracovního postupu.

![Publikování vlastnosti počátečního času z oříznutí streamu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publikování vlastnosti počátečního času z oříznutí streamu*

![Dialog publikovat vlastnost pro čas spuštění](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Dialog publikovat vlastnost pro čas spuštění*

![Dialogové okno Publikovat vlastnost pro čas ukončení](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Dialogové okno Publikovat vlastnost pro čas ukončení*

Pokud teď kontrolujeme kořen našeho pracovního postupu, obě vlastnosti se v něm zobrazí a nakonfiguruje.

![Publikované vlastnosti dostupné v kořenu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Publikované vlastnosti dostupné v kořenu*

Nyní otevřete ořezávání vlastností ze zvukového ořezávání a nakonfigurujte počáteční i koncový dobu pomocí výrazu, který odkazuje na publikované vlastnosti v kořenu našeho pracovního postupu.

Pro čas zahájení oříznutí zvuku:

    ${ROOT_TrimmingStartTime}

a pro svůj koncový čas:

    ${ROOT_TrimmingEndTime}

### <a name="finished-workflow"></a><a id="time_based_trim_finish"></a>Dokončený pracovní postup
![Dokončený pracovní postup](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Dokončený pracovní postup*

## <a name="introducing-the-scripted-component"></a><a id="scripting"></a>Úvod do skriptované komponenty
Skriptované komponenty můžou spouštět libovolné skripty během fází provádění našeho pracovního postupu. Existují čtyři různé skripty, které mohou být provedeny, z nichž každá má konkrétní charakteristiky a jejich vlastní místo v životním cyklu pracovního postupu:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

Dokumentace ke skriptované komponentě je podrobněji popsána v každé z výše uvedených. V [následující části](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)se komponenta **realizeScript** Scripting používá k vytvoření cliplist XML za běhu při spuštění pracovního postupu. Tento skript se volá při instalaci komponenty, která se v životním cyklu stane jenom jednou.

### <a name="scripting-within-a-workflow-hello-world"></a><a id="scripting_hello_world"></a>Skriptování v rámci pracovního postupu: Hello World
Přetáhněte komponentu s skriptem na plochu návrháře a přejmenujte ji (například "SetClipListXML").

![Přidání skriptované komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Přidání skriptované komponenty*

Když prozkoumáte vlastnosti skriptované komponenty, zobrazí se čtyři různé typy skriptů, které se nakonfiguruje na jiný skript.

![Vlastnosti skriptované komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Vlastnosti skriptované komponenty*

Vymažte processInputScript a otevřete editor pro realizeScript. Nyní jsme nastavili a připraveni spustit skriptování.

Skripty jsou napsané v aplikaci Groove, dynamicky kompilovaný skriptovací jazyk pro platformu Java, který zachovává kompatibilitu s jazykem Java. Ve skutečnosti je většina kódu Java platným kódem aplikace Groove.

Pojďme do kontextu našeho realizeScript napsat jednoduchý skript Groove Hello World. V editoru zadejte následující:

    node.log("hello world");

Nyní spusťte místní testovací běh. Po tomto spuštění zkontrolujte (prostřednictvím karty systém v komponentě skriptu) vlastnost Logs.

![Výstup protokolu Hello World](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Výstup protokolu Hello World*

Objekt uzlu, na kterém zavoláte metodu protokolu, odkazuje na náš aktuální "uzel" nebo komponentu, v rámci které provádíte skriptování. Všechny komponenty, které mají možnost výstupovat data protokolování, jsou k dispozici prostřednictvím karty systém. V tomto případě výstup řetězcového literálu "Hello World". Důležité je vědět, že se tady můžete ukázat jako nehodnotný ladicí nástroj, který vám poskytne přehled o tom, co skript skutečně dělá.

V rámci našeho skriptovacího prostředí máme také přístup k vlastnostem jiných komponent. Vyzkoušejte tohle:

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

Náš okno protokolu zobrazuje následující:

![Výstup protokolu pro přístup k cestám uzlu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Výstup protokolu pro přístup k cestám uzlu*

## <a name="frame-based-trimming-of-multibitrate-mp4-output"></a><a id="frame_based_trim"></a>Oříznutí výstupu MP4 s více přenosovými rychlostmi na základě snímků
Od pracovního postupu, který generuje [výstup MP4 s více přenosovými rychlostmi ze vstupu MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), teď budeme hledat ve zdrojovém videu, které bude na základě počtu snímků vystřihovat.

### <a name="blueprint-overview-to-start-adding-trimming-to"></a><a id="frame_based_trim_start"></a>Přehled podrobného plánu pro zahájení přidávání oříznutí do
![Pracovní postup pro zahájení přidávání oříznutí do](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Pracovní postup pro zahájení přidávání oříznutí do*

### <a name="using-the-clip-list-xml"></a><a id="frame_based_trim_clip_list"></a>Použití souboru XML se seznamem klipů
Ve všech předchozích kurzech k pracovnímu postupu jsme jako zdroj vstupu videa použili komponentu pro zadávání multimediálních souborů. Pro tento konkrétní scénář ale použijeme místo toho zdrojovou komponentu pro seznam klipů. To by nemělo být upřednostňovaný způsob práce; Zdroj seznamu klipů použijte pouze v případě, že k tomu dochází v reálném čase (například v následujícím případě, kde používáme možnosti ořezávání vystřihování seznamu klipů).

Chcete-li přepnout ze vstupu multimediálního souboru do zdroje seznamu klipů, přetáhněte zdrojovou komponentu seznamu klipů na návrhovou plochu a připojte kód XML pro seznam klipů k uzlu XML v Návrháři pracovních postupů. Tím se na základě našeho vstupního videa naplní zdroj seznamu klipů výstupními kódy PIN. Nyní připojí nekomprimované video a nekomprimované zvukové kolíky ze zdroje seznamu klipů k příslušným kodérům AVC a spolujezdci streamování. Nyní odstraňte vstup ze souboru média.

![Nahradili jste vstup multimediálního souboru zdrojem seznamu klipů.](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Nahradili jste vstup multimediálního souboru zdrojem seznamu klipů.*

Zdrojová komponenta seznamu klipů má jako svůj vstup "seznam klipů XML". Když vyberete zdrojový soubor, který chcete testovat v místním prostředí, automaticky se vyplní tento seznam klipů XML.

![Automaticky vyplněná vlastnost XML seznamu klipů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automaticky vyplněná vlastnost XML seznamu klipů*

Vypadá to, že se jedná o trochu blíže k souboru XML, jak vypadá takto:

![Dialogové okno Upravit seznam klipů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Dialogové okno Upravit seznam klipů*

To ale neodráží možnosti v souboru XML se seznamem klipů. Jednou z možností je přidat element "střih" do zdroje videa i zvuku, například takto:

![Přidání elementu pro ořezávání do seznamu klipů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Přidání elementu pro ořezávání do seznamu klipů*

Pokud upravíte soubor XML seznamu klipů podobně jako v tomto obrázku a provedete místní testovací běh, zobrazí se video správně ve videu zkrácené o 10 až 20 sekund.

Na rozdíl od toho, co se stane, když v místním spuštění dojde k tomu, že stejný cliplist XML nebude mít stejný účinek jako při použití v pracovním postupu, který běží v Azure Media Services. Po spuštění Azure Premium Encoder se cliplist XML vygeneruje pokaždé znovu, a to na základě vstupního souboru, který úlohu kódování poskytla. To znamená, že jakékoli změny, které v souboru XML provedete, by bohužel byly přepsány.

Aby se cliplist XML vymazalo při spuštění úlohy kódování, můžeme ho znovu vygenerovat hned po zahájení našeho pracovního postupu. Takové vlastní akce je možné provést prostřednictvím toho, co se nazývá "skriptovaná komponenta". Další informace najdete v tématu [Úvod do skriptované komponenty](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Přetáhněte komponentu s skriptem na plochu návrháře a přejmenujte ji na "SetClipListXML".

![Přidání skriptované komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Přidání skriptované komponenty*

Po kontrole vlastností komponenty s skriptem se zobrazí čtyři různé typy skriptů, které se nakonfiguruje na jiný skript.

![Vlastnosti skriptované komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Vlastnosti skriptované komponenty*

### <a name="modifying-the-clip-list-from-a-scripted-component"></a><a id="frame_based_trim_modify_clip_list"></a>Úprava seznamu klipů ze skriptované komponenty
Než můžeme přepsat cliplist XML, který se generuje při spuštění pracovního postupu, bude potřeba mít přístup k vlastnosti a obsahu cliplist XML. Můžeme to udělat takto:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Příchozí protokolovaný seznam klipů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Příchozí protokolovaný seznam klipů*

Nejdřív potřebujeme způsob, jak určit, od kterého bodu chcete video oříznout. Aby to bylo možné pro méně technické uživatele pracovního postupu, publikujte dvě vlastnosti do kořenového adresáře grafu. Provedete to tak, že kliknete pravým tlačítkem myši na plochu návrháře a vyberete Přidat vlastnost:

* První vlastnost: "ClippingTimeStart" typu: "TIMECODE"
* Druhá vlastnost: "ClippingTimeEnd" typu: "TIMECODE"

![Dialogové okno pro přidání vlastnosti pro čas spuštění oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Dialogové okno pro přidání vlastnosti pro čas spuštění oříznutí*

![Publikované časy oříznutí v kořenu pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publikované časy oříznutí v kořenu pracovního postupu*

Nakonfigurujte obě vlastnosti na vhodnou hodnotu:

![Konfigurace počátečních a koncových vlastností ořezu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Konfigurace počátečních a koncových vlastností ořezu*

Z našeho skriptu teď máme přístup k oběma vlastnostem, například:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Okno protokolu zobrazující začátek a konec oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Okno protokolu zobrazující začátek a konec oříznutí*

Pojďme analyzovat timecode řetězce na pohodlnější použití formuláře pomocí jednoduchého regulárního výrazu:

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

![Okno protokolu s výstupem analyzovaných timecode](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Okno protokolu s výstupem analyzovaných timecode*

S těmito informacemi teď můžeme upravit cliplist XML tak, aby odrážela počáteční a koncové časy pro požadovaný snímek s přesným oříznutím videa.

![Kód skriptu pro přidání řezacích prvků](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Kód skriptu pro přidání řezacích prvků*

To bylo provedeno prostřednictvím normální operace manipulace s řetězci. Výsledný upravený seznam klipů XML se zapisuje zpátky do vlastnosti clipListXML v kořenovém adresáři pracovního postupu pomocí metody setProperty. Okno protokolu po dalším testovacím běhu by nám mohlo ukázat následující:

![Protokolování výsledného seznamu klipů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Protokolování výsledného seznamu klipů*

Proveďte testovací spuštění, abyste viděli, jak byly oříznuté streamování videa a zvuku. Jak provedete více než jeden testovací běh s různými hodnotami pro oříznutí bodů, všimnete si, že se ale neberou v úvahu. Důvodem je, že návrhář, na rozdíl od modulu Azure runtime, nepřepisuje cliplist XML při každém spuštění. To znamená, že pouze poprvé jste nastavili body in a out, což způsobí transformaci kódu XML (pokud (`clipListXML.indexOf("<trim>") == -1`)) zabrání v přidání dalšího elementu Trim, pokud již existuje.

Abychom mohli náš pracovní postup dobře testovat v místním prostředí, je vhodné přidat nějaký kód pro vedení práce, který kontroluje, zda již existuje element Trim. Pokud ano, můžeme ho odebrat, než budete pokračovat úpravou XML novými hodnotami. Místo toho, aby se nepoužívaly manipulace s prostými řetězci, je pravděpodobně bezpečnější provést pomocí reálné analýzy modelu objektu XML.

Předtím, než můžeme tento kód přidat, je nutné nejprve na začátek našeho skriptu přidat několik příkazů pro import:

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

Potom můžeme přidat požadovaný čisticí kód:

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

Tento kód se nachází těsně nad bodem, ve kterém přidáme řezací prvky do cliplist XML.

V tuto chvíli můžeme spustit a upravit náš pracovní postup tak často, jak chceme, zatímco se změny projevily někdy.    

### <a name="adding-a-clippingenabled-convenience-property"></a><a id="frame_based_trim_clippingenabled_prop"></a>Přidání vlastnosti pohodlí ClippingEnabled
Vzhledem k tomu, že nechcete, aby oříznutí bylo vždy k dispozici, je třeba dokončit náš pracovní postup přidáním pohodlného logického příznaku, který označuje, jestli chceme povolit ořezávání nebo oříznutí.

Stejně jako dřív publikujte novou vlastnost do kořenového adresáře našeho pracovního postupu s názvem "ClippingEnabled" typu "BOOLEAN".

![Publikovala se vlastnost pro povolení oříznutí.](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publikovala se vlastnost pro povolení oříznutí.*

Pomocí níže uvedené klauzule jednoduché ochrany můžeme zjistit, jestli je potřeba udělat ořezávání, a rozhodnout se, jestli se má tento seznam klipů upravovat, nebo ne.

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

### <a name="complete-code"></a><a id="code"></a>Celý kód

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
[Představujeme Premium Encoding v Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Jak používat kódování Premium v Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Kódování obsahu na vyžádání pomocí Azure Media Service](media-services-encode-asset.md#media-encoder-premium-workflow)

[Pracovní postup, formáty a kodeky kodéru Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)

[Ukázkové soubory pracovního postupu](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Nástroj Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
