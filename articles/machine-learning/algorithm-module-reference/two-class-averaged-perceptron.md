---
title: 'Two-Class Averageed Perceptron: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu Two-Class Averageed Perceptron Module v Návrháři vytvořit binární klasifikátor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: f1161dc99b84026bcc830c08b63b95d0bf3b0994
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421137"
---
# <a name="two-class-averaged-perceptron-module"></a>Two-Class Averageed – modul Perceptron

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete vytvořit model strojového učení na základě průměrného Perceptron algoritmu.  
  
Tento algoritmus klasifikace je metoda učení pod dohledem a vyžaduje *datovou sadu s příznakem*, která obsahuje sloupec popisku. Model můžete proškolit poskytnutím modelu a tagované datové sady jako vstupu pro [vlakový model](./train-model.md). K předpovědi hodnot pro nové příklady vstupu je pak možné použít trained model.  

### <a name="about-averaged-perceptron-models"></a>Průměrné modely Perceptron

*Průměrná metoda Perceptron* je časná a jednoduchá verze neuronové sítě. V tomto přístupu jsou vstupy klasifikovány do několika možných výstupů založených na lineární funkci a pak v kombinaci se sadou vah odvozenými z vektoru funkce, tedy názvu "Perceptron".

Jednodušší modely Perceptron jsou vhodné pro učení lineárních oddělitelných vzorů, zatímco sítě neuronové (hlavně neuronové sítě) můžou modelovat složitější hranice tříd. Perceptrons jsou ale rychlejší a protože zpracovávají případy v sériovém případě, perceptrons se dají použít s průběžným školením.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Jak nakonfigurovat Two-Class Averageed Perceptron

1.  Do vašeho kanálu přidejte **Perceptron modul průměrně se dvěma třídami** .  

2.  Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .  
  
    -   **Jediný parametr**: Pokud víte, jak chcete model konfigurovat, poskytněte konkrétní sadu hodnot jako argumenty.

    -   **Rozsah parametrů**: tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit mazání parametrů. Vyberte rozsah hodnot, na které se mají iterovat, a s [parametry modelu ladění](tune-model-hyperparameters.md) prochází všechny možné kombinace nastavení, které jste zadali, abyste určili, jaké parametry jsou výsledkem optimálních výsledků.  
  
3.  Do pole **rychlost učení** zadejte hodnotu *studijní frekvence*. Hodnoty studijních kurzů určují velikost kroku, který se používá v stochastického přechodu při každém testování a opravování modelu.
  
     Tím, že se rychlost zmenší, otestujete model častěji s rizikem, že se můžete zablokovat v místních stabilní úrovně. Tím, že krok provedete větší, můžete provést sblížení rychleji a na riziko překročení skutečných minimálních minim.
  
4.  V poli **maximální počet iterací** zadejte počet pokusů, kolikrát chcete, aby algoritmus kontroloval školicí data.  
  
     Brzy se často zastavuje lepší generalizace. Zvýšení počtu opakování zlepšuje přizpůsobení při riziku přebudování.
  
5.  V případě **náhodného čísla počáteční** hodnota zadejte celočíselnou hodnotu, která se má použít jako počáteční hodnota. Použití počáteční hodnoty se doporučuje, pokud chcete zajistit reprodukovatelnost kanálu napříč běhy.  
  
1.  Připojte školicí datovou sadu a Vyškolte model:

    + Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, připojíte tagované datové sady a modul [vlakového modelu](train-model.md) .  
  
    + Pokud nastavíte **režim vytvořit Trainer** na **rozsah parametrů**, připojíte s příznakovou datovou sadu a provedete model pomocí [předparametrů ladit model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Pokud předáte rozsah parametru pro [vlakový model](train-model.md), použije se jenom výchozí hodnota v seznamu jednoho parametru.  
    > 
    > Pokud předáte jednu sadu hodnot parametrů do modulu [předparametrů modelu ladění](tune-model-hyperparameters.md) , když očekává rozsah nastavení pro každý parametr, ignoruje hodnoty a použije výchozí hodnoty pro učit se.  
    > 
    > Pokud vyberete možnost **rozsahu parametrů** a zadáte jednu hodnotu pro libovolný parametr, tato jediná hodnota, kterou jste zadali, se použije v celém oblouku, a to i v případě, že se jiné parametry mění v rozsahu hodnot.




## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 