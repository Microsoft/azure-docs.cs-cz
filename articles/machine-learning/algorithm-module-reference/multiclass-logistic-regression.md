---
title: 'Regresní regrese s více třídami: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul logistické regrese ve službě Azure Machine Learning k vytvoření modelu logistické regrese, který se dá použít k předpovědi více hodnot.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 247a0bef8e166c72f185f2d384067fc5814a602e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893743"
---
# <a name="multiclass-logistic-regression-module"></a>Modul logistické regrese pro více tříd

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete vytvořit model logistické regrese, který se dá použít k předpovědi více hodnot.

Klasifikace pomocí logistické regrese je metoda učení pod dohledem a proto vyžaduje datovou sadu s popiskem. Model provedete tak, že zadáte model a datovou sadu s popiskem jako vstup do modulu, jako je například [model výuky](./train-model.md). Školený model se pak dá použít k předpovědi hodnot pro nové vstupní příklady.

Azure Machine Learning také poskytuje modul [logistické regrese se dvěma třídami](./two-class-logistic-regression.md) , který je vhodný pro klasifikaci binárních nebo dichotomous proměnných.

## <a name="about-multiclass-logistic-regression"></a>O mikrotřídě logistické regrese

Logistická regrese je dobře známá metoda, která se používá k předpovědi pravděpodobnosti výsledku a je oblíbená pro úlohy klasifikace. Algoritmus předpovídá pravděpodobnost výskytu události tím, že se data namontuje na logistickou funkci. 

V rámci více tříd logistické regrese lze klasifikátor použít k předpovědi více výsledků.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurace s více třídami logistické regrese

1. Přidejte do kanálu modul **logistické regrese** .

2. Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .

    + **Jediný parametr**: tuto možnost použijte, pokud víte, jak chcete model konfigurovat, a poskytněte konkrétní sadu hodnot jako argumenty.

    + **Rozsah parametrů**: tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit mazání parametrů. Vyberte rozsah hodnot, na které se mají iterovat, a s [parametry modelu ladění](tune-model-hyperparameters.md) prochází všechny možné kombinace nastavení, které jste zadali, abyste určili, jaké parametry jsou výsledkem optimálních výsledků.  

3. **Tolerance optimalizace**zadejte prahovou hodnotu pro konvergenci Optimalizátoru. Pokud je vylepšení mezi iteracemi menší než prahová hodnota, algoritmus se zastaví a vrátí aktuální model.

4. **Pravidelná váha L1**, **váha pro pravidelnost v L2**: zadejte hodnotu, která se má použít pro parametry pro pravidelnou práci L1 a L2. Pro obojí se doporučuje nenulová hodnota.

    Pravidelná metoda představuje způsob, jak zabránit přeložení pomocí postihu modelů s hodnotami extrémního součinitele. Pravidelnou práci můžete provést přidáním pokuty, která je spojená s hodnotami koeficientu, na chybu hypotézy. Přesný model s hodnotami extrémního koeficientu by byl potrestán více, ale méně přesný model s více konzervativními hodnotami by byl méně trestný.

     Pravidelná L1 a L2 mají různé účinky a použití. L1 se dá použít na řídké modely, což je užitečné při práci s vysokými objemy dat. Na rozdíl od je pro data, která nejsou zhuštěná, vhodnější pravidelná navýšení L2.  Tento algoritmus podporuje lineární kombinaci hodnot L1 a L2 pro účely depravidelnosti: to znamená, že pokud `x = L1` a `y = L2` , `ax + by = c` definuje lineární rozpětí regulárních podmínek.

     Pro logistické regresní modely, jako je [elastická pravidelná](https://wikipedia.org/wiki/Elastic_net_regularization)flexibilita, byly navrženy různé lineární kombinace L1 a L2.

6. **Počáteční číslo osiva**: Zadejte celočíselnou hodnotu, která se použije jako počáteční hodnota pro algoritmus, pokud chcete, aby se výsledky opakovaly při spuštění. Jinak se jako počáteční hodnota používá systémová časová hodnota, která může při spuštění stejného kanálu způsobit mírně odlišné výsledky.

8. Propojit s popiskem datovou sadu a vyškolit model:

    + Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, připojíte tagované datové sady a modul [vlakového modelu](train-model.md) .  
  
    + Pokud nastavíte **režim vytvořit Trainer** na **rozsah parametrů**, připojíte s příznakovou datovou sadu a provedete model pomocí [předparametrů ladit model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Pokud předáte rozsah parametru pro [vlakový model](train-model.md), použije se jenom výchozí hodnota v seznamu jednoho parametru.  
    > 
    > Pokud předáte jednu sadu hodnot parametrů do modulu [předparametrů modelu ladění](tune-model-hyperparameters.md) , když očekává rozsah nastavení pro každý parametr, ignoruje hodnoty a použije výchozí hodnoty pro učit se.  
    > 
    > Pokud vyberete možnost **rozsahu parametrů** a zadáte jednu hodnotu pro libovolný parametr, tato jediná hodnota, kterou jste zadali, se použije v celém oblouku, a to i v případě, že se jiné parametry mění v rozsahu hodnot.

9. Odešlete kanál.



## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 