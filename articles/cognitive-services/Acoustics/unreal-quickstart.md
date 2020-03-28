---
title: Rychlý start aplikace Project Acoustics s nereálným
titlesuffix: Azure Cognitive Services
description: Ukázkový obsah můžete použít k experimentování s ovládacími prvky návrhu aplikace Project Acoustics v aplikacích Unreal a Wwise a k nasazení na plochu systému Windows.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72242927"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Rychlý start projektu Akustika Unreal/Wwise
V tomto rychlém startu budete experimentovat s ovládacími prvky návrhu akustiky projektu pomocí ukázkového obsahu pro Unreal Engine a Wwise.

Požadavky na software pro použití ukázkového obsahu:
* [Neskutečný motor](https://www.unrealengine.com/) 4.22
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Stáhněte si ukázkový balíček
Stáhněte si [ukázkový balíček Project Acoustics Unreal a Wwise](https://www.microsoft.com/download/details.aspx?id=58090). Vzorové balení obsahuje:
- Projekt Unreal Engine
- Projekt Wwise pro projekt Unreal
- Modul plug-in Project Acoustics Wwise

## <a name="set-up-the-project-acoustics-sample-project"></a>Nastavení ukázkového projektu Akustika
Nejprve nainstalujte modul plug-in Project Acoustics do wwise. Dále nasadit Wwise binární soubory unreal projektu. Potom upravte modul plug-in Wwise Unreal pro podporu akustiky projektu.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Instalace modulu plug-in Project Acoustics Wwise
Otevřete Wwise Launcher. Na kartě **Pluginy** včásti **Instalovat nové moduly plug-in**vyberte **Přidat z adresáře**. Zvolte adresář *AcousticsWwisePlugin\ProjectAcoustics,* který byl součástí staženého balíčku.

![Možnost instalace pluginu Wwise do spouštěče Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Přidání binárních souborů Wwise do ukázkového projektu Akustika Unreal
1. Ve Wwise Launchervyberte kartu **Unreal Engine.** 
1. Vyberte nabídku "hamburger" (ikona) vedle **položky Nedávné projekty neskutečného motoru**a pak vyberte **Vyhledat projekt**. Otevřete ukázkový soubor projektu Unreal *.uproject* v balíčku *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![Karta Neskutečný ve spouštěči Wwise](media/wwise-unreal-tab.png)

3. Vedle ukázkového projektu Akustika vyberte **integrovat Wwise v projectu**.

   ![Wwise Launcher zobrazuje projekt Acoustics Game Unreal se zvýrazněnou možností Integrace.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Rozšíření funkce zásuvných modulů Wwise Unreal
Modul plug-in Project Acoustics Unreal potřebuje další chování vystavené z rozhraní API modulu plug-in Wwise Unreal. Spusťte dávkový soubor dodané s modulem plug-in Project Acoustics Unreal a automatizujte tyto změny.
* Uvnitř *AcousticsGame\Plugins\ProjectAcoustics\Resources*, spusťte *PatchWwise.bat*.

    ![Skript pro opravu projektu Wwise zvýrazněný v okně Průzkumníka Windows](media/patch-wwise-script.png)

* Pokud nemáte nainstalovanou sadku DirectX SDK: V závislosti na verzi wwise, kterou používáte, `DXSDK_DIR` bude možná nutné komentovat řádek, který obsahuje v *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Editor kódu zobrazující "DXSDK" komentoval](media/directx-sdk-comment.png)

* Pokud kompilujete pomocí sady Visual Studio 2019: Chcete-li obejít chybu propojení s Wwise, ručně změňte výchozí hodnotu *VSVersion* v *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* na **vc150**:

    ![Editor kódu s VSVersion změněn na "vc150"](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Otevření projektu Neskutečný 
Když otevřete projekt Unreal, zobrazí se výzva k opětovnému sestavení modulů. Vyberte **ano**.

Pokud otevření projektu selže z důvodu selhání sestavení, zkontrolujte, že jste nainstalovali modul plug-in Project Acoustics Wwise do stejné verze Wwise, která byla použita v ukázkovém projektu Acoustics projektu.

Pokud používáte verzi [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) starší než verze 2019.1, nelze generovat zvukové banky pomocí ukázkového projektu Akustika projektu. Je třeba integrovat Wwise verze 2019.1 do ukázkového projektu.

## <a name="experiment-with-project-acoustics-design-controls"></a>Experimentujte s ovládacími prvky návrhu aplikace Project Acoustics
Poslechněte si, jak scéna zní, a to výběrem tlačítka přehrávání v editoru Unreal. Pomocí kláves W, A, S a D a myši se pohybovat. Pokud chcete zobrazit klávesové zkratky pro další ovládací prvky, vyberte F1.

Následující informace popisují některé aktivity návrhu vyzkoušet.

### <a name="modify-occlusion-and-transmission"></a>Změna okluze a přenosu
Existují ovládací prvky návrhu aplikace Project Acoustics pro jednotlivé zdroje na každém objektu objektu neskutečného zvuku.

![Ovládací prvky návrhu akustiky Unreal Editor](media/demo-scene-sound-source-design-controls.png)

Pokud je multiplikátor **okluze** větší než 1 (výchozí hodnota je 1), je okluze zveličena. Nastavení menší než 1 dělá efekt okluze jemnější.

Chcete-li povolit přenos přes stěnu, posuňte jezdec **Přenos (dB)** od nejnižší úrovně.

### <a name="modify-wetness-for-a-source"></a>Změna vlhkosti zdroje
Chcete-li změnit, jak rychle se vlhkost mění se vzdáleností, použijte **percepční pokřivení vzdálenosti**. Aplikace Project Acoustics vypočítá úrovně za mokra v celém prostoru prostřednictvím simulace. Úrovně se liší hladce se vzdáleností a poskytují percepční vzdálenost podněty. Chcete-li tento efekt zveličit, zvyšte úrovně mokré hodu související se vzdáleností, abyste zvýšili osnovu vzdálenosti. Hodnoty deformace menší než 1, aby vzdálenost-založené dozvuku změnit jemnější. Můžete také provést jemnější úpravy tohoto efektu pomocí nastavení **Vlhkost (dB).**

Chcete-li prodloužit dobu rozpadu v celém prostoru, upravte **měřítko doby rozpadu**. Vezměme si případ, kdy výsledkem simulace je doba rozpadu 1,5 sekundy. Nastavení **časového měřítka rozpadu** na 2 má za následek dobu rozpadu 3 sekund aplikovanou na zdroj.

### <a name="modify-distance-based-attenuation"></a>Upravit útlum založený na vzdálenosti
Modul plug-in mixer Aplikace Akustika Wwise respektuje útlum založený na ujeté vzdálenosti na zdroj, který je integrován do wwise. Změnou této křivky se změní úroveň suché dráhy. Zásuvný modul Project Acoustics upraví úroveň za mokra tak, aby byla zachována směs za mokra/sucha specifikovaná ovládacími prvky simulace a návrhu.

![Panel Křivky útlumu Wwise zobrazující útlum na 0 před hranicí simulace](media/demo-sounds-attenuation.png)

Aplikace Project Acoustics se vypočítá v poli "oblast simulace", které je soustředěno kolem každého umístění simulovaného přehrávače. Akustika aktiva ve vzorku balíčku byly pečené s poloměrem simulace oblasti 45 metrů. Útlumy byly navrženy tak, aby klesly na 0 před 45 metry. I když tento pokles není přísný požadavek, nese upozornění, že pouze geometrie do 45 metrů od posluchače bude okluzovat zvuky.

## <a name="next-steps"></a>Další kroky
* [Integrujte](unreal-integration.md) modul plug-in Project Acoustics do projektu Unreal.
* [Vytvořte si účet Azure](create-azure-account.md) pro vlastní pečení.
