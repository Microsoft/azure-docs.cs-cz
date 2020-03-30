---
title: 'Lineární regrese: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu lineární regrese v Azure Machine Learning vytvořit lineární regresní model pro použití v kanálu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 602553637e21b17aa4f9bc7402753af024c697c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477557"
---
# <a name="linear-regression-module"></a>Lineární regresní modul
Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření modelu lineární regrese pro použití v kanálu.  Lineární regrese se pokouší vytvořit lineární vztah mezi jednou nebo více nezávislými proměnnými a číselným výsledkem nebo závislou proměnnou. 

Tento modul slouží k definování metody lineární regrese a potom tálovat model pomocí popisované datové sady. Trénovaný model pak lze použít k předpovědi.

## <a name="about-linear-regression"></a>O lineární regresi

Lineární regrese je běžná statistická metoda, která byla přijata ve strojovém učení a vylepšena mnoha novými metodami pro montáž linky a měření chyby. V nejzákladnějším smyslu regrese odkazuje na předpověď číselného cíle. Lineární regrese je stále dobrou volbou, pokud chcete jednoduchý model pro základní prediktivní úkol. Lineární regrese také má tendenci dobře pracovat na vysokorozměrných, řídkých datových sadách, které postrádají složitost.

Azure Machine Learning podporuje celou řadu regresních modelů, kromě lineární regrese. Termín "regrese" však lze interpretovat volně a některé typy regrese v jiných nástrojích nejsou podporovány.

+ Klasický problém regrese zahrnuje jednu nezávislou proměnnou a závislou proměnnou. Tomu se říká *jednoduchá regrese*.  Tento modul podporuje jednoduchou regresi.

+ *Vícelineární regrese* zahrnuje dvě nebo více nezávislých proměnných, které přispívají k jedné závislé proměnné. Problémy, ve kterých se používá více vstupů k předvídání jednoho číselného výsledku, se také nazývají *mnohorozměrná lineární regrese*.

    Lineární **regrese** modul může vyřešit tyto problémy, stejně jako většina ostatních regresních modulů.

+ *Regrese s více popisky* je úkolem předvídání více závislých proměnných v rámci jednoho modelu. Například v logistické regresi s více popisky lze vzorek přiřadit k více různým popiskům. (To se liší od úlohy předvídání více úrovní v rámci jedné proměnné třídy.)

    Tento typ regrese není ve službě Azure Machine Learning podporován. Chcete-li předpovědět více proměnných, vytvořte pro každý výstup, který chcete předpovědět, samostatný student.

Po celá léta statistici vyvíjejí stále vyspělejší metody pro regresi. To platí i pro lineární regrese. Tento modul podporuje dvě metody měření chyby a přizpůsobení regresní čáry: metoda obyčejné nejmenší čtverce a sestup přechodu.

- **Sestup přechodu** je metoda, která minimalizuje množství chyb v každém kroku procesu školení modelu. Existuje mnoho variací na gradientsestup a jeho optimalizace pro různé problémy učení byla rozsáhle studována. Pokud zvolíte tuto možnost pro **metodu Řešení**, můžete nastavit různé parametry pro řízení velikosti kroku, rychlost učení a tak dále. Tato možnost také podporuje použití integrovaného parametru sweep.

- **Obyčejné nejmenší čtverce** jsou jednou z nejčastěji používaných technik v lineární regresi. Například nejmenší čtverečky je metoda, která se používá v analytickém nástroji pro aplikaci Microsoft Excel.

    Obyčejné nejmenší čtverce odkazuje na loss funkce, která počítá chybu jako součet čtverečních vzdálenost od skutečné hodnoty k předpovídané čáry a odpovídá modelu minimalizací kvadratických chyb. Tato metoda předpokládá silný lineární vztah mezi vstupy a závislou proměnnou.

## <a name="configure-linear-regression"></a>Konfigurace lineární regrese

Tento modul podporuje dvě metody pro montáž regresního modelu s různými možnostmi:

+ [Přizpůsobit regresní model pomocí běžných nejmenších čtverců](#create-a-regression-model-using-ordinary-least-squares)

    Pro malé datové sady je nejlepší vybrat obyčejné nejmenší čtverce. To by mělo poskytnout podobné výsledky excelu.
    
+ [Vytvoření regresního modelu pomocí online sestupu přechodu](#create-a-regression-model-using-online-gradient-descent)

    Gradient sestup je lepší funkce ztráty pro modely, které jsou složitější, nebo které mají příliš málo trénovacích dat vzhledem k počtu proměnných.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Vytvoření regresního modelu pomocí běžných nejmenších čtverců

1. Přidejte modul **Model lineární regrese** do kanálu v návrháři.

    Tento modul najdete v kategorii **Machine Learning.** Rozbalte **Inicializovat model**, **rozbalte regresi**a přetáhněte modul **Model lineární regrese** do kanálu.

2. V podokně **Vlastnosti** vyberte v rozevíracím seznamu **Metody řešení** **obyčejné nejmenší čtverce**. Tato možnost určuje metodu výpočtu použitou k nalezení regresní čáry.

3. Do **pole Hmotnost regularizace L2**zadejte hodnotu, která se má použít jako hmotnost pro regularizaci L2. Doporučujeme použít nenulovou hodnotu, abyste se vyhnuli nadměrnému vybavení.

     Další informace o tom, jak regularizace ovlivňuje přizpůsobení modelu, najdete v tomto článku: [Regularizace L1 a L2 pro strojové učení](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Chcete-li zobrazit termín pro zachycení, vyberte možnost **Zahrnout termín zachycení**.

    Tuto volbu odznačte, pokud nepotřebujete zkontrolovat vzorec regrese.

5. Pro **osivo náhodných čísel**můžete volitelně zadat hodnotu pro generátor náhodných čísel používaný modelem.

    Použití hodnoty osiva je užitečné, pokud chcete zachovat stejné výsledky v různých spuštěních stejného kanálu. V opačném případě je výchozí použít hodnotu ze systémových hodin.


7. Přidejte modul [Model vlaku](./train-model.md) do kanálu a připojte označenou datovou sadu.

8. Odešlete potrubí.

### <a name="results-for-ordinary-least-squares-model"></a>Výsledky pro běžný model nejmenších čtverců

Po dokončení tréninku:


+ Chcete-li předpovědi, připojte trénovaný model k modulu [Model skóre](./score-model.md) spolu s datovou sadou nových hodnot. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Vytvoření regresního modelu pomocí online sestupu přechodu

1. Přidejte modul **Model lineární regrese** do kanálu v návrháři.

    Tento modul najdete v kategorii **Machine Learning.** Rozbalte **možnost Inicializovat model**, **rozbalte regresi**a přetáhněte modul **Model lineární regrese** do kanálu.

2. V podokně **Vlastnosti** v rozevíracím seznamu **Metody řešení** zvolte **Online Gradient Descent** jako výpočetní metodu použitou k nalezení regresní čáry.

3. V **části Vytvořit režim podpracovadtu**označte, zda chcete model trénovat s předdefinovanou sadou parametrů, nebo zda chcete model optimalizovat pomocí tažení parametrů.

    + **Jeden parametr**: Pokud víte, jak chcete nakonfigurovat lineární regresní síť, můžete zadat určitou sadu hodnot jako argumenty.
    
    + **Rozsah parametrů**: Tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit tažení parametrů. Vyberte rozsah hodnot, které chcete iterát přes a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates přes všechny možné kombinace nastavení, které jste zadali k určení hyperparameters, které produkují optimální výsledky.  

   
4. V **poli Rychlost učení**zadejte počáteční rychlost učení pro optimalizátor sestupu stochastického gradientu.

5. Pro **počet epoch školení**zadejte hodnotu, která označuje, kolikrát by měl algoritmus iterate prostřednictvím příkladů. Pro datové sady s malým počtem příkladů by toto číslo mělo být velké, aby bylo dosaženo konvergence.

6. **Normalizovat funkce**: Pokud jste již normalizovali číselná data použitá k trénování modelu, můžete tuto možnost zrušit. Ve výchozím nastavení modul normalizuje všechny číselné vstupy na rozsah mezi 0 a 1.

    > [!NOTE]
    > 
    > Nezapomeňte použít stejnou metodu normalizace na nová data používaná pro vyhodnocování.

7. Do **pole Hmotnost regularizace L2**zadejte hodnotu, která se má použít jako hmotnost pro regularizaci L2. Doporučujeme použít nenulovou hodnotu, abyste se vyhnuli nadměrnému vybavení.

    Další informace o tom, jak regularizace ovlivňuje přizpůsobení modelu, najdete v tomto článku: [Regularizace L1 a L2 pro strojové učení](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Vyberte možnost **Snížit rychlost učení**, pokud chcete, aby se rychlost učení s nikým s postupem iterací snižovala.  

10. Pro **osivo náhodných čísel**můžete volitelně zadat hodnotu pro generátor náhodných čísel používaný modelem. Použití hodnoty osiva je užitečné, pokud chcete zachovat stejné výsledky v různých spuštěních stejného kanálu.


12. Přidejte označenou datovou sadu a jeden z trénovacích modulů.

    Pokud nepoužíváte parametr sweep, použijte modul [Model vlaku.](train-model.md)

13. Odešlete potrubí.

### <a name="results-for-online-gradient-descent"></a>Výsledky pro online sestup z gradientu

Po dokončení tréninku:

+ Chcete-li předpovědi, připojte trénovaný model k modulu [Model skóre](./score-model.md) spolu s novými vstupními daty.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 