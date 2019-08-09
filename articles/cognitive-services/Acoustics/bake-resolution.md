---
title: Řešení zanesli, které je v projektu akustické
titlesuffix: Azure Cognitive Services
description: Tento koncepční přehled popisuje rozdíl mezi hrubými a jemnými řešeními v případě akustického a pečení.
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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854353"
---
# <a name="project-acoustics-bake-resolution"></a>Řešení zanesli, které je v projektu akustické
Tento koncepční přehled popisuje rozdíl mezi hrubými a jemnými řešeními v případě akustického a pečení. Toto nastavení si můžete vybrat během kroku sondy pracovního postupu pečení.

## <a name="Coarse-vs-Fine-Resolution"></a>Hrubá přesnost vs – jemné rozlišení

Jediným rozdílem mezi hrubou a jemným nastavením rozlišení je frekvence, s jakou se simulace provádí. Jemné použití frekvence dvakrát tak, jak je hrubý. To má řadu dopadů na akustickou simulaci:

* Vlnová délka pro hrubou hodnotu je dvojnásobná, pokud je to jemný, a proto je voxels dvakrát tak velké.
* Doba simulace je přímo v souvislosti se Voxel velikostí, což vede k hrubému zanesli přibližně 16 krát rychleji než jemný zanesli.
* Portály (například dveře nebo okna) menší než velikost Voxel se nedají simulovat. Toto hrubé nastavení může způsobit, že některé z těchto menších portálů nebudou simulované. proto za běhu nebudou dodávat zvuk. Můžete zjistit, jestli se děje, zobrazením voxels.
* Dolní frekvence simulace má za následek méně Diffraction kolem rohů a hran.
* Zdroje zvuku nejde umístit do vyplněné voxels (tj. voxels, které obsahují geometrii). Výsledkem není žádný zvuk. Je obtížnější umístit zdroje zvuku, aby nebyly uvnitř většího voxelsu hrubého, než je v případě použití nastavení jemné.
* Větší voxels bude intrude víc na portály, jak vidíte níže. První obrázek byl vytvořen pomocí hrubých, zatímco druhý je stejný snímek s použitím jemného rozlišení. Jak ukazují červené označení, je k dispozici mnohem méně neoprávněných vniknutí do této dveře pomocí nastavení jemného příznaku. Modrá čára je dveře, jak je definována v geometrii, zatímco červená čára představuje účinný akustický portál definovaný velikostí voxel. Způsob, jakým se tento průnik v dané situaci zaznamená, zcela závisí na tom, jak se voxels řádek s geometrií portálu, která je určená velikostí a umístěním objektů ve scéně.

![Snímek obrazovky s hrubou voxelsou vyplňování dveří v Unreal](media/unreal-coarse-bake.png)

![Snímek obrazovky s jemnou voxels v Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Další postup

Vyzkoušejte si nastavení hrubých a jemných řešení sami pomocí našich [Unreal](unreal-baking.md) nebo modulů plug-in [Unity](unity-baking.md) .
