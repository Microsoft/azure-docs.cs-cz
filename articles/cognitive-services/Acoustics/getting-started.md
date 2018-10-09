---
title: Začínáme se službou projektu Akustika – Cognitive Services
description: Této úvodní příručky obsahuje pokyny k integraci modulu plug-in ve vašem Unity projektu, zanést scéně a použít Akustika zdrojů zvuku.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: f07fa60555f1bf866d39cb69c4824cfd93dc54f8
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869441"
---
# <a name="getting-started-with-project-acoustics"></a>Začínáme s Akustika projektu
Této úvodní příručky obsahuje pokyny k integraci modulu plug-in ve vašem Unity projektu, zanést scéně a použít Akustika zdrojů zvuku. Pro účely tohoto rychlého startu je potřeba nejprve vytvořit [účet Azure batch](create-azure-account.md). Tento průvodce to předpokládá některé znalost Unity.

## <a name="download-the-plugin"></a>Stáhnout modul plug-in
Zaregistrujte [tady](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) získat verzi Preview návrháře.

## <a name="supported-platforms-for-quickstart"></a>Podporované platformy pro rychlý start
* [Unity 2018.2 +](http://www.unity3d.com)
  * Vyžaduje nastavení projektu **.NET 4.x ekvivalent** skriptování verze modulu runtime 
  * Vyžaduje založené na Windows Unity editoru

## <a name="import-the-plugin"></a>Import modulu plug-in
Importujte Akustika UnityPackage do projektu. 
* V Unity, přejděte na **prostředky > Importovat balíček > vlastní balíček...**

![Importovat balíček](media/ImportPackage.png)  

* Zvolte **MicrosoftAcoustics.unitypackage**

Pokud importujete modul plug-in do existujícího projektu, váš projekt už můžete mít **mcs.rsp** soubor v kořenové složce projektu, který určuje možnosti kompilátoru C#. Bude potřeba sloučit obsah tohoto souboru se souborem mcs.rsp, která se dodává s modulem plug-in Akustika projektu.

## <a name="enable-the-plugin"></a>Povolení modulu plug-in
Která má označení vytvoření část toolkit Akustika vyžaduje verzi skriptovací modul runtime .NET 4.x. Import balíčku se aktualizovat nastavení Unity Playeru. Restartujte Unity pro toto nastavení se projeví.

![Nastavení přehrávače](media/PlayerSettings.png)

![ROZHRANÍ .NET 4.5](media/Net45.png)

## <a name="create-a-navigation-mesh"></a>Vytvoření sítě navigace
Použít standardní [pracovního postupu Unity](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html) vytvořit navigační sítě pro váš projekt. Informace o tom, jak použít vlastní mřížek navigace, najdete v článku [která má označení vytvoření uživatelského rozhraní provede](bake-ui-walkthrough.md).

## <a name="mark-static-meshes-for-acoustics"></a>Označit statické mřížek pro Akustika
Otevřete pomocí okna Akustika **okna > Akustika** v Unity. Toto okno lze ukotvit vedle inspektor.

![Otevřít Akustika okna](media/WindowAcoustics.png)

V okně hierarchie Unity a zrušit výběr všech vybraných položek. V Akustika **objekt** kartě klikněte na zaškrtávací políčko "Akustika geometrie" a označit všechny mřížky a terénů ve scéně jako Akustika geometry.

Na **materiály** kartu, přiřadit akustický materiály materiálech ve scéně. **Výchozí** materiálu má absorpční ekvivalentní konkrétní. Další informace o zadání své vlastní vlastnosti materiály najdete v článku [stránky pro návrh procesu](design-process.md).

![Karta materiály](media/MaterialsTab.png)

## <a name="preview-the-probes"></a>Ve verzi Preview sond
Na **sondy** klikněte na tlačítko **Calculate**. Tento výpočet může trvat několik minut v závislosti na velikosti scény. Po dokončení výpočtu, zobrazí se vám s plovoucí desetinnou čárkou oblastech v režimu scény, které označení míst pro simulaci Akustika nazývá "probe body". Pokud se zobrazí v okně scény blízko k objektu, zobrazí se také voxelization scény. Zelená voxels by měla s objekty, které jste označili jako geometrie řádek nahoru. Test body a zobrazí voxel můžou být v nabídce Gizma v horní části napravo od scény.

![GizmosPreview](media/BakePreviewWithGizmos.png)

## <a name="bake-the-scene"></a>Vytvoření scény.
V **zanést** kartu, zadejte přihlašovací údaje Azure a klikněte na tlačítko **zanést**. Pokud nemáte účet Azure Batch, najdete v článku [tento návod pro nastavení našich doporučených účtu](create-azure-account.md).
Po dokončení která má označení vytvoření datového souboru se automaticky stáhnou do **prostředky/AcousticsData** adresáře ve vašem projektu.

## <a name="set-up-audio-runtime-dsp"></a>Nastavit DSP zvuku runtime
Vložení zvuku runtime DSP pro Akustika v Unity a spatializer framework jsme ji integrovat s na základě HRTF zvuk. Chcete-li povolit zpracování Akustika, přepněte **Acoustics Microsoft** spatializer tak, že přejdete do **Upravit > Nastavení projektu > Zvuk**a vyberte **Microsoft Acoustics** jako **modulu plug-in Spatializer** pro váš projekt. Také se ujistěte, že **velikost vyrovnávací paměti DSP** je nastavena na výkon.

![Nastavení projektu](media/ProjectSettings.png)  

![Spatializer Akustika projektu](media/ChooseSpatializer.png)

Otevřít zvukový Mixer (**okna > zvuku Mixer**). Ujistěte se, že máte alespoň jeden Mixer, se v jedné skupině. Pokud ne, klikněte na tlačítko "+" napravo od **míchání**. Klikněte pravým tlačítkem na konec kanálu pruhu v části efekty a přidejte **Microsoft Akustika Mixer** vliv. Všimněte si, že je podporován pouze jeden projekt Akustika Mixer najednou.

![Zvukový Mixer](media/AudioMixer.png)

## <a name="set-up-the-acoustics-lookup-table"></a>Nastavit Akustika vyhledávací tabulky
Přetáhnout myší **Microsoft Acoustics** prefab z panelu Projekt do vaše Scéna:

![Akustika Prefab](media/AcousticsPrefab.png)

Klikněte na **ProjectAcoustics** hru objektu a přejděte do jeho inspector panelů. Zadejte umístění, která má označení vytvoření výsledků (). ACE souboru, v **prostředky/AcousticsData**) pádem a přetáhněte ji do skriptu Akustika správce, nebo kliknutím na tlačítko kroužek vedle textového pole.

![Správce Akustika](media/AcousticsManager.png)  

## <a name="apply-acoustics-to-sound-sources"></a>Použít Akustika zdrojů zvuku
Vytvoření zdroje zvuku. Klikněte na zaškrtávací políčko v dolní části AudioSource inspector panelů, s upozorněním **Spatialize**. Ujistěte se, že **prostorových Blendu** je nastavena na úplnou 3D.  

![Zdroje zvuku](media/AudioSource.png)

## <a name="apply-post-bake-design"></a>Použití po která má označení vytvoření návrhu
Můžete připojit skript **AcousticsAdjust** ke zdroji zvuku ve scéně umožňující parametrů návrhu další zdroje, kliknutím na **přidat součást** a zvolíte **skripty > Akustika Upravit**:

![AcousticsAdjust](media/AcousticsAdjust.png)

Existují také parametry na **Microsoft Akustika Mixer**. Další informace o návrhu po která má označení vytvoření najdete v tématu [návrh parametry](design-process.md).

## <a name="next-steps"></a>Další postup
* Zkuste [ukázková Scéna](sample-walkthrough.md)
* Další informace o úplnou sadu [zanést funkce](bake-ui-walkthrough.md)
* Prozkoumat podrobnější [návrh parametry](design-process.md)

