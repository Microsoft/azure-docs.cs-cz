---
title: Integrace a nasazení aplikace Akustika akustiky
titlesuffix: Azure Cognitive Services
description: Tento článek popisuje, jak integrovat modul plug-in Project Acoustics Unity do projektu Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243000"
---
# <a name="project-acoustics-unity-integration"></a>Integrace Project Acoustics Unity
Tento článek popisuje, jak integrovat modul plug-in Project Acoustics Unity do projektu Unity.

Požadavky na software:
* [Unity 2018.2+](https://unity3d.com) pro Windows
* [Balíček Project Acoustics Unity](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Import zásuvné modulu
1. Importujte akustiku UnityPackage do projektu. 
 V unity, přejděte na vlastní**balíček** > **importu** >  **prostředků**.

    ![Nabídka Balíček pro import Unity](media/import-package.png)  

1. Zvolte **ProjectAcoustics.unitypackage**.

1. Vyberte tlačítko **Importovat** pro integraci balíčku Unity do projektu.

    ![Dialogové okno Balíček pro import unity](media/import-dialog.png)  

Pokud importujete modul plug-in do existujícího projektu, projekt již může mít soubor *mcs.rsp* v kořenovém adresáři projektu. Tento soubor určuje možnosti kompilátoru Jazyka C#. Sloučit obsah tohoto souboru se souborem mcs.rsp, který je dodáván s modulem plug-in Aplikace Akustika projektu.

## <a name="enable-the-plug-in"></a>Povolení modulu plug-in
Péct část sady nástrojů akustiky vyžaduje .NET 4. *x* skriptovací runtime verze. Import balíčku aktualizuje nastavení přehrávače Unity. Toto nastavení se projeví až po restartování unity.

![Panel Nastavení unity přehrávače](media/player-settings.png)

![Panel Unity Player Settings s vybranou položkou .NET 4.5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Nastavení zvukového dsp
Projekt Akustika obsahuje zvukový modul DSP, který se integruje do rámce prostorového nastavení modulu Unity. Zahrnuje jak prostorovou orientaci založenou na HRTF, tak na posouvání. Chcete-li povolit dsp akustiky projektu, přejděte na **upravit** > **nastavení** > projektu**audio** otevřít nastavení zvuku Unity. Vyberte **Akustiku projektu** jako **modul pro prostorový nastavení** pro váš projekt. Ujistěte se, že **je velikost vyrovnávací paměti DSP** *nastavena*na nejlepší výkon .

![Nabídka Nastavení projektu Unity](media/project-settings.png)  

![Panel Nastavení prostorového zaleskanosti Unity s vybranou možností prostorový izátor akustiky projektu](media/choose-spatializer.png)

Dále otevřete audio mixer **(Okno** > **Audio Mixer**). Ujistěte se, že máte alespoň jeden mixér, s jednou skupinou. Pokud ho nemáte, vyberte **+** tlačítko napravo od **míchačky**. Klikněte pravým tlačítkem myši na konec kanálu v části efekty a přidejte efekt **Microsoft Acoustics Mixer.** Současně je podporován pouze jeden mixer akustiky projektu.

![Unity Audio Mixer hosting projektu Akustika mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Povolení akustiky na zdrojích zvuku
Vytvoření zdroje zvuku: Zaškrtněte políčko **Prostorově zaškrtávat** v dolní části panelu inspektora AudioSource. Ujistěte se, že **je prostorová prolnutí** nastavena na plnou *3D*.  

![Panel Unity Audio Source](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Povolit akustický design
Připojte skript *AcousticsAdjust* ke zdroji zvuku ve scéně, abyste povolili další parametry návrhu zdroje: Vyberte **Přidat komponentu** a zvolte **Skripty** > **Akustika Adjust .**

![Skript Unity AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Další kroky
* [Upérejte svou scénu s projektem Akustika pro jednotu](unity-baking.md).
* [Vytvořte účet Azure Batch](create-azure-account.md) a upevněte scénu v cloudu.
* Prozkoumejte [proces návrhu Project Acoustics Unity](unity-workflow.md).
