---
title: Projekt Akustika která má označení vytvoření řešení
titlesuffix: Azure Cognitive Services
description: Tento přehled popisuje rozdíl mezi hrubý a bez problémů řešení při pečení Akustika.
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 04/05/2019
ms.author: KyleStorck
ms.openlocfilehash: 7dbf63ba39c5dcdebb363cfc37a45f0216a07497
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61335442"
---
# <a name="project-acoustics-bake-resolution"></a>Projekt Akustika která má označení vytvoření řešení
Tento přehled popisuje rozdíl mezi hrubý a bez problémů řešení při pečení Akustika. Toto nastavení během kroku sondy pečení pracovního postupu, vyberte.

## <a name="Coarse-vs-Fine-Resolution"></a>Hrubý vs jemné řešení

Jediným rozdílem mezi nastavení pro nalezení hrubý a bez problémů je frekvenci, s jakou se provádí simulace. Používá jemné frekvenci dvakrát tak vysoké jako hrubý. To má několik dopadů na akustický simulace:

* Vlnové pro hrubý je dvakrát až pořádku, a proto je voxels dvakrát velké.
* Čas simulace přímo souvisí s velikostí voxel, a proto hrubý která má označení vytvoření asi 16krát rychleji než jemné která má označení vytvoření.
* Portály (například dveře nebo windows) menší než velikost voxel nemůže být Simulovaná. Hrubý nastavení může způsobit, že některé z těchto menších portály, které nebudou simulované; Proto že nebudou předávat zvukové prostřednictvím za běhu. Můžete zobrazit, pokud dochází k tomu zobrazením voxels.
* Nižší četností simulace za následek méně diffraction kolem a rohy.
* Zvukové zdroje nemůže být umístěné uvnitř voxels "plného" (to znamená voxels obsahující geometrie). Výsledkem žádný zvukový signál. Je obtížnější umístit zdrojů zvuku, nejsou uvnitř větší voxels z hrubé než při použití nastavení v pořádku.
* Větší voxels bude více pronikat do portálů, jak je znázorněno níže. První image byla vytvořena pomocí hrubý, druhá je stejný vaší bránou pomocí jemné řešení. Označené červenou označení, je mnohem méně neoprávněného vniknutí do vaší bránou pomocí nastavení v pořádku. Modrá čára je vaší bránou dle geometrie, zatímco červená čára je efektivní akustický portál definován velikostí voxel. Jak tento neoprávněného vniknutí hraje v dané situaci závisí zcela jak voxels zarovnejte s geometrie na portálu, který je určena velikost a umístění objektů na scéně.

![Snímek obrazovky s hrubým voxels vyplnění vaší bránou v Unreal](media/unreal-coarse-bake.png)

![Snímek obrazovky jemné voxels ve vaší bránou v Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Další postup

Vyzkoušejte si nastavení pro nalezení hrubý a bez problémů sami pomocí našich [Unreal](unreal-baking.md) nebo [Unity](unity-baking.md) moduly plug-in.