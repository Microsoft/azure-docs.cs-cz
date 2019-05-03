---
title: 'Lineární regrese: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu lineární regrese ve službě Azure Machine Learning k vytvoření trénování modelu lineární regrese pro použití v jednom experimentu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 040f726a703d34a95bae7d5b7cdd766655c62a4e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029712"
---
# <a name="linear-regression-module"></a>Lineární regrese modulu
Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření trénování modelu lineární regrese pro použití v jednom experimentu.  Lineární regrese se pokusí vytvořit lineární vztah mezi jeden nebo více nezávislých proměnných a číselný výsledek nebo závislé na proměnnou. 

Tento modul slouží k definování metody lineární regrese a potom trénování modelu s použitím datové sady s popiskem. Trénovaného modelu je pak možné k následné predikci.

## <a name="about-linear-regression"></a>O lineární regrese

Lineární regrese je běžné statistické metody, která byla přijata ve službě machine learning a vylepšené o mnoho nových metod pro přizpůsobení řádku a měření chyby. V základní smysl regrese odkazuje na predikce číselné cíle. Lineární regrese je stále dobrou volbou, pokud chcete jednoduchý model prediktivní základní úlohy. Lineární regrese obvykle také fungovat dobře ve službě vysokým počtem rozměrů a řídkých datových sad, chybí složitost.

Azure Machine Learning podporuje širokou škálu regresních modelů, kromě lineární regrese. Ale termín "regrese" může být interpretován volně a některé typy regrese k dispozici v jiných nástrojích nejsou podporovány.

+ Klasické regresní problém zahrnuje jednu proměnnou nezávislé a závislé proměnné. Tento postup se nazývá *jednoduché regrese*.  Tento modul podporuje jednoduchý regrese.

+ *Více lineární regrese* zahrnuje dva nebo více nezávislých proměnných, které přispívají k jedné proměnné závislé. Problémy, ve kterých se používají více vstupů pro předpověď jeden číselný výsledek se také označují jako *lineární regrese s množstvím proměnných*.

    **Lineární regrese** modulu může vyřešit tyto problémy, jako tomu většina jiných modulů regrese.

+ *Regrese více popisek* je úkol předpověď více závislé na proměnných v rámci jednoho modelu. Například v několika popisek logistické regrese, ukázku lze přiřadit k několika různých popisky. (To se liší od úlohy předpověď více úrovní v rámci jedné třídy proměnné.)

    Tento typ regrese není podporován ve službě Azure Machine Learning. K předvídání více proměnných, vytvořte samostatné learner pro každý výstup, který chcete předpovědět.

Roky mít byla statistikům vývoj stále rozšířené metody pro regresní. To platí i pro lineární regrese. Tento modul podporuje dvě metody k měření chyba a přizpůsobit regresní přímky: metody běžné čtverců a sestupu.

- **Sestupu** je metoda, která minimalizuje množství chyb v každém kroku procesu trénování modelu. Existuje mnoho variant na sestupu a optimalizace pro různé problémy learning má rozsáhlé zkoumali. Pokud zvolíte tuto možnost pro **řešení metoda**, můžete nastavit různé vlastnosti parametrů řídit velikost kroku rychlost učení a tak dále. Tato možnost podporuje také využívání Uklízení integrované parametrů.

- **Běžné čtverců** je jedním z nejčastěji používaných technik v lineární regrese. Například čtverců je metoda, která se používá v analytické nástroje pro aplikaci Microsoft Excel.

    Běžné čtverců odkazuje na funkci ztráty, vypočítá chyby jako součet hodnot druhou mocninu vzdálenost od skutečné hodnoty předpovězené řádek, který odpovídá modelu minimalizací kvadratické chyby. Tato metoda předpokládá lineární silný vztah mezi vstupů a závislé proměnné.

## <a name="configure-linear-regression"></a>Konfigurace lineární regrese

Tento modul podporuje dvě metody pro přizpůsobení regresní model s použitím různých možností:

+ [Vytvoření regresního modelu pomocí online sestupu](#bkmk_GradientDescent)

    Sestupu je lepší ztráta funkce pro modely, které jsou složitější, nebo, které mají zadaný počet proměnných příliš málo trénovací data.



+ [Přizpůsobit regresního modelu pomocí běžných čtverců](#bkmk_OrdinaryLeastSquares)

    Pro malé datové sady doporučujeme vybrat běžné čtverců. To by měla poskytnout podobné výsledky do Excelu.

## <a name="bkmk_OrdinaryLeastSquares"></a> Vytvoření regresního modelu pomocí běžných čtverců

1. Přidat **trénování modelu lineární regrese** modulu do experimentu v rozhraní.

    V tomto modulu můžete najít **Machine Learning** kategorie. Rozbalte **inicializovat Model**, rozbalte **regrese**a pak přetáhněte **trénování modelu lineární regrese** modulu do experimentu.

2. V **vlastnosti** podokno v **řešení metoda** rozevíracího seznamu vyberte **běžné čtverců**. Tato možnost určuje výpočetní metoda používá k vyhledání regresní přímky.

3. V **L2 regularizace váha**, zadejte hodnotu pro účely L2 regularizace jako váhu. Doporučujeme použít nenulovou hodnotu, aby overfitting.

     Další informace o tom, jak ovlivňuje regularizace modelu přizpůsobování, najdete v tomto článku: [L1 a míry Regularizace L2 pro Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Vyberte možnost, **zahrnout průnik**, pokud chcete zobrazit termín pro intercept.

    Tuto volbu použijte, pokud není nutné zkontrolovat vzorci regrese.

5. Pro **náhodná počáteční hodnota čísla**, můžete volitelně zadat hodnotu pro generátor pseudonáhodných čísel využívané modelem.

    Použití počáteční hodnoty je užitečné, pokud chcete zachovat stejné výsledky v tom stejném experimentu během různých spuštění. V opačném případě výchozí hodnota je na používání hodnoty z systémových hodin.


7. Přidat [Train Model](./train-model.md) modulu do experimentu a připojit se s popiskem datové sady.

8. Spusťte experiment.

## <a name="results-for-ordinary-least-squares-model"></a>Výsledky pro běžné čtverců modelu

Po dokončení školení:

+ Chcete-li zobrazit parametry modelu, klikněte pravým tlačítkem na výstup trainer a vyberte **vizualizovat**.

+ Připojte se k následné predikci, trénovaný model pro [Score Model](./score-model.md) modulu, spolu s datovou sadu hodnot. 


## <a name="bkmk_GradientDescent"></a> Vytvoření regresního modelu pomocí online sestupu

1. Přidat **trénování modelu lineární regrese** modulu do experimentu v rozhraní.

    V tomto modulu můžete najít **Machine Learning** kategorie. Rozbalte **inicializovat Model**, rozbalte **regrese**a přetáhněte ji **trénování modelu lineární regrese** modulů na experiment

2. V **vlastnosti** podokno v **metoda řešení** rozevírací seznam, zvolte **Online sestupu** jako způsob výpočtu použit k vyhledání regresní přímky.

3. Pro **režimu vytváření trainer**, označuje, zda se má pro trénování modelu s předdefinovanou sadu parametrů, nebo pokud chcete optimalizovat modelu s použitím Uklízení parametrů.

    + **Jeden parametr**: Pokud víte, jak chcete provést konfiguraci sítě lineární regrese, můžete zadat konkrétní sadu hodnot jako argumenty.

   
4. Pro **rychlost učení**, určete rychlost učení pro optimalizaci pomocí stochastického sestupu.

5. Pro **počet školení epoch**, typu by měla hodnotu, která označuje, kolikrát algoritmus iteraci v rámci příklady. Pro datové sady s malý počet příklady by měla být velká k dosažení konvergence tohoto čísla.

6. **Normalizovat funkce**: Pokud už mají normalizované číselná data využívají k tréninku modelu, můžete zrušit zaškrtnutí této možnosti. Ve výchozím modulu normalizuje všechny číselné vstupy do rozsahu od 0 do 1.

    > [!NOTE]
    > 
    > Nezapomeňte použít stejnou metodu normalizace pro nová data používá pro vyhodnocení.

7. V **L2 regularizace váha**, zadejte hodnotu pro účely L2 regularizace jako váhu. Doporučujeme použít nenulovou hodnotu, aby overfitting.

    Další informace o tom, jak ovlivňuje regularizace modelu přizpůsobování, najdete v tomto článku: [L1 a míry Regularizace L2 pro Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Vyberte možnost, **rychlost učení snížení**, pokud chcete, aby rychlost učení ke snížení jako průběhu iterace.  

10. Pro **náhodná počáteční hodnota čísla**, můžete volitelně zadat hodnotu pro generátor pseudonáhodných čísel využívané modelem. Použití počáteční hodnoty je užitečné, pokud chcete zachovat stejné výsledky v tom stejném experimentu během různých spuštění.


12. Přidáte s popiskem datovou sadu a jeden z modulů školení.

    Pokud nepoužíváte Uklízení parametrů, použijte [Train Model](train-model.md) modulu.

13. Spusťte experiment.

## <a name="results-for-online-gradient-descent"></a>Výsledky pro online sestupu

Po dokončení školení:

+ Připojte se k následné predikci, trénovaný model pro [Score Model](./score-model.md) modulu, společně s novou vstupní data.


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 