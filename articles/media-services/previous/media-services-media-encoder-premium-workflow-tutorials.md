---
title: Pokročilé kurzy k Media Encoderu Premium Workflow
description: Tento dokument obsahuje návody, které ukazují, jak provádět pokročilé úkoly s pracovním postupem Media Encoder Premium a také jak vytvářet složité pracovní postupy pomocí Návrháře pracovních postupů.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251008"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Pokročilé kurzy k Media Encoderu Premium Workflow
## <a name="overview"></a>Přehled
Tento dokument obsahuje návody, které ukazují, jak přizpůsobit pracovní postupy pomocí **Návrháře pracovních postupů**. Skutečné soubory pracovního postupu naleznete [zde](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>Obsahu
Jsou popsána následující témata:

* [Kódování MXF do jednoho protokolu MP4 o přenosové rychlosti](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Spuštění nového pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Použití vstupu mediálního souboru](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Kontrola datových proudů médií](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Přidání kodéru videa pro . Generování souborů MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Kódování zvukového proudu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexování datových proudů zvuku a videa do kontejneru MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Zápis souboru MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Vytvoření datového zdroje mediálních služeb z výstupního souboru](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testování dokončeného pracovního postupu místně](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Kódování MXF do multibitrate MP4s - dynamické balení povoleno](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Přidání jednoho nebo více dalších výstupů MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Konfigurace názvů výstupů souborů](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Přidání samostatné zvukové stopy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Přidání souboru SMIL "ISM"](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Kódování MXF do multibitrate MP4 - vylepšený plán](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Přehled pracovního postupu pro vylepšení
  * [Konvence pojmenování souborů](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Publikování vlastností součásti do kořenového adresáře pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Vygenerované názvy výstupních souborů spoléhají na publikované hodnoty vlastností](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Přidání miniatur do multibitrate výstupu MP4](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Přehled pracovního postupu pro přidání miniatur
  * [Přidání kódování JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Řešení převodu barevného prostoru](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Psaní miniatur](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Zjišťování chyb v pracovním postupu](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Dokončený pracovní postup](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Časově ořezávání multibitového výstupu MP4](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Přehled pracovního postupu pro zahájení přidávání oříznutí](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Použití zastřihovače streamů](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Dokončený pracovní postup](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Představujeme komponentu skriptovaný](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Skriptování v rámci pracovního postupu: Hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Ořezávání multibitového výkonu MP4 na základě rámu](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Podrobný přehled pro zahájení přidávání oříznutí](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Použití XML seznamu klipů](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Úprava seznamu klipů ze skriptované komponenty](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Přidání vlastnosti pohodlí ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a name="encoding-mxf-into-a-single-bitrate-mp4"></a><a id="MXF_to_MP4"></a>Kódování MXF do jednoho protokolu MP4 o přenosové rychlosti
Tato část ukazuje, jak vytvořit jeden přenosový tok . MP4 soubor s AAC-HE kódované audio z . Vstupní soubor MXF.

### <a name="starting-a-new-workflow"></a><a id="MXF_to_MP4_start_new"></a>Spuštění nového pracovního postupu
Otevřete Návrháře pracovních postupů a vyberte Soubor > Nový pracovní prostor > Podrobný plán překódování

Nový pracovní postup zobrazuje tři prvky:

* Primární zdrojový soubor
* XML seznam klipů
* Výstupní soubor/datový zdroj  

![Nový pracovní postup kódování](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nový pracovní postup kódování*

### <a name="using-the-media-file-input"></a><a id="MXF_to_MP4_with_file_input"></a>Použití vstupu mediálního souboru
Chcete-li přijmout vstupní mediální soubor, začnete přidáním součásti Vstup mediálního souboru. Chcete-li přidat komponentu do pracovního postupu, vyhledejte ji ve vyhledávacím poli Úložiště a přetáhněte požadovanou položku do podokna návrháře. Opakujte akci pro vstup mediálního souboru a připojte komponentu Primární zdrojový soubor ke vstupnímu pinu Název souboru ze vstupu mediálního souboru.

![Vstup připojeného mediálního souboru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Vstup připojeného mediálního souboru*

Zpočátku určete vhodný ukázkový soubor, který se má použít při navrhování vlastního pracovního postupu. Chcete-li tak učinit, klepněte na pozadí podokna návrháře a vyhledejte vlastnost Primární zdrojový soubor v podokně vlastností vpravo. Klepněte na ikonu složky a vyberte požadovaný soubor pro testování pracovního postupu. Součást Vstup mediálního souboru zkontroluje soubor a naplní jeho výstupní kolíky tak, aby odrážely podrobnosti ukázkového souboru, který byl zkontrolován.

![Vstup navyplněný mediální soubor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Vstup navyplněný mediální soubor*

Nyní, když je vstup naplněn, je dalším krokem nastavení kódování výstupu. Podobně jako byl nakonfigurován primární zdrojový soubor, nyní nakonfigurujte vlastnost Proměnná výstupní složky, která je pod ní.

![Nakonfigurované vstupní a výstupní vlastnosti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Nakonfigurované vstupní a výstupní vlastnosti*

### <a name="inspecting-media-streams"></a><a id="MXF_to_MP4_streams"></a>Kontrola datových proudů médií
Často je žádoucí vědět, jak datový proud vypadá, jak toproté pracovního postupu. Chcete-li zkontrolovat datový proud v libovolném bodě pracovního postupu, stačí kliknout na výstup nebo vstupní kolík na libovolné součásti. V takovém případě zkuste kliknout na výstupní pin nekomprimovaného videa ze vstupu mediálního souboru. Otevře se dialogové okno, které umožňuje zkontrolovat odchozí video.

![Kontrola výstupního pinu Nekomprimovaného videa](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Kontrola výstupního pinu Nekomprimovaného videa*

V tomto případě se ukazuje, že video obsahuje vstup 1920x1080 při 24 snímcích za sekundu při vzorkování 4:2:2 pro video téměř 2 minuty.

### <a name="adding-a-video-encoder-for-mp4-file-generation"></a><a id="MXF_to_MP4_file_generation"></a>Přidání kodéru videa pro . Generování souborů MP4
Nyní jsou k dispozici nekomprimované video a více výstupních kolíků nekomprimovaného zvuku pro použití na vstupu mediálního souboru. Chcete-li kódovat příchozí video, musí být do pracovního postupu přidána komponenta kódování - v tomto případě pro generování . MP4 soubory.

Chcete-li kódovat datový proud videa na H.264, přidejte komponentu AVC Video Encoder na povrch návrháře. Tato komponenta přebírá dekompresi datového proudu videa jako vstup a poskytuje komprimovaný datový proud videa AVC na výstupním pinu.

![Nepřipojený kodér AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Nepřipojený kodér AVC*

Jeho vlastnosti určují, jak přesně kódování probíhá. Podívejme se na některá z nejdůležitějších nastavení:

* Šířka výstupu a výška výstupu: určuje rozlišení kódovaného videa. V tomto případě je 640x360 dobrým nastavením.
* Kmitočet snímků: když je nastavena na průchod, bude to jen přijmout zdrojovou kmitočet snímků, je možné to přepsat. Taková konverze snímkové frekvence není kompenzována pohybem.
* Profil a úroveň: určuje profil a úroveň AVC. Chcete-li pohodlně získat další informace o různých úrovních a profilech, klikněte na ikonu otazníku na komponentě AVC Video Encoder a na stránce nápovědy se zobrazí více podrobností o každé z úrovní. V tomto příkladu použijte hlavní profil na úrovni 3.2 (výchozí).
* Režim řízení rychlosti a přenosová rychlost (kbps): v tomto scénáři se vyvažte pro výstup konstantního přenosového toku (CBR) při 1200 kbps
* Formát videa: poskytuje informace o VUI (Video. Informace o použitelnosti), který se zapisuje do datového proudu H.264 (vedlejší informace, které mohou být použity dekodérem k vylepšení zobrazení, ale nejsou nezbytné pro správné dekódování):
* NTSC (typické pro USA nebo Japonsko, pomocí 30 fps)
* PAL (typické pro Evropu, použití 25 fps)
* Režim velikosti GOP: nastavte pevnou velikost GOP pro naše účely s klíčovým intervalem 2 sekundy s uzavřenými GOP. Nastavení 2 sekund zajišťuje kompatibilitu s dynamickým balením, které poskytuje Azure Media Services.

Chcete-li kodér AVC napájet, připojte výstupní pin nekomprimovaného videa ze vstupní součásti Vstup mediálního souboru ke vstupnímu pinu Nekomprimovaného videa z kodéru AVC.

![Připojený kodér AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Připojený hlavní kodér AVC*

### <a name="encoding-the-audio-stream"></a><a id="MXF_to_MP4_audio"></a>Kódování zvukového proudu
V tomto okamžiku je stále potřeba komprimovat původní nekomprimovaný zvukový proud. Pro kompresi zvukového proudu přidejte komponentu Kodéru AAC (Dolby) do pracovního postupu.

![Nepřipojený kodér AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Nepřipojený kodér AAC*

Nyní je tu nekompatibilita: je tu jen jeden nekomprimovaný zvukový vstupní pin z AAC Encoder, zatímco více než pravděpodobné, že vstup mediálního souboru bude mít k dispozici dva různé nekomprimované zvukové proudy: jeden pro levý zvukový kanál a jeden pro pravý. (Pokud máte co do činění s prostorovým zvukem, je to šest kanálů.) Takže není možné přímo připojit zvuk ze zdroje vstupu mediálního souboru do zvukového kodéru AAC. Komponenta AAC očekává takzvaný "prokládáný" zvukový proud: jeden datový proud, který má levý i pravý kanál prokládáný mezi sebou. Jakmile z našeho zdrojového mediálního souboru víme, že zvukové stopy jsou na jaké pozici ve zdroji, můžeme generovat takový proložený zvukový proud se správně přiřazenými polohami reproduktorů pro levou a pravou.

Za prvé, jeden chce generovat prokládání proudu z požadovaných zdrojových zvukových kanálů. Komponenta Audio Stream Interleaver to zvládá za nás. Přidejte jej do pracovního postupu a připojte k němu zvukové výstupy ze vstupu mediálního souboru.

![Připojený prokládání zvukového proudu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Připojený prokládání zvukového proudu*

Nyní, když máme proložený zvukový proud, stále jsme nespecifikovali, kam přiřadit pozici levého nebo pravého reproduktoru. Abychom to mohli specifikovat, můžeme využít přiřazení polohy mluvčího.

![Přidání přiřaditelu pozice mluvčího](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Přidání přiřaditelu pozice mluvčího*

Nakonfigurujte přiřazení polohy reproduktoru pro použití se stereofonním vstupním proudem prostřednictvím přednastaveného filtru kodéru "Custom" a přednastavení kanálu nazvaného "2.0 (L,R)." (Tím se pozice levého reproduktoru přiřadí kanálu 1 a pravá pozice reproduktoru ke kanálu 2.)

Připojte výstup polohového zařízení reproduktoru ke vstupu kodéru AAC. Potom řekněte kodéri AAC, aby pracoval s přednastavením kanálu "2.0 (L,R)", aby věděl, že se má vypořádat se stereofonním zvukem jako se vstupem.

### <a name="multiplexing-audio-and-video-streams-into-an-mp4-container"></a><a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexování datových proudů zvuku a videa do kontejneru MP4
Vzhledem k tomu, naše AVC kódované video stream a naše AAC kódované audio stream, můžeme zachytit jak do . kontejner MP4. Proces míchání různých proudů do jednoho se nazývá "multiplexování" (nebo "muxing"). V tomto případě jsme prokládání audio a video proudy v jednom koherentní . MP4 balíček. Součást, která koordinuje tento pro . Kontejner MP4 se nazývá multiplexer ISO MPEG-4. Přidejte jeden k povrchu návrháře a připojte k jeho vstupům video kodér AVC i kodér AAC.

![Připojený multiplexer MPEG4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Připojený multiplexer MPEG4*

### <a name="writing-the-mp4-file"></a><a id="MXF_to_MP4_writing_mp4"></a>Zápis souboru MP4
Při zápisu výstupního souboru se používá komponenta Výstup souboru. Můžeme to připojit k výstupu multiplexeru ISO MPEG-4 tak, aby se jeho výstup zapsal na disk. Chcete-li to provést, připojte výstupní kolík kontejneru (MPEG-4) ke vstupnímu pinu zápisu výstupu souboru.

![Výstup připojeného souboru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Výstup připojeného souboru*

Název souboru, který se používá, je určen vlastností File. Zatímco tato vlastnost může být pevně zakódována na danou hodnotu, s největší pravděpodobností ji chcete nastavit prostřednictvím výrazu.

Chcete-li, aby pracovní postup automaticky určoval vlastnost výstupního názvu souboru z výrazu, klepněte na tlačítko vedle názvu souboru (vedle ikony složky). V rozevírací nabídce vyberte možnost Výraz. Tím se vyvolá editor výrazů. Nejprve vymažte obsah editoru.

![Prázdný editor výrazů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Prázdný editor výrazů*

Editor výrazů umožňuje zadat libovolnou hodnotu literálu smíchanou s jednou nebo více proměnnými. Proměnné začínají znakem dolaru. Když stisknete klávesu $, editor zobrazí rozevírací seznam s výběrem dostupných proměnných. V našem případě použijeme kombinaci výstupní proměnné adresáře a proměnné názvu základního vstupního souboru:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Vyplněný editor výrazů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Vyplněný editor výrazů*

> [!NOTE]
> Chcete-li zobrazit výstupní soubor úlohy kódování v Azure, musíte zadat hodnotu v editoru výrazů.
>
>

Když potvrdíte výraz stisknutím ok, okno vlastností zobrazí náhled hodnoty, kterou vlastnost File v tomto okamžiku vyřeší.

![Výraz souboru řeší výstupní dir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Výraz souboru řeší výstupní dir*

### <a name="creating-a-media-services-asset-from-the-output-file"></a><a id="MXF_to_MP4_asset_from_output"></a>Vytvoření datového zdroje mediálních služeb z výstupního souboru
I když jsme napsali výstupní soubor MP4, stále potřebujeme označit, že tento soubor patří k výstupnímu majetku, který mediální služby generují v důsledku provádění tohoto pracovního postupu. Za tímto účelem se použije uzel Výstupní soubor/datový zdroj na plátně pracovního postupu. Všechny příchozí soubory do tohoto uzlu tvoří součást výsledného datového zdroje Azure Media Services.

Připojte komponentu Výstup souboru k komponentě Výstupní soubor/Datový zdroj a dokončete pracovní postup.

![Dokončený pracovní postup](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Dokončený pracovní postup*

### <a name="test-the-finished-workflow-locally"></a><a id="MXF_to_MP4_test"></a>Testování dokončeného pracovního postupu místně
Chcete-li pracovní postup otestovat místně, stiskněte tlačítko přehrát na panelu nástrojů v horní části. Po dokončení provádění pracovního postupu zkontrolujte výstup generovaný v nakonfigurované výstupní složce. Uvidíte hotový výstupní soubor MP4, který byl zakódován ze vstupního zdrojového souboru MXF.

## <a name="encoding-mxf-into-mp4---multibitrate-dynamic-packaging-enabled"></a><a id="MXF_to_MP4_with_dyn_packaging"></a>Kódování MXF do MP4 - povoleno dynamické balení s více bitovými rychlostmi
Tento návod vytvoří sadu více bitrate MP4 souborů s AAC kódované audio z jednoho . Vstupní soubor MXF.

Pokud je pro použití v kombinaci s funkcemi dynamického balení nabízenými službou Azure Media Services žádoucí výstup datového zdroje s více přenosovými rychlostmi, bude nutné vygenerovat více souborů MP4 zarovnaných do gop, z nichž každý jiný datový tok a rozlišení. K tomu, [kódování MXF do jednoho průchodu mp4 přenosového toku](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) nám poskytuje dobrý výchozí bod.

![Spuštění pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Spuštění pracovního postupu*

### <a name="adding-one-or-more-additional-mp4-outputs"></a><a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Přidání jednoho nebo více dalších výstupů MP4
Každý soubor MP4 v našem výsledném datovém zdroji Azure Media Services podporuje jiný datový tok a rozlišení. Do pracovního postupu přidáme jeden nebo více výstupních souborů MP4.

Chcete-li se ujistit, že máme všechny naše video kodéry vytvořené se stejným nastavením, je nejvhodnější duplikovat již existující AVC Video Encoder a nakonfigurovat další kombinaci rozlišení a toku (přidáme jeden z 960 x 540 při 25 snímcích za sekundu při 2,5 Mbps ). Chcete-li duplikovat existující kodér, zkopírujte jej vložte na povrch návrháře.

Připojte výstupní pin nekomprimovaného videa vstupu mediálního souboru do naší nové komponenty AVC.

![Druhý kodér AVC připojený](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Druhý kodér AVC připojený*

Nyní přizpůsobte konfiguraci pro náš nový kodér AVC na výstup 960x540 při rychlosti 2,5 Mb/s. (Použijte jeho vlastnosti "Výstupní šířka", "Výstupní výška" a "Přenosová rychlost (kbps)" pro toto.)

Vzhledem k tomu, že chceme použít výsledný datový zdroj společně s dynamickým balením Služby Azure Media Services, koncový bod streamování musí být schopen generovat z těchto souborů MP4 fragmenty HLS/Fragmented MP4/DASH, které jsou přesně zarovnané k sobě navzájem tak, že klienti, kteří přecházejí mezi různými datovými toky, získají jeden plynulý nepřetržitý video a zvukový zážitek. Aby se tak stalo, musíme zajistit, aby ve vlastnostech obou kodéry AVC gop ("skupina obrázků") velikost pro oba soubory MP4 je nastavena na 2 sekundy, což lze provést:

* nastavení režimu velikosti GOP na pevnou velikost
* intervalu klíčového snímku na dvě sekundy.
* také nastavit GOP IDR Control na Uzavřeno GOP zajistit všechny GOP stojí na vlastní pěst bez závislostí

Chcete-li tento pracovní postup usnadnit pochopení, přejmenujte první kodér AVC na "AVC Video Encoder 640x360 1200 kbps" a druhý kodér AVC "AVC Video Encoder 960x540 2500 kbps".

Nyní přidejte druhý multiplexer ISO MPEG-4 a druhý výstup souboru. Připojte multiplexer k novému kodéru AVC a ujistěte se, že jeho výstup je směrován do výstupu souboru. Poté také připojte výstup zvukového kodéru AAC ke vstupu nového multiplexoru. Výstup souboru pak může být připojen k výstupnímu souboru nebo uzlu datového zdroje a přidat jej do datového zdroje Mediálních služeb, který bude vytvořen.

![Druhý muxer a výstup souboru připojen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Druhý muxer a výstup souboru připojen*

Pokud chcete kompatibilitu s dynamickým balením Azure Media Services, nakonfigurujte režim bloku multiplexeru tak, aby počítal nebo trval dobu trvání GOP, a nastavte gopp na blok na 1. (To by mělo být výchozí.)

![Režimy bloku Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Režimy bloku Muxer*

Poznámka: Tento proces můžete zopakovat pro všechny ostatní kombinace datového toku a rozlišení, které chcete přidat do výstupu majetku.

### <a name="configuring-the-file-output-names"></a><a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfigurace názvů výstupů souborů
Do výstupního datového zdroje jsme přidali více než jeden soubor. To poskytuje potřebu ujistěte se, že názvy souborů pro každý z výstupních souborů se liší od sebe navzájem a možná dokonce použít konvence pojmenování souborů, takže je zřejmé z názvu souboru, co máte co do činění s.

Název výstupu souboru lze ovládat pomocí výrazů v návrháři. Otevřete podokno vlastností pro jednu z komponent Výstup souboru a otevřete editor výrazů pro vlastnost File. Náš první výstupní soubor byl nakonfigurován pomocí následujícího výrazu (viz návod pro přepnutí z [MXF na výstup MP4 s jedním datovým tokem](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

To znamená, že náš název souboru je určen dvěma proměnnými: výstupním adresářem pro zápis a základním názvem zdrojového souboru. První je vystavena jako vlastnost v kořenovém adresáři pracovního postupu a druhý je určen příchozím souborem. Výstupní adresář je to, co používáte pro místní testování; tato vlastnost bude přepsána modulem pracovního postupu při spuštění pracovního postupu cloudovým mediálním procesorem ve službě Azure Media Services.
Chcete-li, aby obě naše výstupní soubory konzistentní výstupní pojmenování, změnit první soubor pojmenování výraz:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

a druhý na:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Proveďte zprostředkující test, abyste se ujistili, že jsou oba výstupní soubory MP4 správně generovány.

### <a name="adding-a-separate-audio-track"></a><a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Přidání samostatné zvukové stopy
Jak uvidíme později, když vygenerujeme soubor .ism, který půjde s našimi výstupními soubory MP4, budeme také vyžadovat zvukový soubor MP4 jako zvukovou stopu pro naše adaptivní streamování. Chcete-li vytvořit tento soubor, přidejte do pracovního postupu další muxer (MULTIPLEXer ISO-MPEG-4) a připojte výstupní kolík kodéru AAC se vstupním kolíkem pro stopu 1.

![Audio Muxer přidán](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Audio Muxer přidán*

Vytvořte třetí komponentu Výstup souboru pro výstup odchozího datového proudu z muxeru a nakonfigurujte výraz pojmenování souboru jako:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Zvukový muxer pro vytváření výstupu souboru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Zvukový muxer pro vytváření výstupu souboru*

### <a name="adding-the-ism-smil-file"></a><a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Přidání . Soubor ISM SMIL
Aby dynamické balení fungovalo v kombinaci se soubory MP4 (a pouze s mp4) v našem datovém zdroji Media Services, potřebujeme také soubor manifestu (nazývaný také soubor "SMIL": Synchronizovaný jazyk integrace multimédií). Tento soubor označuje službě Azure Media Services, jaké soubory MP4 jsou k dispozici pro dynamické balení a které z nich je třeba zvážit pro streamování zvuku. Typický soubor manifestu pro sadu MP4 s jedním zvukovým proudem vypadá takto:

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

Soubor .ism obsahuje v rámci příkazu switch, odkaz na každý z jednotlivých video souborů MP4 a kromě těch také jeden (nebo více) zvukový soubor odkazy na MP4, který obsahuje pouze zvuk.

Generování souboru manifestu pro naši sadu MP4 lze provést prostřednictvím součásti s názvem "AMS Manifest Writer." Chcete-li jej použít, přetáhněte jej na povrch a připojte výstupní kolíky "Write Complete" ze tří součástí výstupu souboru se vstupem AMS Manifest Writer. Pak se ujistěte, že připojení výstupu AMS Manifest Writer na výstupní soubor /asset.

Stejně jako u našich dalších součástí výstupu souborů nakonfigurujte výstupní název souboru ISM pomocí výrazu:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Náš hotový pracovní postup vypadá takto:

![Dokončeno MXF pro multibitrate MP4 workflow](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Dokončeno MXF pro multibitrate MP4 workflow*

## <a name="encoding-mxf-into-multibitrate-mp4---enhanced-blueprint"></a><a id="MXF_to__multibitrate_MP4"></a>Kódování MXF do multibitrate MP4 - vylepšený plán
V [předchozím návodu pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) jsme viděli, jak lze jeden vstupní datový zdroj MXF převést na výstupní datový zdroj se soubory MP4 s více přenosovými rychlostmi, souborem MP4 pouze pro zvuk a souborem manifestu pro použití ve spojení s dynamickým balením Azure Media Services.

Tento návod ukazuje, jak některé aspekty mohou být vylepšeny a pohodlnější.

### <a name="workflow-overview-to-enhance"></a><a id="MXF_to_multibitrate_MP4_overview"></a>Přehled pracovního postupu pro vylepšení
![Pracovní postup MP4 s vícebitou rychlostí pro lepší](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Pracovní postup MP4 s vícebitou rychlostí pro lepší*

### <a name="file-naming-conventions"></a><a id="MXF_to__multibitrate_MP4_file_naming"></a>Konvence pojmenování souborů
V předchozím pracovním postupu jsme zadali jednoduchý výraz jako základ pro generování názvů výstupních souborů. Máme však určitou duplikaci: všechny jednotlivé komponenty výstupního souboru specifikované takový výraz.

Například naše výstupní součást souboru pro první soubor videa je nakonfigurována s tímto výrazem:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Zatímco pro druhé výstupní video, máme výraz jako:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Nebylo by to čistší, méně náchylné k chybám a pohodlnější, kdybychom mohli odstranit některé z těchto duplikací a místo toho dělat věci více konfigurovatelné? Naštěstí můžeme: možnosti výrazu návrháře v kombinaci se schopností vytvářet vlastní vlastnosti v kořenovém adresáři pracovního postupu poskytnou další vrstvu pohodlí.

Předpokládejme, že budeme řídit konfiguraci názvu souboru z přenosových rychlostí jednotlivých souborů MP4. Tyto přenosové rychlosti se budeme snažit nakonfigurovat na jednom centrálním místě (v kořenovém adresáři našeho grafu), odkud budou přístupné ke konfiguraci a generování názvu souboru jednotky. Chcete-li to provést, začneme publikováním vlastnosti přenosové rychlosti z obou kodéry AVC do kořenového adresáře našeho pracovního postupu, aby byla přístupná jak z kořenového adresáře, tak z kodéry AVC. (I když je zobrazena na dvou různých místech, existuje pouze jedna základní hodnota.)

### <a name="publishing-component-properties-onto-the-workflow-root"></a><a id="MXF_to__multibitrate_MP4_publishing"></a>Publikování vlastností součásti do kořenového adresáře pracovního postupu
Otevřete první kodér AVC, přejděte na vlastnost Bitrate (kbps) a v rozevírací rozevírací rozevírací části zvolte Publikovat.

![Publikování vlastnosti přenosové rychlosti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publikování vlastnosti přenosové rychlosti*

Nakonfigurujte dialogové okno publikování tak, aby bylo publikováno do kořenového adresáře našeho grafu pracovního postupu, s publikovaným názvem "video1bitrate" a čitelným zobrazovaným názvem "Video 1 Bitrate". Nakonfigurujte vlastní název skupiny s názvem "Streamování datových proudů" a stiskněte příkaz Publikovat.

![Publikování vlastnosti přenosové rychlosti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Dialogové okno publikování pro vlastnost Přenosová rychlost*

Opakujte totéž pro vlastnost datového toku druhého kodéru AVC a pojmenujte ji "video2bitrate" se zobrazovaným názvem "Video 2 Bitrate" ve stejné vlastní skupině "Streaming Bitrates".

Pokud nyní zkontrolujeme kořenové vlastnosti pracovního postupu, zobrazí se naše vlastní skupina se dvěma publikovanými vlastnostmi. Obě odrážejí hodnotu jejich příslušného přetočného kodéru AVC.

![Publikované rekvizity přenosové rychlosti v kořenovém adresáři pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Kdykoli chceme získat přístup k těmto vlastnostem z kódu nebo z výrazu, můžeme tak učinit takto:

* z vložkového kódu z komponenty přímo pod kořenem: node.getPropertyAsString('.. /video1bitrate', null)
* v rámci výrazu: ${ROOT_video1bitrate}

Pojďme dokončit "Streaming Bitrates" skupina zveřejněním naší zvukové stopy datový tok na to také. V rámci vlastností kodéru AAC vyhledejte nastavení Přenosový tok a v rozevíracím seznamu vedle něj vyberte Publikovat. Publikovat do kořenového adresáře grafu s názvem "audio1bitrate" a zobrazovaným názvem "Audio 1 Bitrate" v rámci naší vlastní skupiny "Streaming Bitrates".

![Dialogové okno Publikování pro přenosovou rychlost zvuku](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Dialogové okno Publikování pro přenosovou rychlost zvuku*

![Výsledné video a audio rekvizity na kořen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Výsledné video a audio rekvizity na kořen*

Změna některé z těchto tří hodnot také překonfiguruje a změní hodnoty na příslušné součásti, které jsou propojeny s (a kde jsou publikovány z).

### <a name="have-generated-output-file-names-rely-on-published-property-values"></a><a id="MXF_to__multibitrate_MP4_output_files"></a>Vygenerované názvy výstupních souborů spoléhají na publikované hodnoty vlastností
Namísto hardcoding naše generované názvy souborů, nyní můžeme změnit náš název souboru výraz u každé součásti výstup souboru spoléhat na vlastnosti přenosové rychlosti jsme publikovali v kořenovém adresáři grafu. Počínaje naším prvním výstupem souboru najděte vlastnost Soubor a upravte výraz takto:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Různé parametry v tomto výrazu lze přistupovat a zadávat stisknutím znaku dolaru na klávesnici, zatímco v okně výrazu. Jedním z dostupných parametrů je naše video1bitrate vlastnost, kterou jsme publikovali dříve.

![Přístup k parametrům v rámci výrazu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Přístup k parametrům v rámci výrazu*

Proveďte totéž pro výstup souboru pro naše druhé video:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

a pro výstup pouze pro zvuk:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Pokud nyní změníme datový tok pro některý z video nebo zvukových souborů, příslušný kodér bude překonfigurován a konvence názvů souborů založených na datovém toku bude dodržena všechny automatické.

## <a name="adding-thumbnails-to-multibitrate-mp4-output"></a><a id="thumbnails_to__multibitrate_MP4"></a>Přidání miniatur do multibitrate výstupu MP4
Počínaje pracovním postupem, který generuje [multibitrate MP4 výstup ze vstupu MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), budeme nyní hledat do přidání náhledů do výstupu.

### <a name="workflow-overview-to-add-thumbnails-to"></a><a id="thumbnails_to__multibitrate_MP4_overview"></a>Přehled pracovního postupu pro přidání miniatur
![Pracovní postup MP4 s vícebitou rychlostí, od které se má spustit](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Pracovní postup MP4 s vícebitou rychlostí, od které se má spustit*

### <a name="adding-jpg-encoding"></a><a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Přidání kódování JPG
Srdcem naší generace miniatur bude komponenta JPG Encoder, která je schopna vyprodukovat soubory JPG.

![Kodér JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Kodér JPG*

Nemůžeme však přímo připojit náš nekomprimovaný video stream ze vstupu mediálního souboru do kodéru JPG. Místo toho očekává, že bude předán jednotlivé rámy. To můžeme udělat přes komponentu Video Frame Gate.

![Připojení rámové brány k kodéru JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Připojení rámové brány k kodéru JPG*

Rámová brána jednou za tolik sekund nebo snímků umožňuje průchod video rámečku. Interval a časový posun, se kterým k tomu dochází, lze konfigurovat ve vlastnostech.

![Vlastnosti brány rámce videa](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Vlastnosti brány rámce videa*

Každou minutu vytvoříme miniaturu nastavením režimu na Čas (sekundy) a Interval na 60.

### <a name="dealing-with-color-space-conversion"></a><a id="thumbnails_to__multibitrate_MP4_color_space"></a>Řešení převodu barevného prostoru
I když by se zdálo logické, jak Nekomprimované Video kolíky rámu brány a vstup mediálního souboru lze nyní připojit, dostali bychom varování, pokud bychom tak učinili.

![Chyba vstupního barevného prostoru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Chyba vstupního barevného prostoru*

Je to proto, že způsob, jakým jsou barevné informace zastoupeny v našem původním surovém nekomprimovaném video streamu, pocházejícím z našeho MXF, se liší od toho, co očekává kód JPG. Přesněji řečeno, takzvaný "barevný prostor" "RGB" nebo "Stupně šedi" se očekává, že tok palců To znamená, že příchozí video stream brány Video Frame Gate musí mít nejprve použít konverzi týkající se jeho barevného prostoru.

Přetáhněte do pracovního postupu Převodník barevného prostoru - Intel a připojte jej k naší rámové bráně.

![Připojení převaděče barevného prostoru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Připojení převaděče barevného prostoru*

V okně vlastností vyberte položku BGR 24 ze seznamu Přednastavení.

### <a name="writing-the-thumbnails"></a><a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Psaní miniatur
Na rozdíl od našeho MP4 videa, JPG Encoder komponenty výstupy více než jeden soubor. Aby bylo možné se s tím vypořádat, scene search JPG File Writer komponenta může být použita: to trvá příchozí JPG miniatury a zapíše je, každý název souboru je suffixed jiným číslem. (Číslo obvykle označující počet sekund/jednotek v datovém proudu, ze kterého byla miniatura nakreslena.)

![Představujeme scény Hledání JPG File Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Představujeme scény Hledání JPG File Writer*

Konfigurace vlastnosti Cesta výstupní složky s výrazem: ${ROOT_outputWriteDirectory}

a vlastnost Předpona Filename s:

    ${ROOT_sourceFileBaseName}_thumb_

Předpona určuje, jak jsou pojmenovány soubory miniatur. Jsou přišito s číslem označujícím polohu palce v proudu.

![Vlastnosti zapisovače souborů JPG pro vyhledávání scén](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Vlastnosti zapisovače souborů JPG pro vyhledávání scén*

Připojte zapisovač souborů JPG pro hledání scény k uzlu Výstupní soubor/datový zdroj.

### <a name="detecting-errors-in-a-workflow"></a><a id="thumbnails_to__multibitrate_MP4_errors"></a>Zjišťování chyb v pracovním postupu
Připojte vstup převodníku barevného prostoru k nezpracovanému výstupu nekomprimovaného videa. Nyní proveďte místní test pro pracovní postup. Je pravděpodobné, že pracovní postup náhle přestane provádět a označí červeným obrysem součásti, která zjistila chybu:

![Chyba převaděče barevného prostoru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Chyba převaděče barevného prostoru*

Klikněte na malou červenou ikonu "E" v pravém horním rohu komponenty Color Space Converter, abyste zjistili, jaký je důvod, proč pokus o kódování selhal.

![Dialogové okno Chyba převaděče barevného prostoru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Dialogové okno Chyba převaděče barevného prostoru*

Ukazuje se, jak můžete vidět, že příchozí barevný prostor standard pro převodník barevného prostoru musí být rec601 pro náš požadovaný převod YUV na RGB. Zdá se, že náš proud nenaznačuje jeho rec601. (Rec 601 je standard pro kódování prokládaných analogových video signálů v digitální podobě videa. Specifikuje aktivní oblast zahrnující 720 vzorků jasu a 360 vzorků chrominance na řádek. Systém kódování barev je známý jako YCbCr 4:2:2.)

Chcete-li tento problém vyřešit, uvedeme na metadatech našeho streamu, že máme co do činění s obsahem rec601. K tomu použijeme komponentu Video Data Type Updater, kterou vložíme mezi náš surový zdroj a komponentu převodu barevného prostoru. Tento aktualizační typ dat umožňuje ruční aktualizaci určitých vlastností datového typu videa. Nakonfigurujte jej tak, aby označoval standard barevného prostoru "Rec 601". To způsobí, že aktualizace datového typu videa tagovat datový proud s "Rec 601" barevný prostor, pokud nebyl a dosud definován žádný barevný prostor. (Nepřepíše žádná existující metadata, pokud nebylo zaškrtnuto políčko Přepsat.)

![Aktualizace standardu barevného prostoru v aktualizačním zařízení datového typu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Aktualizace standardu barevného prostoru v aktualizačním zařízení datového typu*

### <a name="finished-workflow"></a><a id="thumbnails_to__multibitrate_MP4_finish"></a>Dokončený pracovní postup
Nyní, když je náš pracovní postup dokončen, proveďte další zkušební běh, abyste viděli, že projde.

![Dokončený pracovní postup pro výstup s více mp4 s miniaturami](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Dokončený pracovní postup pro výstup s více mp4 s miniaturami*

## <a name="time-based-trimming-of-multibitrate-mp4-output"></a><a id="time_based_trim"></a>Časově ořezávání multibitového výstupu MP4
Počínaje pracovním postupem, který generuje [multibitrate MP4 výstup ze vstupu MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), budeme nyní hledat do oříznutí zdrojové video na základě časových razítek.

### <a name="workflow-overview-to-start-adding-trimming-to"></a><a id="time_based_trim_start"></a>Přehled pracovního postupu pro zahájení přidávání oříznutí
![Spuštění pracovního postupu pro přidání oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Spuštění pracovního postupu pro přidání oříznutí*

### <a name="using-the-stream-trimmer"></a><a id="time_based_trim_use_stream_trimmer"></a>Použití zastřihovače streamů
Komponenta Stream Trimmer umožňuje oříznout začátek a konec základny vstupního datového proudu na základě informací o časování (sekundy, minuty, ...). Zastřihovač nepodporuje ořezávání na bázi rámu.

![Zastřihovač streamů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Zastřihovač streamů*

Namísto propojení kodéry AVC a přiřazení polohy reproduktoru přímo se vstupem mediálního souboru vložíme mezi ty zastřihovač i streamů. (Jeden pro video signál a jeden pro prokládání audio signálu.)

![Mezi zastřihovač streamů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Mezi zastřihovač streamů*

Nakonfigurujme zastřihovač tak, abychom ve videu zpracovávali video a zvuk pouze mezi 15 sekundami a 60 sekundami.

Přejděte na vlastnosti zastřihovače videostreamu a nakonfigurujte vlastnosti Počáteční čas (15 s) i Koncový čas (60 s). Abychom se ujistili, že naše zastřihovač zvuku i videa jsou vždy nakonfigurovány na stejné počáteční a koncové hodnoty, publikujeme je do kořenového adresáře pracovního postupu.

![Publikovat vlastnost čas zahájení z streamtrimmeru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publikovat vlastnost čas zahájení z streamtrimmeru*

![Dialogové okno vlastností publikování pro počáteční čas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Dialogové okno vlastností publikování pro počáteční čas*

![Dialogové okno vlastností publikování pro koncový čas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Dialogové okno vlastností publikování pro koncový čas*

Pokud nyní kontrolujeme kořen našeho pracovního postupu, obě vlastnosti jsou úhledně zobrazeny a konfigurovatelné odtud.

![Publikované vlastnosti dostupné v kořenovém adresáři](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Publikované vlastnosti dostupné v kořenovém adresáři*

Nyní otevřete vlastnosti oříznutí ze zvukového zastřihovače a nakonfigurujte počáteční i koncový čas pomocí výrazu, který odkazuje na publikované vlastnosti v kořenovém adresáři našeho pracovního postupu.

Pro čas zahájení oříznutí zvuku:

    ${ROOT_TrimmingStartTime}

a na jeho konec:

    ${ROOT_TrimmingEndTime}

### <a name="finished-workflow"></a><a id="time_based_trim_finish"></a>Dokončený pracovní postup
![Dokončený pracovní postup](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Dokončený pracovní postup*

## <a name="introducing-the-scripted-component"></a><a id="scripting"></a>Představujeme komponentu skriptovaný
Skriptované komponenty mohou spouštět libovolné skripty během fází provádění našeho pracovního postupu. Existují čtyři různé skripty, které mohou být provedeny, každý se specifickými vlastnostmi a jejich vlastní místo v životním cyklu pracovního postupu:

* **příkazScript**
* **realizovatScript**
* **processInputScript**
* **lifeCycleScript**

Dokumentace skriptované komponenty jde podrobněji pro každou z výše uvedených. V [následující části](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)se komponenta **skriptování realizeScript** používá k vytvoření cliplistu xml za chodu při spuštění pracovního postupu. Tento skript se nazývá během nastavení součásti, což se děje pouze jednou v jeho životním cyklu.

### <a name="scripting-within-a-workflow-hello-world"></a><a id="scripting_hello_world"></a>Skriptování v rámci pracovního postupu: Hello world
Přetáhněte komponentu s skripty na povrch návrháře a přejmenujte ji (například SetClipListXML).

![Přidání skriptované komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Přidání skriptované komponenty*

Při kontrole vlastností součásti Skriptovaný se zobrazí čtyři různé typy skriptů, z nichž každý bude konfigurovatelný na jiný skript.

![Vlastnosti skriptované součásti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Vlastnosti skriptované součásti*

Zrušte zaškrtnutí processInputScript a otevřete editor pro realizeScript. Teď jsme připraveni začít skriptovat.

Skripty jsou napsány v Groovy, dynamicky kompilovaném skriptovacím jazyce pro platformu Java, který zachovává kompatibilitu s Javou. Ve skutečnosti, většina Java kód je platný Groovy kód.

Pojďme napsat jednoduchý hello world groovy skript v kontextu našeho realizeScript. Do editoru zadejte následující:

    node.log("hello world");

Nyní proveďte místní test. Po tomto spuštění zkontrolujte (prostřednictvím karty Systém na skriptované součásti) vlastnost Protokoly.

![Dobrý den, svět log výstup](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Dobrý den, svět log výstup*

Objekt uzlu, na který voláme metodu protokolu, odkazuje na náš aktuální "uzel" nebo komponentu, ve které skriptujeme. Každá komponenta jako taková má schopnost výstup dat protokolování, k dispozici prostřednictvím systémové karty. V tomto případě jsme výstup řetězce literálu "hello world." Důležité pochopit, zde je, že to může ukázat jako neocenitelný ladicí nástroj, který vám poskytne přehled o tom, co skript skutečně dělá.

V našem skriptovacím prostředí máme také přístup k vlastnostem na jiných součástech. Vyzkoušejte tohle:

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

Naše log okno nám ukazuje následující:

![Protokolovat výstup pro přístup k cestám uzlů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Protokolovat výstup pro přístup k cestám uzlů*

## <a name="frame-based-trimming-of-multibitrate-mp4-output"></a><a id="frame_based_trim"></a>Ořezávání multibitového výkonu MP4 na základě rámu
Počínaje pracovním postupem, který generuje [multibitrate MP4 výstup ze vstupu MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), budeme nyní hledat do oříznutí zdrojové video na základě počtu snímků.

### <a name="blueprint-overview-to-start-adding-trimming-to"></a><a id="frame_based_trim_start"></a>Podrobný přehled pro zahájení přidávání oříznutí
![Pracovní postup pro zahájení přidávání oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Pracovní postup pro zahájení přidávání oříznutí*

### <a name="using-the-clip-list-xml"></a><a id="frame_based_trim_clip_list"></a>Použití XML seznamu klipů
Ve všech předchozích kurzech pracovního postupu jsme jako zdroj vstupu videa použili komponentu Vstup mediálního souboru. Pro tento konkrétní scénář však budeme místo toho používat komponentu Zdroj seznamu klipů. To by neměl být preferovaný způsob práce; Zdroj seznamu klipů používejte pouze v případě, že k tomu existuje skutečný důvod (například v následujícím případě, kdy využíváme možnosti oříznutí seznamu klipů).

Chcete-li přepnout ze vstupu mediálního souboru do zdroje seznamu klipů, přetáhněte komponentu Zdroj seznamu klipů na návrhovou plochu a připojte pin XML seznamu klipů k uzlu XML seznamu klipů návrháře pracovního postupu. To naplní zdroj seznamu klipů výstupními kolíky podle našeho vstupního videa. Nyní připojte piny Nekomprimované video a nekomprimované audio ze zdroje seznamu klipů k příslušným kodéry AVC a audio streamovému prokládání. Nyní odeberte vstup mediálního souboru.

![Byl nahrazen vstupem mediálního souboru zdrojem seznamu klipů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Byl nahrazen vstupem mediálního souboru zdrojem seznamu klipů*

Komponenta Zdroj seznamu klipů bere jako svůj vstup "XML seznamu klipů". Při výběru zdrojového souboru, který chcete testovat místně, je tento seznam klipů xml automaticky vyplněn pro vás.

![Automaticky vyplněná vlastnost XML seznamu klipů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automaticky vyplněná vlastnost XML seznamu klipů*

Při pohledu trochu blíže k xml, to je, jak to vypadá:

![Dialogové okno Upravit seznam klipů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Dialogové okno Upravit seznam klipů*

To však neodráží možnosti xml seznamu klipů. Jednou z možností, kterou máme, je přidat prvek "Trim" pod video i audio zdroj, jako je tento:

![Přidání prvku oříznutí do seznamu klipů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Přidání prvku oříznutí do seznamu klipů*

Pokud změníte seznam klipů xml, jako je tento výše, a provést místní test spustit, uvidíte video správně oříznuté mezi 10 a 20 sekund ve videu.

Na rozdíl od toho, co se stane, když provedete místní spuštění, by však stejný xml seznamu klipů neměl stejný účinek při použití v pracovním postupu, který běží ve službě Azure Media Services. Při spuštění Azure Premium Encoder, cliplist xml se vygeneruje pokaždé znovu, na základě vstupního souboru, který byla zadána úloha kódování. To znamená, že všechny změny, které provedeme na xml by bohužel přepsat.

Chcete-li čelit cliplist xml se vymaže při spuštění úlohy kódování, můžeme regenerovat za běhu těsně po zahájení našeho pracovního postupu. Tyto vlastní akce lze přijmout prostřednictvím toho, co se nazývá "Scripted Component." Další informace naleznete [v tématu Introducing the Scripted Component](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Přetáhněte komponentu skriptového na povrch návrháře a přejmenujte ji na SetClipListXML.

![Přidání skriptované komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Přidání skriptované komponenty*

Při kontrole vlastností součásti Skriptovaný je zobrazeny čtyři různé typy skriptů, z nichž každý bude konfigurovatelný na jiný skript.

![Vlastnosti skriptované součásti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Vlastnosti skriptované součásti*

### <a name="modifying-the-clip-list-from-a-scripted-component"></a><a id="frame_based_trim_modify_clip_list"></a>Úprava seznamu klipů ze skriptované komponenty
Než budeme moci přepsat xml seznamu klipů, který je generován při spuštění pracovního postupu, budeme muset mít přístup k vlastnosti a obsahu xml seznamu klipů. Můžeme to udělat takto:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Protokolovaný seznam příchozích klipů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Protokolovaný seznam příchozích klipů*

Nejprve potřebujeme způsob, jak určit, z jakého bodu, do kterého okamžiku chceme zkrátit video. Chcete-li to pohodlné pro méně technické uživatele pracovního postupu, publikujte dvě vlastnosti do kořenového adresáře grafu. Chcete-li to provést, klepněte pravým tlačítkem myši na povrch návrháře a vyberte možnost Přidat vlastnost:

* První vlastnost: "ClippingTimeStart" typu: "TIMECODE"
* Druhá vlastnost: "ClippingTimeEnd" typu: "TIMECODE"

![Dialogové okno Přidat vlastnosti pro počáteční čas oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Dialogové okno Přidat vlastnosti pro počáteční čas oříznutí*

![Publikované časové rekvizity oříznutí v kořenovém adresáři pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publikované časové rekvizity oříznutí v kořenovém adresáři pracovního postupu*

Nakonfigurujte obě vlastnosti na vhodnou hodnotu:

![Konfigurace počátečních a koncových vlastností oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Konfigurace počátečních a koncových vlastností oříznutí*

Nyní, z našeho skriptu, můžeme přistupovat k oběma vlastnostem, takto:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Okno protokolu zobrazující začátek a konec oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Okno protokolu zobrazující začátek a konec oříznutí*

Pojďme analyzovat řetězce časového kódu do vhodnější ho použít formulář pomocí jednoduchého regulárního výrazu:

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

![Okno protokolu s výstupem analyzovaného časového kódu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Okno protokolu s výstupem analyzovaného časového kódu*

S těmito informacemi po ruce, nyní můžeme upravit cliplist xml tak, aby odrážely počáteční a koncový čas pro požadovaný snímek-přesné oříznutí filmu.

![Kód skriptu pro přidání prvků oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Kód skriptu pro přidání prvků oříznutí*

To bylo provedeno prostřednictvím běžných operací manipulace s řetězci. Výsledný upravený seznam klipů xml je zapsán zpět do vlastnosti clipListXML v kořenovém pracovním postupu prostřednictvím metody "setProperty". Okno protokolu po dalším spuštění testu nám ukáže následující:

![Protokolování výsledného seznamu klipů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Protokolování výsledného seznamu klipů*

Proveďte test-run vidět, jak video a audio proudy byly oříznuty. Jak budete dělat více než jeden test-run s různými hodnotami pro ořezávání bodů, všimnete si, že ty nebudou vzaty v úvahu však! Důvodem je, že návrhář, na rozdíl od runtime Azure, není přepsat xml cliplist každý běh. To znamená, že pouze při prvním nastavení bodů dovnitř a vyvolení způsobí, že xml transformovat, všechny ostatní časy, naše guard klauzule (if(`clipListXML.indexOf("<trim>") == -1`)) zabrání workflow z přidání dalšího trim prvek, když je již jeden přítomen.

Chcete-li, aby náš pracovní postup byl vhodný pro testování místně, doporučujeme přidat nějaký domácí kód, který kontroluje, zda byl prvek trim již přítomen. Pokud ano, můžeme jej před pokračováním odstranit úpravou xml s novými hodnotami. Spíše než pomocí prostý řetězec manipulace, je to pravděpodobně bezpečnější provést prostřednictvím skutečného objektového modelu XML analýzy.

Než budeme moci přidat takový kód i když, budeme muset přidat řadu import prohlášení na začátku našeho skriptu jako první:

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

Poté můžeme přidat požadovaný čisticí kód:

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

Tento kód jde těsně nad bod, ve kterém přidáme trim prvky do cliplistu xml.

V tomto okamžiku můžeme spustit a upravit náš pracovní postup, stejně jako chceme, zatímco změny jsou použity vždy čas.    

### <a name="adding-a-clippingenabled-convenience-property"></a><a id="frame_based_trim_clippingenabled_prop"></a>Přidání vlastnosti pohodlí ClippingEnabled
Jak možná ne vždy chcete, aby se ořezávání stalo, dokončeme náš pracovní postup přidáním vhodnélogické vlajky, která označuje, zda chceme povolit oříznutí / oříznutí.

Stejně jako dříve publikujte novou vlastnost do kořenového adresáře našeho pracovního postupu s názvem "ClippingEnabled" typu "BOOLEAN".

![Publikována vlastnost pro povolení oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publikována vlastnost pro povolení oříznutí*

S níže jednoduchou klauzulí stráž, můžeme zkontrolovat, zda je nutné ořezávání a rozhodnout, zda náš seznam klipů jako takový musí být upraven, nebo ne.

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
[Zavedení kódování Premium ve službě Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Jak používat kódování Premium ve službě Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Kódování obsahu na vyžádání pomocí služby Azure Media Service](media-services-encode-asset.md#media-encoder-premium-workflow)

[Formáty a kodeky kodéru Premium pro média](media-services-premium-workflow-encoder-formats.md)

[Ukázkové soubory pracovního postupu](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Nástroj Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
