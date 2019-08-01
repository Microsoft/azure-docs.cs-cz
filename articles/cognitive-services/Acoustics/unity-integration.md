---
title: Nasazení a integrace Unity v projektu
titlesuffix: Azure Cognitive Services
description: Tento postup vysvětluje integraci modulu plug-in Unity v projektu do vašeho projektu Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 1a90f6102d35dc1a3bb97c840f2955b54f35bbad
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706647"
---
# <a name="project-acoustics-unity-integration"></a>Integrace Unity v projektu
Tento postup vysvětluje integraci modulu plug-in Unity v projektu do vašeho projektu Unity.

Požadavky na software:
* [Unity 2018.2 +](https://unity3d.com) pro Windows
* [Balíček Unity s akustickými projekty](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Import modulu plug-in
Importujte akustické UnityPackagey do projektu. 
* V Unity jděte na **assets > Import balíčku > vlastní balíček...**

    ![Snímek nabídky balíčku importu Unity](media/import-package.png)  

* Vyberte **ProjectAcoustics. unitypackage**

Pokud importujete modul plug-in do existujícího projektu, projekt již může mít soubor **MCS. rsp** v kořenovém adresáři projektu, který určuje možnosti C# kompilátoru. Obsah tohoto souboru bude nutné sloučit se souborem MCS. rsp, který je součástí modulu plug-in s akustickými a projektovým modulem.

## <a name="enable-the-plugin"></a>Povolení modulu plug-in
Zanesli část akustického množství Toolkit vyžaduje verzi modulu runtime skriptování .NET 4. x. Při importu balíčku se aktualizují nastavení přehrávače Unity. Restartujte Unity, aby se toto nastavení projevilo.

![Snímek obrazovky s panelem nastavení přehrávače Unity](media/player-settings.png)

![Snímek obrazovky s panelem nastavení přehrávače Unity s vybraným .NET 4,5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Nastavení DSP zvukového zařízení
Akustické projekty zahrnují rozhraní DSP pro modul runtime zvuku, které se integruje do spatializer architektury zvukového modulu Unity. Zahrnuje jak na základě HRTF, tak pro posouvání. Povolte, aby projekt byl v důsledku akustického přenosu, a to tak, že otevřete nastavení zvuku Unity pomocí možnosti **upravit > nastavení projektu > zvuk**a pak jako **modul plug-in Spatializer** pro svůj projekt vyberete **akustické projekty** . Ujistěte se, že **Velikost vyrovnávací paměti DSP** je nastavená na nejlepší výkon.

![Snímek obrazovky s panelem nastavení projektu Unity](media/project-settings.png)  

![Snímek obrazovky s panelem nastavení Spatializer Unity s možnostmi akustického projektu Spatializer](media/choose-spatializer.png)

Pak otevřete směšovač zvuku (**okno > směšovač zvuku**). Ujistěte se, že máte aspoň jeden směšovač s jednou skupinou. Pokud to neuděláte, klikněte na tlačítko "+" napravo od **směšovačů**. Pravým tlačítkem myši klikněte na dolní část pruhu kanálu v části efekty a přidejte efekt **směšovače akustického projektu** . Mějte na paměti, že v jednu chvíli je podporovaný jenom jeden směšovač akustického projektu.

![Snímek obrazovky s směšovačem zvukových stop v Unity pro mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Povolit akustické zvuky na zdrojích zvuku
Vytvořte zdroj zvuku. Klikněte na zaškrtávací políčko v dolní části panelu inspektora AudioSource, který říká **Spatialize**. Ujistěte se, že je **prostorový Blend** nastavený na plný 3D.  

![Snímek panelu zdroje zvuku Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Povolit akustický návrh
Připojte skript **AcousticsAdjust** ke zdroji zvuku ve scéně, abyste mohli povolit další parametry pro návrh zdrojového kódu, a to tak, že kliknete na **Přidat součást** a zvolíte skripty > nastavovat **zvukové úpravy**:

![Snímek obrazovky s AcousticsAdjust skriptem Unity](media/acoustics-adjust.png)

## <a name="next-steps"></a>Další postup
* [Zanesli své scény díky akustickým projektům pro Unity](unity-baking.md)
* [Vytvoření účtu Azure Batch](create-azure-account.md) k zaneslií scény v cloudu
* Prozkoumejte [proces návrhu Unity s akustickými projekty](unity-workflow.md).

