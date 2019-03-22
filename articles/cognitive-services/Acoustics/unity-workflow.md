---
title: Projekt Akustika Unity návrhu kurz
titlesuffix: Azure Cognitive Services
description: Tento kurz popisuje pracovní postup návrhu pro projekt Akustika v Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 01783aa12f586f61583b1503c796f9b523770104
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310621"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Projekt Akustika Unity návrhu kurz
Tento kurz popisuje nástroje pro návrh a pracovní postup pro projekt Akustika v Unity.

Požadavky:
* Unity 2018.2 + pro Windows
* Unity scény dokončené Akustika assetu

Pro účely tohoto kurzu můžete získat scény Unity dokončené Akustika assetu dvěma způsoby:
* [Přidat projekt Akustika svým projektem Unity](unity-integration.md), pak [získat účet služby Azure Batch](create-azure-account.md), pak [zanést vaše Scéna Unity](unity-baking.md)
* Nebo můžete použít [Unity projektu Akustika ukázkový obsah](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Seznamte se s koncepty návrhu procesu
Akustika projekt používá běžné zpracování signálu (DSP) metody digitální zvukový ke zpracování zdrojů a získáte tak kontrolu nad známé Akustika vlastnosti včetně uzavření, mix wet/zkušební a délka tail reverberation (RT60). Ale základní [koncept proces návrhu projektu Akustika](design-process.md) , nikoli přímo nastavení těchto vlastností, určujete, jak se výsledky simulace používají k řízení těchto vlastností. Výchozí nastavení pro každý ovládací prvek představují fyzicky přesné Akustika.

## <a name="design-acoustics-for-each-source"></a>Návrh Akustika pro jednotlivé zdroje
Projekt Akustika obsahuje celé řady kontrolních mechanismů specifická pro zdroj Akustika návrhu. To umožňuje řídit poměr ve scéně, kdy se klade důraz některé zdroje a deaktivace emphasizing ostatní.

### <a name="adjust-distance-based-attenuation"></a>Upravit na základě vzdálenosti zeslabení
Zvuk poskytované DSP **projektu Akustika** modulu plug-in spatializer Unity respektuje integrované do editoru Unity na základě vzdálenosti zeslabení-source. Ovládací prvky pro na základě vzdálenosti zeslabení **zdroje zvuku** komponenta nalezena v **inspektoru** panelu zvuk v oblasti zdrojů **3D nastavení zvuku**:

![Panel možností zeslabení vzdálenost snímek obrazovky s Unity](media/distance-attenuation.png)

Akustika provádí výpočet v poli "simulace oblasti" zaměřená na umístění přehrávače. Pokud je Vzdálená přehrávači nacházejících se mimo tuto oblast simulace zdroje zvuku pouze geometrie v rámci pole bude mít vliv na zvukové šíření hodnoty (jako je například způsobí uzavření), což poměrně dobře funguje, když jsou occluders pixelům přehrávač. Ale v případech, když hráč je ve volném prostoru, ale occluders blíží vzdálené zdrojem zvuku zvuk může stát unrealistically disoccluded. Naše navrhované alternativním řešením je v takových případech Ujistěte se, že zvukový zeslabení spadá 0 přibližně 45 m, výchozí vzdálenost vodorovné přehrávače k okraji pole.

![Snímek obrazovky s Unity SpeakerMode panel možností](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Upravte uzavření a přenosu
Připojení **AcousticsAdjust** skript ke zdroji povolí optimalizaci parametrů pro tento zdroj. Připojit skriptu, klikněte na tlačítko **přidat součást** v dolní části **inspektoru** panelu a přejděte do **skripty > Upravit Akustika**. Tento skript má šest ovládací prvky:

![Snímek obrazovky s Unity AcousticsAdjust skriptu](media/acoustics-adjust.png)

* **Povolit Akustika** – Určuje, zda Akustika je použito k tomuto zdroji. Pokud není zaškrtnuto, zdroj bude spatialized HRTFs nebo posouvání, ale nedojde k žádné Akustika. To znamená bez překážky, uzavření nebo reverberation dynamické parametry, například úroveň a decay čas. Reverberation se uplatní s pevnou úroveň a decay čas.
* **Uzavření** – používají multiplikátoru na úrovni databáze uzavření počítají tak, že Akustika systému. Pokud tento multiplikátor je větší než 1, uzavření bude exaggerated, při hodnoty menší než 1. Ujistěte se, efekt uzavření složitější a hodnota 0 zakáže uzavření.
* **Přenos (databáze)** -nastavit zeslabení (v databázi) způsobené přenos přes geometry. Nastavte tento posuvník na nejnižší úrovni zakázat přenos. Akustika spatializes počáteční suchého zvuk jako přicházejících kolem geometrie scény (portaling). Přenos poskytuje další suchého doručení, která spatialized ve směru z dohlednost. Všimněte si, že je použito také křivky zeslabení vzdálenost pro zdroj.

### <a name="adjust-reverberation"></a>Upravit reverberation
* **Wetness (databáze)** – upraví dozvuku výkon, v databázi, podle vzdálenosti ze zdroje. Kladné hodnoty provádět zvuk více reverberant při záporné hodnoty provést více suchého zvuk. Klikněte na křivku ovládací prvek (zelená čára) a zobrazte si editoru křivky. Klepněte na Přidat body levým tlačítkem myši a přetažení tyto body k vytvoření funkce, že které chcete upravte křivky. Osy x je vzdálenost od zdroje a osy y je úprava dozvuku v databázi. Další informace o úpravě křivky, najdete v tomto [Unity ruční](https://docs.unity3d.com/Manual/EditingCurves.html). Pokud chcete obnovit výchozí křivku, klikněte pravým tlačítkem na **Wetness** a vyberte **resetování**.
* **Decay – časové měřítko** – nastaví dobu decay multiplikátoru. Například pokud Určuje, která má označení vytvoření výsledku decay čas 750 milisekund, ale tato hodnota nastavená na 1.5, je čas decay použití ke zdroji 1,125 milisekund.
* **Outdoorness** -sčítání úprava na odhad systému Akustika jak "venku" by měl zvukové reverberation ve zdroji. Tuto hodnotu nastavíte na 1 způsobí, že zdroj vždy zvukové zcela venku, při nastavení na hodnotu -1 způsobí, že zdroj zvukové zcela budovách.

Připojení **AcousticsAdjustExperimental** skript, který zdroj umožňuje další experimentální parametry ladění u tohoto zdroje. Připojit skriptu, klikněte na tlačítko **přidat součást** dole **inspektoru** panelu a přejděte do **skripty > Akustika upravit experimentální**. Aktuálně nejsou k dispozici jeden experimentální ovládací prvek:

![Snímek obrazovky s Unity AcousticsAdjustExperimental skriptu](media/acoustics-adjust-experimental.png)

* **Percepční Warp vzdálenost** – použití exponenciální pokřivení vzdálenosti slouží k výpočtu poměru suchého vlhkého stavu. Systém Akustika vypočítá vlhkou úrovně v rámci oboru, které hladce lišit podle vzdálenosti a poskytují Percepční vzdálenost pomůcky. Pokřivení hodnoty větší než 1 exaggerate tento efekt zvýšením úrovně reverberation související vzdálenost, provádění zvuk "vzdálené". Pokřivení hodnoty menší než 1 Zkontrolujte reverberation na základě vzdálenosti změnit složitější a zvukových další "tohoto".

## <a name="design-acoustics-for-all-sources"></a>Návrh Akustika pro všechny zdroje
Upravit parametry pro všechny zdroje, klikněte na kanál pruhu v Unity a **zvuk Mixer**a upravit parametry na **projektu Akustika Mixer** vliv.

![Snímek obrazovky z projektu Akustika Unity Mixer přizpůsobení panelu](media/mixer-parameters.png)

* **Upravit wetness** – upraví dozvuku výkon, v databázi, ze všech zdrojů ve scéně podle vzdálenosti zdroj naslouchacího procesu. Kladné hodnoty provádět zvuk více reverberant při záporné hodnoty provést více suchého zvuk.
* **Škálování RT60** – násobení skalární dozvuku dobu.
* **Použijte pro posouvání** – ovládací prvky, zda je zvukový je výstup jako binaural (0) nebo multichannel posouvání (1). Libovolná hodnota kromě 1 označuje binaural. Binaural výstup je spatialized s HRTFs pro použití s sluchátka a vícekanálový výstupu je spatialized s VBAP pro použití s vícekanálový obklopit mluvčího systémy. Pokud pomocí vícekanálový výřez, nezapomeňte vybrat režim reproduktoru, který odpovídá nastavení zařízení, najdete v části **nastavení projektu** > **zvuk**.

## <a name="check-proper-sound-source-placement"></a>Zkontrolujte umístění správné zdrojem zvuku
Zvukové zdrojů umístěných uvnitř obsazené voxels nedostali akustický zpracování. Voxels prodloužit minulé geometrie viditelné scény, proto je možné umístit zdroj uvnitř voxel když se zobrazí unoccluded podle visual geometrie. Můžete zobrazit projekt Akustika voxels přepínáním zaškrtávacího políčka voxel mřížky v **Gizma** nabídky v pravém horním rohu **scény** zobrazení.

![Snímek obrazovky s Unity Gizma nabídky](media/gizmos-menu.png)  

Zobrazení voxel může také pomoct zjistit, jestli vizuální komponenty ve hře použitou k nim transformací. Pokud ano, použít k hostování GameObject stejnou transformaci **Akustika správce**.

### <a name="bake-time-vs-run-time-voxels"></a>Vytvoření čas vs. voxels běhu
Je možné zobrazit voxels v okně editoru v době návrhu her a v okně hry v době běhu. Velikost voxels se liší v těchto zobrazeních. Toto je vzhledem k tomu interpolace Akustika modul runtime používá kvalitnější grid voxel výsledky hladší interpolace. Umístění zvuku zdroj by měl ověřit pomocí voxels modulu runtime.

Návrh voxels čas:

![Snímek obrazovky projektu Akustika voxels v době návrhu](media/voxels-design-time.png)

Modul runtime voxels:

![Snímek obrazovky projektu Akustika voxels za běhu](media/voxels-runtime.png)

## <a name="next-steps"></a>Další postup
* Prozkoumat případové studie zvýraznění koncepci [návrhu procesu](design-process.md)

