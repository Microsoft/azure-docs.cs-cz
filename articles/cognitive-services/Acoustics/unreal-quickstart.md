---
title: Rychlý Start akustického projektu pomocí Unreal
titlesuffix: Azure Cognitive Services
description: Pomocí ukázkového obsahu můžete experimentovat s ovládacími prvky návrhu projektů v Unreal a Wwise a nasazovat je do desktopu Windows.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 927ce403130460c302f546038ff3a0c3a16e0368
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933014"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Projekt akustické Unreal/Wwise rychlý Start
V tomto rychlém startu budete experimentovat s ovládacími prvky návrhu s akustickým obsahem projektu pomocí poskytnutého ukázkového obsahu pro modul Unreal a Wwise.

Požadavky na software pro použití ukázkového obsahu:
* [Unreal Engine](https://www.unrealengine.com/) 4,22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Stažení ukázkového balíčku
Stáhněte si [vzorový balíček Unreal + Wwise s akustickým projektem](https://www.microsoft.com/download/details.aspx?id=58090). Vzorový balíček obsahuje projekt Unreal Engine, projekt Wwise pro tento projekt Unreal a modul plug-in, který je v projektu akustický.

## <a name="set-up-the-project-acoustics-sample-project"></a>Nastavení projektu Ukázka akustického projektu
Pokud chcete nastavit projekt s ukázkovým projektem Unreal/Wwise, musíte nejdřív nainstalovat modul plug-in akustického projektu do Wwise. Pak nasaďte binární soubory Wwise do projektu Unreal a upravte modul plug-in Wwise Unreal tak, aby podporovaly akustické projekty.

### <a name="install-the-project-acoustics-wwise-plugin"></a>Instalace modulu plug-in Wwiseů projektu
Spusťte spouštěč Wwise a pak na kartě **moduly plug-** in v části **instalovat nové moduly plug-in**vyberte **Přidat z adresáře**. `AcousticsWwisePlugin\ProjectAcoustics` Vyberte adresář, který byl zahrnut do balíčku, který jste stáhli.

![Snímek obrazovky s Wwise spouštěčem s možností instalovat modul plug-in Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Přidání binárních souborů Wwise do ukázkového projektu akustické Unrealy projektu
Z Wwise spouštěče klikněte na kartu **modul Unreal** , potom klikněte na nabídku hamburgerovou "vedle položky **Poslední projekty Unreal Engine** a vyberte **Procházet pro projekt**. Otevřete vzorový soubor projektu `.uproject` Unreal v balíčku. `AcousticsSample\AcousticsGame\AcousticsGame.uproject`

![Snímek obrazovky s kartou Unreal spouštěče Wwise](media/wwise-unreal-tab.png)

Potom klikněte vedle projektu Ukázka akustického projektu na možnost integrovat **Wwise do projektu**.

![Snímek obrazovky s Wwise spouštěčem ukazující Unreal projekt se zvukovými hrami](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Rozšiřování funkcí Wwise modulů plug-in Unreal
Modul plug-in Unrealů projektu vyžaduje dodatečné chování z rozhraní API modulu plug-in Wwise Unreal. Spuštěním dávkového souboru poskytnutého modulem plug-in Unreal projektu akustické a automatizace těchto úprav:
* Uvnitř `AcousticsGame\Plugins\ProjectAcoustics\Resources`spusťte .`PatchWwise.bat`

    ![Snímek obrazovky okna Průzkumníka Windows zobrazující skript k opravě projektu Wwise](media/patch-wwise-script.png)

* Pokud sadu DirectX SDK nemáte nainstalovanou, v závislosti na verzi Wwise, kterou používáte, může být potřeba odkomentovat řádek, který obsahuje `DXSDK_DIR`: `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Snímek obrazovky editoru kódu zobrazující DXSDK s komentářem](media/directx-sdk-comment.png)

* Pokud kompilujete pomocí sady Visual Studio 2019, chcete-li s Wwise pracovat s chybou propojení, ručně `VSVersion` upravte výchozí `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs` hodnotu `vc150`v nástroji na:

    ![Snímek obrazovky editoru kódu zobrazující VSVersion změněné na vc150](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Otevřete projekt Unreal. 
Budete vyzváni k opětovnému sestavení modulů; klikněte na tlačítko Ano.

>Pokud při selhání sestavení dojde k chybě při otevírání projektu, ověřte, že jste nainstalovali modul plug-in Wwise pro projekt na stejnou verzi Wwise použitou v projektu Ukázka akustického projektu.

>Pokud používáte verzi [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) starší než 2019,1, nebudete moci generovat zvukové banky s ukázkovým projektem projektu s akustickými objemy.  Pro správné použití je nutná integrace Wwise verze 2019,1 do ukázkového projektu.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experiment s ovládacími prvky návrhu projektů s akustickými vazbami
Kliknutím na tlačítko Přehrát v editoru Unreal nahrajte, jak se scéna nachází. V desktopu můžete pohybovat pomocí W, A, S, D a myši. Pokud zobrazit klávesové zkratky dalších možností ovládání, stiskněte klávesu **F1**. Tady jsou některé aktivity návrhu, které byste mohli vyzkoušet:

### <a name="modify-occlusion-and-transmission"></a>Upravit překrytí a přenos
Existují ovládací prvky návrhu pro každý zdrojový projekt v každém Unreal Sound actor:

![Snímek obrazovky s ovládacími prvky návrhu Unreal Editor](media/demo-scene-sound-source-design-controls.png)

Pokud je násobitel **překrytí** větší než 1 (výchozí hodnota je 1), překrytí bude exaggerated. Nastavení menší než 1 způsobí, že překrytí efekt bude jemnější.

Pokud chcete povolit přenos přes zeď, přesuňte posuvník **přenos (DB)** od nejnižší úrovně. 

### <a name="modify-wetness-for-a-source"></a>Upravit wetness pro zdroj
Chcete-li změnit, jak rychle wetness změny pomocí vzdálenosti, použijte **Perceptuální**. Akustické a projektové prostředky počítají vlhké úrovně v celém prostoru z simulace, která se škáluje plynule se vzdáleností a poskytují neperceptuální upozornění na dálku. Zvýšením úrovně vlhkého nárůstu vzdálenosti exaggerates tím, že se zvýší úroveň mokrého vztahu na dálku. Rozkládání hodnot menší než 1 zjednodušuje reverberationou změnu na dálku. Tento efekt se dá také upravit v přesnější podrobnostech úpravou **Wetness (DB)** .

Zvyšte Decay dobu v celém prostoru úpravou **časového měřítka Decay**. Vezměte v úvahu případ, kdy je výsledkem simulace Decay čas 1,5 s. Nastavení **Decay doby trvání** na 2 bude mít za následek Decay čas, který se použije na zdroj 3 s.

### <a name="modify-distance-based-attenuation"></a>Úprava zeslabení na základě vzdálenosti
Modul plug-in Wwise mixer má za následek odkládání na dálku v Wwise. Změnou této křivky dojde ke změně úrovně suchého umístění. Modul plug-in akustického projektu upraví úroveň mokrého míchání tak, aby udržovala mokrou směs určenou ovládacími prvky pro simulaci a návrh.

![Snímek obrazovky s panelem křivky zeslabení Wwise s odútlumem, který před hranicí simulace přechází na nulu](media/demo-sounds-attenuation.png)

Akustické projekty jsou výpočty v políčku simulace oblasti uprostřed kolem každého simulovaného umístění přehrávače. Akustické prostředky v ukázkovém balíčku byly vloženými s poloměrem oblasti simulace 45 m a jejich útlumy byly navrženy tak, aby klesly na 0 před 45 m. I když tato intenzita není přísným požadavkem, dojde k upozornění, že occlude zvuky pouze geometrie v rámci 45 m.

## <a name="next-steps"></a>Další postup
* Integrujte modul plug-in [akustického projektu](unreal-integration.md) do projektu Unreal
* [Vytvoření účtu Azure](create-azure-account.md) pro vlastní vypalování


