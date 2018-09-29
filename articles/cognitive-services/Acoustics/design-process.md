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
ms.openlocfilehash: 8f594be67c4677fae00cb01598d3899e30dae1e8
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433220"
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

### <a name="tuning-scene-parameters"></a>Optimalizace parametrů scény
Chcete-li upravit parametry pro všechny zdroje, klikněte na kanál pruhu v Unity a **Mixer zvuk**a upravit parametry na **Akustika Mixer** vliv.

![Přizpůsobení Mixer](media/MixerParameters.png)

### <a name="tuning-source-parameters"></a>Ladění zdrojových parametrů
Připojení **AcousticsSourceCustomization** skript ke zdroji povolí optimalizaci parametrů pro tento zdroj. Připojit skriptu, klikněte na tlačítko **přidat součást** v dolní části **inspektoru** panelu a přejděte do **skripty > Přizpůsobení zdroj Akustika**. Tento skript má tři parametry:

![Vlastní nastavení zdroje](media/SourceCustomization.png)

* **Upravit Power dozvuku** – upraví dozvuku výkon, v databázi. Kladné hodnoty provádět zvuk více reverberant při záporné hodnoty provést více suchého zvuk.
* **Decay – časové měřítko** – nastaví dobu decay multiplikátoru. Například pokud Určuje, která má označení vytvoření výsledku decay čas 750 milisekund, ale tato hodnota nastavená na 1.5, je čas decay použití ke zdroji 1,125 milisekund.
* **Povolit Akustika** – Určuje, zda Akustika je použito k tomuto zdroji. Pokud není zaškrtnuto, bude se zdroji spatialized s HRTFs, ale bez Akustika, to znamená bez překážky uzavření a reverberation dynamické parametry, například úroveň a decay čas. Reverberation se uplatní s pevnou úroveň a decay čas.

Různých zdrojů mohou vyžadovat různé nastavení k dosažení určitých efektů aesthetic nebo hraní her. Dialogové okno je jedním z příkladů je to možné. Lidské mazat je více attuned k reverberation v řeči, zatímco dialogové okno často musí být srozumitelné pro hraní her. To můžete účtu přitom dialogové okno bez diegetic úpravou power dozvuku dolů.
