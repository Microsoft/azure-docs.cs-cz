---
title: Pokročilé kurzy pracovní postup kodéru Media Encoder Premium
description: Tento dokument obsahuje návody, které ukazují, jak provádět pokročilé úlohy s pracovním postupům kodéru Media Encoder Premium a také jak vytvářet komplexní pracovní postupy pomocí návrháře postupu provádění.
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
ms.date: 10/30/2018
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: 15393f8e10fcda99820d0279d46edf88c7781ff8
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811960"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Pokročilé kurzy pracovní postup kodéru Media Encoder Premium
## <a name="overview"></a>Přehled
Tento dokument obsahuje návody, které ukazují, jak přizpůsobit pracovní postupy s **návrháře postupu provádění**. Můžete najít soubory samotný pracovní postup [tady](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>TOC
Jsou pokryta následující témata:

* [Kódování MXF do s jednou přenosovou rychlostí MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Spouští se nový pracovní postup](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Pomocí vstupního souboru média](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Kontrola streamů médií](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Přidání videa kodéru pro. Generování souboru MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Kódování zvukový datový proud](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Datové proudy multiplexní zvuku a videa do kontejneru MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Probíhá zápis souboru MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Vytvoření Assetu Media Services z výstupního souboru](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Test místně dokončení pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Kódování MXF do souboru soubory MP4 rychlostmi – dynamické balení povoleno](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Přidání jednoho nebo více dalších MP4 výstupy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Konfigurace názvů výstupních souborů](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Přidání samostatné zvukové stopy](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Přidává se soubor SMIL "ISM"](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Kódování MXF do souboru MP4 – vylepšené podrobného plánu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * [Přehled pracovního postupu k vylepšení](#workflow-overview-to-enhance)
  * [Zásady vytváření názvů souborů](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Vlastnosti publikování součástí do kořenového pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Vygenerování výstupního souboru, který názvy využívají hodnoty publikovaných vlastností](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Přidání miniatury do souboru výstupu MP4](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * [Přehled pracovního postupu pro přidání miniatury do](#workflow-overview-to-add-thumbnails-to)
  * [Přidání kódování JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Řešení převodu barevný prostor](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Vytváření miniatur](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Zjištění chyb v pracovním postupu](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Dokončení pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Podle času oříznutí výstupu souboru MP4](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Přehled pracovního postupu můžete začít přidávat k oříznutí](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Pomocí Stream oříznutí](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Dokončení pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Představujeme skriptované komponenty](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Skriptování v rámci pracovního postupu: hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Oříznutí založených na snímcích výstupu souboru MP4](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Přehled podrobného plánu můžete začít přidávat k oříznutí](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Pomocí seznamu klip XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Úprava seznamu klip ze součásti se skripty](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Přidání vlastnosti usnadnění ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Kódování MXF do s jednou přenosovou rychlostí MP4
Tato část ukazuje, jak vytvořit s jednou přenosovou rychlostí. Soubor MP4 s AAC-HE zakódován zvuk ze. MXF vstupního souboru.

### <a id="MXF_to_MP4_start_new"></a>Spouští se nový pracovní postup
Otevřete návrháře pracovních postupů a vyberte soubor > Nový pracovní prostor > překódování podrobného plánu

Nový pracovní postup ukazuje tři prvky:

* Primárním zdrojovém souboru
* Seznam klip XML
* Výstupní soubor nebo prostředek  

![Nový pracovní postup kódování](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nový pracovní postup kódování*

### <a id="MXF_to_MP4_with_file_input"></a>Pomocí vstupního souboru média
Aby bylo možné přijímat vstupní mediální soubor, se spustí s přidáváním komponentu vstupní soubor média. Chcete-li přidat součásti do pracovního postupu, podívejte se do vyhledávacího pole úložiště a přetáhněte na požadovanou položku na podokno návrháře. Opakování akce pro vstupní soubor média a připojit komponenta primární zdrojový soubor pro název souboru vstupní pin z vstupní soubor média.

![Připojených mediálních souborů vstup](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Připojených mediálních souborů vstup*

Na začátku Identifikujte příslušné ukázkového souboru pro použití při navrhování vlastních pracovních postupů. Uděláte to tak, klikněte na podokno návrháře na pozadí a vyhledejte vlastnost primární zdrojový soubor v podokně napravo vlastnost. Klikněte na ikonu složky a vyberte požadovaný soubor pro testování pracovního postupu. Součást Media vstupního souboru zkontroluje soubor a naplní její výstupní spojky tak, aby odrážely podrobnosti ukázkový soubor, který ho prozkoumat.

![Vstupní soubor mají údaj vyplněný média](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Vstupní soubor mají údaj vyplněný média*

Teď, když se vyplní vstupu, dalším krokem je nastavení výstup nastavení kódování. Podobným způsobem primární zdrojový soubor byl nakonfigurován, teď nakonfigurovat vlastnost výstupní složky proměnné pod ní.

![Nakonfigurovaný vstupní a výstupní vlastnosti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Nakonfigurovaný vstupní a výstupní vlastnosti*

### <a id="MXF_to_MP4_streams"></a>Kontrola streamů médií
Často je žádoucí vědět, jak datového proudu vypadá jako procházejících pracovního postupu. Ke kontrole datový proud v libovolném bodě v pracovním postupu, stačí klikněte výstupu nebo vstupní PIN kód v některé z těchto komponent. V takovém případě zkuste kliknete na výstupní spojky nekomprimované Video ze vstupního souboru média. Dialogové okno otevře, který umožňuje zkontrolovat odchozí videa.

![Kontrola výstupní spojky nekomprimované Video](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Kontrola výstupní spojky nekomprimované Video*

V tomto případě zobrazuje, že video obsahuje 1920 × 1080 vstupu na 24 snímků za sekundu ve 4:2:2 vzorkování pro video téměř 2 minut.

### <a id="MXF_to_MP4_file_generation"></a>Přidání videa kodéru pro. Generování souboru MP4
Teď nekomprimované Video a více nekomprimované zvukový výstup, které jsou k dispozici pro PIN kódy pomocí na vstupní soubor média. Aby bylo možné zakódovat příchozích video, musí být přidán do pracovního postupu – v tomto případě pro generování komponentu kódování. Soubory MP4.

Ke kódování videa datového proudu k H.264, přidáte součást grafickou kodér AVC na plochu návrháře. Tato součást přijímá proud videa uncompress jako vstup a poskytuje AVC komprimovaný datový proud videa na svůj kód pin výstup.

![Nepřipojené AVC kodér](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Nepřipojené AVC kodér*

Jeho vlastnosti určují, jak kódování právě probíhá. Pojďme Podíváme se na některé další důležitá nastavení:

* Výstupní šířky a výšky výstup: Určuje rozlišení kódované video. 640 × 360 v tomto případě je vhodné nastavení.
* Snímková frekvence: Pokud je nastavena na průchod právě přijme Snímková frekvence zdroje, je možné to ale přepsat. Takový převod snímkovou není pohybu kompenzována.
* Profil a úroveň: Určuje profil AVC a úroveň. Pohodlná získat další informace o různých úrovních a profily, klikněte na ikonu otazníku v komponenta kodéru AVC videa a na stránce nápovědy se zobrazí další podrobnosti o každé z úrovní. V tomto příkladu použijte profil hlavní na úrovni 3.2 (výchozí).
* Režim ovládacího prvku a s přenosovou rychlostí (kb/s): v tomto scénáři optimalizované pro konstantní bitrate (CBR) výstup na 1 200 kb/s
* Formát videa: poskytuje informace o VUI (informace o použitelnosti Video), který získá zápisu do datového proudu H.264 (informace na straně, který může být používán dekodér vylepšit zobrazení, ale není nutná správně dekódovat):
* NTSC (typická pro USA a Japonsku, použití 30 snímků za sekundu)
* PAL (typická pro Evropu, pomocí 25 snímků za sekundu)
* GOP Režim velikosti: nastavení pevné velikosti GOP pro naše účely s intervalem klíče 2 sekundy s GOPs uzavřen. Nastavení 2 sekundy zajistí, že poskytuje kompatibilitu s dynamickým vytvářením paketů Azure Media Services.

Ke kanálu kodér AVC, připojení k vstupní kód pin nekomprimované videa z kodéru AVC výstupní spojky nekomprimované Video ze vstupního souboru média součásti.

![Připojené AVC kodér](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Kodér připojených AVC Main*

### <a id="MXF_to_MP4_audio"></a>Kódování zvukový datový proud
V tomto okamžiku původního nekomprimované zvukový datový proud stále potřebuje ke komprimaci. Komprese zvukový datový proud přidejte komponentu AAC kodér (Dolby) do pracovního postupu.

![Nepřipojené AVC kodér](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Kodér nepřipojené AAC*

Teď je k dispozici nekompatibility: při více než pravděpodobné, že vstupní soubor média bude mít dva různé nekomprimované audiostreamy k dispozici je pouze jeden nekomprimované zvuku vstupní PIN kód z kodéru AAC: jeden pro levého kanálu zvuku a jeden pro vpravo. (V případě, že pracujete s prostorový zvuk, který je šest kanálů.) Proto není možné k přímému připojení zvuk ze zdrojového média vstupního souboru do kodér AAC zvuku. Komponenta AAC očekává, že takzvané "prokládané" zvukový stream: jeden datový proud, který má levé straně a správným kanálům prokládané mezi sebou. Jednou víme z našich zdrojového souboru média, jsou zvukové stopy na jaké umístění ve zdroji vygenerujeme takové prokládané zvukový datový proud s pozicemi správně přiřazená mluvčího pro vlevo a vpravo.

Nejdřív jednu chce generovat prokládané datový proud z požadovaných zdrojových zvukové kanály. Komponenta zvukový Stream Interleaver zpracovává to pro nás. Přidat do pracovního postupu a připojit zvuku výstupů ze vstupního souboru médií do něj.

![Připojené Interleaver zvukový Stream](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Připojené Interleaver zvukový Stream*

Když teď máme prokládané zvukový datový proud, jsme stále neměli zadejte, kam chcete přiřadit doleva nebo doprava mluvčího pozic. Aby bylo možné tuto verzi uveďte, můžeme využít Pověřující osoby pozice mluvčího.

![Přidání Přidělovač pozice mluvčího](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Přidání Přidělovač pozice mluvčího*

Nakonfigurování Pověřující osoby mluvčího pozici pro použití s stereo vstupního datového proudu přes filtr přednastavení kodér "Vlastní" a předvolby kanálu, které se nazývá "2.0 (L, R)." (Toto přiřadí pozice levého mluvčího do kanálu 1 a pozice správné mluvčího do kanálu 2.)

Propojte výstup modulu Pověřující osoby pozice mluvčího se vstupem AAC kodér. Sdělte AAC kodéru pro práci s "2.0 (L, R)" přednastavení kanálu, aby věděl, řešit stereo zvuk jako vstup.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Datové proudy multiplexní zvuku a videa do kontejneru MP4
Vzhledem k naší AVC kódovaného datový proud videa a naše AAC kódovaný zvukový datový proud, můžeme zaznamenávat do. MP4 kontejneru. Proces kombinování různých datových proudů do jednoho objektu se nazývá "multiplexing" (nebo "muxing"). V tomto případě jsme už prokládání zvuk a video datových proudů v jediném souvislé. MP4 balíčku. Komponenta, která koordinuje pro. MP4 kontejner má název multiplexor ISO MPEG-4. Přidejte jej na plochu návrháře a připojte se k jeho vstupů kodéru AVC videa a AAC kodér.

![Připojené MPEG4 multiplexor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Připojené MPEG4 multiplexor*

### <a id="MXF_to_MP4_writing_mp4"></a>Probíhá zápis souboru MP4
Při zápisu výstupního souboru, se používá výstup souboru součásti. Můžeme připojit to k výstupu multiplexor ISO MPEG-4 tak, aby získá jeho výstup zapsán na disk. K tomuto účelu připojte výstupní spojky kontejneru (MPEG-4) na vstupní kód pin zápisu výstupního souboru.

![Připojené výstupního souboru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Připojené výstupního souboru*

Název souboru, který se používá, je určen vlastností souboru. Vlastnosti mohou být pevně dané hodnotě, nejpravděpodobnější chce nastavit prostřednictvím výrazu.

Pracovní postup automaticky určit výstup souboru vlastnost name z výrazu, klikněte na tlačítko vedle názvu souboru (vedle ikona složky). V rozevírací nabídce vyberte "Výraz". Otevře editor výrazů. Nejprve zrušte obsah editoru.

![Prázdný Editor výrazů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Prázdný Editor výrazů*

Editor výrazů umožňuje zadat jakékoli hodnota literálu smíšeného s jednou nebo více proměnných. Proměnné začínají znak dolaru. Dosažení klíči $ editor hlásí rozevíracího seznamu s možností volby dostupných proměnných. V našem případě použijeme kombinaci proměnná výstupního adresáře a základní vstupní soubor název proměnné:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Vyplněné mimo Editor výrazů](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Vyplněné mimo Editor výrazů*

> [!NOTE]
> Chcete-li zobrazit výstupní soubor kódovací úlohy v Azure, je nutné zadat hodnotu v editoru výrazů.
>
>

Po potvrzení výraz stisknutím ok v okně Vlastnosti zobrazí náhled, jakou hodnotu řeší vlastností souboru v daném okamžiku.

![Soubor výraz se přeloží výstupní adresář](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Soubor výraz se přeloží výstupní adresář*

### <a id="MXF_to_MP4_asset_from_output"></a>Vytvoření Assetu Media Services z výstupního souboru
Když jsme napsali výstupní soubor MP4, stále potřebujeme k označení, že tento soubor patří na výstupní asset, který služba media services generuje důsledku spuštění tohoto pracovního postupu. Za tímto účelem se používá uzel výstupního souboru nebo prostředku na plátně pracovního postupu. Všechny příchozí soubory do tohoto uzlu zařazení výsledný prostředku Azure Media Services.

Komponenta výstupního souboru se připojte k součást výstupního souboru nebo prostředku, kterou dokončení pracovního postupu.

![Dokončení pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Dokončení pracovního postupu*

### <a id="MXF_to_MP4_test"></a>Test místně dokončení pracovního postupu
K testování pracovního postupu místně, klikněte na tlačítko Přehrát na panelu nástrojů v horní části. Po dokončení provádění pracovního postupu zkontrolujte, zda výstup generovaný do nakonfigurovaného výstupní složky. Zobrazí se vám dokončení MP4 výstupní soubor, který byl zakódován z MXF vstupní zdrojový soubor.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Kódování MXF do MP4 - souboru povoleny dynamické balení
Tento návod vytvoří sadu soubory MP4 s více přenosovými rychlostmi pomocí AAC kódovaný zvuku z jedné. MXF vstupního souboru.

Pokud výstupní asset s více přenosovými rychlostmi požadované pro použití v kombinaci s dynamickým vytvářením paketů funkcí, které nabízí služba Azure Media Services, více souborů MP4 zarovnaný GOP každé jiné s přenosovou rychlostí a řešení bude nutné vygenerovat. Uděláte to tak, [kódování MXF do s jednou přenosovou rychlostí MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) názorný postup obsahuje nám s dobrým výchozím bodem.

![Spuštění pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Spuštění pracovního postupu*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Přidání jednoho nebo více dalších MP4 výstupy
Každý soubor MP4 v našich výsledný prostředku Azure Media Services podporuje různé přenosové rychlosti a řešení. Pojďme přidat jeden nebo více výstupních souborů MP4 do pracovního postupu.

Pokud chcete mít jistotu, že máme všechny naše videa kodérů vytvořené se stejným nastavením, je nejvhodnější pro už existující kodér videa AVC duplicitní a nakonfigurovat jiné kombinace řešení a s přenosovou rychlostí (přidejme jeden 960 × 540 na 25 snímků za sekundu při 2,5 MB/s ). Duplikování existujícího kodér, zkopírujte jej vložte na návrhové ploše.

Připojte výstupní spojky nekomprimované Video ze vstupního souboru médií do naší nové komponenty AVC.

![Druhý AVC kodér připojení](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Druhý AVC kodér připojení*

Nyní přizpůsobit konfiguraci pro náš nový kodér AVC výstup 960 × 540 při 2,5 MB/s. (Použít jeho vlastnosti "výstupní šířku", "Výstup výšku" a "S přenosovou rychlostí (kb/s)" pro tuto.)

Zadaný chceme použít výsledný asset spolu s dynamickým vytvářením paketů Azure Media Services, musí koncový bod streamování musí být schopný vytvářet z těchto souborů MP4 HLS nebo fragmentovaný soubor MP4/DASH fragmenty, které přesně odpovídají dalším způsobem, který klienti, kteří jsou přepínání mezi různých přenosových rychlostí získat jednotné průběžné video a audiostreamů hladký. Chcete-li docílit, musíme zkontrolujte, že ve vlastnosti obou AVC kodérů GOP ("skupina obrázky") je velikost pro oba soubory MP4 nastavená na 2 sekundy, které lze provést:

* nastavení režimu velikost GOP GOP pevnou velikost a
* Interval snímku klíč na dvou sekund.
* také nastavit ovládací prvek IDR GOP uzavřeno GOP zajistit všechny GOPs jsou připraveni na své vlastní bez závislostí

Srozumitelnější tento pracovní postup, přejmenujte je tak první AVC kodéru pro "kodéru AVC videa 640 x 360 1200 kb/s" a druhá kodér AVC "kodéru AVC videa 960 × 540 2 500 kb/s."

Nyní přidejte druhý multiplexor ISO MPEG-4 a druhý výstupního souboru. Připojte se k nový kodér AVC multiplexor a ujistěte se, že jeho výstup se přesměruje do výstupního souboru. Připojte také AAC zvuku kodér výstup do nového multiplexor input. Výstupní soubor zase pak dá připojit k uzlu výstupní soubor nebo prostředek se přidá do Assetu Media Services, která bude vytvořena.

![Druhý multiplexor a výstup souboru připojení](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Druhý multiplexor a výstup souboru připojení*

Z důvodu kompatibility s dynamickým vytvářením paketů Azure Media Services nakonfigurujte multiplexor na blok dat režimu GOP počet a doby trvání a GOPs za blok dat, nastavte na 1. (To by měl být výchozí.)

![Režimy multiplexor bloků dat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Režimy multiplexor bloků dat*

Poznámka: můžete chtít tento postup opakujte pro další s přenosovou rychlostí a vyřešení kombinace, které chcete přidali na výstupní asset.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfigurace názvů výstupních souborů
Máme více než jeden soubor přidán na výstupní asset. Získáte třeba Ujistěte se, že se liší od sebe navzájem filenames pro každou výstupní soubory a možná i použití konvence pojmenování souboru tak bude zřejmé z názvu souboru, už pracujete s.

Názvy výstupního souboru se dá řídit přes výrazy v návrháři. Otevřete podokno vlastností pro jednu součást výstupního souboru a otevřete editor výrazů vlastnosti souboru. Naše první výstupní soubor byl nakonfigurován prostřednictvím následující výraz (najdete v kurzu pro přechod z [MXF k s jednou přenosovou rychlostí výstupu MP4 se](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

To znamená, že naše název souboru je určeno dvě proměnné: výstupní adresář k zápisu a základní název zdrojového souboru. První je vystavena jako vlastnost v kořenovém adresáři pracovního postupu a je určen podle příchozího souboru. Výstupní adresář je používat pro místní testování. Tato vlastnost přepíše modul workflow pracovního postupu je spuštěn metodou založené na cloudu mediálním procesorem v Azure Media Services.
Výstup konzistentní pojmenování poskytnout i naše výstupní soubory, změňte první výraz, který se pojmenování souborů:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

a druhou pro:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Spuštění zprostředkující testovacího běhu, aby Ujistěte se, že oba MP4 výstupní soubory jsou generovány správně.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Přidání samostatné zvukové stopy
Jak uvidíme dále když se vygeneruje soubor .ism pro naše výstupní soubory MP4, jsme také vyžaduje pouze zvukový soubor MP4 jako zvukové stopy pro naše adaptivního streamování. K vytvoření tohoto souboru, přidejte další multiplexor pracovního postupu (ISO-MPEG-4 multiplexor) a připojte výstupní spojky kodér AAC s jeho vstupní kód pin pro sledování 1.

![Přidat zvukový multiplexor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Přidat zvukový multiplexor*

Vytvořte třetí součást výstupního souboru, výstupní výstupní datový proud z multiplexor a konfigurace souboru pojmenování jako výraz:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Zvukový multiplexor vytvoření výstupního souboru](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Zvukový multiplexor vytvoření výstupního souboru*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Přidává. Správci služeb sítě Internet SMIL souboru
Pro dynamické balení pro práci v kombinaci s souborů MP4 (a pouze se zvukem MP4) v našich asset Media Services, musíme také soubor manifestu (nazývaný také soubor "SMIL": Synchronizovat multimediální integrace jazyka). Tento soubor Určuje pro Azure Media Services jsou dostupné pro dynamické balení a které z těch, které je potřeba zvážit zvukový datový proud soubory MP4. Typické souboru manifestu sady MP4 s jeden zvukový datový proud vypadá takto:

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

Obsahuje soubor .ism uvnitř příkazu switch, odkaz na každé jednotlivé soubory video MP4 a navíc tyto odkazy zvukový soubor také (nejméně) MP4, které obsahuje pouze zvuku.

Generuje se soubor manifestu pro naše sada MP4. lze provést prostřednictvím komponenty s názvem "Zapisovač AMS Manifest." Jeho použití, přetáhněte na plochu a připojte "Napsat dokončených" výstupní spojky ze tří komponent výstupního souboru pro vstup manifestu zapisovače AMS. Nezapomeňte potom propojte výstup modulu zapisovač AMS Manifest do výstupního souboru nebo prostředku.

Stejně jako u našich dalších souboru výstupu komponentách, nakonfigurujte název výstupního soubor .ism s výrazem:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Dokončené pracovní postup vypadá jako následující:

![Dokončení MXF do souboru MP4 pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Dokončení MXF do souboru MP4 pracovního postupu*

## <a id="MXF_to__multibitrate_MP4"></a>Kódování MXF do souboru MP4 – vylepšené podrobného plánu
V [předchozím návodu pracovního postupu](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) zaznamenali jsme, jak jeden vstupní asset MXF, mohou být převedeny na výstupní asset s soubory MP4 s více přenosovými rychlostmi, pouze zvukový soubor MP4 a soubor manifestu pro použití ve spojení s Azure Media Dynamické balení služby.

Tento návod ukazuje, jak některé aspekty jdou vylepšit a provedli pohodlnější.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Přehled pracovního postupu k vylepšení
![Vylepšení pracovního postupu souboru MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Vylepšení pracovního postupu souboru MP4*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>Zásady vytváření názvů souborů
V předchozím postupu jsme zadali jednoduchý výraz jako základ pro vytváření názvů výstupních souborů. I když máme několik duplicitních: všech součástí jednotlivých výstupní soubor zadán tento výraz.

Například naši součást výstupní soubor pro první soubor videa se nakonfigurují tento výraz:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Zatímco u druhého výstupu videa, máme výraz jako:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Nebylo by čistější, méně chyby náchylné k chybám a pohodlnější, pokud jsme může odebrat některé tyto duplikace a ujistěte se, co konfigurovatelnější místo? Naštěstí můžeme: výraz funkce návrháře v kombinaci s možnost vytvářet vlastní vlastnosti v kořenovém adresáři naše pracovního postupu se nabízejí další úroveň pohodlí.

Předpokládejme, že nám budete jednotka název souboru konfigurace z přenosových rychlostí jednotlivých souborů MP4. Tyto přenosových rychlostí, které budete usilujeme o konfiguraci v jednom centrálním místě (v kořenovém adresáři naše graf), kde k nim bude přistupovat ke konfiguraci a generování názvu souboru jednotky. K tomuto účelu začneme publikováním s přenosovou rychlostí vlastnost z obou AVC kodérů do kořenového adresáře pracovní postup, takže bude přístupný z obou kořene stejně jako z AVC kodérů. (I když se zobrazí ve dvou různých míst, je pouze jedna nadřazená hodnota.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Vlastnosti publikování součástí do kořenového pracovního postupu
Otevřete první kodér AVC, přejděte na vlastnost s přenosovou rychlostí (kb/s) a z rozevíracího seznamu zvolte možnost publikovat.

![Publikování s přenosovou rychlostí vlastnost](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publikování s přenosovou rychlostí vlastnost*

Konfigurace dialogového okna Publikovat a publikovat do kořenového adresáře naše grafu pracovního postupu pomocí publikovaných název "video1bitrate" a "Video 1 s přenosovou rychlostí s" čitelné zobrazovaný název. Nakonfigurovat vlastní název skupiny nazývá "Streamování přenosových rychlostí" kliknu na publikovat.

![Publikování s přenosovou rychlostí vlastnost](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Dialogové okno Vlastnosti s přenosovou rychlostí*

Opakujte stejný pro s přenosovou rychlostí vlastnost Druhý AVC kodér a pojmenujte ho "video2bitrate" zobrazovaný název s "Videa 2 s přenosovou rychlostí", ve stejné skupině vlastní "Streamování přenosových rychlostí".

Pokud jsme teď umožní zkontrolovat vlastnosti kořenového pracovního postupu, uvidíme náš vlastní skupiny dvě vlastnosti publikovaných zobrazí. Obě jsou odráží hodnoty jejich odpovídajících kodér bitrate AVC.

![Vlastnosti publikovaných s přenosovou rychlostí v kořenovém adresáři pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Pokaždé, když chceme přistupovat k těmto vlastnostem z kódu nebo z výrazu, jsme to lze provést následujícím způsobem:

* z vloženého kódu z komponenty přímo pod kořenovým adresářem: node.getPropertyAsString('.. / video1bitrate ", null)
* ve výrazu: ${ROOT_video1bitrate}

Ukončeme publikováním naše zvukové stopy s přenosovou rychlostí v něm i skupiny "Streamování přenosových rychlostí". Ve vlastnostech kodér AAC vyhledejte nastavení s přenosovou rychlostí a vyberte z rozevíracího seznamu vedle něj Publish. Publikovat do kořenového adresáře grafu s názvem "audio1bitrate" a zobrazovaný název "Zvuk 1 s přenosovou rychlostí s" v rámci naší vlastní skupiny "Streamování přenosových rychlostí".

![Dialogové okno pro zvuk s přenosovou rychlostí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Dialogové okno pro zvuk s přenosovou rychlostí*

![Výsledný props video a audiostreamů v kořenovém adresáři](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Výsledný props video a audiostreamů v kořenovém adresáři*

Změna některý z těchto tří hodnot také změní konfiguraci a změní hodnoty do příslušných komponent, které jsou propojeny s (a je-li publikovat).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Vygenerování výstupního souboru, který názvy využívají hodnoty publikovaných vlastností
Místo hardcoding naše názvů generovaného souboru jsme nyní můžete změnit náš výraz názvu souboru na jednotlivých součástí výstupu souboru přináší setrvávání u vlastnosti s přenosovou rychlostí, které jsme zveřejnili na kořenovém adresáři grafu. Od naší první výstupní soubor, najděte vlastnost souboru a upravit výraz takto:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Různé parametry v tomto výrazu jde přistupovat a zadali tím, že kliknete na klávesnici, když v okně výrazu znak dolaru. Jeden z dostupných parametrů je naše video1bitrate vlastnost, kterou jsme dříve publikovali.

![Přístup k parametrům ve výrazu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Přístup k parametrům ve výrazu*

Proveďte totéž pro výstupní soubor pro naše druhý video:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

a pro výstupní soubor určený jen pro zvuk:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Pokud nyní Změníme přenosové rychlosti pro některý ze souborů video nebo zvuk, příslušné kodér bude potřeba překonfigurovat tak, a konvence název souboru s přenosovou rychlostí podle budou zachované vše probíhá automaticky.

## <a id="thumbnails_to__multibitrate_MP4"></a>Přidání miniatury do souboru výstupu MP4
Spouští se z pracovního postupu, který generuje [souboru MP4 výstup z MXF vstupní](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), bude nyní se díváme na přidání miniatury do výstupu.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Přehled pracovního postupu pro přidání miniatury do
![Souboru MP4 pracovní postup spustit z](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Souboru MP4 pracovní postup spustit z*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Přidání kódování JPG
Srdce naše generování miniatur, bude komponenta kodéru JPG, moct výstupní soubory JPG.

![JPG Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Kodér JPG*

Nemůžeme připojit ale přímo naší stream nekomprimované Video ze vstupního souboru médií do kodér JPG. Místo toho se očekává, že předává jednotlivé snímky. To můžeme udělat prostřednictvím brány rámce Video součásti.

![Připojení brány rámce kodér JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Připojení brány rámce kodér JPG*

Brána rámce každých tolik sekund nebo snímků umožňuje snímků předat. Časový interval a časový posun, pomocí které to se stane, je možné konfigurovat ve vlastnostech.

![Vlastnosti brány rámce videa](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Vlastnosti brány rámce videa*

Pojďme vytvořit miniaturu každou minutu nastavením je režim na čas (v sekundách) a Interval 60.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Řešení převodu barevný prostor
Zatímco se jeví logické, že se že teď dá připojit oba PIN kódy nekomprimované Video brány rámce a vstupního souboru média, dostali bychom upozornění, pokud by to uděláme.

![Vstupní barva místo chyby](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Vstupní barva místo chyby*

Totiž způsob, jakým barvou, který je reprezentován informace v našich původní nezpracovaná nekomprimované datový proud videa, pocházející z našich MXF není totéž co JPG Kodér očekává. Přesněji řečeno takzvané "barevný prostor" "RGB" nebo "Ve stupních šedi" očekává se v toku. To znamená, že příchozí videa datový proud videa rámce brány musí mít převod použije první týkající se jeho barevný prostor.

Přetáhněte pracovní postup místo převaděč barev – procesor Intel a jejím připojení k naší brány rámce.

![Připojení převaděči místo barva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Připojení převaděči místo barva*

V okně Vlastnosti vyberte BGR 24 položku ze seznamu nastavení.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Vytváření miniatur
Liší od našich video MP4, komponenta kodéru JPG vyprodukuje více než jeden soubor. Aby bylo možné to vyřešit, je možné součást zapisovače souboru JPG hledání scény: přijímá příchozí miniatury JPG a zapisuje je rozložení, každý název souboru se příponou podle jiné číslo. (Počet obvykle indikuje počet sekund/jednotek v datovém proudu, který na miniaturu vykreslení z.)

![Úvod do zapisovače souboru scény hledání JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Úvod do zapisovače souboru scény hledání JPG*

Nakonfigurovat vlastnost Cesta k výstupní složce s výrazem: ${ROOT_outputWriteDirectory}

a vlastnost Filename předpona s:

    ${ROOT_sourceFileBaseName}_thumb_

Předpona, která určuje, jak jsou jmenován miniatur soubory. Tyto jsou doplněny číslo určující pozici v datovém proudu.

![Vlastnosti zapisovače vyhledávání JPG souboru scény](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Vlastnosti zapisovače vyhledávání JPG souboru scény*

Zapisovače souboru scény hledání JPG připojte k uzlu výstupního souboru nebo prostředku.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Zjištění chyb v pracovním postupu
Vstup převaděč barev místa se připojte k nezpracované nekomprimované výstup videa. Teď proveďte místní testovací běh pracovního postupu. Je velmi pravděpodobné pracovního postupu náhle zastaví provádění a označují se červené ohraničení v komponentě došlo k chybě:

![Chyba místo převaděč barev](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Chyba místo převaděč barev*

Klikněte na malou červenou ikonu "E" v horním pravém rohu převaděč barev místo komponenty si můžete zobrazit, jaký je důvod kódování pokus o se nezdařilo.

![Dialogové okno chyby místo převaděč barev](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Dialogové okno chyby místo převaděč barev*

Ukázalo se, jak je vidět, že příchozí barevný prostor úrovně standard pro převaděč barev prostor musí být rec601 pro naše požadovaný převod YUV RGB. Naše stream zjevně neukazuje, jeho rec601. (Dop. 601 je standard pro kódování prokládaných analogové videa signály v digitální podobě videa. Určuje na aktivní oblast pokrývající 720 světelnost a 360 chrominance vzorků na řádek. Barevné kódování systému se označuje jako YCbCr 4:2:2.)

Chcete-li to vyřešit, budete Udáváme o metadatech naše datového proudu, který jsme pracujete s obsahem rec601. K tomu použijeme součásti Updater typ dat videa, která dáme mezi naše nezpracovaná zdrojová a složku barvy místo převodu. Tento aktualizační typ dat umožňuje ruční aktualizace určitá videa data vlastnosti typu. Nakonfigurujte k označení místa barva standardní z "Dop 601". To způsobí, že Updater typ dat videa k označení datovým proudem s barevný prostor "Dop 601". Pokud se žádná barevný prostor dosud nebylo definováno. (Ho nepřepíšete všechna existující metadata, pokud došlo k zaškrtnutí políčka přepsání.)

![Aktualizuje se barva prostoru Standard na Updater typ dat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Aktualizuje se barva prostoru Standard na Updater typ dat*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Dokončení pracovního postupu
Teď, když pracovní postup je dokončené, proveďte další testovací běh zobrazíte předá.

![Dokončení pracovního postupu pro výstup mp4 s více s miniaturami](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Dokončení pracovního postupu pro výstup mp4 s více s miniaturami*

## <a id="time_based_trim"></a>Podle času oříznutí výstupu souboru MP4
Spouští se z pracovního postupu, který generuje [souboru MP4 výstup z MXF vstupní](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), jsme se teď se podíváme do ořezávání zdrojového videa podle časová razítka.

### <a id="time_based_trim_start"></a>Přehled pracovního postupu můžete začít přidávat k oříznutí
![Spuštění pracovního postupu přidáte k oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Spuštění pracovního postupu přidáte k oříznutí*

### <a id="time_based_trim_use_stream_trimmer"></a>Pomocí Stream oříznutí
Komponenta oříznutí Stream umožňuje oříznout na začátek a konec vstupního datového proudu base na časování informace (sekund, minut,...). Oříznutí nepodporuje založených na snímcích oříznutí.

![Stream oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Stream oříznutí*

Místo propojení AVC kodérů a pozice mluvčího Pověřující osoby vstupního souboru média přímo, dáme mezi ty oříznutí datového proudu. (Jeden pro video signál a jeden pro prokládané zvukový signál.)

![Vložit mezi Stream oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Vložit mezi Stream oříznutí*

Nakonfigurujme oříznutí tak, že jsme bude pouze zpracovávat video a zvuk mezi 15 sekund a 60 sekund ve videu.

Přejděte do vlastností oříznutí Stream Video a nakonfigurovat i čas spuštění (15 s) a koncový čas (60 s) vlastnosti. Pokud chcete mít jistotu, že jak náš audio a video oříznutí vždy konfigurují stejné počáteční a koncová hodnota, budeme publikovat do kořenového pracovního postupu.

![Publikování – vlastnost času začátku ze Stream oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publikování – vlastnost času začátku ze Stream oříznutí*

![Dialogové okno Vlastnosti pro publikování pro čas spuštění](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Dialogové okno Vlastnosti pro publikování pro čas spuštění*

![Publikování dialogové okno vlastností pro koncový čas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publikování dialogové okno vlastností pro koncový čas*

Pokud jsme nyní vyhledejte kořenový naše pracovního postupu, jsou obě vlastnosti elegantně zobrazené a konfigurovat z něj.

![Publikované vlastnosti, které jsou k dispozici v kořenovém adresáři](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Publikované vlastnosti, které jsou k dispozici v kořenovém adresáři*

Nyní otevřete vlastnosti oříznutí ze zvukových oříznutí a nakonfigurovat výraz, který odkazuje na publikovaná vlastnosti v kořenovém adresáři pracovní postup s počátečním a koncovým časem.

Zvuk ořezávání počáteční čas:

    ${ROOT_TrimmingStartTime}

a pro jeho koncový čas:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Dokončení pracovního postupu
![Dokončení pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Dokončení pracovního postupu*

## <a id="scripting"></a>Představujeme skriptované komponenty
Skriptované komponenty můžete spustit skripty libovolné během fáze spuštění našich pracovního postupu. Existují čtyři různé skripty, které mohou být provedeny, každý s konkrétními vlastnostmi a jejich vlastní místo v životním cyklu pracovního postupu:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

Dokumentace ke službě součásti skripty přejde podrobněji pro každý z výše uvedených. V [části](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), **realizeScript** skriptovací komponenty se používá ke konstrukci cliplist xml v reálném čase při spuštění pracovního postupu. Tento skript je volána v průběhu instalace součástí, které dojde jenom jednou v jeho životní cyklus.

### <a id="scripting_hello_world"></a>Skriptování v rámci pracovního postupu: hello world
Přetáhněte komponentu skripty na návrhové ploše a přejmenujte jej (například "SetClipListXML").

![Přidání skriptované komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Přidání skriptované komponenty*

Pokud si prohlédnout vlastnosti součásti skripty, čtyři typy další skript bude uvedeno, každý konfigurovat, a další skript.

![Vlastnosti skriptované komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Vlastnosti skriptované komponenty*

Zrušte processInputScript a otevřete editor pro realizeScript. Nyní jsme máte nastavené a připravené ke spuštění skriptů.

Skripty jsou napsané v technologii Groovy, dynamicky kompilovaných skriptovací jazyk pro platformu Java, který bude mít kompatibilitu s Java. Většina kódu v jazyce Java ve skutečnosti, je platný Groovy kód.

V rámci naší realizeScript napíšeme jednoduchý hello world groovy skriptu. V editoru zadejte následující údaje:

    node.log("hello world");

Nyní spusťte místní testovací běh. Po spuštění zkontrolujte (prostřednictvím systému kartu komponenty skripty) vlastnost protokoly.

![Výstup protokolu Hello world](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Výstup protokolu Hello world*

Objekt uzlu, který budeme volat metodu protokolu, odkazuje na naše aktuální "uzel" nebo komponenta, kterou jsme už skriptování v rámci. Všechny komponenty jako taková nemá schopnost data protokolování výstupu prostřednictvím karty systému. V takovém případě odešleme výstup řetězcový literál "hello world." Důležité pochopit, zde je, že to může být být neocenitelnou ladicí nástroje vám poskytnou přehled na co skript skutečně dělají.

Z v rámci naší skriptovací prostředí, máme i přístup k vlastnostem na ostatních součástech. Zkuste tohle:

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

Naše okno protokol zobrazuje následující:

![Výstup protokolu pro přístup k uzlu cesty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Výstup protokolu pro přístup k uzlu cesty*

## <a id="frame_based_trim"></a>Oříznutí založených na snímcích výstupu souboru MP4
Spouští se z pracovního postupu, který generuje [souboru MP4 výstup z MXF vstupní](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), jsme se teď se podíváme do ořezávání zdrojového videa na základě počtu snímků.

### <a id="frame_based_trim_start"></a>Přehled podrobného plánu můžete začít přidávat k oříznutí
![Pracovní postup můžete začít přidávat k oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Pracovní postup můžete začít přidávat k oříznutí*

### <a id="frame_based_trim_clip_list"></a>Pomocí seznamu klip XML
Ve všech předchozích kurzech pracovního postupu jsme použili komponentu vstupní soubor média jako naše videa vstupní zdroj. Pro tento konkrétní scénář, ale budeme používat klip seznamu zdrojovou součástí místo. To by neměl být preferovaný způsob práce; Po skutečný důvod k tomu použít pouze zdroj seznamu klipu (jako v následujícím případě, kdy provádíme použití možností oříznutí seznamu klip).

Přepnutí z našich vstupního souboru médií do zdrojového seznamu klip, přetáhněte komponentu klip zdroj seznamu na návrhovou plochu a PIN kód XML seznamu klip se připojit k uzlu klip seznamu XML Návrháře pracovního postupu. Tím vyplníte klip zdroj seznamu se výstupní spojky podle našich vstup videa. Nyní připojení nekomprimované Video a PIN kódů nekomprimovaný zvuk ze zdrojového seznamu klip příslušných AVC kodérů a Interleaver zvukový Stream. Teď odeberte vstupního souboru média.

![Nahradí vstupního souboru média zdrojového seznamu klipu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Nahradí vstupního souboru média zdrojového seznamu klipu*

Galerie seznamu zdrojovou součástí přijímá jako vstup "Klip seznamu XML." Při výběru zdrojového souboru pro testování s místně, tento klip seznamu xml se vyplní automaticky za vás.

![Automaticky vyplněný klip seznamu XML – vlastnost](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automaticky vyplněný klip seznamu XML – vlastnost*

Vyhledávání o něco blíž k souboru xml, to je, jak to funguje:

![Dialogové okno seznam klip upravit](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Dialogové okno seznam klip upravit*

Tato funkce xml seznamu klip ale neodráží. Jednou z možností, které máme k dispozici je přidání "Trim" element v rámci obou video a audiostreamů zdroje, například takto:

![Přidávání do seznamu klip element a uvolnění dočasné paměti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Přidávání do seznamu klip element a uvolnění dočasné paměti*

Pokud upravíte xml seznamu klip takto výše a provádět místní testovací běh, zobrazí se video správně byl oříznut mezi 10 a 20 sekund ve videu.

Rozporu s co se stane, když je ale provést místní spuštění nebude tento stejný cliplist xml mají stejný účinek při použití v pracovním postupu, na kterém běží ve službě Azure Media Services. Při spuštění kodér úrovně Premium Azure cliplist xml je generována pokaždé, když se znovu, založené na vstupní soubor, který byl zadán úlohy kódování. To znamená, že by všechny změny, které děláme xml bohužel přepsat.

Čítači cliplist xml vymazání při spuštění úlohy kódování, jsme lze obnovit v reálném čase bezprostředně po začátku pracovní postup. Je možné provést tato vlastní akce prostřednictvím co se nazývá "Skriptované komponenty." Další informace najdete v tématu [Představujeme komponentu skripty](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Přetáhněte komponentu skripty na návrhové ploše a přejmenujte jej na "SetClipListXML."

![Přidání skriptované komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Přidání skriptované komponenty*

Když si prohlédnout vlastnosti součásti skripty, jsou čtyři typy další skript je vidět, se každý konfigurovat, a další skript.

![Vlastnosti skriptované komponenty](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Vlastnosti skriptované komponenty*

### <a id="frame_based_trim_modify_clip_list"></a>Úprava seznamu klip ze součásti se skripty
Předtím, než jsme můžete přepsat cliplist xml, který je generován během spuštění pracovního postupu, potřebujeme mít přístup k vlastnosti cliplist xml a obsah. Můžeme to udělat takto:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Příchozí seznamu klip přihlašováno](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Příchozí seznamu klip přihlašováno*

Nejdřív potřebujeme způsob, jak určit v tom okamžiku až do bodu, které chceme, aby k oříznutí videa. Chcete-li to vhodné pro uživatele bez technické pracovního postupu, publikujte dvě vlastnosti do kořenového adresáře grafu. Chcete-li to provést, klikněte pravým tlačítkem na plochu návrháře a vyberte "Přidat vlastnost":

* První vlastnost: "ClippingTimeStart" typu: ČASOVÝ "KÓD"
* Druhá vlastnost: "ClippingTimeEnd" typu: ČASOVÝ "KÓD"

![Přidat dialogové okno vlastností pro oříznutí počáteční čas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Přidat dialogové okno vlastností pro oříznutí počáteční čas*

![Publikování oříznutí props čas v kořenovém adresáři pracovního postupu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publikování oříznutí props čas v kořenovém adresáři pracovního postupu*

Konfigurace obou vlastností vhodnou hodnotu:

![Konfigurace výstřižek počáteční a koncové vlastnosti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Konfigurace výstřižek počáteční a koncové vlastnosti*

Nyní z v rámci skriptu, můžeme použít obě vlastnosti, například takto:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Okno protokol zobrazuje začátku a konce oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Okno protokol zobrazuje začátku a konce oříznutí*

Umožňuje analyzovat kód konce řetězce do výhodnější používat formuláře pomocí jednoduchého regulární výraz:

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

![Okno Protokol s výstupem analyzovaný kód konce](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Okno Protokol s výstupem analyzovaný kód konce*

Tyto informace aktuální můžeme nyní můžete upravit cliplist xml tak, aby odrážela počáteční a koncový čas pro požadovanou rámce přesné oříznutí videa.

![Kód skriptu k přidání prvků uvolnění dočasné paměti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Kód skriptu k přidání prvků uvolnění dočasné paměti*

To se provádí prostřednictvím operace manipulace s řetězci závislé normální. Výsledný seznam xml klip se zapíšou zpátky do vlastnost clipListXML v kořenovém adresáři pracovního postupu pomocí metody "setProperty". Okno Protokol po jiný testovací běh bude k těmto akcím nás:

![Protokolování v rozevíracím seznamu klipu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Protokolování v rozevíracím seznamu klipu*

Proveďte testovací běh zobrazit, jak mají byl oříznut videí a zvukových streamů. Jak budete používat více než jeden testovací běh s různými hodnotami pro oříznutí body, můžete si všimnout, že ty nebude brát v úvahu ale! Důvodem je, že návrhář, na rozdíl od modul runtime služby Azure, nepřepisuje cliplist xml každé spuštění. To znamená, že pouze první jste nastavili vstupní a výstupní body, způsobí, že xml k transformaci, všech jiných okolností naše guard – klauzule (pokud (clipListXML.indexOf ("<trim>") == -1)) zabrání přidání jiný element uvolnění dočasné paměti, když je pracovní postup již jedna nachází.

Chcete-li pracovní postup praktický k otestování místně, nejlepší přidáme údržby kódu, který kontroluje, pokud element a uvolnění dočasné paměti byla již existuje. Pokud ano, Odebereme ho před pokračováním úpravou souboru xml s novými hodnotami. Místo použití manipulace prostý řetězec, je pravděpodobně bezpečnější to provést prostřednictvím analýzy skutečných xml objektového modelu.

Takový kód jsme mohli přidat, i když budeme muset nejdřív přidat celou řadou příkazy pro import na začátku skriptu:

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

Poté můžeme přidat kód vyžaduje čištění:

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

Tento kód přejde přímo nad bodu, na které přidáme do cliplist xml uvolnění dočasné paměti elementy.

V tuto chvíli jsme můžete spustit a upravit pracovní postup podle času podobně jako chceme přitom má změny se použily nikdy.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Přidání vlastnosti usnadnění ClippingEnabled
Libovolný nemusí vždy ořezávání, které se provedou, jsou teď dokončit mimo pracovní postup tak, že přidáte pohodlný příznak logické hodnoty označující, zda chcete povolení ořezávání / oříznutí.

Stejně jako dříve publikujte nové vlastnosti do kořenového adresáře pracovní postup s názvem "ClippingEnabled" typu "Logická hodnota".

![Publikovat vlastnosti umožňující použití oříznutí](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publikovat vlastnosti umožňující použití oříznutí*

S pod klauzule jednoduché guard, můžeme zkontrolujte, jestli se vyžaduje oříznutí a rozhodněte, pokud náš seznam klip jako takové musí být upravena nebo ne.

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
[Úvod do Premium Encoding v Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Jak používat Premium Encoding v Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Kódování obsahu na vyžádání pomocí Azure Media Service](media-services-encode-asset.md#media-encoder-premium-workflow)

[Formáty a kodeky pracovního postupu Media Encoderu Premium](media-services-premium-workflow-encoder-formats.md)

[Ukázkové soubory pracovního postupu](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Nástroj Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
