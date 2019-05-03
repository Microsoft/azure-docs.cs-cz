---
title: 'Rozhodovací les se dvěma třídami: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu rozhodovací les se dvěma třídami ve službě Azure Machine Learning k vytvoření služby machine learning model založený na doménových strukturách algoritmus rozhodnutí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f3e7cab6e33fa9373095441685f6ecb04f1d91a5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029367"
---
# <a name="two-class-decision-forest-module"></a>Modul rozhodovací les se dvěma třídami

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření služby machine learning model založený na doménových strukturách algoritmus rozhodnutí.  

Rozhodnutí doménových strukturách jsou rychlé, pod dohledem komplet modely. Tento modul je dobrou volbou, pokud chcete předpovědět cíl s maximálně dva výsledky. 

## <a name="understanding-decision-forests"></a>Principy rozhodnutí doménových struktur

Tento algoritmus rozhodnutí doménové struktury je kompletu učení metoda určené pro úlohy klasifikace. Komplet metody jsou založeny na obecné zásady, které nespoléhat se na jednom modelu, můžete získat lepší výsledky a obecnější modelu vytvořením více souvisejících modelů a jejich sloučením nějakým způsobem. Modely komplet obecně poskytují lepší pokrytí a přesnost než jeden rozhodovacích stromů. 

Existuje mnoho způsobů, jak vytvořit jednotlivé modely a zkombinujte je v kompletu. Tato konkrétní implementace rozhodovací les funguje tak, že vytváření více rozhodovacích stromů a potom **hlasování** na nejoblíbenější třída output. Hlasování je jedním z better-known metody pro generování výsledků v modelu skupiny stromů. 

+ Vytvoření mnoha stromech jednotlivé klasifikace pomocí celou datovou sadu, ale jiné (obvykle randomizovaného) počáteční body. Tím se liší od přístupu náhodné doménové struktury, ve kterém může používat jednotlivé rozhodovacích stromů pouze část náhodná data nebo funkce.
+ Každý stromu v rozhodovacím stromu doménové struktury výstupy histogram nenormalizovaný četnost popisků. 
+ Proces agregace součet těchto histogramy a normalizuje výsledek, který má získat "pravděpodobností" pro každý popisek. 
+ Stromové struktury, které mají vysokou predikce budete mít větší váhu konečné rozhodnutí skupiny stromů.

Rozhodovací stromy obecně mají mnoho výhod pro úlohy klasifikace:
  
- Hranice nelineárních rozhodnutí, můžete zachytit.
- Můžete trénování a předvídání na velké množství dat, jako jsou efektivní výpočet a využití paměti.
- Výběr funkce je integrovaná do procesy trénování a klasifikace.  
- Stromy zvládne hlučného dat a mnoho funkcí.  
- Jsou-ukazatelů modely, což znamená, že zpracovávají data pomocí různých distribucí. 

Však jednoduché rozhodovacích stromů můžete overfit na data a jsou méně generalizovatelného než umožňující stromu.

Další informace najdete v tématu [doménových struktur rozhodnutí](http://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Postup konfigurace
  
1.  Přidat **rozhodovací les se dvěma třídami** modulu do experimentu v Azure Machine Learning a otevřete **vlastnosti** podokně modulu. 

    Můžete najít v modulu v rámci **Machine Learning**. Rozbalte **inicializovat**a potom **klasifikace**.  
  
2.  Pro **převzorkování metoda**, zvolte metodu použitou k vytvoření jednotlivé stromové struktury.  Můžete si vybrat z **pytlování** nebo **replikovat**.  
  
    -   **Pytlování**: Pytlování se také nazývá *bootstrap agregaci*. V této metodě se na nové ukázky, vytvořil náhodně vzorkování původní datové sady s nahrazení, dokud máte datovou sadu velikosti původní pěstuje každém stromu.  
  
         Výstupy modelů jsou zkombinované podle *hlasování*, což je způsob agregace. Každý stromu v doménové struktuře klasifikace rozhodnutí výstupy histogram Nenormalizovaná četnost popisků. Agregace je součet těchto histogramy a normalizovat zobrazíte "pravděpodobností" pro každý popisek. Tímto způsobem budete mít stromové struktury, které mají vysokou predikce větší váhu konečné rozhodnutí skupiny stromů.  
  
         Další informace naleznete v příspěvku Wikipedia pro zavedení agregaci.  
  
    -   **Replikovat**: Při replikaci každý stromu trénovaných na přesně stejné vstupní data. Určení, které rozdělení predikát použije se pro každý uzel stromu zůstane náhodné a stromy budou mít různé.   
  
3.  Určení způsobu modelu zaškolení, tak, že nastavíte **režimu vytváření trainer** možnost.  
  
    -   **Jeden parametr**: Pokud víte, jak chcete provést konfiguraci modelu, můžete zadat konkrétní sadu hodnot jako argumenty.
  
4.  Pro **počet rozhodovacích stromů**, zadejte maximální počet rozhodovacích stromů, které mohou být vytvořeny v skupiny stromů. Vytvořením další rozhodovacích stromů, může potenciálně získat lepší pokrytí, ale když školení zvýší.  
  
    > [!NOTE]
    >  Tato hodnota také řídí počet stromové struktury zobrazují při vizualizaci trénovaného modelu. Pokud chcete zobrazit nebo vytisknout jediného stromu, můžete nastavit hodnotu na 1. Však může být pouze jeden stromové struktury (stromové struktury s počáteční sadu parametrů) vytvořen a jsou prováděny žádné další iterace.
  
5.  Pro **maximální hloubka rozhodovacích stromů**, zadejte číslo pro omezení maximální hloubky jakékoli rozhodovací strom. Zvýšení hloubku stromu může zvýšit přesnost, nese chvíli školení overfitting a vyšší.
  
6.  Pro **počet náhodných rozdělí na jeden uzel**, zadejte počet rozdělení pro použití při vytváření jednotlivých uzlů stromu. A *rozdělit* náhodně dělí prostředky, které nabízí v každé úrovni stromu (uzel).
  
7.  Pro **minimální počet vzorků za uzel typu list**, určit minimální počet případů, které jsou nutné k vytvoření libovolného uzlu terminálu (listový) ve stromové struktuře.
  
     Zvýšením tuto hodnotu zvýšit prahovou hodnotu pro vytvoření nového pravidla. Výchozí hodnotu 1, ještě jeden případ může způsobit, který se má vytvořit nové pravidlo. Pokud zvýšíte hodnotu 5, museli byste trénovací data obsahují aspoň pět případy, které splňují stejných podmínek.  
  
8.  Vyberte **povolit neznámé hodnoty zařazené do kategorií funkcí** možnost vytvořit skupinu pro neznámé hodnoty v sadách školení nebo ověření. Model může být méně přesné pro známé hodnoty, ale může poskytovat lepší předpovědi pro nové hodnoty (neznámá). 

     Pokud výběr této možnosti modelu může přijmout hodnoty, které jsou obsaženy v trénovací data.
  
9. Připojení s popiskem datovou sadu a jeden z [školicí moduly](module-reference.md):  
  
    -   Pokud nastavíte **režimu vytváření trainer** k **jediný parametr**, použijte [trénování modelu](./train-model.md) modulu.  
  
    
## <a name="results"></a>Výsledky

Po dokončení školení:

+ Pokud chcete zobrazit stromu, který byl vytvořen při každé iteraci, klikněte pravým tlačítkem na výstup [Train Model](./train-model.md) modul a vyberte **vizualizovat**.
  
    Klikněte na každém stromu podrobnostem rozdělení a zobrazit pravidla pro každý uzel.

+ Uložte snímek model, klikněte pravým tlačítkem myši **Trénovaného modelu** výstupní a vyberte **uložit Model**. Uložené modelu není aktualizován na následné spuštění experimentu.

+ Chcete-li použít model pro vyhodnocení, přidejte **Score Model** modulů na experiment.


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 