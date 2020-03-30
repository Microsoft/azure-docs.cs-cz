---
title: 'Posílená regrese rozhodovacího stromu: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu regrese posíleného rozhodovacího stromu v Azure Machine Learning vytvořit soubor regresních stromů pomocí boostingu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 79a2ccae31fac31d8d10bb643c35a41a3d7cb5d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456722"
---
# <a name="boosted-decision-tree-regression-module"></a>Posílený modul regrese rozhodovacího stromu

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření souboru regresních stromů pomocí zesílení. *Zvýšení* znamená, že každý strom je závislý na předchozích stromech. Algoritmus se učí tím, že odpovídá zbytku stromů, které mu předcházely. Tak, posílení v souboru rozhodovací strom má tendenci zlepšit přesnost s některými malým rizikem menšípokrytí.  
  
Tato regresní metoda je metoda učení pod dohledem, a proto vyžaduje *označenou datovou sadu*. Sloupec popisku musí obsahovat číselné hodnoty.  

> [!NOTE]
> Tento modul používejte pouze u datových sad, které používají číselné proměnné.  

Po definování modelu jej trénujte pomocí [modelu vlaku](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Další informace o posílených regresních stromech  

Boosting je jednou z několika klasických metod pro vytváření modelů souborů, spolu s pytlováním, náhodnými lesy a tak dále.  V Azure Machine Learning, posílené rozhodovací stromy používají efektivní implementaci algoritmu zvýšení přechodu MART. Posílení přechodu je technika strojového učení pro regresní problémy. Vytvoří každý regresní strom krokem způsobem, pomocí předdefinované funkce ztráty k měření chyby v každém kroku a opravit pro něj v dalším kroku. Predikční model je tedy ve skutečnosti soubor slabších predikčních modelů.  
  
V regresních problémech, zvýšení staví řadu stromů v krok-moudrý způsobem a pak vybere optimální strom pomocí libovolné diferenciovatelné funkce ztráty.  
  
Další informace naleznete v těchto článcích:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Tento článek Wikipedie o zvyšování gradientu poskytuje určité zázemí na posílené stromy. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: Od RankNetu přes LambdaRank až po LambdaMART: Přehled. Podle J.C. Burgesovi.

Metoda zesílení gradientu může být také použita pro problémy klasifikace tím, že je sníží na regresi s vhodnou funkcí ztráty. Další informace o implementaci posílených stromů pro úkoly klasifikace naleznete [v tématu Two-Class Boosted Decision Tree](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Jak nakonfigurovat regresi posíleného rozhodovacího stromu

1.  Přidejte do kanálu modul **Posílený rozhodovací strom.** Tento modul najdete v části **Strojové učení**, **Inicializovat**, v kategorii **Regrese.** 
  
2.  Určete, jak má být model trénovaný, nastavením **možnosti Vytvořit režim trenažéru.**  
  
    -   **Jeden parametr**: Tuto možnost vyberte, pokud víte, jak chcete model nakonfigurovat, a zadejte určitou sadu hodnot jako argumenty. 
     
    -   **Rozsah parametrů**: Tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit tažení parametrů. Vyberte rozsah hodnot, které chcete iterát přes a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates přes všechny možné kombinace nastavení, které jste zadali k určení hyperparameters, které produkují optimální výsledky.    
   
  
3. **Maximální počet listů na strom**: Uveďte maximální počet koncových uzlů (listů), které lze vytvořit v libovolném stromu.  

    Zvýšením této hodnoty můžete potenciálně zvětšit velikost stromu a získat lepší přesnost s rizikem nadměrného vybavení a delší doby tréninku.  

4. **Minimální počet vzorků na uzel listu**: Uveďte minimální počet případů potřebných k vytvoření libovolného koncového uzlu (listu) ve stromu.

    Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel. Například s výchozí hodnotou 1 může i jeden případ způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, data školení by musel obsahovat alespoň 5 případů, které splňují stejné podmínky.

5. **Míra učení**: Zadejte číslo mezi 0 a 1, které definuje velikost kroku při učení. Rychlost učení určuje, jak rychle nebo pomalu se student konverguje s optimálním řešením. Pokud je velikost kroku příliš velká, můžete přestřelit optimální řešení. Pokud je velikost kroku příliš malá, školení trvá déle, než se sbíhají na nejlepší řešení.

6. **Počet vytvořených stromů**: Uveďte celkový počet rozhodovacích stromů, které mají být vytvořeny v souboru. Vytvořením více rozhodovacích stromů můžete potenciálně získat lepší pokrytí, ale doba tréninku se zvyšuje.

    Tato hodnota také určuje počet stromů zobrazených při vizualizaci trénovaného modelu. Pokud chcete zobrazit nebo vytisknout jeden strom, můžete nastavit hodnotu na 1; však pouze jeden strom je produkován (strom s počáteční sadou parametrů) a žádné další iterace jsou prováděny.

7. **Osiva náhodných čísel**: Zadejte volitelné nezáporné celé číslo, které se použije jako náhodná hodnota osiva. Určení osiva zajišťuje reprodukovatelnost napříč spuštěními, které mají stejná data a parametry.

    Ve výchozím nastavení je náhodné osivo nastaveno na 0, což znamená, že počáteční hodnota osiva je získána ze systémových hodin.
  

9. Přidejte trénovací datovou sadu a jeden z trénovacích modulů:

    - Pokud nastavíte možnost **Vytvořit režim treniace** na **jeden parametr**, použijte modul [Model vlaku.](train-model.md)  
  
    

10. Odešlete potrubí.  
  
## <a name="results"></a>Výsledky

Po dokončení tréninku:

+ Chcete-li model použít pro vyhodnocování, připojte jej k [modelu skóre](./score-model.md), abyste předpověděli hodnoty pro nové vstupní příklady.

+ Chcete-li uložit snímek trénovaného modelu, vyberte kartu **Výstupy** v pravém panelu **trénovaného modelu** a klepněte na **Register dataset** icon. Kopie trénovaného modelu bude uložena jako modul ve stromu modulu a nebude aktualizována při následných spuštěních kanálu.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
