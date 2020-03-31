---
title: 'Vícetřídový posílený rozhodovací strom: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Multiclass Boosted Decision Tree v Azure Machine Learning vytvořit třídění pomocí označených dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920055"
---
# <a name="multiclass-boosted-decision-tree"></a>Posílený rozhodovací strom s několika třídami

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření modelu strojového učení, který je založen na algoritmu boosted rozhodovací stromy.

Posílený rozhodovací strom je metoda učení souboru, ve které druhý strom opravuje chyby prvního stromu, třetí strom opravuje chyby prvního a druhého stromu a tak dále. Předpovědi jsou založeny na souboru stromů dohromady.

## <a name="how-to-configure"></a>Jak nakonfigurovat 

Tento modul vytvoří netrénovaný klasifikační model. Vzhledem k tomu, že klasifikace je metoda učení pod dohledem, potřebujete *označenou datovou sadu,* která obsahuje sloupec popisku s hodnotou pro všechny řádky.

Tento typ modelu můžete trénovat pomocí [modelu vlaku](././train-model.md). 

1.  Přidejte do kanálu modul **Vícetřídový posílený rozhodovací strom.**

1.  Určete, jak má být model trénovaný, nastavením **možnosti Vytvořit režim trenažéru.**

    + **Jeden parametr**: Pokud víte, jak chcete nakonfigurovat model, můžete zadat určitou sadu hodnot jako argumenty.
    
    + **Rozsah parametrů**: Tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit tažení parametrů. Vyberte rozsah hodnot, které chcete iterát přes a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates přes všechny možné kombinace nastavení, které jste zadali k určení hyperparameters, které produkují optimální výsledky.  

1. **Maximální počet listů na strom** omezuje maximální počet koncových uzlů (listů), které lze vytvořit v libovolném stromu.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **Minimální počet vzorků na uzel listu** označuje počet případů potřebných k vytvoření libovolného koncového uzlu (listu) ve stromu.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **Míra učení** definuje velikost kroku při učení. Zadejte číslo mezi 0 a 1.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **Počet vytvořených stromů** označuje celkový počet rozhodovacích stromů, které mají být vytvořeny v souboru. Vytvořením více rozhodovacích stromů můžete potenciálně získat lepší pokrytí, ale doba tréninku se zvýší.

1. **Osiva náhodného čísla** volitelně nastaví nezáporné celé číslo, které se použije jako náhodná hodnota osiva. Určení osiva zajišťuje reprodukovatelnost napříč spuštěními, které mají stejná data a parametry.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> Pokud nastavíte **režim Vytvořit trenér** na jeden **parametr**, připojte tagovované datové sady a modul [model vlaku.](./train-model.md)

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
