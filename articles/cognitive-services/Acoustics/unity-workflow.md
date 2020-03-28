---
title: Kurz návrhu jednoty akustiky projektu
titlesuffix: Azure Cognitive Services
description: Tento kurz popisuje pracovní postup návrhu pro akustiku projektu v jednotě.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fd00e4105ce4edae9d014df2a83c5ae3aaf778da
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854268"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Kurz návrhu jednoty akustiky projektu
Tento kurz popisuje návrhové nástroje a pracovní postup pro akustiku projektu v jednotě.

Požadavky:
* Unity 2018.2+ pro Windows
* Scéna Unity s pečeným akustikou

V tomto kurzu můžete získat unity scénu s pečeným akustikou aktivum dvěma způsoby:
* [Přidejte projekt ovou akustiku do projektu Unity](unity-integration.md), pak [získejte účet Azure Batch](create-azure-account.md)a pak [upečte scénu Unity](unity-baking.md)
* Nebo použijte [ukázkový obsah Project Acoustics Unity](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Zkontrolovat koncepty procesu návrhu
Aplikace Project Acoustics používá ke zpracování zdrojů běžné metody zpracování digitálního signálu (DSP) a umožňuje vám kontrolu nad známými vlastnostmi akustiky, včetně okluze, mokré/suché směsi a délky ocasu (RT60). Ale základní [koncepce procesu návrhu akustika projektu](design-process.md) je, že namísto nastavení těchto vlastností přímo, můžete řídit, jak se výsledky simulace používají k řízení těchto vlastností. Výchozí nastavení pro každý ovládací prvek představují fyzicky přesnou akustiku.

## <a name="design-acoustics-for-each-source"></a>Návrh akustiky pro každý zdroj
Aplikace Project Acoustics poskytuje řadu ovládacích prvků návrhu akustiky specifické pro zdroj. To vám umožní ovládat mix ve scéně s důrazem na některé zdroje a de-s důrazem na ostatní.

### <a name="adjust-distance-based-attenuation"></a>Nastavení útlumu na základě vzdálenosti
Zvukový DSP poskytovaný modulu plug-in spatializer **project acoustics** unity respektuje útlum založený na zdroji zabudovaný do editoru Unity. Ovládací prvky pro útlum na základě vzdálenosti jsou v součásti **Zdroj zvuku,** která se nachází v panelu **Inspektor** zdrojů zvuku, v části **3D nastavení zvuku**:

![Snímek obrazovky panelu volby vzdálenosti Unity](media/distance-attenuation.png)

Akustika provádí výpočty v poli "simulační oblast" soustředěné kolem umístění přehrávače. Pokud je zdroj zvuku vzdálený od přehrávače, který se nachází mimo tuto simulační oblast, pouze geometrie uvnitř pole ovlivní šíření zvuku (například způsobuje okluzi), které funguje přiměřeně dobře, když jsou okluze v blízkosti přehrávače. Nicméně, v případech, kdy je hráč v otevřeném prostoru, ale okluzory jsou v blízkosti vzdáleného zdroje zvuku, zvuk se může stát nerealisticky disoccluded. Naše navrhované řešení je zajistit v takových případech, že útlum zvuku spadne na 0 na asi 45 m, výchozí vodorovná vzdálenost hráče k okraji krabice.

![Snímek obrazovky s panelem možností Unity SpeakerMode](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Nastavení okluze a přenosu
Připojení skriptu **AcousticsAdjust** ke zdroji umožňuje optimalizaci parametrů pro tento zdroj. Chcete-li skript připojit, klepněte v dolní části panelu **Inspektor** na **Přidat komponentu** a přejděte na **Skripty > Akustiku Upravit**. Skript má šest ovládacích prvků:

![Snímek obrazovky se skriptem Unity AcousticsAdjust](media/acoustics-adjust.png)

* **Povolit akustiku** - Určuje, zda je u tohoto zdroje použita akustika. Není-li zaškrtnuto, bude zdroj prostorově vybaven hrthy nebo posouváním, ale nebude existovat žádná akustika. To znamená, že žádná překážka, okluze, nebo dynamické dozvuku parametry, jako je úroveň a doba rozpadu. Dozvuk je stále aplikován s pevnou úrovní a dobou rozpadu.
* **Okluze** - Aplikujte multiplikátor na úroveň okluze dB vypočítanou akustickým systémem. Pokud tento multiplikátor je větší než 1, okluze bude přehnané, zatímco hodnoty menší než 1, aby efekt okluze jemnější a hodnota 0 zakáže okluze.
* **Přenos (dB)** - Nastavte útlum (v dB) způsobený přenosem přes geometrii. Nastavte tento jezdec na nejnižší úroveň, abyste zakázali přenos. Akustika prostorově dokumentuje počáteční suchý zvuk, který přichází kolem geometrie scény (portaling). Přenos poskytuje další suchý příjezd, který je prostorový ve směru zorného pole. Všimněte si, že je také použita křivka útlumu vzdálenosti pro zdroj.

### <a name="adjust-reverberation"></a>Upravit dozvuk
* **Vlhkost (dB)** - Upravuje sílu reverbu v dB podle vzdálenosti od zdroje. Pozitivní hodnoty dělají zvuk více dozvuku, zatímco negativní hodnoty dělají zvuk suchější. Kliknutím na ovládací prvek křivky (zelená čára) zobrazíte editor křivek. Upravte křivku klepnutím doleva, chcete-li přidat body a přetažením těchto bodů, abyste vytvořili požadovanou funkci. Osa x je vzdálenost od zdroje a osa y je nastavení reverbu v dB. Další informace o úpravách křivek naleznete v této [příručce Unity Manual](https://docs.unity3d.com/Manual/EditingCurves.html). Chcete-li křivku obnovit zpět na výchozí hodnotu, klepněte pravým tlačítkem myši na **položku Wetness** a vyberte příkaz **Obnovit**.
* **Měřítko doby rozpadu** – Upraví násobitel pro dobu rozpadu. Pokud například výsledek pečení určuje dobu rozpadu 750 milisekund, ale tato hodnota je nastavena na 1,5, je doba rozpadu aplikovaná na zdroj 1 125 milisekund.
* **Outdoorness** - Aditivní úprava na akustický systém odhad toho, jak "venku" dozvuk na zdroj by měl zvuk. Nastavení této hodnoty na 1 způsobí, že zdroj bude vždy znít úplně venku, zatímco jeho nastavení na -1 způsobí, že zdrojový zvuk bude zcela uvnitř.

Připojení **skriptu AcousticsAdjustExperimental** ke zdroji umožňuje další parametry experimentálního ladění pro tento zdroj. Chcete-li skript připojit, klepněte na tlačítko **Přidat komponentu** v dolní části panelu **Inspektor** a přejděte na **skripty > akustiku Upravit experimentální**. V současné době existuje jedna experimentální kontrola:

![Snímek obrazovky se skriptem Unity AcousticsAdjustExperimental](media/acoustics-adjust-experimental.png)

* **Percepční warp vzdálenosti** – Aplikujte exponenciální deformaci na vzdálenost použitou k výpočtu poměru sucho-mokro. Akustický systém počítá mokré úrovně v celém prostoru, které se plynule mění podle vzdálenosti a poskytují percepční vzdálenosti. Pokřivení hodnoty větší než 1 zveličují tento efekt zvýšením úrovně dozvuku související se vzdáleností, takže zvuk "vzdálené". Pokřivení hodnoty menší než 1, aby vzdálenost-založené dozvuku změnit jemnější, takže zvuk více "přítomen".

## <a name="design-acoustics-for-all-sources"></a>Návrh akustiky pro všechny zdroje
Chcete-li upravit parametry pro všechny zdroje, klikněte na pás kanálu v **jednotě Audio Mixer**a upravte parametry na **efektu Míchání akustiky projektu.**

![Snímek obrazovky panelu Přizpůsobení aplikace Akustika Unity](media/mixer-parameters.png)

* **Nastavení vlhkosti** - Upravuje sílu reverbu v dB napříč všemi zdroji ve scéně na základě vzdálenosti zdroj-posluchač. Pozitivní hodnoty dělají zvuk více dozvuku, zatímco negativní hodnoty dělají zvuk suchější.
* **RT60 Scale** - Multiplikativní skalární pro reverb čas.
* **Použití posouvání** – Určuje, zda je zvuk výstupem jako binaurální (0) nebo vícekanálové posouvání (1). Jakákoli hodnota kromě 1 označuje binaurální. Binaurální výstup je prostorový s HRTFs pro použití se sluchátky a vícekanálový výstup je prostorový s VBAP pro použití s vícekanálovými prostorovými reproduktorovými systémy. Pokud používáte vícekanálový panner, nezapomeňte vybrat režim reproduktoru, který odpovídá nastavení vašeho zařízení, který najdete v části **Nastavení** > projektu**Zvuk**.

## <a name="check-proper-sound-source-placement"></a>Zkontrolujte správné umístění zdroje zvuku
Zvukové zdroje umístěné uvnitř obsazených voxelů nedostanou akustické ošetření. Vzhledem k tomu, že voxely protéká kolem viditelné geometrie scény, je možné umístit zdroj do voxelu, zatímco se zdá být unoccluded vizuální geometrií. Voxels aplikace Project Acoustics můžete zobrazit přepnutím zaškrtávacího políčka mřížky voxel v nabídce **Gizmos** v pravém horním části zobrazení **Scéna.**

![Snímek obrazovky s nabídkou Unity Gizmos](media/gizmos-menu.png)  

Displej voxel může také pomoci určit, zda vizuální komponenty ve hře mají transformaci použitou na ně. Pokud ano, použijte stejnou transformaci na GameObject hostující **správce akustiky**.

### <a name="bake-time-vs-run-time-voxels"></a>Doba pečení vs. doba běhu voxels
Je možné zobrazit voxels v okně editoru v době herního designu a v okně hry za běhu. Velikost voxels se liší v těchto pohledech. Je to proto, že interpolace za běhu akustiky používá jemnější mřížku voxel pro hladší výsledky interpolace. Umístění zdroje zvuku by mělo být ověřeno pomocí runtime voxels.

Čas návrhu voxels:

![Snímek obrazovky projektu Akustika voxels během návrhu](media/voxels-design-time.png)

Runtime voxels:

![Snímek obrazovky projektu Akustika voxels za běhu](media/voxels-runtime.png)

## <a name="next-steps"></a>Další kroky
* Prozkoumejte případové studie zdůrazňující koncepty [procesu návrhu](design-process.md)

