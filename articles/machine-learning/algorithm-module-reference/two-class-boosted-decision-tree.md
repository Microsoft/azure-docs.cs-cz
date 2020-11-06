---
title: 'Two-Class zvýšení rozhodovacího stromu: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu Two-Class posíleného stromu rozhodovacích stromů v Návrháři vytvořit binární klasifikátor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 568cd7b280b8fb65b5f75588a2832631361938dd
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420610"
---
# <a name="two-class-boosted-decision-tree-module"></a>Modul Two-Class zvýšení rozhodovacího stromu

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete vytvořit model strojového učení, který je založený na algoritmu zesílených rozhodovacích stromů. 

Posílený rozhodovací strom je metoda učení kompletu, ve které druhý strom opravuje chyby prvního stromu, třetí strom pro chyby první a druhé stromy a tak dále. Předpovědi jsou založené na celé kompletování stromů dohromady, které tvoří předpověď.
  
Obecně platí, že při správné konfiguraci jsou posílené rozhodovací stromy nejjednodušším způsobem, kterým získáte nejvyšší výkon nejrůznějších úloh strojového učení. Jsou to však také jedním z dalších učí náročných na paměť a aktuální implementace uchovává vše v paměti. Proto je možné, že model zesíleného rozhodovacího stromu nebude moci zpracovat velké datové sady, které mohou procesy lineárních učí zpracovat.

Tento modul je založený na LightGBM algoritmu.

## <a name="how-to-configure"></a>Jak nakonfigurovat

Tento modul vytváří nevlakový model klasifikace. Vzhledem k tomu, že klasifikace je metoda učení pod dohledem, budete potřebovat *datovou sadu s příznakem* , která obsahuje sloupec popisku s hodnotou pro všechny řádky.

Tento typ modelu můžete vyškolit pomocí [modelu výuky](././train-model.md). 

1.  V Azure Machine Learning přidejte do svého kanálu modul **zesíleného rozhodovacího stromu** .
  
2.  Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .
  
    + **Jediný parametr** : Pokud víte, jak chcete model konfigurovat, můžete zadat konkrétní sadu hodnot jako argumenty.
  
    + **Rozsah parametrů** : Pokud si nejste jisti nejlepšími parametry, můžete najít optimální parametry pomocí modulu [ladit model parametrů](tune-model-hyperparameters.md) . Poskytnete určitou škálu hodnot a Trainer iterovat více kombinací nastavení a určí kombinaci hodnot, které vytvářejí nejlepší výsledek.
  
3.  U **maximálního počtu pochodů na strom** určete maximální počet uzlů terminálu (opustí), které je možné vytvořit v jakémkoli stromu.
  
     Zvýšením této hodnoty můžete zvýšit velikost stromu a dosáhnout lepší přesnosti, a to na riziko přebudování a delší dobu školení.
  
4.  Pro **minimální počet vzorků na uzel listu** určete počet případů vyžadovaných k vytvoření libovolného uzlu terminálu (list) ve stromu.  
  
     Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel. Například výchozí hodnota 1, dokonce i jeden případ, může způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, musí školicí data obsahovat alespoň pět případů splňujících stejné podmínky.
  
5.  Do pole **rychlost učení** zadejte číslo od 0 do 1, které definuje velikost kroku při učení.  
  
     Studijní frekvence určuje, jak rychle nebo pomalu se učí sblížení s optimálním řešením. Pokud je velikost kroku moc velká, můžete optimální řešení vyhodnotit. Pokud je velikost kroku příliš malá, školení trvá déle, než se sblížení dostanou na nejlepší řešení.
  
6.  Pro **počet konstruovaných stromů** určete celkový počet rozhodovacích stromů, které se mají v kompletu vytvořit. Vytvořením dalších rozhodovacích stromů můžete získat lepší pokrytí, ale čas školení se zvýší.
  
     Tato hodnota také určuje počet stromů zobrazených při vizualizaci výukového modelu. Pokud chcete zobrazit nebo vytisknout jeden strom, nastavte hodnotu na 1. Nicméně když to uděláte, vytvoří se jenom jeden strom (strom s počáteční sadou parametrů) a žádné další iterace se neprovádí.
  
7.  V případě **náhodného čísla počáteční** hodnota zadejte nezáporné celé číslo, které chcete použít jako hodnotu náhodného osazení. Určení počáteční hodnoty zajišťuje reprodukovatelnost v různých spuštěních, která mají stejná data a parametry.  
  
     Náhodné osazení je ve výchozím nastavení nastaveno na hodnotu 0, což znamená, že počáteční počáteční hodnota se získá ze systémových hodin.  Po sobě jdoucí běhy s náhodným osivem můžou být odlišné výsledky.
  

9. Výukové modely:

    + Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr** , připojíte tagované datové sady a modul [vlakového modelu](train-model.md) .  
  
    + Pokud nastavíte **režim vytvořit Trainer** na **rozsah parametrů** , připojíte s příznakovou datovou sadu a provedete model pomocí [předparametrů ladit model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Pokud předáte rozsah parametru pro [vlakový model](train-model.md), použije se jenom výchozí hodnota v seznamu jednoho parametru.  
    > 
    > Pokud předáte jednu sadu hodnot parametrů do modulu [předparametrů modelu ladění](tune-model-hyperparameters.md) , když očekává rozsah nastavení pro každý parametr, ignoruje hodnoty a použije výchozí hodnoty pro učit se.  
    > 
    > Pokud vyberete možnost **rozsahu parametrů** a zadáte jednu hodnotu pro libovolný parametr, tato jediná hodnota, kterou jste zadali, se použije v celém oblouku, a to i v případě, že se jiné parametry mění v rozsahu hodnot.  
   
## <a name="results"></a>Výsledky

Po dokončení školení:

+ Pokud chcete uložit snímek výukového modelu, vyberte kartu **výstupy** na pravém panelu modulu **výuka modelu** . Výběrem ikony **Registrovat datovou sadu** uložte model jako opakovaně použitelný modul.

+ Chcete-li použít model pro bodování, přidejte modul určení **skóre modelu** do kanálu.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 