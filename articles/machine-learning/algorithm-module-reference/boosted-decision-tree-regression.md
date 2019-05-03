---
title: 'Posílená regrese rozhodovacího stromu: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu Boosted regrese rozhodovacího stromu ve službě Azure Machine Learning k vytvoření kompletu regresní stromy pomocí zvýšení skóre.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b4ebf1740ec2b0288d8052cb075a61b720b031a2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028332"
---
# <a name="boosted-decision-tree-regression-module"></a>Posílená regrese rozhodovacího stromu modulu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření kompletu regresní stromy pomocí zvýšení skóre. *Zvýšení skóre* znamená, že je závislá na předchozí stromů každém stromu. Algoritmus učí montáží zbývající stromové struktury, které ho. Proto zvýšení skóre v komplet stromu rozhodnutí obvykle zvyšte přesnost s určité riziko malé nižší pokrytí.  
  
Tato metoda regrese je metoda učení a vyžaduje tudíž *označené jako datovou sadu*. Popisek sloupce musí obsahovat číselné hodnoty.  

> [!NOTE]
> Tento modul slouží pouze u datových sad použijte číselné proměnné.  

Po definování model trénovat pomocí [trénování modelu](./train-model.md).

> [!TIP]
> Chcete se dozvědět víc o stromové struktury, které byly vytvořeny? Jakmile model se trénuje, klikněte pravým tlačítkem na výstup [Train Model](./train-model.md) modul a vyberte **vizualizovat** zobrazíte stromu, který byl vytvořen při každé iteraci. Můžete k podrobnostem rozdělení pro každou strom a zobrazit pravidla pro každý uzel.  
  
## <a name="more-about-boosted-regression-trees"></a>Další informace o Posílený regresní stromy  

Zvýšení skóre je jedním z několika klasické metody pro vytváření modelů skupiny stromů, spolu s pytlování, náhodných doménových struktur a tak dále.  Ve službě Azure Machine Learning použijte Posílený rozhodovací stromy efektivní provádění přechodu TRŽIŠTĚ zvýšení skóre algoritmus. Přechodu zvýšení skóre je strojové učení techniku pro regresní problémy. Sestaví každém stromu regrese způsobem podle jednotlivých kroků, pomocí funkce předdefinované ztráty pro měření chyb v každém kroku a opravit ho v dalším. Prediktivní model proto je ve skutečnosti kompletu sady slabší prediktivní modely.  
  
Regrese problémy zvýšení skóre sestavení řadu stromové struktury podle jednotlivých kroků způsobem a potom vybere optimální stromu použití libovolného differentiable ztráta funkce.  
  
Další informace najdete v těchto článcích:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Tohoto článku na wikipedii na přechodu zvýšení poskytuje základní informace na Posílený stromové struktury. 
  
-  [http://research.microsoft.com/apps/pubs/default.aspx?id=132652](http://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: Z RankNet k LambdaRank k LambdaMART: Přehled. Podle J.C. Burges.

Přechodu zvýšení skóre metody lze také pro klasifikaci problémy snížením k regresi s funkcí vhodný ztráty. Další informace o implementaci Posílený stromů pro úlohy klasifikace najdete v tématu [Two-Class Boosted Decision Tree](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Jak nakonfigurovat Boosted regrese rozhodovacího stromu

1.  Přidat **Boosted Decision Tree** modulu do experimentu. Můžete najít tento modul v rámci **Machine Learning**, **inicializovat**v části **regrese** kategorie. 
  
2.  Určení způsobu modelu zaškolení, tak, že nastavíte **režimu vytváření trainer** možnost.  
  
    -   **Jeden parametr**: Tuto možnost vyberte, pokud víte, jak chcete konfigurovat modelu a poskytnout konkrétní sadu hodnot jako argumenty.  
   
  
3. **Maximální počet listů za stromu**: Umožňuje určete maximální počet uzlů terminálu (listy), které je možné vytvořit v libovolné stromu.  

    Zvýšením tuto hodnotu potenciálně zvýšit velikost stromu a získat vyšší přesnost, nese overfitting a už školení čas.  

4. **Minimální počet vzorků za uzel typu list**: Určete minimální počet případů vyžadovaných k vytvoření libovolného uzlu terminálu (listový) ve stromové struktuře.

    Zvýšením tuto hodnotu zvýšit prahovou hodnotu pro vytvoření nového pravidla. Výchozí hodnotu 1, ještě jeden případ může způsobit, který se má vytvořit nové pravidlo. Pokud zvýšíte hodnotu 5, museli byste trénovacích dat obsahovat aspoň 5 případy, které splňují stejných podmínek.

5. **Rychlost učení**: Zadejte číslo mezi 0 a 1, který definuje velikost kroku při učení. Rychlost učení Určuje, jak rychle nebo pomalu learner sladila na optimální řešení. Pokud velikost kroku je příliš velká, může být překročení optimální řešení. Pokud je příliš malá velikost kroku, školení trvá déle a umožňuje konvergovat v nejlepším řešením.

6. **Počet stromů vytvořený**: Určení celkového počtu rozhodovacích stromů k vytvoření v skupiny stromů. Vytvořením další rozhodovacích stromů, může potenciálně získat lepší pokrytí, ale když školení zvýší.

    Tato hodnota také řídí počet stromové struktury zobrazují při vizualizaci trénovaného modelu. Pokud chcete zobrazit nebo vytisknout strom jedna, můžete nastavit hodnotu 1. však je vytvořen pouze jeden stromové struktury (stromové struktury s počáteční sadu parametrů) a jsou prováděny žádné další iterace.

7. **Náhodné číslo**: Zadejte volitelný nezáporné celé číslo pro použití jako náhodný počáteční hodnoty. Určení základní hodnota zajistí reprodukovatelnost během spuštění, které mají stejnou dat a parametry.

    Ve výchozím nastavení náhodná počáteční hodnota je nastavena na hodnotu 0, což znamená, že je počáteční počáteční hodnoty získané ze systémových hodin.
  
8. **Povolit neznámé zařazené do kategorií úrovně**: Vyberte tuto možnost, chcete-li vytvořit skupinu pro neznámé hodnoty v sadách školení a ověřování. Pokud výběr této možnosti modelu může přijmout hodnoty, které jsou obsaženy v trénovací data. Model může být méně přesné pro známé hodnoty, ale může poskytovat lepší předpovědi pro nové hodnoty (neznámá).

9. Přidáte trénovací datovou sadu a jeden z modulů školení:

    - Pokud nastavíte **režimu vytváření trainer** umožňuje **jeden parametr**, použijte [Train Model](train-model.md) modulu.  
  
    

10. Spusťte experiment.  
  
### <a name="results"></a>Výsledky

Po dokončení školení:

+ Pokud chcete zobrazit stromu, který byl vytvořen při každé iteraci, klikněte pravým tlačítkem na výstup [Train Model](train-model.md) modul a vyberte **vizualizovat**.
  
     Klikněte na každém stromu podrobnostem rozdělení a zobrazit pravidla pro každý uzel.  

+ K použití modelu pro vyhodnocení, připojte ho k [určení skóre modelu](./score-model.md), k předpovědi hodnot pro nový vstupní příklady.

+ Uložte snímek trénovaného modelu, klikněte pravým tlačítkem myši **Trained model** výstup školicí modul a vyberte **uložit jako**. Kopírování trénovaného modelu, který můžete uložit není aktualizován na následné spuštění experimentu.

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 