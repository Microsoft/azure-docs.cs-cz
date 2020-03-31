---
title: 'Rozhodnutí Forest Regrese: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Perceptron dvoutřídy v Azure Machine Learning vytvořit model strojového učení založený na průměrném algoritmu perceptronu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e23dd7d350ea63e9fd8b933a525a9d8aad9e3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920769"
---
# <a name="two-class-averaged-perceptron-module"></a>Dvoutřídní průměr perceptronového modulu

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření modelu strojového učení založeného na průměrném algoritmu perceptronu.  
  
Tento klasifikační algoritmus je metoda učení pod dohledem a vyžaduje *tagovované datové sady*, která obsahuje sloupec popisku. Model můžete trénovat poskytnutím modelu a tagged datové sady jako vstup [do Train Model](./train-model.md). Trénovaný model pak lze předpovědět hodnoty pro nové vstupní příklady.  

### <a name="about-averaged-perceptron-models"></a>O průměrných perceptronových modelech

*Průměrná perceptronová metoda* je raná a jednoduchá verze neuronové sítě. V tomto přístupu jsou vstupy rozděleny do několika možných výstupů založených na lineární funkci a poté kombinovány se sadou vah, které jsou odvozeny z vektoru funkce – odtud název "perceptron".

Jednodušší perceptronové modely jsou vhodné pro učení lineárně oddělitelných vzorů, zatímco neuronové sítě (zejména hluboké neuronové sítě) mohou modelovat složitější hranice třídy. Nicméně, perceptrony jsou rychlejší, a protože zpracovávají případy sériově, perceptrony mohou být použity s kontinuálním tréninkem.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Jak nakonfigurovat průměrnou perceptron dvoutřídy

1.  Přidejte do svého potrubí modul **Two-Class Averaged Perceptron.**  

2.  Určete, jak má být model trénovaný, nastavením **možnosti Vytvořit režim trenažéru.**  
  
    -   **Jeden parametr**: Pokud víte, jak chcete nakonfigurovat model, zadejte určitou sadu hodnot jako argumenty.

    -   **Rozsah parametrů**: Tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit tažení parametrů. Vyberte rozsah hodnot, které chcete iterát přes a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates přes všechny možné kombinace nastavení, které jste zadali k určení hyperparameters, které produkují optimální výsledky.  
  
3.  V **poli Míra učení**zadejte hodnotu míry *učení*. Hodnoty rychlosti učení řídí velikost kroku, který se používá ve stochastickém sestupu přechodu při každém testování a opravě modelu.
  
     Tím, že rychlost menší, můžete testovat model častěji, s rizikem, že byste mohli uvíznout v místní plošině. Tím, že krok větší, můžete sbíhat rychleji, s rizikem překročení skutečné minima.
  
4.  Pro **maximální počet iterací**zadejte počet, kolikrát má algoritmus zkontrolovat trénovací data.  
  
     Zastavení brzy často poskytuje lepší generalizaci. Zvýšení počtu iterací zlepšuje montáž, s rizikem nadměrného přizpůsobení.
  
5.  Do **pole Osiva náhodných čísel**volitelně zadejte hodnotu celéčíslo, která se má použít jako osiva. Použití osiva se doporučuje, pokud chcete zajistit reprodukovatelnost kanálu přes běží.  
  
1.  Připojte trénovací datovou sadu a jeden z trénovacích modulů:
  
    -   Pokud nastavíte **vytvořit režim trenažéru** na **jeden parametr**, použijte modul [Model vlaku.](train-model.md)




## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 