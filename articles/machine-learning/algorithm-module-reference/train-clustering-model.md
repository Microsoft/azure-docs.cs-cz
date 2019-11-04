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
ms.date: 05/06/2019
ms.openlocfilehash: 1b26fc251aeb527041b1e648f19f9dd67f7701b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490389"
---
# <a name="train-clustering-model"></a>Trénování modelu clusteringu

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Tento modul slouží ke školení modelu clusteringu.

Modul převezme provedený model clusteringu, který jste už nakonfigurovali pomocí technologie [K-označuje modul clusteringu](k-means-clustering.md) a navlakuje model pomocí označené nebo neoznačené sady dat. Modul vytvoří jak vyškolený model, který můžete použít pro předpověď, a sadu přiřazení clusteru pro každý případ v školicích datech.

> [!NOTE]
> Model clusteringu nejde být vyškolený pomocí modulu [výuka modelu](train-model.md) , který je obecným modulem pro školení modelů strojového učení. Důvodem je to, že [model výuky](train-model.md) funguje jenom se základními algoritmy učení. K-znamená a další algoritmy clusteringu umožňují bezdohledné učení, což znamená, že se algoritmus může naučit z neoznačených dat.  
  
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


+  Chcete-li zobrazit hodnoty v datové sadě, klikněte pravým tlačítkem na modul, vyberte **výslednou**datovou sadu a klikněte na **vizualizovat**.

+ Pokud chcete vyškolený model Uložit pro pozdější opětovné použití, klikněte pravým tlačítkem myši na modul, vyberte **trained model**a klikněte na **Uložit jako trained model**.

+ K vygenerování skóre z modelu použijte [přiřazení dat do clusterů](assign-data-to-clusters.md).



## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 