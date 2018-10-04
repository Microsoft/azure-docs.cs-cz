---
title: Přehled procesu návrhu pro Akustika – Cognitive Services
description: Tento dokument popisuje, jak vyjádřit záměr vašeho návrhu ve všech třech fázích Akustika projektu pracovního postupu.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: b6bb04d9cec690198de663189dacd41fcbe960eb
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248600"
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

## <a name="post-bake-design"></a>Po vytvoření návrhu
Která má označení vytvoření výsledky jsou uloženy v souboru ACE jako parametry uzavření a reverberation pro všechny páry umístění zdroje naslouchací proces v celém scény. Tato fyzicky přesných výsledků lze použít pro váš projekt jako – a je skvělý výchozí bod pro návrh. Proces návrhu po která má označení vytvoření určuje pravidla pro transformaci parametrů výsledek která má označení vytvoření za běhu.

### <a name="distance-based-attenuation"></a>Zeslabení na základě vzdálenosti
Zvuk poskytované DSP **Microsoft Acoustics** modulu plug-in spatializer Unity respektuje integrované do editoru Unity na základě vzdálenosti zeslabení-source. Ovládací prvky pro na základě vzdálenosti zeslabení **zdroje zvuku** komponenta nalezena v **inspektoru** panelu zvuk v oblasti zdrojů **3D nastavení zvuku**:

![Zeslabení vzdálenost](media/distanceattenuation.png)

Akustika provádí výpočet v poli "simulace oblasti" zaměřená na umístění přehrávače. Pokud je Vzdálená přehrávači nacházejících se mimo tuto oblast simulace zdroje zvuku pouze geometrie v rámci pole bude mít vliv na zvukové šíření hodnoty (jako je například způsobí uzavření), což poměrně dobře funguje, když jsou occluders pixelům přehrávač. Ale v případech, když hráč je ve volném prostoru, ale occluders blíží vzdálené zdrojem zvuku zvuk může stát unrealistically disoccluded. Naše navrhované alternativním řešením je v takových případech Ujistěte se, že zvukový zeslabení spadá 0 přibližně 45 m, výchozí vzdálenost vodorovné přehrávače k okraji pole.

### <a name="tuning-scene-parameters"></a>Optimalizace parametrů scény
Chcete-li upravit parametry pro všechny zdroje, klikněte na kanál pruhu v Unity a **Mixer zvuk**a upravit parametry na **Akustika Mixer** vliv.

![Přizpůsobení Mixer](media/MixerParameters.png)

### <a name="tuning-source-parameters"></a>Ladění zdrojových parametrů
Připojení **AcousticsDesign** skript ke zdroji povolí optimalizaci parametrů pro tento zdroj. Připojit skriptu, klikněte na tlačítko **přidat součást** dole **inspektoru** panelu a přejděte do **skripty > Akustika návrhu**. Tento skript má šest ovládací prvky:

![AcousticsDesign](media/AcousticsDesign.png)

* **Faktor uzavření** – používají multiplikátoru na úrovni databáze uzavření počítají tak, že Akustika systému. Pokud tento multiplikátor je větší než 1, uzavření bude exaggerated, při hodnoty menší než 1. Ujistěte se, efekt uzavření složitější a hodnota 0 zakáže uzavření.
* **Přenos (databáze)** -nastavit zeslabení (v databázi) způsobené přenos přes geometry. Nastavte tento posuvník na nejnižší úrovni zakázat přenos. Akustika spatializes počáteční suchého zvuk jako přicházejících kolem geometrie scény (portaling). Přenos poskytuje další suchého doručení, která spatialized ve směru z dohlednost. Všimněte si, že je použito také křivky zeslabení vzdálenost pro zdroj.
* **Upravit wetness (databáze)** – upraví dozvuku výkon, v databázi, podle vzdálenosti ze zdroje. Kladné hodnoty provádět zvuk více reverberant při záporné hodnoty provést více suchého zvuk. Klikněte na křivku ovládací prvek (zelená čára) a zobrazte si editoru křivky. Klepněte na Přidat body levým tlačítkem myši a přetažení tyto body k vytvoření funkce, že které chcete upravte křivky. Osy x je vzdálenost od zdroje a osy y je úprava dozvuku v databázi. Najdete v tomto [Unity ruční](https://docs.unity3d.com/Manual/EditingCurves.html) podrobné informace o úpravách křivky. Pokud chcete obnovit výchozí křivku, klikněte pravým tlačítkem na **Wetness upravit** a vyberte **resetování**.
* **Decay – časové měřítko** – nastaví dobu decay multiplikátoru. Například pokud Určuje, která má označení vytvoření výsledku decay čas 750 milisekund, ale tato hodnota nastavená na 1.5, je čas decay použití ke zdroji 1,125 milisekund.
* **Povolit Akustika** – Určuje, zda Akustika je použito k tomuto zdroji. Pokud není zaškrtnuto, bude se zdroji spatialized s HRTFs, ale bez Akustika, to znamená bez překážky uzavření a reverberation dynamické parametry, například úroveň a decay čas. Reverberation se uplatní s pevnou úroveň a decay čas.
* **Úprava outdoorness** -sčítání úprava na odhad systému Akustika jak "venku" by měl zvukové reverberation ve zdroji. Toto nastavení na hodnotu 1 způsobí, že zdroj vždy zvukové zcela venku, při nastavení na hodnotu -1 bude zdroje zvuku budovách.

Různých zdrojů mohou vyžadovat různé nastavení k dosažení určitých efektů aesthetic nebo hraní her. Dialogové okno je jedním z příkladů je to možné. Lidské mazat je více attuned k reverberation v řeči, zatímco dialogové okno často musí být srozumitelné pro hraní her. Můžete účet pro toto přitom dialogové okno bez diegetic přechodem **Wetness upravit** dolů, nastavení **Percepční Warp vzdálenost** parametr je popsáno níže, přidání některých **Přenosu** pro některé suchého zvuku boost šíření prostřednictvím stěn a/nebo omezení **uzavření faktor** od 1 do mají další zvuk doručení prostřednictvím portálů.

Připojení **AcousticsDesignExperimental** skript, který zdroj umožňuje další experimentální parametry ladění u tohoto zdroje. Připojit skriptu, klikněte na tlačítko **přidat součást** dole **inspektoru** panelu a přejděte do **skripty > experimentální návrh Akustika**. Aktuálně nejsou k dispozici jeden experimentální ovládací prvek:

![AcousticsDesignExperimental](media/AcousticsDesignExperimental.png)

* **Percepční Warp vzdálenost** – použití exponenciální pokřivení vzdálenosti slouží k výpočtu poměru suchého vlhkého stavu. Systém Akustika vypočítá vlhkou úrovně v rámci oboru, které hladce lišit podle vzdálenosti a poskytují Percepční vzdálenost pomůcky. Pokřivení hodnoty větší než 1 exaggerate tento efekt zvýšením úrovně reverberation související vzdálenost, provedete zvukové "vzdálené", zatímco pokřivení hodnoty menší než 1 Zkontrolujte složitější, provádění zvuk informace na základě vzdálenosti reverberation změní "k dispozici".

