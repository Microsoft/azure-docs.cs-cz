---
title: 'Rozhodovací les regrese: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu Two-Class Zprůměrované Perceptron ve službě Azure Machine Learning vytvořit model podle průměrné perceptron algoritmu strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f0fec525ed87f91cf102053383b2934aac4b71c0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029232"
---
# <a name="two-class-averaged-perceptron-module"></a>Modul Perceptron Zprůměrované Two-Class

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření model podle průměrné perceptron algoritmu strojového učení.  
  
Tento algoritmus klasifikace je metoda učení a vyžaduje *označené datovou sadu*, který obsahuje popisek sloupce. Můžou natrénujete tím, že poskytuje model a označené datovou sadu jako vstup do [trénování modelu](./train-model.md). Trénovaného modelu lze pak použít k předpovědi hodnot pro nový vstupní příklady.  

### <a name="about-averaged-perceptron-models"></a>O modelech průměrné perceptron

*Byla metoda perceptron* je raných fázích i jednoduchý verze neuronové sítě. V takovém případě jsou vstupy rozdělit do několika možných výstupy na základě lineární funkce a následně se spojí dohromady sadu váhy, které jsou odvozeny z funkce vector – proto název "perceptron."

Jednodušší perceptron modely jsou vhodné k učení lineárně oddělitelné vzory, zatímco neuronových sítí (zejména hluboké neuronové sítě) můžete modelovat složitější hranice třídy. Ale perceptrons jsou rychlejší a vzhledem k tomu, že budou zpracovávat případů sériově perceptrons jde použít s průběžné vzdělávání.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Jak nakonfigurovat Perceptron Zprůměrované Two-Class

1.  Přidat **Two-Class Zprůměrované Perceptron** modulu do experimentu.  

2.  Určení způsobu modelu zaškolení, tak, že nastavíte **režimu vytváření trainer** možnost.  
  
    -   **Jeden parametr**: Pokud víte, jak chcete provést konfiguraci modelu, poskytují konkrétní sadu hodnot jako argumenty.
  
3.  Pro **rychlost učení**, zadejte hodnotu *rychlost učení*. Rychlost učení hodnoty ovládacího prvku velikost krok, který se používá v stochastického sestupu pokaždé, když model je testováno a opravit.
  
     Tím, že míra menších, při testování modelu častěji, s využitím riziko, že můžou uváznout ve místní limitu. Tím, že v kroku větší, můžete použít sloučit rychleji, nese překročení true minima.
  
4.  Pro **maximální počet iterací**, zadejte počet pokusů o má algoritmus pro zjištění trénovací data.  
  
     Zastavuje se již v rané fázi často poskytuje lepší generalizace. Zvýšení počtu iterací zlepšuje přizpůsobování nese overfitting.
  
5.  Pro **náhodná počáteční hodnota čísla**, volitelně zadejte celočíselnou hodnotu pro použití jako počáteční hodnoty. Pokud chcete Ujistěte se, že běží reprodukovatelnost experimentu napříč, doporučuje se použít počáteční hodnoty.  
  
1.  Připojte trénovací datovou sadu a jeden z modulů školení:
  
    -   Pokud nastavíte **režimu vytváření trainer** k **jediný parametr**, použijte [trénování modelu](train-model.md) modulu.

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Chcete-li zobrazit souhrn parametry modelu, společně s funkcí váhy získané při školení, klikněte pravým tlačítkem na výstup [Train Model](./train-model.md).


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 