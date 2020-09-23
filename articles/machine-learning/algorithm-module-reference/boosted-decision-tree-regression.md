---
title: 'Zesílené regrese rozhodovacího stromu: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat v Azure Machine Learning modul pro regresi se zvýšením rozhodovacího stromu a vytvořit ze sady regresních stromů pomocí zvyšování úrovně.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 21cd1491da4374babaff8b5b4b451a3cce874368
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898839"
---
# <a name="boosted-decision-tree-regression-module"></a>Zesílený modul pro regresi rozhodovacího stromu

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Tento modul použijte k vytvoření kompletu regresních stromů pomocí zvyšování úrovně. *Zvyšování úrovně* znamená, že každý strom je závislý na předchozí stromové struktuře. Algoritmus se učí tím, že se dovedou zbytky stromů, které jí předcházejí. Proto zvýšení přesnosti v kompletu rozhodovacího stromu zamýšlí zlepšit přesnost s malým rizikem menšího rozsahu.  

Tento modul je založený na LightGBM algoritmu.
  
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
     
    -   **Rozsah parametrů**: tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit mazání parametrů. Vyberte rozsah hodnot, na které se mají iterovat, a s [parametry modelu ladění](tune-model-hyperparameters.md) prochází všechny možné kombinace nastavení, které jste zadali, abyste určili, jaké parametry jsou výsledkem optimálních výsledků.    
   
  
3. **Maximální počet pochodů na stromovou strukturu**: Určuje maximální počet uzlů terminálu (opustí), které je možné vytvořit v jakémkoli stromu.  

    Zvýšením této hodnoty můžete zvýšit velikost stromu a dosáhnout lepší přesnosti, a to na riziko přebudování a delší dobu školení.  

4. **Minimální počet vzorků na uzel typu list**: Určuje minimální počet případů vyžadovaných k vytvoření libovolného uzlu terminálu (list) ve stromu.

    Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel. Například výchozí hodnota 1, dokonce i jeden případ, může způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, musí školicí data obsahovat alespoň 5 případů, které splňují stejné podmínky.

5. **Studijní frekvence**: zadejte číslo od 0 do 1, které definuje velikost kroku při učení. Studijní frekvence určuje, jak rychle nebo pomalu se učí sblížení s optimálním řešením. Pokud je velikost kroku příliš velká, můžete optimální řešení vyhodnotit. Pokud je velikost kroku příliš malá, školení trvá déle, než se sblížení dostanou na nejlepší řešení.

6. **Počet konstruovaných stromů**: uveďte celkový počet rozhodovacích stromů, které se mají v kompletu vytvořit. Vytvořením dalších rozhodovacích stromů můžete potenciálně získat lepší pokrytí, ale zvýší se čas školení.

    Tato hodnota také určuje počet stromů zobrazených při vizualizaci výukového modelu. Pokud chcete zobrazit nebo vytisknout jeden strom, můžete hodnotu nastavit na 1. je však vytvořen pouze jeden strom (strom s počáteční sadou parametrů) a nejsou provedeny žádné další iterace.

7. **Počáteční číslo osiva**: zadejte volitelné nezáporné celé číslo, které se použije jako hodnota náhodného základu. Určení počáteční hodnoty zajišťuje reprodukovatelnost v různých spuštěních, která mají stejná data a parametry.

    Ve výchozím nastavení je náhodné osazení nastaveno na hodnotu 0, což znamená, že počáteční počáteční hodnota se získá ze systémových hodin.
  

9. Výukové modely:

    + Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, připojíte tagované datové sady a modul [vlakového modelu](train-model.md) .  
  
    + Pokud nastavíte **režim vytvořit Trainer** na **rozsah parametrů**, připojíte s příznakovou datovou sadu a provedete model pomocí [předparametrů ladit model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Pokud předáte rozsah parametru pro [vlakový model](train-model.md), použije se jenom výchozí hodnota v seznamu jednoho parametru.  
    > 
    > Pokud předáte jednu sadu hodnot parametrů do modulu [předparametrů modelu ladění](tune-model-hyperparameters.md) , když očekává rozsah nastavení pro každý parametr, ignoruje hodnoty a použije výchozí hodnoty pro učit se.  
    > 
    > Pokud vyberete možnost **rozsahu parametrů** a zadáte jednu hodnotu pro libovolný parametr, tato jediná hodnota, kterou jste zadali, se použije v celém oblouku, a to i v případě, že se jiné parametry mění v rozsahu hodnot.
    

10. Odešlete kanál.  
  
## <a name="results"></a>Výsledky

Po dokončení školení:

+ Chcete-li použít model pro vyhodnocování, připojte model [výuky](train-model.md) k [modelu stanovení skóre](./score-model.md)a předpovídat hodnoty pro nové vstupní příklady.

+ Snímek trained model uložíte tak, že vyberete kartu **výstupy** na pravém panelu **trained model** a kliknete na ikona **zaregistrovat datovou sadu** . Kopie vyškolené modelu bude uložena jako modul ve stromu modulu a nebude aktualizována při následných spuštěních kanálu.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
