---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu SMOTE v Azure Machine Learning zvýšit počet příkladů s nízkým výskytem v datové sadě pomocí převzorkování.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: ed6d9e86143c3a5d6c97c4bd92a07c258bbd1bbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477455"
---
# <a name="smote"></a>SMOTE

Tento článek popisuje, jak používat modul SMOTE v návrháři Azure Machine Learning (preview) ke zvýšení počtu nedostatečně zastoupených případů v datové sadě, která se používá pro strojové učení. SMOTE je lepší způsob, jak zvýšit počet vzácných případů, než jen duplikovat stávající případy.  

Připojíte modul SMOTE k datové sadě, která je *nevyvážená*. Existuje mnoho důvodů, proč může být datová sada nevyvážená. Například kategorie, na kterou cílíte, může být v populaci vzácná nebo může být obtížné shromažďovat data. Obvykle se používá SMOTE, pokud *je třída,* kterou chcete analyzovat, nedostatečně zastoupena. 
  
Modul vrátí datovou sadu, která obsahuje původní vzorky. Vrátí také počet vzorků syntetické menšiny, v závislosti na procento, které zadáte.  
  
## <a name="more-about-smote"></a>Více o SMOTE

Technika převzorkování syntetické menšiny (SMOTE) je statistická technika pro vyvážené zvýšení počtu případů v datové sadě. Modul funguje tak, že generuje nové instance z existujících menšinových případů, které zadáte jako vstup. Tato implementace SMOTE *nemění* počet většinových případů.

Nové instance nejsou jen kopie existujících menšinových případů. Místo toho algoritmus odebere vzorky *prostoru funkce* pro každou cílovou třídu a její nejbližší sousedy. Algoritmus pak generuje nové příklady, které kombinují funkce cílového případu s funkcemi jeho sousedů. Tento přístup zvyšuje funkce, které jsou k dispozici pro každou třídu a vzorky obecnější.
  
SMOTE bere celou datovou sadu jako vstup, ale zvyšuje procento pouze menšinové případy. Předpokládejme například, že máte nevyváženou datovou sadu, kde pouze 1 procento případů má cílovou hodnotu A (menšinová třída) a 99 procent případů má hodnotu B. Chcete-li zvýšit procento menšinových případů na dvojnásobek předchozího procenta, zadejte **200** pro **procento SMOTE** ve vlastnostech modulu.  
  
## <a name="examples"></a>Příklady  

Doporučujeme zkusit použít SMOTE s malou datovou sadou, abyste zjistili, jak to funguje. Následující příklad používá datovou sadu darování krve, která je k dispozici v návrháři Azure Machine Learning.
  
Pokud přidáte datovou sadu do kanálu a vyberete **Visualize** na výstupu datové sady, uvidíte, že z 748 řádků nebo případů v datové sadě, 570 případů (76 procent) jsou třídy 0 a 178 případů (24 procent) jsou třídy 1. I když tento výsledek není strašně nevyvážený, třída 1 představuje lidi, kteří darovali krev, takže tyto řádky obsahují *prostor funkce,* který chcete modelovat.
 
Chcete-li zvýšit počet případů, můžete nastavit hodnotu **procenta SMOTE**pomocí násobků 100 takto:

||Třída 0|Třída 1|celkem|  
|-|-------------|-------------|-----------|  
|Původní datová sada<br /><br /> (ekvivalent **ssmote procentní podíl** = **0**)|570<br /><br /> 76%|178<br /><br /> 24%|748|  
|**SMOTE procento** = **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**SMOTE procento** = **200**|570<br /><br /> 52%|534<br /><br /> 48%|1,104|  
|**Procento SMOTE** = **300**|570<br /><br /> 44%|712<br /><br /> 56%|1,282|  
  
> [!WARNING]
> Zvýšení počtu případů pomocí SMOTE není zaručeno, že vyrábět přesnější modely. Zkuste pipelining s různými procenty, různými sadami funkcí a různými počty nejbližších sousedů, abyste zjistili, jak přidávání případů ovlivňuje váš model.  
  
## <a name="how-to-configure-smote"></a>Jak nakonfigurovat SMOTE
  
1.  Přidejte modul SMOTE do kanálu. Modul naleznete v části **Moduly transformace dat**v kategorii **Manipulace.**

2. Připojte datovou sadu, kterou chcete propagovat. Pokud chcete určit prostor prvku pro vytváření nových případů, buď pomocí pouze určité sloupce nebo vyloučením některé, použijte [vybrat sloupce v datové sadě](select-columns-in-dataset.md) modulu. Potom můžete izolovat sloupce, které chcete použít před použitím SMOTE.
  
    V opačném případě je vytváření nových případů prostřednictvím SMOTE založeno na *všech* sloupcích, které zadáte jako vstupy. Alespoň jeden sloupec sloupců funkce je číselný.
  
3.  Ujistěte se, že je vybrán sloupec, který obsahuje popisek nebo cílovou třídu. SMOTE přijímá pouze binární popisky.
  
4.  Modul SMOTE automaticky identifikuje menšinovou třídu ve sloupci popisků a poté získá všechny příklady pro menšinovou třídu. Všechny sloupce nemohou mít hodnoty NaN.
  
5.  Do možnosti **Procento SMOTE** zadejte celé číslo, které ve výstupní datové sadě označuje cílové procento menšinových případů. Například:  
  
    - Zadáte **0**. Modul SMOTE vrátí přesně stejnou datovou sadu, kterou jste zadali jako vstup. Nepřidává žádné nové případy menšin. V této datové sadě se poměr třídy nezměnil.  
  
    - Zadáte **hodnotu 100**. Modul SMOTE generuje nové případy menšin. Přidá stejný počet menšinových případů, které byly v původní datové sadě. Vzhledem k tomu, že SMOTE nezvyšuje počet většinových případů, podíl případů každé třídy se změnil.  
  
    - Zadáte **200**. Modul zdvojnásobí procento menšinových případů ve srovnání s původní datovou sadou. To *nevede* k tomu, že by bylo dvakrát více případů menšin než dříve. Velikost datové sady se naopak zvyšuje tak, že počet většinových případů zůstává stejný. Počet menšinových případů se zvyšuje, dokud neodpovídá požadované procentuální hodnotě.  
  
    > [!NOTE]
    > Pro procento SMOTE používejte pouze násobky 100.

6.  Pomocí možnosti **Počet nejbližších sousedé** k určení velikosti prostoru prvku, který algoritmus SMOTE používá při vytváření nových případů. Nejbližší soused je řádek dat (případ), který je podobný cílovému případu. Vzdálenost mezi libovolnými dvěma případy se měří kombinací vážených vektorů všech prvků.  
  
    + Zvýšením počtu nejbližších sousedů získáte funkce z více případů.
    + Udržováním nízkého počtu nejbližších sousedů používáte funkce, které se více podobají funkcím v původní ukázce.  
  
7. Pokud chcete zajistit stejné výsledky při spuštění stejného kanálu se stejnými daty, zadejte hodnotu do pole **Náhodné osiva.** V opačném případě modul generuje náhodné osiva na základě hodnot hodin procesoru při nasazení kanálu. Generování náhodného osiva může způsobit mírně odlišné výsledky během spuštění.

8. Odešlete potrubí.  
  
   Výstupem modulu je datová sada, která obsahuje původní řádky plus počet přidaných řádků s menšinovými případy.  

## <a name="technical-notes"></a>Technické poznámky

+ Při publikování modelu, který používá modul **SMOTE,** odeberte **SMOTE** z prediktivního kanálu před jeho publikováním jako webová služba. Důvodem je, že SMOTE je určen pro zlepšení modelu během tréninku, ne pro bodování. Pokud publikovaný prediktivní kanál obsahuje modul SMOTE, může se stát chyba.

+ Často můžete získat lepší výsledky, pokud vyčistíte chybějící hodnoty nebo použijete jiné transformace k opravě dat před použitím SMOTE. 

+ Někteří badatelé zkoumali, zda je SMOTE účinný na vysokorozměrných nebo řídkých datech, jako jsou data použitá v textové klasifikaci nebo datové sady genomiky. Tento článek obsahuje dobrý přehled účinků a teoretické platnosti použití SMOTE v takových případech: [Blagus a Lusa: SMOTE pro vysoce rozměrné třídy nevyvážené údaje](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106).

+ Pokud SMOTE není efektivní ve vaší datové sadě, další přístupy, které byste mohli zvážit, zahrnují:
  + Metody převzorkování menšinových případů nebo podvzorkování většiny případů.
  + Ensemble techniky, které pomáhají studentpřímo pomocí clustering, pytlování, nebo adaptivní posílení.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

