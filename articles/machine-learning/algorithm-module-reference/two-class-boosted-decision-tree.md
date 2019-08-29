---
title: 'Zvýšení rozhodovacího stromu se dvěma třídami: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul zesíleného rozhodovacího stromu se dvěma třídami ve službě Azure Machine Learning, abyste vytvořili model strojového učení, který je založený na algoritmu posílené rozhodovací stromy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 812fb8d312b165bd43f600da520f390f9c6399fe
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128405"
---
# <a name="two-class-boosted-decision-tree-module"></a>Modul pro zvýšení rozhodovacího stromu se dvěma třídami

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Pomocí tohoto modulu můžete vytvořit model strojového učení, který je založený na algoritmu zesílených rozhodovacích stromů. 

Posílený rozhodovací strom je metoda učení kompletu, ve které druhý strom opravuje chyby prvního stromu, třetí strom pro chyby první a druhé stromy a tak dále.  Předpovědi jsou založené na celé kompletování stromů dohromady, které tvoří předpověď.
  
Obecně platí, že při správné konfiguraci jsou posílené rozhodovací stromy nejjednodušším způsobem, kterým získáte nejvyšší výkon nejrůznějších úloh strojového učení. Jsou to však také jedním z dalších učí náročných na paměť a aktuální implementace uchovává vše v paměti. Proto je možné, že model zesíleného rozhodovacího stromu nebude moci zpracovat velké datové sady, které mohou procesy lineárních učí zpracovat.

## <a name="how-to-configure"></a>Jak nakonfigurovat

Tento modul vytváří nevlakový model klasifikace. Vzhledem k tomu, že klasifikace je metoda učení pod dohledem, budete potřebovat datovou *sadu* s příznakem, která obsahuje sloupec popisku s hodnotou pro všechny řádky.

Tento typ modelu můžete vyškolit pomocí [modelu výuky](././train-model.md). 

1.  V Azure Machine Learning přidejte do experimentu modul **zesíleného rozhodovacího stromu** .
  
2.  Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .
  
    + **Jeden parametr**: Pokud víte, jak chcete model konfigurovat, můžete zadat konkrétní sadu hodnot jako argumenty.
  
  
3.  U **maximálního počtu pochodů na strom**určete maximální počet uzlů terminálu (opustí), které je možné vytvořit v jakémkoli stromu.
  
     Zvýšením této hodnoty můžete zvýšit velikost stromu a dosáhnout lepší přesnosti, a to na riziko přebudování a delší dobu školení.
  
4.  Pro **minimální počet vzorků na uzel listu**určete počet případů vyžadovaných k vytvoření libovolného uzlu terminálu (list) ve stromu.  
  
     Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel. Například výchozí hodnota 1, dokonce i jeden případ, může způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, musí školicí data obsahovat alespoň pět případů splňujících stejné podmínky.
  
5.  Do pole **rychlost učení**zadejte číslo od 0 do 1, které definuje velikost kroku při učení.  
  
     Studijní frekvence určuje, jak rychle nebo pomalu se učí sblížení s optimálním řešením. Pokud je velikost kroku moc velká, můžete optimální řešení vyhodnotit. Pokud je velikost kroku příliš malá, školení trvá déle, než se sblížení dostanou na nejlepší řešení.
  
6.  Pro **počet konstruovaných stromů**určete celkový počet rozhodovacích stromů, které se mají v kompletu vytvořit. Vytvořením dalších rozhodovacích stromů můžete získat lepší pokrytí, ale čas školení se zvýší.
  
     Tato hodnota také určuje počet stromů zobrazených při vizualizaci výukového modelu. Pokud chcete zobrazit nebo vytisknout jeden strom, nastavte hodnotu na 1. Nicméně když to uděláte, vytvoří se jenom jeden strom (strom s počáteční sadou parametrů) a žádné další iterace se neprovádí.
  
7.  V případě **náhodného čísla počáteční**hodnota zadejte nezáporné celé číslo, které chcete použít jako hodnotu náhodného osazení. Určení počáteční hodnoty zajišťuje reprodukovatelnost v různých spuštěních, která mají stejná data a parametry.  
  
     Náhodné osazení je ve výchozím nastavení nastaveno na hodnotu 0, což znamená, že počáteční počáteční hodnota se získá ze systémových hodin.  Po sobě jdoucí běhy s náhodným osivem můžou být odlišné výsledky.
  

9. Proveďte výuku modelu.
  
    + Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, připojíte tagované datové sady a modul [vlakového modelu](./train-model.md) .  
  
   
## <a name="results"></a>Výsledky

Po dokončení školení modelů klikněte pravým tlačítkem na výstup výukového [modelu](./train-model.md) pro zobrazení výsledků:

+ Chcete-li zobrazit strom, který byl vytvořen při každé iteraci, vyberte **vizualizovat**. 
+ Chcete-li přejít k podrobnostem o rozdělení a zobrazit pravidla pro každý uzel, klikněte na jednotlivé stromové struktury.


## <a name="next-steps"></a>Další postup

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 