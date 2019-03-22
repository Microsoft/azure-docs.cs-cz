---
title: Přehled Akustika projektu
titlesuffix: Azure Cognitive Services
description: Projekt Akustika je simulaci Akustika modul pro 3D interaktivní prostředí, integrace vloženými wave fyzika s ovládacími prvky interaktivního návrhu.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 1fc125322b83a0eb51095fac21dee05c7ffb39c1
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313613"
---
# <a name="what-is-project-acoustics"></a>Co je Project Acoustics?
Projekt Akustika je modul Akustika wave pro 3D interaktivní prostředí. Bez nutnosti ruční zóny značek modeluje wave účinky jako diffraction, portaling a dozvuku efekty při vytváření složitých scén. Zahrnuje také herní engine a zvukové middleware integrace. Projekt Akustika filozofií je podobný statické osvětlení: zanést do offline režimu podrobné fyzika zajistit fyzické směrný plán a využití jednoduché modulu runtime s ovládacími prvky návrhu výrazové ke splnění svých cílů umělecký.

![Snímek obrazovky Gears of War 4 zobrazující Akustika voxels](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Použití pro interaktivní Akustika fyzika wave
Na základě Ray Akustika metody můžete vyhledávat pomocí přetypování jeden zdroj pro naslouchací proces ray uzavření nebo jednotka dozvuku odhadem místní scény svazku s několika paprsky. Ale může být těchto technik nespolehlivé, protože společností pebble occludes navýší boulder. Paprsky není účet způsobem zvukové ohybů okolo objektů, jev říká diffraction. Simulace projektu Akustika zaznamená negativních dopadů pomocí simulace na základě wave. Výsledkem je předvídatelný a spolehlivý.

Inovace projektu Akustika klíče je akustický simulace několik s koncepty návrhu tradiční zvuku. Tradiční zvuku DSP parametry pro uzavření, portaling a dozvuku je přeložen výsledky simulace. Návrhář používá ovládací prvky průběhu tohoto procesu překladu. Podrobné informace o základní technologie za Akustika projekt, přejděte [stránce projektu research](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animace zobrazuje vodorovný 2D řez šíření wave do scény](media/wave-simulation.gif)

## <a name="setup"></a>Nastavení
[Projekt integrace Unity Akustika](unity-integration.md) přetahování myší a zahrnuje modul plug-in zvukového modulu Unity. Posílení ovládací prvky zdroje zvuku Unity připojení projektu Akustika C# součást každé audio – objekt, ovládací prvky.

[Projekt Akustika Unreal integrace](unreal-integration.md) zahrnuje moduly plug-in editoru a hry pro Unreal a k pluginu mixer Wwise. Komponentu vlastní zvuku rozšiřuje známých Wwise funkcí v rámci Unreal pomocí živých Akustika ovládací prvky návrhu. Ovládací prvky návrhu jsou také zobrazuje v Wwise pro modul plug-in mixer.

## <a name="workflow"></a>Pracovní postup
* **Předběžné vytvoření:** Začněte s nastavením která má označení vytvoření tak, že vyberete geometrii, která reaguje na Akustika, například světla šachty se ignoruje. Potom upravte automatické přiřazení materiálu a výběr oblastí navigace k naslouchacímu procesu Průvodce vzorkování. Neexistuje žádné ruční značky pro zóny dozvuku/portal/místnosti.
* **Vytvoření:** Na krok analýzy je spustit místně, na které se voxelization a ostatní geometrické analýzy na scénu na základě výše uvedeného výběru. Výsledky jsou vizualizovány v editoru ověřte nastavení scény. Při odeslání, která má označení vytvoření voxel se odešlou Azure a nevrátíte se zpátky prostředek Akustika her.
* **Modul runtime:** Načíst aktiva do vaší úrovně a budete připraveni k naslouchání Akustika ve vaší úrovni. Návrh Akustika živě v editoru pomocí ovládacích prvků detailní za zdroje. Ovládací prvky mohou být řízeny také od úrovně skriptování.

## <a name="platforms"></a>Platformy
Moduly plug-in modulu runtime projektu Akustika je nyní nasadit na následujících platformách:
* Windows
* Android
* Xbox One

## <a name="download"></a>Ke stažení
* [Modul plug-in Akustika Unity projektu a ukázky](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Projekt Akustika Unreal & Wwise moduly plug-in a ukázky](https://www.microsoft.com/download/details.aspx?id=58090)
  * Pro binární soubory Xbox a podporu kontaktujte nás přes formulář zaregistrovat

## <a name="contact-us"></a>Kontaktujte nás
* [Fóra Akustika projektu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics)
* [Přihlásit k odběru aktualizací na Akustika projektu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>Další postup
* Vyzkoušejte [Akustika projekt rychlý start pro Unity](unity-quickstart.md) nebo [Unreal](unreal-quickstart.md)
* Prozkoumejte [zvukové filosofie návrhu tříd z projektu Akustika](design-process.md)

