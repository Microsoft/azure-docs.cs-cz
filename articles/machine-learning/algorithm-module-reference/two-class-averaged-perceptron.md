---
title: 'Regrese rozhodovací doménové struktury: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat Perceptron modul s průměrem dvou tříd v Azure Machine Learning k vytvoření modelu Machine Learning založeného na průměrném algoritmu Perceptron.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 379cddd9654cc897b49fd085d8df55fcd77a7ce8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490366"
---
# <a name="two-class-averaged-perceptron-module"></a>Průměrný Perceptron modul se dvěma třídami

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Pomocí tohoto modulu můžete vytvořit model strojového učení na základě průměrného Perceptron algoritmu.  
  
Tento algoritmus klasifikace je metoda učení pod dohledem a vyžaduje *datovou sadu s příznakem*, která obsahuje sloupec popisku. Model můžete proškolit poskytnutím modelu a tagované datové sady jako vstupu pro [vlakový model](./train-model.md). K předpovědi hodnot pro nové příklady vstupu je pak možné použít trained model.  

### <a name="about-averaged-perceptron-models"></a>Průměrné modely Perceptron

*Průměrná metoda Perceptron* je časná a jednoduchá verze neuronové sítě. V tomto přístupu jsou vstupy klasifikovány do několika možných výstupů založených na lineární funkci a pak v kombinaci se sadou vah odvozenými z vektoru funkce, tedy názvu "Perceptron".

Jednodušší modely Perceptron jsou vhodné pro učení lineárních oddělitelných vzorů, zatímco sítě neuronové (hlavně neuronové sítě) můžou modelovat složitější hranice tříd. Perceptrons jsou ale rychlejší a protože zpracovávají případy v sériovém případě, perceptrons se dají použít s průběžným školením.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Jak nakonfigurovat průměrnou Perceptroni dvou tříd

1.  Do vašeho kanálu přidejte **Perceptron modul průměrně se dvěma třídami** .  

2.  Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .  
  
    -   **Jediný parametr**: Pokud víte, jak chcete model konfigurovat, poskytněte konkrétní sadu hodnot jako argumenty.
  
3.  Do pole **rychlost učení**zadejte hodnotu *studijní frekvence*. Hodnoty studijních kurzů určují velikost kroku, který se používá v stochastického přechodu při každém testování a opravování modelu.
  
     Tím, že se rychlost zmenší, otestujete model častěji s rizikem, že se můžete zablokovat v místních stabilní úrovně. Tím, že krok provedete větší, můžete provést sblížení rychleji a na riziko překročení skutečných minimálních minim.
  
4.  V poli **maximální počet iterací**zadejte počet pokusů, kolikrát chcete, aby algoritmus kontroloval školicí data.  
  
     Brzy se často zastavuje lepší generalizace. Zvýšení počtu opakování zlepšuje přizpůsobení při riziku přebudování.
  
5.  V případě **náhodného čísla počáteční**hodnota zadejte celočíselnou hodnotu, která se má použít jako počáteční hodnota. Použití počáteční hodnoty se doporučuje, pokud chcete zajistit reprodukovatelnost kanálu napříč běhy.  
  
1.  Připojte datovou sadu školení a jeden z školicích modulů:
  
    -   Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, použijte modul [vlakového modelu](train-model.md) .

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Pokud chcete zobrazit souhrn parametrů modelu spolu s váhy funkcí zjištěnými ze školení, klikněte pravým tlačítkem myši na výstup [modelu výuky](./train-model.md).


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 