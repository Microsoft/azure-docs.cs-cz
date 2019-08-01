---
title: Kurz návrhu Unity na akustické projekty
titlesuffix: Azure Cognitive Services
description: Tento kurz popisuje pracovní postup návrhu pro akustické projekty v Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: f9ff4225e7e855ed666d3554631015b8ce51df37
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706593"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Kurz návrhu Unity na akustické projekty
Tento kurz popisuje nástroje pro návrh a pracovní postup pro akustické projekty v Unity.

Požadavky:
* Unity 2018.2 + pro Windows
* Scéna Unity s vloženými akustickým prostředkem

Pro tento kurz můžete získat scénu v Unity s vloženými akustickým prostředkem dvěma způsoby:
* [Přidejte do projektu Unity akustické projekty](unity-integration.md), potom [Získejte účet Azure Batch](create-azure-account.md)a potom [zanesli svou scénu Unity](unity-baking.md) .
* Můžete také použít [ukázkový obsah projektu s akustickým obsahem Unity](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Kontrola konceptů procesu návrhu
Akustické zdroje projektu používají ke zpracování vašich zdrojů běžné metody DSP (audio Digital Signal Processing) a umožňují řídit známé akustické vlastnosti, včetně překrytí, vlhkého/suchého míchání a reverberation koncových délek (RT60). Ale základní [koncept procesu návrhu akustického projektu](design-process.md) je, že místo toho, abyste tyto vlastnosti nastavují přímo, můžete určit, jak se mají výsledky simulace používat k ovládání těchto vlastností. Výchozí nastavení pro každý ovládací prvek představuje fyzicky přesné akustické akustické hodnoty.

## <a name="design-acoustics-for-each-source"></a>Návrh akustické pro každý zdroj
Akustické a projektové zdroje poskytují řadu ovládacích prvků návrhu, které jsou specifické pro zdroj. To vám umožní ovládat směs na scéně tím, že zvýrazníte některé zdroje a zvýrazníte jiné.

### <a name="adjust-distance-based-attenuation"></a>Úprava zeslabení na základě vzdálenosti
Zvukový procesor, který je poskytováný modulem plug- **in Unity spatializer** , dodržuje odkládání na dálku, které jsou součástí editoru Unity. Ovládací prvky pro odkládání na dálku jsou součástí **zdrojové zvukové** komponenty, která se nachází na panelu inspektora zvukových zdrojů, v části **Nastavení 3D zvuku**:

![Snímek obrazovky s panelem možností pro zeslabení vzdálenosti Unity](media/distance-attenuation.png)

Akustické a vychází z umístění přehrávače v poli "oblasti simulace" na střed. Pokud je zdroj zvuku z přehrávače, který je umístěný mimo tuto oblast simulace, ovlivní pouze geometrie v rámci tohoto pole šíření zvuku (například způsobující překrytí), které funguje přiměřeně i v případě, že occluders jsou v okolí přehrávače. V případech, kdy je hráč v otevřeném prostoru, ale occluders se blíží zdroji vzdáleného zvuku, se může stát, že se zvuk stane nerealistickému disoccluded. Náš doporučený postup je zajistit, aby se v takových případech odložení zvuku nesměroval na 0 v přibližně 45 m, výchozí vodorovná vzdálenost přehrávače k okraji boxu.

![Snímek obrazovky s panelem možností SpeakerMode Unity](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Upravit překrytí a přenos
Připojení skriptu **AcousticsAdjust** ke zdroji povoluje parametry ladění pro tento zdroj. Pokud chcete skript připojit, klikněte na **Přidat komponentu** v dolní části panelu **inspektora** a přejděte ke **skriptům > úpravy akustického**množství. Skript má šest ovládacích prvků:

![Snímek obrazovky s AcousticsAdjust skriptem Unity](media/acoustics-adjust.png)

* **Povolit akustické** – určuje, jestli se na tento zdroj aplikují akustické akustické množství. Pokud není zaškrtnuto, bude zdroj prostorový s HRTFs nebo posouváním, ale nebudou žádné akustické. To znamená, že nejsou k dispozici žádné překážky, překrytí nebo dynamické parametry reverberation, jako je například úroveň a Decay. Reverberation se pořád aplikuje s pevnou úrovní a časem Decay.
* **Překrytí** – aplikujte násobitel na úroveň překrytí DB vypočítanou v systému akustického množství. Pokud je tento násobitel větší než 1, překrytí se exaggerated, zatímco hodnoty menší než 1 způsobí větší význam překrytí a hodnota 0 zakáže překrytí.
* **Přenos (DB)** – nastaví zeslabení odkládání (v dB) z důvodu přenosu prostřednictvím geometrie. Nastavením tohoto posuvníku na nejnižší úroveň zakážete přenos. Akustické spatializes počáteční suchý zvuk, který se dorazí kolem geometrie scény (portáling). Přenos poskytuje další suché doručení, které je v rámci směru pohledu na pohled. Všimněte si, že křivka zeslabení vzdálenosti pro zdroj je také použita.

### <a name="adjust-reverberation"></a>Upravit reverberation
* **Wetness (DB)** – upravuje reverb napájení v databázi podle vzdálenosti od zdroje. Kladné hodnoty vytvářejí zvuk větší reverberant, zatímco záporné hodnoty povedou k lepšímu vysušení. Kliknutím na ovládací prvek křivka (zelená čára) zobrazte Editor křivky. Upravte křivku tak, že levým tlačítkem myši kliknete na Přidat body a přetáhnete tyto body, abyste mohli vytvořit požadovanou funkci. Osa x je vzdálenost od zdroje a osa y je reverbou úpravou v databázi. Další informace o úpravách křivek najdete v této [příručce k Unity](https://docs.unity3d.com/Manual/EditingCurves.html). Pokud chcete obnovit výchozí křivku zpátky, klikněte pravým tlačítkem na **Wetness** a vyberte **resetovat**.
* **Časové měřítko Decay** – upravuje násobitel pro Decay čas. Pokud například výsledek zanesli určuje Decay čas 750 milisekund, ale tato hodnota je nastavená na 1,5, čas Decay použití na zdroj bude 1 125 milisekund.
* **Provozní dveře** – doplňková úprava na odhadu toho, jak "venku" reverberation na zdroji by měl zvuk. Když tuto hodnotu nastavíte na 1, bude zdroj vždy zvuk zcela venku, ale při jeho nastavení na hodnotu-1 dojde k úplnému zastavení zdrojového zvuku.

Připojení skriptu **AcousticsAdjustExperimental** ke zdroji umožní další experimentální parametry ladění pro tento zdroj. Pokud chcete skript připojit, klikněte na **Přidat komponentu** v dolní části panelu **inspektora** a přejděte ke **skriptům > akustické a upravte experimentální**. V současné době je k dispozici jeden experimentální ovládací prvek:

![Snímek obrazovky s AcousticsAdjustExperimental skriptem Unity](media/acoustics-adjust-experimental.png)

* **Perceptuální** . deformace na dálku – aplikujte exponenciální pokřivení na vzdálenost, která se používá k výpočtu poměru suchého vlhku. Systém akustické propustnosti vypočítá vlhké úrovně v celém prostoru, které se škálují plynule se vzdáleností a poskytují až Perceptuální vzdáleností. Pokřivení hodnot větší než 1 exaggerate tímto účinkem zvýšením úrovně reverberation na dálku, čímž se zavede zvuk "Vzdálená". Rozkládání hodnot menší než 1 zjednodušuje reverberationou změnu na základě vzdálenosti, takže zvuk bude větší.

## <a name="design-acoustics-for-all-sources"></a>Vytváření akustických prostředků pro všechny zdroje
Chcete-li upravit parametry pro všechny zdroje, klikněte na kanál kanálu v **směšovači zvuku**Unity a upravte parametry na efektu **směšovače akustického projektu** .

![Snímek obrazovky s panelem nástrojů přizpůsobení směšovače Unity v projektu](media/mixer-parameters.png)

* **Wetness upravit** – upraví reverb výkon v databázi napříč všemi zdroji ve scéně na základě vzdálenosti zdrojového a naslouchacího procesu. Kladné hodnoty vytvářejí zvuk větší reverberant, zatímco záporné hodnoty povedou k lepšímu vysušení.
* **RT60** multiplikativní pro horizontální navýšení kapacity pro reverbou dobu.
* **Použít posouvání** – určuje, jestli je zvuk ve výstupu binaural (0) nebo vícekanálové posouvání (1). Jakákoli hodnota kromě 1 označuje binaural. Výstup binaural je prostorový s HRTFs pro použití s využitím sluchátek a vícekanálového výstupu je prostorový s VBAP pro použití se vícekanálovými systémy pro reproduktory Surround. Pokud používáte vícekanálový posouvací modul, nezapomeňte vybrat režim mluvčího, který odpovídá vašemu nastavení zařízení, nalezeno v **Nastavení** > projektu**zvuk**.

## <a name="check-proper-sound-source-placement"></a>Kontrolovat správné umístění zdroje zvuku
Zdroje zvuku umístěné uvnitř obsazené voxels nebudou mít akustickou expozici. Vzhledem k tomu, že voxels rozšířila po viditelné geometrii scény, je možné umístit zdroj do Voxel, zatímco se zobrazuje unoccluded pomocí vizuální geometrie. Pomocí zaškrtávacího políčka Voxel Grid v nabídce **gizma** v pravém horním rohu zobrazení **scény** můžete zobrazit akustické voxelsy projektu.

![Snímek nabídky Gizma Unity](media/gizmos-menu.png)  

Zobrazení Voxel můžete také použít k určení, zda se na komponenty vizuálů ve hře aplikuje transformace. Pokud ano, použijte stejnou transformaci na GameObject hostující **správce akustického**prostředí.

### <a name="bake-time-vs-run-time-voxels"></a>Zanesli čas a voxels běhu
V okně editoru je možné zobrazit voxels v době návrhu hry a v okně hry za běhu. Velikost voxels se v těchto zobrazeních liší. Důvodem je to, že interpolace akustického běhu používá jemnější Voxel mřížku pro výsledky hladší interpolace. Umístění zdroje zvuku by se mělo ověřit pomocí voxels modulu runtime.

Voxels doby návrhu:

![Snímek obrazovky s akustickými voxelsy projektu během doby návrhu](media/voxels-design-time.png)

Běhové voxels:

![Snímek obrazovky s akustickými voxelsy projektu během běhu](media/voxels-runtime.png)

## <a name="next-steps"></a>Další postup
* Prozkoumat případové studie zvýraznění konceptů za [procesem návrhu](design-process.md)

