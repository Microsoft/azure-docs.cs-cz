---
title: 'Přidat sloupce: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Přidat sloupce v Azure Machine Learning zřetězit dvě datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: f2e067f76d6ed7d89a38e9b8920c407f161969a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456774"
---
# <a name="add-columns-module"></a>Přidat modul Sloupce

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží ke zřetězení dvou datových sad. Zkombinujete všechny sloupce ze dvou datových sad, které zadáte jako vstupy, a vytvoříte tak jednu datovou sadu. Pokud potřebujete zřetězit více než dvě datové sady, použijte několik instancí **Přidat sloupce**.



## <a name="how-to-configure-add-columns"></a>Jak nakonfigurovat přidat sloupce
1. Přidejte do kanálu modul **Přidat sloupce.**

2. Připojte dvě datové sady, které chcete zřetězit. Pokud chcete zkombinovat více než dvě datové sady, můžete zřetězit několik kombinací **přidat sloupce**.

    - Je možné kombinovat dva sloupce, které mají jiný počet řádků. Výstupní datová sada je doplněna chybějícími hodnotami pro každý řádek v menším zdrojovém sloupci.

    - Nelze vybrat jednotlivé sloupce, které chcete přidat. Všechny sloupce z každé datové sady jsou zřetězeny při použití **funkce Přidat sloupce**. Pokud tedy chcete přidat pouze podmnožinu sloupců, vytvořte pomocí příkazu Vybrat sloupce v datové sadě datovou sadu s požadovanými sloupci.

3. Odešlete potrubí.

### <a name="results"></a>Výsledky
Po spuštění potrubí:

- Chcete-li zobrazit první řádky nové datové sady, klepněte pravým tlačítkem myši na modul **Přidat sloupce** a vyberte možnost Vizualizovat. Nebo Vyberte modul a přepněte na kartu **Výstupy** v pravém panelu, klikněte na ikonu histogramu ve **výstupech portu** a vizualizovat výsledek.

Počet sloupců v nové datové sadě se rovná součtu sloupců obou vstupních datových sad.

Pokud jsou ve vstupních datových sadách dva sloupce se stejným názvem, je k názvu sloupce přidána číselná přípona. Například pokud existují dvě instance sloupce s názvem TargetOutcome, levý sloupec by byl přejmenován TargetOutcome_1 a pravý sloupec by byl přejmenován TargetOutcome_2.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 