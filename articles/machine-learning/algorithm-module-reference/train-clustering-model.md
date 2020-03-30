---
title: 'Model shlukování vlaků: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Model clusteringu vlaků v Azure Machine Learning trénovat modely clusteringu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c29baf3cdda998a2ab78c84f3311b84d37086bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477421"
---
# <a name="train-clustering-model"></a>Trénování modelu clusteringu

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k trénování modelu clusteringu.

Modul přebírá model netrénovaného clusteringu, který jste již nakonfigurovali pomocí modulu [K-Means Clustering,](k-means-clustering.md) a třídí model pomocí sady dat s popiskem nebo bez popisku. Modul vytvoří trénovaný model, který můžete použít pro předpověď a sadu přiřazení clusteru pro každý případ v trénovacích datech.

> [!NOTE]
> Model clusteringu nelze trénovat pomocí modulu [Train Model,](train-model.md) což je obecný modul pro trénování modelů strojového učení. Je to [proto, že Train Model](train-model.md) pracuje pouze s algoritmy učení pod dohledem. K-prostředky a další clustering algoritmy umožňují učení bez dozoru, což znamená, že algoritmus může učit z neoznačených dat.  
  
## <a name="how-to-use-train-clustering-model"></a>Použití modelu clusteringu vlaků  

1.  Přidejte modul **Model clusteringu vlaků** do kanálu v návrháři. Modul najdete v části **Moduly strojového učení**v kategorii **Vlak.**  
  
2. Přidejte modul [Clustering K-Means](k-means-clustering.md) nebo jiný vlastní modul, který vytvoří kompatibilní model clusteringu a nastavte parametry clustering modelu.  
    
3.  Připojte trénovací datovou sadu k pravému vstupu **modelu shlukování vlaků**.
  
5.  V **sadě sloupců**vyberte sloupce z datové sady, které se mají použít v clusterech budov. Nezapomeňte vybrat sloupce, které dělají dobré funkce: například se vyhněte použití ID nebo jiných sloupců, které mají jedinečné hodnoty, nebo sloupce, které mají všechny stejné hodnoty.

    Pokud je popisek k dispozici, můžete jej použít jako prvek nebo jej vynechat.  
  
6. Vyberte **možnost, Vyhledat připojit nebo zrušit zaškrtnutí pouze výsledek**, pokud chcete výstup trénovacích dat spolu s novým popiskem clusteru.

    Pokud tuto volbu zrušíte, budou výstupem pouze přiřazení clusteru. 

7. Odešlete kanál nebo klepněte na modul **Model shlukování vlaků** a vyberte **Spustit vybrané**.  
  
### <a name="results"></a>Výsledky

Po ukončení výcviku:

+ Chcete-li uložit snímek trénovaného modelu, vyberte kartu **Výstupy** v pravém panelu modulu **modelu Vlak.** Vyberte ikonu **Registrovat datovou sadu,** chcete-li model uložit jako opakovaně použitelný modul.

+ Chcete-li generovat skóre z modelu, použijte [přiřadit data clusterům](assign-data-to-clusters.md).

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 