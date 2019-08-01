---
title: Přehled akustického projektu
titlesuffix: Azure Cognitive Services
description: Akustické projekty jsou akustickým modulem pro 3D interaktivní prostředí a integrují simulaci vloženýmich fyziky pomocí interaktivních ovládacích prvků návrhu.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: a654dd966c40b5b079f92b910ade52f4eda10344
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704752"
---
# <a name="what-is-project-acoustics"></a>Co je Project Acoustics?
Akustické navýšení projektu je akustický modul Wave pro 3D interaktivní prostředí. Modely IT se podobají efektům vln, jako je Diffraction, portáling a reverb, v komplexních scénách bez nutnosti ručního označování zóny. Zahrnuje také herní a audio modul pro integraci middlewaru. Akustické filozofiey projektu se podobají statickému osvětlení: zanesli podrobné fyzika offline k poskytování fyzických standardních hodnot a použití zjednodušeného modulu runtime s ovládacími prvky pro vyjádření návrhu pro uspokojení vašich uměleckéch cílů.

![Snímek obrazovky z ozubeného kolace války 4 ukazující akustické voxels](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Použití fyzika Wave pro interaktivní akustické práce
Metody akustického množství založené na ray mohou kontrolovat překrytí pomocí jediného přetypování zdroje na naslouchací proces, nebo na reverb pomocí odhadu lokálního objemu scény s několika paprsky. Tyto techniky ale můžou být nespolehlivé, protože Pebble occludes, a to mnohem jako Boulder. Paprsky nezpůsobují způsob, jakým se zvuk roztéká kolem objektů, což je tento jev známý jako Diffraction. Simulace akustického projektu zachycuje tyto efekty pomocí simulace založené na vlnovce. Výsledek je předvídatelný a spolehlivý.

Klíčové inovace s využitím klíčových projektů je k pro pár akustické simulace s tradičními koncepty návrhu zvuku. Překládá výsledky simulace do tradičních parametrů DSP pro překrytí, na portáling a reverb. Návrhář používá ovládací prvky nad tímto procesem překladu. Další informace o základních technologiích, které jsou na akustickém projektu, najdete na [stránce výzkumného projektu](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animace ukazující horizontální 2D řez šíření vlny prostřednictvím scény](media/wave-simulation.gif)

## <a name="setup"></a>Instalace
Cloudová [integrace Unity](unity-integration.md) je přetažena myší a obsahuje modul plug-in pro zvukové moduly Unity. Rozšiřte ovládací prvky zdroje zvuku ve službě Unity tím, že připojíte komponentu C# ovládacích prvků projekt ke každému zvukovému objektu.

[Unreal integrace projektu](unreal-integration.md) zahrnuje editory a herní moduly plug-in pro Unreal a modul plug-in Wwise mixer. Vlastní zvuková komponenta rozšiřuje známé funkce Wwise v rámci Unreal s ovládacími prvky návrhu pro živé akustické práci. Ovládací prvky návrhu jsou také zpřístupněny v Wwise modulu plug-in směšovače.

## <a name="workflow"></a>Pracovní postup
* **Před zanesli:** Začněte nastavením zanesli, a to tak, že vyberete, který geometrii reaguje na akustické, například se ignorováním lehkých hřídelí. Pak upravte automatické přiřazení materiálu a vyberte navigační oblasti pro vzorkování naslouchacího procesu. Neexistují žádné ruční označení pro zóny reverb/Portal nebo místnost.
* **Zanesli** Krok analýzy se spouští místně, který provádí voxelization a další geometrickou analýzu scény na základě výše uvedených výběrů. Výsledky jsou vizuální editory pro ověření nastavení scény. Při odesílání zanesli se data Voxel odesílají do Azure a Vy se dostanete ke zvukovému prostředku.
* **Runtime** Načte Asset do vaší úrovně a Vy jste připraveni naslouchat akustickým hladinám na úrovni. Navrhněte akustické akustické využití v editoru pomocí podrobnějších ovládacích prvků pro zdroj. Ovládací prvky lze také řídit pomocí skriptování na úrovni.

## <a name="platforms"></a>Platformy
Moduly plug-in pro modul runtime v projektu se teď dají nasadit na tyto platformy:
* Windows
* Android
* Xbox One

## <a name="download"></a>Ke stažení
* [Modul plug-in a ukázky pro projekt akustické Unity](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Projekt akustické Unreal & moduly plug-in a ukázky Wwise](https://www.microsoft.com/download/details.aspx?id=58090)
  * V případě binárních souborů a podpory Xbox kontaktujte nás prostřednictvím formuláře pro registraci níže.

## <a name="contact-us"></a>Kontaktujte nás
* [Diskuze o problémech v projektu a vytváření sestav problémů](https://github.com/microsoft/ProjectAcoustics/issues)
* [Zaregistrujte se a dostanete aktualizace na akustické projekty.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>Další postup
* Vyzkoušejte si [rychlý Start akustického projektu pro Unity](unity-quickstart.md) nebo [Unreal](unreal-quickstart.md)
* Seznamte se s [návrhem zvuku filozofie akustického množství projektů](design-process.md)

