---
title: Přehled akustického projektu
titlesuffix: Azure Cognitive Services
description: Akustické projekty jsou akustickým modulem pro 3D interaktivní prostředí a integrují simulaci vloženýmich fyziky pomocí interaktivních ovládacích prvků návrhu.
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
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351151"
---
# <a name="what-is-project-acoustics"></a>Co je Project Acoustics?
Akustické navýšení projektu je akustický modul Wave pro 3D interaktivní prostředí. Modely IT napodobují účinky vlny, jako je překrytí, překážka, na portál a reverberation, v komplexních scénách bez nutnosti ručního označení zóny nebo raytracing náročné na procesor. Zahrnuje také herní a audio modul pro integraci middlewaru. Akustické filozofiey projektu jsou podobné statickému osvětlení: zanesli podrobné fyzika offline pro zajištění fyzických standardních hodnot a použití zjednodušeného modulu runtime s ovládacími prvky pro vyjádření návrhu pro splnění vašich uměleckéch cílů pro účely akustického vývoje z vašeho virtuálního světa.

![Snímek obrazovky z ozubeného kolace války 4 ukazující akustické voxels](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Použití fyzika Wave pro interaktivní akustické práce
Metody akustického množství založené na ray mohou kontrolovat překrytí pomocí jediného přetypování zdroje na naslouchací proces, nebo na reverb pomocí odhadu lokálního objemu scény s několika paprsky. Tyto techniky ale můžou být nespolehlivé, protože Pebble occludes, a to mnohem jako Boulder. Paprsky nezpůsobují způsob, jakým se zvuk roztéká kolem objektů, což je tento jev známý jako Diffraction. Simulace akustického projektu zachycuje tyto efekty pomocí simulace založené na vlnovce. Akustické a přesné a bezproblémové zvukové.

Cloudové inovace s využitím cloudu jsou v rámci tradičních konceptů návrhu zvuku spojeny s akustickou simulací zvuku Wave. Překládá výsledky simulace do tradičních parametrů DSP pro překrytí, na portáling a reverb. Návrhář používá ovládací prvky nad tímto procesem překladu. Další informace o základních technologiích, které jsou na akustickém projektu, najdete na [stránce výzkumného projektu](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animace ukazující horizontální 2D řez šíření vlny prostřednictvím scény](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>Prezentace videa z GDC 2019 (~ 30 min)
[Video o akustickém projektu ![](https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "Přehrát video kliknutím")

## <a name="setup"></a>Nastavení
Cloudová [integrace Unity](unity-integration.md) je přetažena myší a obsahuje modul plug-in pro zvukové moduly Unity. Rozšiřte ovládací prvky zdroje zvuku ve službě Unity tím, že připojíte komponentu C# ovládacích prvků projekt ke každému zvukovému objektu.

[Unreal integrace projektu](unreal-integration.md) zahrnuje editory a herní moduly plug-in pro Unreal a modul plug-in Wwise mixer. Vlastní zvuková komponenta rozšiřuje známé funkce Wwise v rámci Unreal s ovládacími prvky návrhu pro živé akustické práci. Ovládací prvky návrhu jsou také zpřístupněny v Wwise modulu plug-in směšovače.

## <a name="workflow"></a>Pracovní postup
* **Před zanesli:** Začněte nastavením zanesli, a to tak, že vyberete, který geometrii reaguje na akustické, například se ignorováním lehkých hřídelí. Pak upravte automatické přiřazení materiálu a vyberte navigační oblasti pro vzorkování naslouchacího procesu. Neexistují žádné ruční označení pro zóny reverb/Portal nebo místnost.
* **Zanesli:** Krok analýzy se spouští místně, který provádí voxelization a další geometrickou analýzu scény na základě výše uvedených výběrů. Výsledky jsou vizuální editory pro ověření nastavení scény. Při odesílání zanesli se data Voxel odesílají do Azure a Vy se dostanete ke zvukovému prostředku.
* **Modul runtime:** Načte Asset do vaší úrovně a Vy jste připraveni naslouchat akustickým hladinám na úrovni. Navrhněte akustické akustické využití v editoru pomocí podrobnějších ovládacích prvků pro zdroj. Ovládací prvky lze také řídit pomocí skriptování na úrovni.

## <a name="runtime-platforms"></a>Běhové platformy
Moduly plug-in pro modul runtime v projektu se teď dají nasadit na tyto platformy:
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Platformy editoru
Modul plug-in Editor akustického projektu je k dispozici pro následující platformy:
* Windows
* MacOS (jenom Unity)

## <a name="download"></a>Stáhnout
* [Modul plug-in a ukázky pro projekt akustické Unity](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Projekt akustické Unreal & moduly plug-in a ukázky Wwise](https://www.microsoft.com/download/details.aspx?id=58090)
  * V případě binárních souborů Xbox a další podpory kontaktujte nás prostřednictvím [fóra](https://github.com/microsoft/ProjectAcoustics/issues).

## <a name="contact-us"></a>Kontaktujte nás
* [Diskuze o problémech v projektu a vytváření sestav problémů](https://github.com/microsoft/ProjectAcoustics/issues)

## <a name="next-steps"></a>Další kroky
* Vyzkoušejte si [rychlý Start akustického projektu pro Unity](unity-quickstart.md) nebo [Unreal](unreal-quickstart.md)
* [Seznamte se s návrhem zvuku filozofie akustického množství projektů](design-process.md)

