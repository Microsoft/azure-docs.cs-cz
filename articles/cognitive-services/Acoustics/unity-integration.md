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
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 7d8edd7b092ee1ed4f953d753b2bbe864e075378
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137725"
---
# <a name="project-acoustics-unity-integration"></a>Integrace s Project Akustika Unity
Tento návod popisuje integrace modulu plug-in Akustika Unity projektů ve vašem Unity projektu.

Požadavky na software:
* [Unity 2018.2 +](http://unity3d.com) pro Windows
* [Balíček Akustika Unity projektu](https://www.microsoft.com/en-us/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Import modulu plug-in
Importujte Akustika UnityPackage do projektu. 
* V Unity, přejděte na **prostředky > Importovat balíček > vlastní balíček...**

    ![Importovat balíček](media/import-package.png)  

* Zvolte **ProjectAcoustics.unitypackage**

Pokud importujete modul plug-in do existujícího projektu, váš projekt už můžete mít **mcs.rsp** soubor v kořenové složce projektu, který určuje možnosti kompilátoru C#. Bude potřeba sloučit obsah tohoto souboru se souborem mcs.rsp, která se dodává s modulem plug-in Akustika projektu.

## <a name="enable-the-plugin"></a>Povolení modulu plug-in
Která má označení vytvoření část toolkit Akustika vyžaduje verzi skriptovací modul runtime .NET 4.x. Import balíčku se aktualizovat nastavení Unity Playeru. Restartujte Unity pro toto nastavení se projeví.

![Nastavení přehrávače](media/player-settings.png)

![.NET 4.5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Nastavit DSP zvuku
Projekt Akustika zahrnuje zvuku runtime DSP, která se integruje do architektury spatializer zvukového modulu Unity. Obsahuje prostorový zvuk na základě HRTF i posouvání. Povolit DSP Akustika projekt tak, že otevřete nastavení zvuku Unity pomocí **Upravit > Nastavení projektu > zvuku**, pak vyberete **projektu Akustika** jako **modulu plug-in Spatializer** pro váš projekt. Ujistěte se, že **velikost vyrovnávací paměti DSP** je nastavena na výkon.

![Nastavení projektu](media/project-settings.png)  

![Spatializer Akustika projektu](media/choose-spatializer.png)

Pak otevřete Mixer zvuku (**okna > zvuk Mixer**). Ujistěte se, že máte alespoň jeden Mixer, se v jedné skupině. Pokud ne, klikněte na tlačítko "+" napravo od **míchání**. Klikněte pravým tlačítkem na konec kanálu pruhu v části efekty a přidejte **projektu Akustika Mixer** vliv. Všimněte si, že je podporován pouze jeden projekt Akustika Mixer najednou.

![Audio Mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Povolit Akustika na zvukové zdroje
Vytvoření zdroje zvuku. Klikněte na zaškrtávací políčko v dolní části AudioSource inspector panelů, s upozorněním **Spatialize**. Ujistěte se, že **prostorových Blendu** je nastavena na úplnou 3D.  

![Zdroje zvuku](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Povolit akustický návrh
Připojit skript **AcousticsAdjust** ke zdroji zvuku ve scéně umožňující parametrů návrhu další zdroje, kliknutím na **přidat součást** a zvolíte **skripty > Upravit Akustika** :

![AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Další postup
* [Vytvoření vašeho scény s Akustika projektu pro Unity](unity-baking.md)
* [Vytvoření účtu služby Azure Batch](create-azure-account.md) k vytvoření vašeho scény v cloudu
* Prozkoumejte [proces návrhu projektu Akustika Unity](unity-workflow.md).

