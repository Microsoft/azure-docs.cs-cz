---
title: 'Zesílené regrese rozhodovacího stromu: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat v Azure Machine Learning modul pro regresi se zvýšením rozhodovacího stromu a vytvořit ze sady regresních stromů pomocí zvyšování úrovně.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 4271a180a0f00ae921de9b8deb9a3e5123f9b519
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314483"
---
# <a name="boosted-decision-tree-regression-module"></a>Zesílený modul pro regresi rozhodovacího stromu

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Tento modul použijte k vytvoření kompletu regresních stromů pomocí zvyšování úrovně. *Zvyšování úrovně* znamená, že každý strom je závislý na předchozí stromové struktuře. Algoritmus se učí tím, že se dovedou zbytky stromů, které jí předcházejí. Proto zvýšení přesnosti v kompletu rozhodovacího stromu zamýšlí zlepšit přesnost s malým rizikem menšího rozsahu.  
  
Tato regresní metoda je metoda učení pod dohledem, a proto vyžaduje *datovou sadu s popiskem*. Sloupec popisku musí obsahovat číselné hodnoty.  

> [!NOTE]
> Tento modul použijte pouze s datovými sadami, které používají číselné proměnné.  

Po definování modelu ho pomocí [modelu vlaků](./train-model.md)provedete.

  
## <a name="more-about-boosted-regression-trees"></a>Další informace o zesílených regresních stromech  

Zvýšení úrovně je jedna z různých klasických metod pro vytváření modelů kompletů, včetně zaznamenání na náhodné doménové struktury a tak dále.  V Azure Machine Learning se posílené rozhodovací stromy používaly efektivní implementací algoritmu pro zvýšení počtu TRŽIŠTě. Zvýšení barevného přechodu je technika strojového učení pro regresní problémy. Sestaví každý regresní strom v rámci krokování pomocí předdefinované funkce ztráty k měření chyby v jednotlivých krocích a jejich opravě v dalším kroku. Proto je model předpovědi ve skutečnosti ze sady slabších předpovědí modelů.  
  
Ve regresních problémech zvyšuje sestavování řadu stromů v rámci kroků a pak vybere optimální strom pomocí libovolné funkce differentiable ztráty.  
  
Další informace najdete v těchto článcích:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Tento článek Wikipedii o nárůstu gradientu poskytuje určité pozadí na zesílených stromech. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: od RankNet do LambdaRank do LambdaMART: Přehled. Od J.C. Burges.

Metodu zvyšování barevného přechodu lze také použít pro problémy s klasifikací tím, že je zmenšíte na regresi pomocí vhodné funkce ztráty. Další informace o implementaci posílených stromů pro úlohy klasifikace najdete v článku o [rozhodovacím stromu se dvěma třídami](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Jak nakonfigurovat zvýšení regrese rozhodovacího stromu

1.  Přidejte do svého kanálu modul **zesíleného rozhodovacího stromu** . Tento modul najdete pod **Machine Learning**, **inicializovat**v rámci kategorie **regrese** . 
  
2.  Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .  
  
    -   **Jeden parametr**: tuto možnost vyberte, pokud víte, jak chcete model konfigurovat, a poskytněte konkrétní sadu hodnot jako argumenty.  
   
  
3. **Maximální počet pochodů na stromovou strukturu**: Určuje maximální počet uzlů terminálu (opustí), které je možné vytvořit v jakémkoli stromu.  

    Zvýšením této hodnoty můžete zvýšit velikost stromu a dosáhnout lepší přesnosti, a to na riziko přebudování a delší dobu školení.  

4. **Minimální počet vzorků na uzel typu list**: Určuje minimální počet případů vyžadovaných k vytvoření libovolného uzlu terminálu (list) ve stromu.

    Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel. Například výchozí hodnota 1, dokonce i jeden případ, může způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, musí školicí data obsahovat alespoň 5 případů, které splňují stejné podmínky.

5. **Studijní frekvence**: zadejte číslo od 0 do 1, které definuje velikost kroku při učení. Studijní frekvence určuje, jak rychle nebo pomalu se učí sblížení s optimálním řešením. Pokud je velikost kroku příliš velká, můžete optimální řešení vyhodnotit. Pokud je velikost kroku příliš malá, školení trvá déle, než se sblížení dostanou na nejlepší řešení.

6. **Počet konstruovaných stromů**: uveďte celkový počet rozhodovacích stromů, které se mají v kompletu vytvořit. Vytvořením dalších rozhodovacích stromů můžete potenciálně získat lepší pokrytí, ale zvýší se čas školení.

    Tato hodnota také určuje počet stromů zobrazených při vizualizaci výukového modelu. Pokud chcete zobrazit nebo vytisknout jeden strom, můžete hodnotu nastavit na 1. je však vytvořen pouze jeden strom (strom s počáteční sadou parametrů) a nejsou provedeny žádné další iterace.

7. **Počáteční číslo osiva**: zadejte volitelné nezáporné celé číslo, které se použije jako hodnota náhodného základu. Určení počáteční hodnoty zajišťuje reprodukovatelnost v různých spuštěních, která mají stejná data a parametry.

    Ve výchozím nastavení je náhodné osazení nastaveno na hodnotu 0, což znamená, že počáteční počáteční hodnota se získá ze systémových hodin.
  
8. **Povolení neznámých úrovní kategorií**: tuto možnost vyberte, pokud chcete vytvořit skupinu pro neznámé hodnoty v sadách pro školení a ověření. Pokud zrušíte výběr této možnosti, může model přijímat pouze hodnoty, které jsou obsaženy v školicích datech. Model může být pro známé hodnoty méně přesný, ale může poskytovat lepší předpovědi pro nové (neznámé) hodnoty.

9. Přidejte datovou sadu školení a jeden z školicích modulů:

    - Pokud nastavíte možnost **vytvořit režim Trainer** na **jeden parametr**, použijte modul [vlakového modelu](train-model.md) .  
  
    

10. Spuštění kanálu  
  
## <a name="results"></a>Výsledky

Po dokončení školení:

+ Chcete-li použít model pro bodování, připojte ho k [modelu skóre](./score-model.md)a předpovídat hodnoty pro nové vstupní příklady.

+ Snímek trained model uložíte tak, že vyberete kartu **výstupy** na pravém panelu **trained model** a kliknete na ikona **zaregistrovat datovou sadu** . Kopie vyškolené modelu bude uložena jako modul ve stromu modulu a nebude aktualizována při následných spuštěních kanálu.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
