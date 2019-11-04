---
title: 'Více třídou rozhodovacího stromu s zvýšením úrovně: Reference k modulu'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul více tříd pro rozhodovací strom s více třídami v Azure Machine Learning k vytvoření klasifikátoru pomocí popisků dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 08/22/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b53e504e98cab34fdc50ee8715ec162c910dd40d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465982"
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


    *  **Maximální počet ponechání na stromovou strukturu** omezuje maximální počet uzlů terminálů (opustí), které je možné vytvořit v jakémkoli stromu.
    
        Zvýšením této hodnoty můžete zvýšit velikost stromu a dosáhnout vyšší přesnosti, a to na riziko přebudování a delší dobu školení.
  
    * **Minimální počet vzorků na uzel na list** určuje počet případů vyžadovaných k vytvoření libovolného uzlu terminálu (list) ve stromu.  

         Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel. Například výchozí hodnota 1, dokonce i jeden případ, může způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, musí školicí data obsahovat alespoň pět případů splňujících stejné podmínky.

    * **Studijní frekvence** definuje velikost kroku při učení. Zadejte číslo od 0 do 1.

         Studijní frekvence určuje, jak rychle nebo pomalu se učí sblížení s optimálním řešením. Pokud je velikost kroku moc velká, můžete optimální řešení vyhodnotit. Pokud je velikost kroku příliš malá, školení trvá déle, než se sblížení dostanou na nejlepší řešení.

    * **Počet vytvořených stromů** označuje celkový počet rozhodovacích stromů, které se mají v kompletu vytvořit. Vytvořením dalších rozhodovacích stromů můžete získat lepší pokrytí, ale čas školení se zvýší.

    *  **Náhodné číslo semen** volitelně nastaví nezáporné celé číslo, které se použije jako hodnota náhodného základu. Určení počáteční hodnoty zajišťuje reprodukovatelnost v různých spuštěních, která mají stejná data a parametry.  

         Náhodné osazení je ve výchozím nastavení nastaveno na 42. Po sobě jdoucí běhy s různými náhodnými osivy můžou mít různé výsledky.

> [!Note]
> Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, připojíte tagované datové sady a modul [vlakového modelu](./train-model.md) .

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
