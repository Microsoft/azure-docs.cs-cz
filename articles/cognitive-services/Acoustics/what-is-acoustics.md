---
title: Přehled akustiky projektu
titlesuffix: Azure Cognitive Services
description: Projekt Akustika je akustický engine pro 3D interaktivní zážitky, integrující simulaci fyziky pečených vln s interaktivními ovládacími prvky návrhu.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 65678f08399f378b8580eed79e49197dd4d84c64
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351151"
---
# <a name="what-is-project-acoustics"></a>Co je Project Acoustics?
Projekt Akustika je vlnový akustický engine pro 3D interaktivní zážitky. To modely vlnové efekty, jako je okluze, obstrukce, portaling a dozvuku účinky ve složitých scénách bez nutnosti ruční zóny značky nebo CPU intenzivní raytracing. To také zahrnuje herní engine a audio middleware integrace. Filozofie projektu Akustika je podobná statickému osvětlení: pečte podrobnou fyziku offline, abyste poskytli fyzickou základnu, a použijte lehký runtime s expresivními ovládacími prvky návrhu, abyste splnili své umělecké cíle pro akustiku vašeho virtuálního světa.

![Snímek obrazovky z Gears of War 4 zobrazující akustiku voxels](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Použití vlnové fyziky pro interaktivní akustiku
Metody akustiky založené na paprsku mohou kontrolovat okluzi pomocí jediného přetypování paprsku zdroj-posluchač nebo dozvuku jednotky odhadem objemu místní scény pomocí několika paprsků. Ale tyto techniky mohou být nespolehlivé, protože oblázek okluzuje stejně jako balvan. Paprsky nezohledňuje způsob, jakým se zvuk ohýbá kolem objektů, jev známý jako difrakce. Simulace akustiky projektu zachycuje tyto efekty pomocí simulace založené na vlnách. Akustika je předvídatelnější, přesnější a plynulejší.

Hlavní inovací projektu Acoustics je spojit skutečnou akustickou simulaci založenou na zvukových vlnách s tradičními koncepty zvukového designu. Převádí výsledky simulace do tradičních audio DSP parametrů pro okluzi, portaling a reverb. Návrhář používá ovládací prvky v průběhu tohoto procesu překladu. Další podrobnosti o základních technologiích projektu Akustika naleznete na [stránce výzkumného projektu](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animace zobrazující vodorovný 2D výseč šíření vln prostřednictvím scény](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>Video prezentace z GDC 2019 (~30 min)
[![Video o akustice projektu](https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "Klikněte pro přehrání videa")

## <a name="setup"></a>Nastavení
[Integrace Aplikace Akustika Unity projektu](unity-integration.md) je přetažení a obsahuje modul plug-in modulu zvuku Unity. Rozšiřte ovládací prvky zdroje zvuku Unity připojením součásti Project Acoustics C# ke každému zvukovému objektu.

[Integrace projektu Acoustics Unreal](unreal-integration.md) zahrnuje editora a herní pluginy pro Unreal a plugin Wwise mixer. Vlastní zvuková komponenta rozšiřuje známé funkce Wwise v rámci unreal u ovládacích prvků návrhu živé akustiky. Ovládací prvky návrhu jsou také vystaveny ve Wwise na modulu plug-in mixer.

## <a name="workflow"></a>Pracovní postup
* **Předem péct:** Začněte nastavením pečení výběrem geometrie, která reaguje na akustiku, například ignorováním světelných hřídelí. Poté upravte automatická přiřazení materiálu a vyberte navigační oblasti, které budou vodítkem pro vzorkování posluchače. Neexistuje žádná ruční značka pro zóny reverb / portál / místnost.
* **Pečeme:** Krok analýzy je spuštěn lokálně, což provádí voxelizaci a další geometrickou analýzu scény na základě výše uvedených výběrů. Výsledky jsou vizualizovány v editoru k ověření nastavení scény. Při odesílání pečoume se data voxelu odesílají do Azure a vy získáte zpět herní podklad pro akustiku.
* **Doba běhu:** Načtěte aktivum do své úrovně a jste připraveni poslouchat akustiku ve své úrovni. Navrhněte akustiku živě v editoru pomocí podrobných ovládacích prvků pro jeden zdroj. Ovládací prvky lze také řídit z úrovně skriptování.

## <a name="runtime-platforms"></a>Runtime platformy
Moduly runtime aplikace Project Acoustics lze aktuálně nasadit na následující platformy:
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Editor platformy
Modul plug-in editoru Project Acoustics je k dispozici pro následující platformy:
* Windows
* MacOS (pouze jednota)

## <a name="download"></a>Stáhnout
* [Plugin a ukázky project Acoustics Unity](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Projekt Akustika Unreal & Wwise pluginy a ukázky](https://www.microsoft.com/download/details.aspx?id=58090)
  * Pro Xbox binární soubory a další podporu, kontaktujte nás prostřednictvím [fóra](https://github.com/microsoft/ProjectAcoustics/issues).

## <a name="contact-us"></a>Kontaktujte nás
* [Diskuse o akustice projektu a vykazování problémů](https://github.com/microsoft/ProjectAcoustics/issues)

## <a name="next-steps"></a>Další kroky
* Vyzkoušejte [rychlý start aplikace Project Acoustics pro Unity](unity-quickstart.md) nebo [Unreal](unreal-quickstart.md)
* Seznamte se s [filozofií zvukového designu projektu Acoustics](design-process.md)

