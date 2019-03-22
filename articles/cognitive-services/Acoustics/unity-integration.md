---
title: Integrace s Project Akustika Unity a nasazení
titlesuffix: Azure Cognitive Services
description: Tento návod popisuje integrace modulu plug-in Akustika Unity projektů ve vašem Unity projektu.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: aa7c5c513d65310bf9bffab29c1d18e7e7a85b49
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316282"
---
# <a name="project-acoustics-unity-integration"></a>Integrace s Project Akustika Unity
Tento návod popisuje integrace modulu plug-in Akustika Unity projektů ve vašem Unity projektu.

Požadavky na software:
* [Unity 2018.2 +](http://unity3d.com) pro Windows
* [Balíček Akustika Unity projektu](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Import modulu plug-in
Importujte Akustika UnityPackage do projektu. 
* V Unity, přejděte na **prostředky > Importovat balíček > vlastní balíček...**

    ![Snímek obrazovky s Unity importovat balíček nabídky](media/import-package.png)  

* Zvolte **ProjectAcoustics.unitypackage**

Pokud importujete modul plug-in do existujícího projektu, váš projekt už můžete mít **mcs.rsp** soubor v kořenové složce projektu, který určuje možnosti kompilátoru C#. Bude potřeba sloučit obsah tohoto souboru se souborem mcs.rsp, která se dodává s modulem plug-in Akustika projektu.

## <a name="enable-the-plugin"></a>Povolení modulu plug-in
Která má označení vytvoření část toolkit Akustika vyžaduje verzi skriptovací modul runtime .NET 4.x. Import balíčku se aktualizovat nastavení Unity Playeru. Restartujte Unity pro toto nastavení se projeví.

![Panel nastavení snímku obrazovky z Unity Playeru](media/player-settings.png)

![Snímek obrazovky s Unity Playeru nastavení panelu s vybrané rozhraní .NET 4.5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Nastavit DSP zvuku
Projekt Akustika zahrnuje zvuku runtime DSP, která se integruje do architektury spatializer zvukového modulu Unity. Obsahuje prostorový zvuk na základě HRTF i posouvání. Povolit DSP Akustika projekt tak, že otevřete nastavení zvuku Unity pomocí **Upravit > Nastavení projektu > zvuku**, pak vyberete **projektu Akustika** jako **modulu plug-in Spatializer** pro váš projekt. Ujistěte se, že **velikost vyrovnávací paměti DSP** je nastavena na výkon.

![Snímek obrazovky nastavení projektů Unity panel](media/project-settings.png)  

![Panel nastavení Spatializer Unity – snímek obrazovky s projektu Akustika spatializer vybrané](media/choose-spatializer.png)

Pak otevřete Mixer zvuku (**okna > zvuk Mixer**). Ujistěte se, že máte alespoň jeden Mixer, se v jedné skupině. Pokud ne, klikněte na tlačítko "+" napravo od **míchání**. Klikněte pravým tlačítkem na konec kanálu pruhu v části efekty a přidejte **projektu Akustika Mixer** vliv. Všimněte si, že je podporován pouze jeden projekt Akustika Mixer najednou.

![Snímek obrazovky z Unity zvuk Mixer hostování mixer Akustika projektu](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Povolit Akustika na zvukové zdroje
Vytvoření zdroje zvuku. Klikněte na zaškrtávací políčko v dolní části AudioSource inspector panelů, s upozorněním **Spatialize**. Ujistěte se, že **prostorových Blendu** je nastavena na úplnou 3D.  

![Snímek obrazovky s Unity zvuk zdroj panel](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Povolit akustický návrh
Připojit skript **AcousticsAdjust** ke zdroji zvuku ve scéně umožňující parametrů návrhu další zdroje, kliknutím na **přidat součást** a zvolíte **skripty > Upravit Akustika** :

![Snímek obrazovky s Unity AcousticsAdjust skriptu](media/acoustics-adjust.png)

## <a name="next-steps"></a>Další postup
* [Vytvoření vašeho scény s Akustika projektu pro Unity](unity-baking.md)
* [Vytvoření účtu služby Azure Batch](create-azure-account.md) k vytvoření vašeho scény v cloudu
* Prozkoumejte [proces návrhu projektu Akustika Unity](unity-workflow.md).

