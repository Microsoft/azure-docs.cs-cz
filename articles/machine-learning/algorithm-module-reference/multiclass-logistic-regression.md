---
title: 'Regresní regrese ve více třídách: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul logistické regrese ve službě Azure Machine Learning k vytvoření modelu logistické regrese, který se dá použít k předpovědi více hodnot.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: d51bc48944204b4c7c50790949927849869f26fc
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128641"
---
# <a name="multiclass-logistic-regression-module"></a>Modul logistické regrese pro více tříd

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Pomocí tohoto modulu můžete vytvořit model logistické regrese, který se dá použít k předpovědi více hodnot.

Klasifikace pomocí logistické regrese je metoda učení pod dohledem a proto vyžaduje datovou sadu s popiskem. Model provedete tak, že zadáte model a datovou sadu s popiskem jako vstup do modulu, jako je například [model výuky](./train-model.md). Školený model se pak dá použít k předpovědi hodnot pro nové vstupní příklady.

Azure Machine Learning také poskytuje modul [logistické regrese se dvěma třídami](./two-class-logistic-regression.md) , který je vhodný pro klasifikaci binárních nebo dichotomous proměnných.

## <a name="about-multiclass-logistic-regression"></a>O mikrotřídě logistické regrese

Logistická regrese je dobře známá metoda, která se používá k předpovědi pravděpodobnosti výsledku a je oblíbená pro úlohy klasifikace. Algoritmus předpovídá pravděpodobnost výskytu události tím, že se data namontuje na logistickou funkci. 

V rámci více tříd logistické regrese lze klasifikátor použít k předpovědi více výsledků.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurace s více třídami logistické regrese

1. Přidejte do experimentu modul logistické regrese.

2. Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .

    + **Jeden parametr**: Tuto možnost použijte, pokud víte, jak chcete model konfigurovat, a poskytněte konkrétní sadu hodnot jako argumenty.

    + **Rozsah parametrů**: Tuto možnost použijte, pokud si nejste jisti nejlepšími parametry a chcete použít Sweep parametrů.

3. **Tolerance optimalizace**zadejte prahovou hodnotu pro konvergenci Optimalizátoru. Pokud je vylepšení mezi iteracemi menší než prahová hodnota, algoritmus se zastaví a vrátí aktuální model.

4. **Pravidelná váha L1**, **váha pro pravidelnost v L2**: Zadejte hodnotu, která se má použít pro parametry pro pravidelnost L1 a L2. Pro obojí se doporučuje nenulová hodnota.

    Pravidelná metoda představuje způsob, jak zabránit přeložení pomocí postihu modelů s hodnotami extrémního součinitele. Pravidelnou práci můžete provést přidáním pokuty, která je spojená s hodnotami koeficientu, na chybu hypotézy. Přesný model s hodnotami extrémního koeficientu by byl potrestán více, ale méně přesný model s více konzervativními hodnotami by byl méně trestný.

     Pravidelná L1 a L2 mají různé účinky a použití. L1 se dá použít na řídké modely, což je užitečné při práci s vysokými objemy dat. Na rozdíl od je pro data, která nejsou zhuštěná, vhodnější pravidelná navýšení L2.  Tento algoritmus podporuje lineární kombinaci hodnot L1 a L2 pro účely depravidelnosti: to znamená, `x = L1` že `y = L2`Pokud `ax + by = c` a, definuje lineární rozpětí regulárních podmínek.

     Pro logistické regresní modely, jako je [elastická pravidelná](https://wikipedia.org/wiki/Elastic_net_regularization)flexibilita, byly navrženy různé lineární kombinace L1 a L2.

6. **Počáteční číslo prvního čísla**: Zadejte celočíselnou hodnotu, která se použije jako počáteční hodnota pro algoritmus, pokud chcete, aby se výsledky opakovaly při spuštění. V opačném případě je jako počáteční hodnota použita systémová časová hodnota, která může v průběhu stejného experimentu způsobit mírně odlišné výsledky.

8. Propojit s popiskem datovou sadu a jeden z modulů vlaků:

    + Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, použijte modul [vlakového modelu](./train-model.md) .

9. Spusťte experiment.

## <a name="results"></a>Výsledky

Po dokončení školení uvidíte souhrn parametrů modelu spolu s váhy funkcí zjištěnými ze školení, klikněte pravým tlačítkem na výstup modulu [vlakového modelu](./train-model.md) a vyberte **vizualizovat**.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 