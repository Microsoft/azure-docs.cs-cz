---
title: 'Přidat sloupce: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul přidat sloupce ve službě Azure Machine Learning ke zřetězení dvou datových sad.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: cbb1249b529fde1368bfaff0c6c251c93fa6c309
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693861"
---
# <a name="add-columns-module"></a>Modul přidat sloupce

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

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

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 