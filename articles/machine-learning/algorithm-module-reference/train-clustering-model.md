---
title: 'Model clusteringu pro vlak: Reference k modulu'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul výukového modelu clusteringu v Azure Machine Learning ke školení modelů clusteringu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: 6b154cdbf6490abd935156e6d081d2260cfbc578
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719360"
---
# <a name="train-clustering-model"></a>Trénování modelu clusteringu

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Tento modul slouží ke školení modelu clusteringu.

Modul převezme provedený model clusteringu, který jste už nakonfigurovali pomocí technologie [K-označuje modul clusteringu](k-means-clustering.md) a navlakuje model pomocí označené nebo neoznačené sady dat. Modul vytvoří jak vyškolený model, který můžete použít pro předpověď, a sadu přiřazení clusteru pro každý případ v školicích datech.

> [!NOTE]
> Model clusteringu se nedá vyškolet pomocí modulu [vlakového modelu](train-model.md) , který je obecným modulem pro školení modelů strojového učení. Důvodem je to, že [model výuky](train-model.md) funguje jenom se základními algoritmy učení. K-znamená a další algoritmy clusteringu umožňují bezdohledné učení, což znamená, že se algoritmus může naučit z neoznačených dat.  
  
## <a name="how-to-use-train-clustering-model"></a>Použití modelu výukového clusteringu  

1.  Přidejte modul **clusteringu clustering model** do kanálu v návrháři. Modul najdete v části **Machine Learning moduly**v kategorii **vlaků** .  
  
2. Přidejte modul pro clusteringu, který je [prostředkem pro clustering](k-means-clustering.md) , nebo jiný vlastní modul, který vytvoří kompatibilní model clusteringu, a nastavte parametry modelu clusteringu.  
    
3.  Připojte školicí datovou sadu k pravému vstupu **modelu clusteringu s výukou**.
  
5.  V části **sada sloupců**vyberte sloupce z datové sady, které chcete použít při vytváření clusterů. Nezapomeňte vybrat sloupce, které mají dobré funkce, například Vyhněte se použití ID nebo jiných sloupců, které mají jedinečné hodnoty, nebo sloupců, které mají všechny stejné hodnoty.

    Pokud je popisek k dispozici, můžete ho buď použít jako funkci, nebo ho nechat zapnutý.  
  
6. Vyberte možnost a **zaškrtněte políčko připojit nebo zrušit zaškrtnutí pouze pro výsledek**, pokud chcete výstup školicích dat společně s novým označením clusteru.

    Pokud zrušíte výběr této možnosti, budou výstupem pouze přiřazení clusteru. 

7. Spusťte kanál nebo klikněte na modul **výukového modelu clusteringu** a vyberte **Spustit vybrané**.  
  
### <a name="results"></a>Výsledky

Po dokončení školení:

+ Pokud chcete uložit snímek výukového modelu, vyberte kartu **výstupy** na pravém panelu modulu **výuka modelu** . Výběrem ikony **Registrovat datovou sadu** uložte model jako opakovaně použitelný modul.

+ K vygenerování skóre z modelu použijte [přiřazení dat do clusterů](assign-data-to-clusters.md).

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 