---
title: Rychlý Start akustického projektu pomocí Unreal
titlesuffix: Azure Cognitive Services
description: Pomocí ukázkového obsahu můžete experimentovat s ovládacími prvky návrhu pro práci s projekty v Unreal a Wwise a nasazovat na desktopovou plochu Windows.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242927"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Projekt akustické Unreal/Wwise rychlý Start
V tomto rychlém startu budete experimentovat s ovládacími prvky návrhu s akustickým obsahem projektu pomocí ukázkového obsahu pro modul Unreal a Wwise.

Požadavky na software pro použití ukázkového obsahu:
* [Unreal Engine](https://www.unrealengine.com/) 4,22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Stažení ukázkového balíčku
Stáhněte si [ukázkový balíček projekt akustické Unreal a Wwise](https://www.microsoft.com/download/details.aspx?id=58090). Vzorový balíček obsahuje:
- Projekt modulu Unreal
- Projekt Wwise pro projekt Unreal
- Wwise modul plug-in projekt akustického navýšení

## <a name="set-up-the-project-acoustics-sample-project"></a>Nastavení projektu Ukázka akustického projektu
Nejdřív nainstalujte do Wwise modul plug-in projekt akustického navýšení. Dále nasaďte binární soubory Wwise do projektu Unreal. Pak upravte modul plug-in Wwise Unreal tak, aby podporoval akustické projekty.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Instalace modulu plug-in pro projekt akustické Wwisey
Otevřete spouštěč Wwise. Na kartě **moduly plug** **-in v části instalovat nové moduly plug-in**vyberte **Přidat z adresáře**. Vyberte adresář *AcousticsWwisePlugin\ProjectAcoustics* , který jste zahrnuli do balíčku, který jste stáhli.

![Možnost instalace modulu plug-in Wwise ve Spouštěči Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Přidání binárních souborů Wwise do ukázkového projektu akustické Unrealy projektu
1. Ve Spouštěči Wwise vyberte kartu **modul Unreal** . 
1. Vyberte nabídku hamburgerovou "(ikona) vedle **posledních projektů Unreal Engine**a pak vyberte **vyhledat projekt**. Otevřete vzorový soubor Unreal projektu *. uproject* v balíčku *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![Karta Unreal ve Spouštěči Wwise](media/wwise-unreal-tab.png)

3. Vedle projektu Ukázka akustického projektu vyberte možnost **integrovat Wwise do projektu**.

   ![Spouštěč Wwise zobrazuje projekt Unreal herních her s zvýrazněnou možností integrace.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Rozšiřování funkcí modulu plug-in Wwise Unreal
Modul plug-in Unrealí projektu potřebuje další chování vystavené z rozhraní API modulu plug-in Wwise Unreal. Spusťte dávkový soubor, který byl dodán s modulem plug-in pro projekt akustické Unrealy pro automatizaci těchto úprav.
* V *AcousticsGame\Plugins\ProjectAcoustics\Resources*spusťte *PatchWwise. bat*.

    ![Skript pro opravu projektu Wwise, který je zvýrazněný v okně Průzkumníka Windows](media/patch-wwise-script.png)

* Pokud sadu DirectX SDK nemáte nainstalovanou: v závislosti na verzi Wwise, kterou používáte, může být potřeba odkomentovat řádek, který obsahuje `DXSDK_DIR` v *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Editor kódu zobrazující komentář "DXSDK"](media/directx-sdk-comment.png)

* Pokud kompilujete pomocí sady Visual Studio 2019: Chcete-li se vyhnout chybě propojení s Wwise, ručně změňte výchozí hodnotu *VSVersion* v *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* na **vc150**:

    ![Editor kódu s VSVersion se změnil na "vc150".](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Otevřete projekt Unreal 
Když otevřete projekt Unreal, zobrazí se výzva k opětovnému sestavování modulů. Vyberte **Ano**.

Pokud otevření projektu z důvodu selhání sestavení neproběhne úspěšně, ověřte, že jste nainstalovali modul plug-in Wwisey projektu na stejnou verzi Wwise, jakou jste použili v projektu Ukázka akustického projektu.

Pokud používáte verzi [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) starší než 2019,1, nemůžete generovat zvukové banky pomocí projektu Ukázka akustického projektu. Musíte do ukázkového projektu integrovat Wwise verze 2019,1.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experiment s ovládacími prvky návrhu projektů s akustickými vazbami
Vyberte tlačítko Přehrát v editoru Unreal a naslouchat tak, jak scény. Pomocí kláves W, A, S a D a myši můžete pohybovat. Chcete-li zobrazit klávesové zkratky pro další ovládací prvky, vyberte F1.

Následující informace popisují některé aktivity návrhu, které je potřeba vyzkoušet.

### <a name="modify-occlusion-and-transmission"></a>Upravit překrytí a přenos
Existují ovládací prvky návrhu pro každý zdrojový projekt v každém Unreal Sound actor.

![Editor Unrealy akustické ovládací prvky návrhu](media/demo-scene-sound-source-design-controls.png)

Pokud je násobitel **překrytí** větší než 1 (výchozí hodnota je 1), překrytí je exaggerated. Nastavení menší než 1 způsobí, že překrytí efekt bude jemnější.

Pokud chcete povolit přenos přes zeď, přesuňte posuvník **přenos (DB)** směrem od jeho nejnižší úrovně.

### <a name="modify-wetness-for-a-source"></a>Upravit wetness pro zdroj
Pokud chcete změnit, jak rychle wetness změny pomocí vzdálenosti, použijte **Perceptuální na dálkovou osnovu**. Akustické projekty počítají vlhké úrovně během celého prostoru prostřednictvím simulace. Úrovně se v různých odstupech škálují plynule a poskytují až Perceptuální vzdáleností. Pokud chcete tento efekt exaggerate, zvyšte úroveň vlhkého rozsahu, aby se zvýšila velikost pokřivení vzdálenosti. Rozkládání hodnot menší než 1 vede ke změně reverberation na dálku. Pomocí nastavení **Wetness (DB)** můžete v tomto efektu taky dělat jemnější úpravy.

Pokud chcete zvýšit Decay dobu v celém prostoru, upravte **časový rozsah Decay**. Vezměte v úvahu případ, kdy je výsledkem simulace decayý čas 1,5 sekund. Nastavení **Decay časového rozsahu** na 2 má za následek Decayou dobu 3 sekund, která se pro zdroj nastavila.

### <a name="modify-distance-based-attenuation"></a>Úprava zeslabení na základě vzdálenosti
Modul plug-in Wwise mixer má za následek odkládání na dálku, která je integrovaná do Wwise. Změna této křivky změní úroveň suché cesty. Modul plug-in projekt akustického množství upraví úroveň mokrého přístupnosti za účelem zachování kombinace mokrého/suchého řízení určeného ovládacími prvky simulace a návrhu.

![Panel křivky pro zeslabení Wwise ukazující odútlumování, které se před hranicí simulace přechází na 0](media/demo-sounds-attenuation.png)

V poli "simulace", která se nachází na střed, se vypočítají akustické a neočekávané umístění. Akustické prostředky v ukázkovém balíčku byly vloženými s poloměrem oblasti simulace 45 měřičů. Odútlumy byly navrženy tak, aby klesly na 0 před 45 měřičů. I když tato intenzita není přísným požadavkem, nese upozornění, že pouze geometrie v 45 metrech naslouchacího procesu bude occlude zvuky.

## <a name="next-steps"></a>Další kroky
* Integrujte do projektu Unreal modul plug-in [akustického projektu](unreal-integration.md) .
* [Vytvořte si účet Azure](create-azure-account.md) pro vlastní vytváří.
