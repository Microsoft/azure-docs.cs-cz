---
title: Projekt Akustika Pečeme Rozlišení
titlesuffix: Azure Cognitive Services
description: Tento koncepční přehled popisuje rozdíl mezi hrubým a jemným rozlišením při pečení akustiky.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d8eb3b2cbaf7b4e842d8338eefde756f6d381111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854353"
---
# <a name="project-acoustics-bake-resolution"></a>Projekt Akustika Pečeme Rozlišení
Tento koncepční přehled popisuje rozdíl mezi hrubým a jemným rozlišením při pečení akustiky. Toto nastavení zvolíte během kroku Sondy pracovního postupu pečení.

## <a name="coarse-vs-fine-resolution"></a><a name="Coarse-vs-Fine-Resolution"></a>Hrubé vs jemné rozlišení

Jediným rozdílem mezi nastavením hrubého a jemného rozlišení je frekvence, při které se simulace provádí. Fine používá frekvenci dvakrát vyšší než hrubá. To má řadu důsledků pro akustickou simulaci:

* Vlnová délka pro hrubé je dvakrát tak dlouhá, že je v pořádku, a proto jsou voxely dvakrát větší.
* Doba simulace přímo souvisí s velikostí voxelu, takže hrubé pečení asi 16 krát rychleji než jemné pečení.
* Portály (například dveře nebo okna) menší než velikost voxelu nelze simulovat. Hrubé nastavení může způsobit, že některé z těchto menších portálů nebudou simulovány; proto nebudou procházet zvukem za běhu. Můžete zjistit, zda se to děje tím, že si prohlížíte voxels.
* Nižší frekvence simulace vede k menší difrakce kolem rohů a hran.
* Zdroje zvuku nemohou být umístěny uvnitř "vyplněných" voxelů (tj. voxelů, které obsahují geometrii). Výsledkem je žádný zvuk. Je to těžší umístit zdroje zvuku, takže nejsou uvnitř větší voxels hrubé, než je při použití jemné nastavení.
* Větší voxels bude zasahovat více do portálů, jak je znázorněno níže. První obrázek byl vytvořen pomocí hrubé, zatímco druhý je stejný vchod pomocí jemného rozlišení. Jak je uvedeno v červených značkách, je mnohem méně vniknutí do dveří pomocí jemného nastavení. Modrá čára je dveře definované geometrií, zatímco červená čára je efektivní akustický portál definovaný velikostí voxelu. Jak se toto vniknutí odehrává v dané situaci, zcela závisí na tom, jak se voxels zarovnají s geometrií portálu, která je určena velikostí a umístěním vašich objektů ve scéně.

![Snímek obrazovky s hrubými voxely, které vyplňují dveře v Unrealu](media/unreal-coarse-bake.png)

![Snímek obrazovky s jemnými voxels ve dveřích v Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si hrubé a jemné rozlišení nastavení sami pomocí našich [pluginů Unreal](unreal-baking.md) nebo [Unity.](unity-baking.md)
