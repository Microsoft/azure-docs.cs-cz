---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Naučte se používat modul SMOTE v Azure Machine Learning ke zvýšení počtu příkladů s nízkým dopadem v datové sadě pomocí převzorkování.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 501f3e8946023d28d67a33fbbfca661afbc6306d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898257"
---
# <a name="smote"></a>SMOTE

Tento článek popisuje, jak použít modul SMOTE v Návrháři Azure Machine Learning ke zvýšení počtu nezastoupených případů v datové sadě, která se používá pro strojové učení. SMOTE je lepší způsob, jak zvýšit počet vzácných případů, než jednoduše duplikují existující případy.  

Připojíte modul SMOTE k datové sadě, která je *nevyvážená*. Existuje mnoho důvodů, proč může být datová sada nevyvážená. Například kategorie, kterou cílíte, může být v populaci vzácná nebo může být obtížné shromažďovat data. Obvykle používáte SMOTE, pokud je *Třída* , kterou chcete analyzovat, nereprezentace. 
  
Modul vrátí datovou sadu, která obsahuje původní ukázky. Také vrátí počet syntetických minoritních vzorků v závislosti na procentuální hodnotě, kterou zadáte.  
  
## <a name="more-about-smote"></a>Další informace o SMOTE

Syntetická technika minoritního vzorkování (SMOTE) je statistická metoda pro zvýšení počtu případů ve vaší datové sadě vyváženým způsobem. Modul funguje tak, že generuje nové instance z existujících minoritních případů, které zadáte jako vstup. Tato implementace SMOTE *nemění počet* případů většiny.

Nové instance neobsahují pouze kopie stávajících menšinových případů. Místo toho algoritmus přebírá vzorky *prostoru funkce* pro každou třídu cíle a nejbližší sousední sousední. Algoritmus následně vygeneruje nové příklady, které kombinují funkce cílového případu s funkcemi jeho sousedů. Tento přístup zvyšuje všechny funkce, které jsou pro každou třídu dostupné, a vytváří obecnější ukázky.
  
SMOTE přebírá celou datovou sadu jako vstup, ale zvyšuje procento jenom z menšinových případů. Předpokládejme například, že máte nevyváženou datovou sadu, kde pouze 1 procento případů má cílovou hodnotu A (třída menšin) a 99 procent z případů má hodnotu B. Chcete-li zvýšit procento minoritních případů na dvojnásobek předchozího procenta, zadejte do vlastností modulu **200** pro **SMOTE procento** .  
  
## <a name="examples"></a>Příklady  

Doporučujeme, abyste se pokusili používat SMOTE s malou datovou sadou k tomu, abyste viděli, jak funguje. Následující příklad používá datovou sadu krevní darování dostupnou v Návrháři Azure Machine Learning.
  
Pokud přidáte datovou sadu do kanálu a vyberete **vizualizovat** na výstupu datové sady, vidíte, že 748 řádků nebo případů v datové sadě, 570 (76 procent), jsou třídy 0 a v případě 178 případů (24 procent) jsou třídy 1. I když tento výsledek není vážně selhalo, třída 1 představuje lidi, kteří zavedli krev, takže tyto řádky obsahují *prostor funkcí* , který chcete modelovat.
 
Chcete-li zvýšit počet případů, můžete nastavit hodnotu **SMOTE procent**pomocí násobcích 100, a to následujícím způsobem:

||Třída 0|Třída 1|celkem|  
|-|-------------|-------------|-----------|  
|Původní datová sada<br /><br /> (ekvivalentní k **SMOTE procent**  =  **0**)|570<br /><br /> 76%|178<br /><br /> 24|748|  
|**Procento SMOTE**  =  **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**Procento SMOTE**  =  **200**|570<br /><br /> 52%|534<br /><br /> 48%|1 104|  
|**Procento SMOTE**  =  **300**|570<br /><br /> 44%|712<br /><br /> 56%|1 282|  
  
> [!WARNING]
> Zvýšení počtu případů pomocí SMOTE není zaručené vytvoření přesnější modelů. Vyzkoušejte si kanály s různou procentuální hodnotou, různými sadami funkcí a různými čísly nejbližších sousedů, abyste viděli, jak přidávání případů ovlivní váš model.  
  
## <a name="how-to-configure-smote"></a>Jak nakonfigurovat SMOTE
  
1.  Přidejte modul SMOTE do svého kanálu. Modul najdete v části **moduly transformace dat**v kategorii **manipulace** .

2. Připojte datovou sadu, kterou chcete zvýšit. Pokud chcete určit místo pro funkci pro vytváření nových případů, buď pomocí pouze konkrétních sloupců, nebo vyloučením některých, použijte modul [Výběr sloupců v datové sadě](select-columns-in-dataset.md) . Potom můžete před použitím SMOTE izolovat sloupce, které chcete použít.
  
    V opačném případě vytváření nových případů prostřednictvím SMOTE vychází ze *všech* sloupců, které zadáte jako vstupy. Nejméně jeden sloupec sloupců funkce je numerický.
  
3.  Zajistěte, aby byl vybrán sloupec, který obsahuje popisek nebo cílovou třídu. SMOTE přijímá pouze binární popisky.
  
4.  Modul SMOTE automaticky identifikuje třídu menšin ve sloupci Label a pak získá všechny příklady pro třídu menšin. Všechny sloupce nemůžou mít hodnoty NaN.
  
5.  V možnosti **procento SMOTE** zadejte celé číslo, které označuje cílové procento minoritních případů ve výstupní datové sadě. Například:  
  
    - Zadáte **0**. Modul SMOTE vrací přesně stejnou datovou sadu, kterou jste zadali jako vstup. Nepřidává žádné nové menšinové případy. V této datové sadě se nezměnila procentní část třídy.  
  
    - Zadáte **100**. Modul SMOTE generuje nové menšinové případy. Přidává stejný počet menšinových případů, které byly v původní datové sadě. Vzhledem k tomu, že SMOTE nezvyšuje počet případů většiny, změnil se poměr případů každé třídy.  
  
    - Zadáte **200**. Modul zdvojnásobí procento menšinových případů v porovnání s původní datovou sadou. To *nemá za následek dvakrát* tolik minoritních případů jako předtím. Místo toho je velikost datové sady zvýšena tak, že počet případů většiny je stejný. Počet menšinových případů se zvyšuje, dokud se neshoduje s požadovanou procentuální hodnotou.  
  
    > [!NOTE]
    > Pro SMOTE procento použijte pouze násobky 100.

6.  Pomocí možnosti **počet nejbližších sousedů** určete velikost prostoru funkcí, který SMOTE algoritmus používá při vytváření nových případů. Nejbližší sousední uzel je řádek dat (případ), který je podobný cílovému případu. Vzdálenost mezi dvěma případy se měří kombinací vážených vektorů všech funkcí.  
  
    + Zvýšením počtu nejbližších sousedů získáte funkce z více případů.
    + Když zachováte počet nejbližších sousedů, budete používat funkce, které jsou v původní ukázce větší, jako u nich.  
  
7. Zadejte hodnotu do pole **náhodný základ** , pokud chcete zajistit, aby byly stejné výsledky v průběhu spuštění stejného kanálu se stejnými daty. V opačném případě modul vygeneruje náhodný základ na základě hodnot hodin procesoru při nasazení kanálu. Generace náhodného osazení může způsobit mírně odlišné výsledky při spuštění.

8. Odešlete kanál.  
  
   Výstupem modulu je datová sada, která obsahuje původní řádky plus počet přidaných řádků s menšinovými případy.  

## <a name="technical-notes"></a>Technické poznámky

+ Když publikujete model, který používá modul **SMOTE** , odeberte **SMOTE** z prediktivního kanálu před jeho publikováním jako webovou službu. Důvodem je to, že SMOTE je určený pro zlepšení modelu během školení, nikoli pro bodování. Pokud publikovaný prediktivní kanál obsahuje modul SMOTE, může se zobrazit chyba.

+ Můžete často získat lepší výsledky, pokud vyčistíte chybějící hodnoty, nebo použijete jiné transformace k opravě dat před použitím SMOTE. 

+ Někteří výzkumníki prozkoumali, jestli je SMOTE efektivní pro rozsáhlá nebo zhuštěná data, jako jsou například data používaná v klasifikaci textu nebo datové sady genomiky. Tento dokument má dobrý přehled o účincích a teoretické platnosti použití SMOTE v takových případech: [Blagus a Lusa: SMOTE pro velmi dimenzionální data s nevyváženými třídami](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106).

+ Pokud SMOTE v datové sadě neplatí, další přístupy, které byste měli zvážit, zahrnují:
  + Metody pro převzorkování menšinových případů nebo podvzorkování případů většiny.
  + Techniky kompletování, které pomůžou naučit přímo pomocí clusteringu, vytváření zavazadel nebo adaptivního zvyšování úrovně.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

