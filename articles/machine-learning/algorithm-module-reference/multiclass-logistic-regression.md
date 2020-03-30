---
title: 'Vícetřídová logistická regrese: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Multiclass Logistic Regression v Azure Machine Learning vytvořit model logistické regrese, který lze použít k předvídání více hodnot.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ca446b0ab67a8a202c1f4d505262660ac55f42db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456161"
---
# <a name="multiclass-logistic-regression-module"></a>Vícetřídový logistický regresní modul

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření logistického regresního modelu, který lze použít k předvídání více hodnot.

Klasifikace pomocí logistické regrese je metoda učení pod dohledem, a proto vyžaduje označenou datovou sadu. Trénování modelu poskytnutím modelu a popisné datové sady jako vstup do modulu, jako je [například train model](./train-model.md). Trénovaný model pak lze předpovědět hodnoty pro nové vstupní příklady.

Azure Machine Learning také poskytuje [modul dvoutřídní logistické regrese,](./two-class-logistic-regression.md) který je vhodný pro klasifikaci binárních nebo dichotomických proměnných.

## <a name="about-multiclass-logistic-regression"></a>O vícetřídové logistické regresi

Logistická regrese je dobře známá metoda ve statistikách, která se používá k předvídání pravděpodobnosti výsledku a je populární pro úkoly klasifikace. Algoritmus předpovídá pravděpodobnost výskytu události přizpůsobením dat do logistické funkce. 

V regresi vícetřídy logistické třídění lze předpovědět více výsledků.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurace logistické regrese více tříd

1. Přidejte do kanálu modul **Vícetřídové logistické regrese.**

2. Určete, jak má být model trénovaný, nastavením **možnosti Vytvořit režim trenažéru.**

    + **Jeden parametr**: Tuto možnost použijte, pokud víte, jak chcete nakonfigurovat model a zadat určitou sadu hodnot jako argumenty.

    + **Rozsah parametrů**: Tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit tažení parametrů. Vyberte rozsah hodnot, které chcete iterát přes a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates přes všechny možné kombinace nastavení, které jste zadali k určení hyperparameters, které produkují optimální výsledky.  

3. **Tolerance optimalizace**, zadejte prahovou hodnotu pro konvergenci optimalizátoru. Pokud je zlepšení mezi iterací menší než prahová hodnota, algoritmus zastaví a vrátí aktuální model.

4. **Hmotnost regularizace L1**, **hmotnost regularizace L2**: Zadejte hodnotu, která se má použít pro parametry regularizace L1 a L2. Pro oba se doporučuje nenulová hodnota.

    Regularizace je metoda, která zabraňuje nadměrnému vybavení penalizací modelů hodnotami s extrémními koeficienty. Regularizace funguje přidáním penalizace, která je spojena s hodnotami koeficientu k chybě hypotézy. Přesný model s hodnotami extrémních koeficientů by byl penalizován více, ale méně přesný model s konzervativnějšími hodnotami by byl penalizován méně.

     Legalizace L1 a L2 má různé účinky a použití. L1 lze použít pro řídké modely, což je užitečné při práci s vysokorozměrnými daty. Naproti tomu regularizace L2 je vhodnější pro data, která nejsou řídká.  Tento algoritmus podporuje lineární kombinaci hodnot regularizace L1 `x = L1` a `y = L2` `ax + by = c` L2: to znamená if a , definuje lineární rozsah termínů regularizace.

     Pro logistické regresní modely byly navrženy různé lineární kombinace termínů L1 a L2, jako je [například regulace elastické sítě](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Osiva náhodných čísel**: Zadejte celou hodnotu, která se má použít jako osivo pro algoritmus, pokud chcete, aby výsledky byly opakovatelné přes spuštění. V opačném případě se jako osiva používá hodnota systémových hodin, která může způsobit mírně odlišné výsledky při spuštění stejného kanálu.

8. Připojte označenou datovou sadu a jeden z modulů vlaku:

    + Pokud nastavíte **vytvořit režim trenažéru** na **jeden parametr**, použijte modul [Model vlaku.](./train-model.md)

9. Odešlete potrubí.



## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 