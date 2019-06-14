---
title: 'Two-Class posíleného rozhodovacího stromu: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu Two-Class Boosted Decision Tree ve službě Azure Machine Learning vytvořit model, který je založen na algoritmu Posílený rozhodovací stromy strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09ea530cac5bdbd62208f134177e5ceaccb545e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027942"
---
# <a name="two-class-boosted-decision-tree-module"></a>Two-Class Boosted Decision Tree modulu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření modelu strojového učení, který je založen na algoritmu Posílený rozhodovací stromy. 

Posíleného rozhodovacího stromu je kompletu učení metody, ve kterém stromu druhý opraví chyby stromu první stromu třetí opraví chyby prvního a druhého stromy a tak dále.  Předpovědi jsou založené na celé skupiny stromů stromů společně, umožňující do predikce.
  
Obecně platí pokud správně nakonfigurovaná, jsou Posílený rozhodovací stromy nejjednodušší metody, pomocí kterého se má získat maximální výkon na širokou škálu úlohy strojového učení. Nicméně jsou také jedním z inteligentních algoritmů více vysokými nároky na paměť a aktuální implementace obsahuje všechno, co v paměti. Model posíleného rozhodovacího stromu proto nemusí být schopna zpracovat velké datové sady, který dokáže zpracovat některé lineární inteligentních algoritmů.

## <a name="how-to-configure"></a>Postup konfigurace

Tento modul vytvoří model Nezkušený klasifikace. Protože klasifikace je metodu učení pro trénování modelu, je třeba *označené datovou sadu* , který obsahuje popisek sloupce s hodnotou pro všechny řádky.

Trénovat tento typ modelu pomocí [trénování modelu](././train-model.md). 

1.  Ve službě Azure Machine Learning, přidejte **Boosted Decision Tree** modulu do experimentu.
  
2.  Určení způsobu modelu zaškolení, tak, že nastavíte **režimu vytváření trainer** možnost.
  
    + **Jeden parametr**: Pokud víte, jak chcete provést konfiguraci modelu, můžete zadat konkrétní sadu hodnot jako argumenty.
  
  
3.  Pro **maximální počet listů za stromu**, umožňuje určit maximální počet uzlů terminálu (listy), které je možné vytvořit v libovolné stromu.
  
     Zvýšením tuto hodnotu potenciálně zvýšit velikost stromu a získat vyšší přesnost, nese overfitting a už školení čas.
  
4.  Pro **minimální počet vzorků za uzel typu list**, určit počet případů vyžadovaných k vytvoření libovolného uzlu terminálu (listový) ve stromové struktuře.  
  
     Zvýšením tuto hodnotu zvýšit prahovou hodnotu pro vytvoření nového pravidla. Výchozí hodnotu 1, ještě jeden případ může způsobit, který se má vytvořit nové pravidlo. Pokud zvýšíte hodnotu 5, museli byste trénovací data obsahují aspoň pět případy, které splňují stejných podmínek.
  
5.  Pro **rychlost učení**, zadejte číslo mezi 0 a 1, který definuje velikost kroku při učení.  
  
     Rychlost učení Určuje, jak rychle nebo pomalu learner sladila na optimální řešení. Pokud velikost kroku je příliš velký, může být překročení optimální řešení. Pokud je příliš malá velikost kroku, školení trvá déle a umožňuje konvergovat v nejlepším řešením.
  
6.  Pro **počet stromů vytvořený**, určení celkového počtu rozhodovacích stromů k vytvoření v skupiny stromů. Vytvořením další rozhodovacích stromů, můžete potenciálně získat lepší pokrytí, ale čas školení zvýší.
  
     Tato hodnota také řídí počet stromové struktury zobrazují při vizualizaci trénovaného modelu. Pokud chcete zobrazit nebo vytisknout jediného stromu, nastavte hodnotu na 1. Ale pokud tak učiníte, pouze jeden stromu je vytvořen (stromové struktury s počáteční sadu parametrů) a jsou prováděny žádné další iterace.
  
7.  Pro **náhodná počáteční hodnota čísla**, volitelně zadejte nezáporné celé číslo, který se použije jako náhodný počáteční hodnoty. Určení základní hodnota zajistí reprodukovatelnost během spuštění, které mají stejnou dat a parametry.  
  
     Náhodná počáteční hodnota je standardně nastavena na 0, což znamená, že je počáteční počáteční hodnoty získané ze systémových hodin.  Po sobě jdoucích běhy využívající náhodná počáteční hodnota může vést k odlišným výsledkům.
  

9. Trénování modelu.
  
    + Pokud nastavíte **režimu vytváření trainer** k **jeden parametr**, připojení označené datové sady a [Train Model](./train-model.md) modulu.  
  
   
## <a name="results"></a>Výsledky

Po dokončení cvičení modelu klikněte pravým tlačítkem na výstup [Train Model](./train-model.md) zobrazíte výsledky:

+ Pokud chcete zobrazit stromu, který byl vytvořen při každé iteraci, vyberte **vizualizovat**. 
+ K podrobnostem o rozdělení a zobrazit pravidla pro každý uzel, klikněte na každém stromu.


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 