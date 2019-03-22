---
title: Rychlý start Akustika projektu pomocí Unreal
titlesuffix: Azure Cognitive Services
description: Použijete ukázkový obsah, Experimentujte s projektu Akustika ovládací prvky návrhu v Unreal a Wwise a nasazení pro Windows Desktop.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 1314e393d292145ef112e700abf6ab1ef199db7d
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138181"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Rychlý start Unreal/Wwise Akustika projektu
V tomto rychlém startu jste budete experimentovat s projektu Akustika návrh ovládacích prvků pomocí poskytnutý ukázkový obsah pro Unreal Engine a Wwise.

Požadavky na software:
* [Unreal Engine 4.21](https://www.unrealengine.com/)
* [Wwise 2018.1.6](https://www.audiokinetic.com/products/wwise/)

## <a name="download-the-sample-package"></a>Stáhněte si balíček ukázky
Stáhněte si [projektu Akustika Unreal + Wwise ukázkový balíček](http://www.microsoft.com/downloads/details.aspx?FamilyID=f03dff5a-5780-462e-87ef-e6d039d0748d). Ukázkový balíček obsahuje projekt Unreal Engine, projekt Wwise pro Unreal projektu a projekt Akustika Wwise modulu plug-in.

## <a name="set-up-the-project-acoustics-sample-project"></a>Nastavte si ukázkový projekt Akustika projektu
Nastavení projektu Akustika Unreal/Wwise ukázkového projektu, musíte nejdřív nainstalovat modul plug-in Akustika projektu do Wwise. Pak nasaďte binární soubory Wwise Unreal projektu a upravit Wwise Unreal modulu plug-in pro podporu Akustika projektu.

### <a name="install-the-project-acoustics-wwise-plugin"></a>Instalace modulu plug-in Wwise Akustika projektu
Otevřete Wwise spouštěče a pak v **moduly plug-in** ve skupině **nainstalovat nové moduly plug-in**vyberte **přidat z adresáře**. Zvolte `AcousticsWwisePlugin\ProjectAcoustics` adresář, který je zahrnutý v balíčku, který jste stáhli.

![Instalace modulu plug-in Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Přidání binárních souborů Wwise do projektu Akustika Unreal ukázkového projektu
Ze Spouštěče Wwise, klikněte na tlačítko **Unreal Engine** kartu a potom klikněte na "hamburgerové" nabídky vedle **posledních projektů Unreal Engine** a vyberte **vyhledat projektu**. Otevřete ukázkový projekt Unreal `.uproject` souborů v balíčku `AcousticsSample\AcousticsGame\AcousticsGame.uproject`.

![Karta Wwise Unreal](media/wwise-unreal-tab.png)

Potom vedle projektu Akustika ukázkového projektu a klikněte na tlačítko **integrovat Wwise v projektu**.

![Projekt hry Unreal Wwise Akustika](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Rozšíření funkcí na Wwise Unreal modulu plug-in
Projekt Akustika Unreal plugin vyžaduje další chování být zveřejněna před modul plug-in Wwise Unreal rozhraní API. Spusťte dávkový soubor součástí projektu Akustika Unreal modulu plug-in pro automatizaci tyto změny:
* Uvnitř `AcousticsGame\Plugins\ProjectAcoustics\Resources`spuštěním `PatchWwise.bat`.

    ![Oprava Wwise skriptu](media/patch-wwise-script.png)

* Pokud nemáte nainstalované rozhraní DirectX SDK, budete muset zakomentovali řádek obsahující DXSDK_DIR v `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![DXSDK komentář](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Otevřete Unreal projekt. 
Budete dotázáni, můžete nově sestavit moduly; Klikněte na tlačítko Ano.

Pokud otevřete projekt na selhání sestavení selže, zkontrolujte, že jste nainstalovali modul plug-in Wwise Akustika projektu na stejnou verzi Wwise používá v projektu vzorku Akustika projektu.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experimentujte s ovládacími prvky návrhu Akustika projektu
Jak scéně podle názvu dalo čekat po kliknutí na tlačítko Přehrát v editoru Unreal naslouchat. Ve stolním počítači, použijte W, A, S, D a myší uspořádat jinak. Pokud zobrazit klávesové zkratky dalších možností ovládání, stiskněte klávesu **F1**. Tady jsou některé aktivity návrhu vyzkoušet:

### <a name="modify-occlusion-and-transmission"></a>Upravit uzavření a přenosu
Existují-source projektu Akustika návrh ovládacích prvků na každého herce Unreal zvuku:

![DemoSceneSoundSourceDesignControls](media/demo-scene-sound-source-design-controls.png)

Pokud **uzavření** multiplikátor je větší než 1 (výchozí hodnota je 1), bude možné exaggerated uzavření. Nastavení je méně než 1 je vhodné projeví složitější.

Chcete-li povolit přenos přes wall, přesuňte **přenosu (databáze)** posuvník vypnuto jeho nejnižší úroveň. 

### <a name="modify-wetness-for-a-source"></a>Upravit wetness zdroje
Chcete-li změnit, jak rychle mění wetness vzdálenost, použijte **Percepční Warp vzdálenost**. Projekt Akustika vypočítá vlhkou úrovně v rámci místo z simulace, které hladce lišit podle vzdálenosti a poskytují Percepční vzdálenost pomůcky. Podle zvyšující se vzdáleností související vlhkou úroveň zvětšení warp vzdálenost zvýrazní tohoto efektu. Pokřivení hodnoty menší než 1 provést na základě vzdálenosti reverberation změnit složitější. Také je možné upravit tento efekt podrobně citlivější úpravou **Wetness (databáze)**.

Prodloužit dobu decay v celém prostoru úpravou **Decay časové měřítko**. Vezměte si situaci, kdy výsledek simulace je doba decay 1,5 s. Nastavení **Decay časové měřítko** 2 způsobí decay čas použití ke zdroji 3 s.

### <a name="modify-distance-based-attenuation"></a>Upravit na základě vzdálenosti zeslabení
Modul plug-in mixer projektu Akustika Wwise respektuje integrovaným Wwise na základě vzdálenosti zeslabení-source. Tato křivka se změna úrovně suchého path. Modul plug-in Akustika projektu upraví vlhkou úroveň chcete-li udržovat wet suchého poměru určeném simulace a návrh ovládacích prvků.

![DemoSoundsAttenuation](media/demo-sounds-attenuation.png)

Projektu Akustika provádí výpočet v poli "simulace oblasti" zaměřená na každé simulované player umístění. Akustika prostředky v ukázkového balíčku byly vloženými s protokolem radius simulace oblasti 45 m a attenuations byly navržené tak, aby 0 před 45 m. Zatímco tato slabá intenzita není striktní požadavek, představuje výstrahou, že pouze geometrie v rámci 45 milion naslouchacího procesu se occlude zvuky.

## <a name="next-steps"></a>Další postup
* [Integrace Akustika projektu](unreal-integration.md) modulu plug-in do projektu Unreal
* [Vytvoření účtu Azure](create-azure-account.md) pro vlastní vypalování


