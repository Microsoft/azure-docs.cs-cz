---
title: Přehled procesu návrhu pro Akustika projektu
titlesuffix: Azure Cognitive Services
description: Tento dokument popisuje, jak vyjádřit záměr vašeho návrhu ve všech třech fázích Akustika projektu pracovního postupu.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: cf38b2096e958a7484e5161277a608ec2cb88224
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470481"
---
# <a name="design-process-overview"></a>Přehled procesu návrhu
Máte v úmyslu návrhu můžete vyjádřit ve všech třech fázích Akustika projektu pracovního postupu: předem zanést scény nastavení, zvukové zdrojové umístění a která má označení po vytvoření návrhu. Proces vyžaduje méně značek přidružené k uvedení dozvuku svazky při zachování návrháře kontrolu nad jak zvuky scény.

## <a name="pre-bake-design"></a>Předběžné vytvoření návrhu
Proces instalace předběžné která má označení vytvoření scény vytváří scény a metadata, která se používají pro simulaci wave zvuk, která zahrnuje vybrat, které prvky scény se účastní simulace a poskytuje occlusions, odrazů a reverberation. Metadata pro scénu je výběr akustický materiálů pro každý prvek scény. Akustický materiály řízení velikosti šířky zvukové energie projeví zpět z každé povrchu.

Pohltivosti výchozí pro všechny plochy je 0.04, což je vysoce odrazivý. Účinky aesthetic a hraní her můžete dosáhnout optimalizace koeficienty absorpční z různých materiálů v rámci scény, které vynikají zejména pro posluchače při jejich slyšet přechody z jedné oblasti od scény do jiného. Například přechod z tmavě reverberant místnosti k jasně,-reverberant venkovní scény vylepšuje dopad na přechod. Tohoto efektu dosáhnete tak, vylaďte koeficienty absorpční na vyšší venkovní scény materiály.

Čas reverberation dané materiálů v místnosti nepřímo souvisí jeho pohltivosti většina materiálů s absorpce hodnoty v rozsahu 0.01, 0.20 a novější. Jsou velmi absorpčního materiály s absorpční koeficienty mimo tento rozsah.

![Graf doby dozvuku](media/ReverbTimeGraph.png)

[Která má označení vytvoření uživatelského rozhraní provede](bake-ui-walkthrough.md) předběžné která má označení vytvoření ovládacích prvků podrobně popisuje.

## <a name="sound-source-placement"></a>Umístění zdroje zvuku
Zobrazení body voxels a kontroly za běhu může pomoci ladit problémy pomocí zdrojů zvuku se zablokuje a uvnitř voxelized geometrie. Chcete-li přepnout voxel mřížky a kontroly bodů, se zobrazí, klikněte na odpovídajícího zaškrtávacího políčka v nabídce Gizma v pravém horním rohu scény. Pokud je zdrojem zvuku uvnitř wall voxel, přesuňte ho do voxel vzduchu.

![Gizma nabídky](media/GizmosMenu.png)  

Zobrazení voxel může pomoct zjistit, jestli vizuální komponenty ve hře použitou k nim transformací. Pokud ano, použít k hostování GameObject stejnou transformaci **Akustika správce**.

### <a name="voxel-size-discrepancies"></a>Velikost nesrovnalostí Voxel
Můžete si všimnout, že velikost voxels slouží ke znázornění, které se od scény mřížek účastní která má označení vytvoření Akustika se liší v zobrazení Návrh čas a modulu runtime. Tento rozdíl nemá vliv na kvalitu/členitost četnost vaší vybrané simulace, ale je spíše biproduct použití modulu runtime voxelized scény. Za běhu jsou voxels simulace "kontrast" pro podporu interpolaci mezi zdrojová umístění. To také umožňuje návrh čas umístění zvuku zdroje blíž ke mřížek scény než velikost voxel simulace umožňuje – od zdrojů uvnitř voxel obsahujících akusticky ošetřených síť Nedovolte, aby byly všechny zvuk.

Tady jsou dvě bitové kopie, které zobrazuje rozdíl mezi voxels návrhu (předběžné která má označení vytvoření) a modulu runtime (po která má označení vytvoření) voxels, jak vizualizovat pomocí modulu plug-in Unity:

Návrh voxels čas:

![VoxelsDesignTime](media/VoxelsDesignTime.png)

Modul runtime voxels:

![VoxelsRuntime](media/VoxelsRuntime.png)

Pomocí voxels režimu návrhu, ne modulu runtime vizualizace kontrast voxels je třeba rozhodnutí na Určuje, jestli síť voxel přesně reprezentuje mřížek architektura/strukturální scény.

## <a name="post-bake-design"></a>Po vytvoření návrhu
Která má označení vytvoření výsledky jsou uloženy v souboru ACE jako parametry uzavření a reverberation pro všechny páry umístění zdroje naslouchací proces v celém scény. Tato fyzicky přesných výsledků lze použít pro váš projekt jako – a je skvělý výchozí bod pro návrh. Proces návrhu po která má označení vytvoření určuje pravidla pro transformaci parametrů výsledek která má označení vytvoření za běhu.

### <a name="distance-based-attenuation"></a>Zeslabení na základě vzdálenosti
Zvuk poskytované DSP **Microsoft Acoustics** modulu plug-in spatializer Unity respektuje integrované do editoru Unity na základě vzdálenosti zeslabení-source. Ovládací prvky pro na základě vzdálenosti zeslabení **zdroje zvuku** komponenta nalezena v **inspektoru** panelu zvuk v oblasti zdrojů **3D nastavení zvuku**:

![Zeslabení vzdálenost](media/distanceattenuation.png)

Akustika provádí výpočet v poli "simulace oblasti" zaměřená na umístění přehrávače. Pokud je Vzdálená přehrávači nacházejících se mimo tuto oblast simulace zdroje zvuku pouze geometrie v rámci pole bude mít vliv na zvukové šíření hodnoty (jako je například způsobí uzavření), což poměrně dobře funguje, když jsou occluders pixelům přehrávač. Ale v případech, když hráč je ve volném prostoru, ale occluders blíží vzdálené zdrojem zvuku zvuk může stát unrealistically disoccluded. Naše navrhované alternativním řešením je v takových případech Ujistěte se, že zvukový zeslabení spadá 0 přibližně 45 m, výchozí vzdálenost vodorovné přehrávače k okraji pole.

### <a name="tuning-scene-parameters"></a>Optimalizace parametrů scény
Upravit parametry pro všechny zdroje, klikněte na kanál pruhu v Unity a **zvuk Mixer**a upravit parametry na **projektu Akustika Mixer** vliv.

![Přizpůsobení Mixer](media/MixerParameters.png)

* **Upravit wetness** – upraví dozvuku výkon, v databázi, ze všech zdrojů ve scéně podle vzdálenosti zdroj naslouchacího procesu. Kladné hodnoty provádět zvuk více reverberant při záporné hodnoty provést více suchého zvuk.
* **Škálování RT60** – násobení skalární dozvuku dobu.
* **Použijte pro posouvání** – ovládací prvky, zda je zvukový je výstup jako binaural (0) nebo multichannel posouvání (1). Libovolná hodnota kromě 1 označuje binaural. Binaural výstup je spatialized s HRTFs pro použití s sluchátka a vícekanálový výstupu je spatialized s VBAP pro použití s vícekanálový obklopit mluvčího systémy. Pokud pomocí vícekanálový výřez, nezapomeňte vybrat režim reproduktoru, který odpovídá nastavení zařízení, najdete v části **nastavení projektu** > **zvuk**.

![SpeakerMode](media/SpeakerMode.png)

### <a name="tuning-source-parameters"></a>Ladění zdrojových parametrů
Připojení **AcousticsAdjust** skript ke zdroji povolí optimalizaci parametrů pro tento zdroj. Připojit skriptu, klikněte na tlačítko **přidat součást** v dolní části **inspektoru** panelu a přejděte do **skripty > Upravit Akustika**. Tento skript má šest ovládací prvky:

![AcousticsAdjust](media/AcousticsAdjust.png)

* **Povolit Akustika** – Určuje, zda Akustika je použito k tomuto zdroji. Pokud není zaškrtnuto, zdroj bude spatialized HRTFs nebo posouvání, ale nedojde k žádné Akustika. To znamená bez překážky, uzavření a reverberation dynamické parametry, například úroveň a decay čas. Reverberation se uplatní s pevnou úroveň a decay čas.
* **Uzavření** – používají multiplikátoru na úrovni databáze uzavření počítají tak, že Akustika systému. Pokud tento multiplikátor je větší než 1, uzavření bude exaggerated, při hodnoty menší než 1. Ujistěte se, efekt uzavření složitější a hodnota 0 zakáže uzavření.
* **Přenos (databáze)** -nastavit zeslabení (v databázi) způsobené přenos přes geometry. Nastavte tento posuvník na nejnižší úrovni zakázat přenos. Akustika spatializes počáteční suchého zvuk jako přicházejících kolem geometrie scény (portaling). Přenos poskytuje další suchého doručení, která spatialized ve směru z dohlednost. Všimněte si, že je použito také křivky zeslabení vzdálenost pro zdroj.
* **Wetness (databáze)** – upraví dozvuku výkon, v databázi, podle vzdálenosti ze zdroje. Kladné hodnoty provádět zvuk více reverberant při záporné hodnoty provést více suchého zvuk. Klikněte na křivku ovládací prvek (zelená čára) a zobrazte si editoru křivky. Klepněte na Přidat body levým tlačítkem myši a přetažení tyto body k vytvoření funkce, že které chcete upravte křivky. Osy x je vzdálenost od zdroje a osy y je úprava dozvuku v databázi. Další informace o úpravě křivky, najdete v tomto [Unity ruční](https://docs.unity3d.com/Manual/EditingCurves.html). Pokud chcete obnovit výchozí křivku, klikněte pravým tlačítkem na **Wetness** a vyberte **resetování**.
* **Decay – časové měřítko** – nastaví dobu decay multiplikátoru. Například pokud Určuje, která má označení vytvoření výsledku decay čas 750 milisekund, ale tato hodnota nastavená na 1.5, je čas decay použití ke zdroji 1,125 milisekund.
* **Outdoorness** -sčítání úprava na odhad systému Akustika jak "venku" by měl zvukové reverberation ve zdroji. Tuto hodnotu nastavíte na 1 způsobí, že zdroj vždy zvukové zcela venku, při nastavení na hodnotu -1 způsobí, že zdroj zvukové budovách.

Různých zdrojů mohou vyžadovat různé nastavení k dosažení určitých efektů aesthetic nebo hraní her. Dialogové okno je jedním z příkladů je to možné. Lidské mazat je více attuned k reverberation v řeči, zatímco dialogové okno často musí být srozumitelné pro hraní her. Můžete účet pro toto přitom dialogové okno bez diegetic přechodem **Wetness** dolů, nastavení **Percepční Warp vzdálenost** parametr je popsáno níže, přidání některých  **Přenos** pro některé suchého zvuku boost šíření prostřednictvím stěn a/nebo omezení **uzavření** od 1 do mají další zvuk doručení prostřednictvím portálů.

Připojení **AcousticsAdjustExperimental** skript, který zdroj umožňuje další experimentální parametry ladění u tohoto zdroje. Připojit skriptu, klikněte na tlačítko **přidat součást** dole **inspektoru** panelu a přejděte do **skripty > Akustika upravit experimentální**. Aktuálně nejsou k dispozici jeden experimentální ovládací prvek:

![AcousticsAdjustExperimental](media/AcousticsAdjustExperimental.png)

* **Percepční Warp vzdálenost** – použití exponenciální pokřivení vzdálenosti slouží k výpočtu poměru suchého vlhkého stavu. Systém Akustika vypočítá vlhkou úrovně v rámci oboru, které hladce lišit podle vzdálenosti a poskytují Percepční vzdálenost pomůcky. Pokřivení hodnoty větší než 1 exaggerate tento efekt zvýšením úrovně reverberation související vzdálenost, provádění zvuk "vzdálené". Pokřivení hodnoty menší než 1 Zkontrolujte reverberation na základě vzdálenosti změnit složitější a zvukových další "tohoto".

