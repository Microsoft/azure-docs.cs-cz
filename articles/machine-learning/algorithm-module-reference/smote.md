---
title: SMOTE
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul SMOTE ve službě Azure Machine Learning ke zvýšení počtu příkladů s nízkým dopadem v datové sadě pomocí převzorkování.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 072268af52ebf7d3bede564d8c949eec9fc9f625
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516185"
---
# <a name="smote"></a>SMOTE

Tento článek popisuje, jak použít modul **SMOTE** v Návrháři Azure Machine Learning (Preview) ke zvýšení počtu nezastoupených případů v datové sadě, která se používá pro strojové učení. SMOTE je lepší způsob, jak zvýšit počet vzácných případů, než jednoduše duplikují existující případy.  

 Připojíte modul SMOTE k datové sadě, která je *nevyvážená*. Existuje mnoho důvodů, proč může být datová sada nevyvážená: kategorie, kterou cílíte, může být v populaci vzácná nebo může být obtížné shromažďovat data. Obvykle používáte SMOTE, pokud je *Třída* , kterou chcete analyzovat, v rámci reprezentace. 
  
 Modul vrátí datovou sadu, která obsahuje původní ukázky, a další počet syntetických minoritních vzorků v závislosti na procentuální hodnotě, kterou zadáte.  
  
### <a name="more-about-smote"></a>Další informace o SMOTE

**SMOTE** představuje *syntetickou metodu náhodného vzorkování menšin*. Jedná se o statistickou techniku pro zvýšení počtu případů v datové sadě vyváženým způsobem.  Modul funguje tak, že generuje nové instance z existujících minoritních případů, které zadáte jako vstup. Tato implementace SMOTE **nemění počet** případů většiny.

Nové instance neobsahují jenom kopie stávajících menšinových případů. místo toho algoritmus přebírá vzorky *prostoru funkce* pro každou třídu cíle a nejbližší sousední sousední a generuje nové příklady, které kombinují funkce cílového případu s funkcemi jeho sousedů. Tento přístup zvyšuje všechny funkce, které jsou pro každou třídu dostupné, a vytváří obecnější ukázky.
  
SMOTE přebírá celou datovou sadu jako vstup, ale zvyšuje procento jenom z menšinových případů. Předpokládejme například, že máte nevyváženou datovou sadu, kde pouze 1% z případů má cílovou hodnotu A (třída menšin) a 99% případů má hodnotu B. Chcete-li zvýšit procento minoritních případů na dvojnásobek předchozího procenta, zadejte do vlastností modulu 200 pro **SMOTE procento** .  
  
## <a name="examples"></a>Příklady  

Doporučujeme, abyste se pokusili používat **SMOTE** s malou datovou sadou k tomu, abyste viděli, jak funguje. Následující příklad používá datovou sadu krevní darování dostupnou v Návrháři Azure Machine Learning.
  
Pokud přidáte datovou sadu do kanálu a kliknete na možnost **vizualizovat** ve výstupu datové sady, můžete vidět, že 748 řádků nebo případů v datové sadě je 570 případů (76%). z třídy 0 a 178 případů (24%) třídy 1. I když to není vážně selhalo, třída 1 představuje lidi, kteří zavedli krev, a proto tyto řádky obsahují *prostor funkcí* , který chcete modelovat.
 
Chcete-li zvýšit počet případů, můžete nastavit hodnotu **SMOTE PERCENTAGE**pomocí násobcích 100, a to následujícím způsobem:

||Třída 0|Třída 1|čtení|  
|-|-------------|-------------|-----------|  
|Původní datová sada<br /><br /> (ekvivalentní k **SMOTE procentům** = **0**)|570<br /><br /> 76%|178<br /><br /> 24|748|  
|**Procento SMOTE** = **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**Procento SMOTE** = **200**|570<br /><br /> 52%|534<br /><br /> 48%|1104|  
|**Procento SMOTE** = **300**|570<br /><br /> 44%|712<br /><br /> 56%|1282|  
  
> [!WARNING]
>  Zvýšení počtu případů použití SMOTE není zaručené vytváření přesnější modelů. Měli byste vyzkoušet kanály s různou procentuální hodnotou, různými sadami funkcí a různými čísly nejbližších sousedů, abyste viděli, jak přidávání případů ovlivní váš model.  
  
## <a name="how-to-configure-smote"></a>Jak nakonfigurovat SMOTE
  
1.  Přidejte modul SMOTE do svého kanálu. Modul najdete v části moduly transformace dat v kategorii manipulace.

2. Připojte datovou sadu, kterou chcete zvýšit. Pokud chcete určit místo pro funkci pro vytváření nových případů, buď pomocí pouze konkrétních sloupců, nebo vyloučením některých, použijte modul [Výběr sloupců v datové sadě](select-columns-in-dataset.md) k izolaci sloupců, které chcete použít před použitím **SMOTE**.
  
    V opačném případě vytváření nových případů pomocí **SMOTE** vychází ze **všech** sloupců, které zadáte jako vstupy. Nejméně jeden sloupec sloupců funkce je numerický.
  
3.  Zajistěte, aby byl vybrán sloupec, který obsahuje popisek nebo cílovou třídu.  **SMOTE** akceptuje pouze binární popisky.
  
4.  Modul **SMOTE** automaticky identifikuje třídu menšin ve sloupci Label a pak získá všechny příklady pro třídu menšin. Všechny sloupce nemohou mít NaN.
  
5.  V možnosti **procento SMOTE** zadejte celé číslo, které označuje cílové procento minoritních případů ve výstupní sadě dat. Například:  
  
    - Zadáte **0** (%). Modul SMOTE vrací přesně stejnou datovou sadu, kterou jste zadali jako vstup, a nepřidá žádné nové menšinové případy. V této datové sadě se nezměnila procentní část třídy.  
  
    -   Zadejte **100** (%). Modul SMOTE generuje nové menšinové případy a přidává stejný počet menšinových případů, které byly v původní datové sadě. Vzhledem k tomu, že SMOTE nezvyšuje počet většiny případů, byl nyní změněn poměr případů každé třídy.  
  
    -   Zadejte **200** (%). Modul zdvojnásobí procento menšinových případů v porovnání s původní datovou sadou. To **nemá za následek dvakrát** tolik minoritních případů jako předtím.  Místo toho je velikost datové sady zvýšena takovým způsobem, že počet případů většiny je stejný a počet menšinových případů se zvyšuje, dokud nebude odpovídat požadované procentuální hodnotě.  
  
    > [!NOTE]
    > Pro SMOTE procento použijte pouze násobky 100.

6.  Pomocí možnosti **počet nejbližších sousedů** určete velikost prostoru funkcí, který SMOTE algoritmus používá při vytváření nových případů. *Nejbližší sousední uzel* je řádek dat (případ), který je podobný nějakému cílovému případu. Vzdálenost mezi dvěma případy se měří kombinací vážených vektorů všech funkcí.  
  
     + Zvýšením počtu nejbližších sousedů získáte funkce z více případů.
     + Když zachováte počet nejbližších sousedů, budete používat funkce, které jsou v původní ukázce větší, jako u nich.  
  
7. Zadejte hodnotu do pole **náhodného počátečního** textu, pokud chcete zajistit, aby byly stejné výsledky v průběhu spuštění stejného kanálu se stejnými daty. V opačném případě modul generuje náhodný základ na základě hodnot hodin procesoru při nasazení kanálu, což může způsobit mírně odlišné výsledky při spuštění.

8. Spuštění kanálu  
  
     Výstupem modulu je datová sada obsahující původní řádky a určitý počet přidaných řádků s menšinovými případy.  

## <a name="technical-notes"></a>Technické poznámky

+ Při publikování modelu, který používá modul **SMOTE** , odeberte **SMOTE** z prediktivního kanálu před jeho publikováním jako webovou službu.  Důvodem je to, že SMOTE je určený ke zlepšení modelu během školení a není určen pro bodování.  Pokud publikovaný prediktivní kanál obsahuje modul SMOTE, může se zobrazit chyba.

+ Pokud použijete možnost čištění chybějících hodnot nebo jiné transformace k opravě dat před použitím SMOTE, můžete často získat lepší výsledky. 

+ Někteří výzkumníki prozkoumali, jestli je SMOTE efektivní pro rozsáhlá nebo zhuštěná data, jako jsou například ta, která se používají v klasifikaci textu nebo v datových sadách genomiky. Tento dokument obsahuje dobrý přehled účinků a teoretické platnosti použití SMOTE v takových případech: [Blagus a Lusa: SMOTE pro rozsáhlá data s nevyváženými třídami](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106) .

    Pokud SMOTE ve vaší datové sadě neplatí, další přístupy, které byste měli zvážit, zahrnují různé metody pro převzorkování menšinových případů nebo odvzorkování většiny případů a také techniky, které se seznámí přímo s tímto nástrojem, pomocí clusteringu. zazavazadlí nebo adaptivního zvyšování.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 

