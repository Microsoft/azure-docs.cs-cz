---
title: 'Přidat sloupce: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul přidat sloupce v Azure Machine Learning k zřetězení dvou datových sad.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 55981279cb1902424d1a0f77af097dc379d7222f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493975"
---
# <a name="add-columns-module"></a>Modul přidat sloupce

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Tento modul slouží ke zřetězení dvou datových sad. Všechny sloupce můžete kombinovat ze dvou datových sad, které zadáte jako vstupy, a vytvořit tak jedinou datovou sadu. Pokud potřebujete zřetězit více než dvě datové sady, použijte několik instancí **Přidat sloupce**.



## <a name="how-to-configure-add-columns"></a>Postup konfigurace přidat sloupce
1. Přidejte do svého kanálu modul **Přidat sloupce** .

2. Připojte dvě datové sady, které chcete zřetězit. Pokud chcete kombinovat více než dvě datové sady, můžete zřetězit několik kombinací **sloupců přidat**.

    - Je možné kombinovat dva sloupce, které mají různý počet řádků. Výstupní datová sada je doplněna o chybějící hodnoty pro každý řádek v menším zdrojovém sloupci.

    - Nelze zvolit jednotlivé sloupce, které chcete přidat. Všechny sloupce z každé datové sady jsou zřetězené při použití **Přidat sloupce**. Proto pokud chcete přidat pouze podmnožinu sloupců, použijte možnost vybrat sloupce v datové sadě k vytvoření datové sady se sloupci, které chcete.

3. Spuštění kanálu

### <a name="results"></a>Výsledky
Po spuštění kanálu:

- Chcete-li zobrazit první řádky nové datové sady, klikněte pravým tlačítkem myši na výstup **Přidat sloupce** a vyberte vizualizovat.

Počet sloupců v nové datové sadě se rovná součtu sloupců obou vstupních datových sad.

Pokud ve vstupních datových sadách existují dva sloupce se stejným názvem, do názvu sloupce se přidá číselná přípona. Například pokud existují dvě instance sloupce s názvem TargetOutcome, levý sloupec by se přejmenoval na TargetOutcome_1 a pravý sloupec by byl přejmenován TargetOutcome_2.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 