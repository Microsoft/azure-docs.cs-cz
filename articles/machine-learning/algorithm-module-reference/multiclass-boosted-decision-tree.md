---
title: 'Více třídou rozhodovacího stromu s zvýšením úrovně: Reference k modulu'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul více tříd pro rozhodovací strom s více třídami v Azure Machine Learning k vytvoření klasifikátoru pomocí popisků dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920055"
---
# <a name="multiclass-boosted-decision-tree"></a>Posílený rozhodovací strom s několika třídami

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Pomocí tohoto modulu můžete vytvořit model strojového učení, který je založený na algoritmu zesílených rozhodovacích stromů.

Posílený rozhodovací strom je metoda učení kompletu, ve které druhý strom opravuje chyby prvního stromu, třetí strom pro chyby první a druhé stromy a tak dále. Předpovědi jsou založené na kompletu stromů dohromady.

## <a name="how-to-configure"></a>Jak nakonfigurovat 

Tento modul vytváří nevlakový model klasifikace. Vzhledem k tomu, že klasifikace je metoda učení pod dohledem, potřebujete *datovou sadu s popiskem* , která obsahuje sloupec popisku s hodnotou pro všechny řádky.

Tento typ modelu můžete vyškolit pomocí [modelu vlakové soupravy](././train-model.md). 

1.  Přidejte do vašeho kanálu modul pro **zvýšení rozhodovacího stromu s více třídami** .

1.  Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .

    + **Jediný parametr**: Pokud víte, jak chcete model konfigurovat, můžete zadat konkrétní sadu hodnot jako argumenty.
    
    + **Rozsah parametrů**: tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit mazání parametrů. Vyberte rozsah hodnot, na které se mají iterovat, a s [parametry modelu ladění](tune-model-hyperparameters.md) prochází všechny možné kombinace nastavení, které jste zadali, abyste určili, jaké parametry jsou výsledkem optimálních výsledků.  

1. **Maximální počet ponechání na stromovou strukturu** omezuje maximální počet uzlů terminálů (opustí), které je možné vytvořit v jakémkoli stromu.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **Minimální počet vzorků na uzel na list** určuje počet případů vyžadovaných k vytvoření libovolného uzlu terminálu (list) ve stromu.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **Studijní frekvence** definuje velikost kroku při učení. Zadejte číslo od 0 do 1.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **Počet vytvořených stromů** označuje celkový počet rozhodovacích stromů, které se mají v kompletu vytvořit. Vytvořením dalších rozhodovacích stromů můžete získat lepší pokrytí, ale čas školení se zvýší.

1. **Náhodné číslo semen** volitelně nastaví nezáporné celé číslo, které se použije jako hodnota náhodného základu. Určení počáteční hodnoty zajišťuje reprodukovatelnost v různých spuštěních, která mají stejná data a parametry.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, připojíte tagované datové sady a modul [vlakového modelu](./train-model.md) .

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
