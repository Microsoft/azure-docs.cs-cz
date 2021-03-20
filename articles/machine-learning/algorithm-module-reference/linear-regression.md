---
title: 'Lineární regrese: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul lineární regrese v Azure Machine Learning k vytvoření modelu lineární regrese pro použití v kanálu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 809b1be4f9f12e1963ff5caeaacd109c84db154f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93323658"
---
# <a name="linear-regression-module"></a>Modul lineární regrese
Tento článek popisuje modul v Návrháři Azure Machine Learning.

Tento modul slouží k vytvoření modelu lineární regrese pro použití v kanálu.  Lineární regrese se pokusí vytvořit lineární vztah mezi jednou nebo více nezávislými proměnnými a číselným výstupem nebo závislou proměnnou. 

Pomocí tohoto modulu můžete definovat metodu lineární regrese a potom vytvořit model pomocí popisku DataSet. K vytvoření předpovědi se pak dá použít trained model.

## <a name="about-linear-regression"></a>O lineární regresi

Lineární regrese je běžná statistická metoda, kterou jste přijali v machine learningu a vylepšili jsme mnoho nových metod pro přizpůsobení řádku a měření chyby. Jednoduše řečeno, regrese odkazuje na předpověď číselného cíle. Lineární regrese je stále dobrá volbou, pokud chcete jednoduchý model pro základní prediktivní úlohu. Lineární regrese má také v úmyslu dobře fungovat i u vysoce dimenzionálních a řídkých datových sad, které nemají složitost.

Azure Machine Learning podporuje různé regresní modely kromě lineární regrese. Výraz "regrese" však lze volně interpretovat a některé typy regrese poskytované v jiných nástrojích nejsou podporovány.

+ K problému s klasickými regresi se skládá jediná nezávislá proměnná a závislá proměnná. Tato metoda se nazývá *Jednoduchá regrese*.  Tento modul podporuje jednoduchou regresi.

+ *Více lineární regrese* zahrnuje dvě nebo více nezávislých proměnných, které přispívají k jedné závislé proměnné. Problémy, ve kterých se pro předpověď jednoho číselného výsledku používá více vstupů, se také nazývají *Lineární lineární regrese*.

    Modul **lineární regrese** může tyto problémy vyřešit, stejně jako většina ostatních regresních modulů.

+ *Regrese s více popisky* je úkol předpovědi více závislých proměnných v rámci jednoho modelu. Například v případě logistické regrese s více štítky lze vzorek přiřadit více různým popiskům. (To se liší od úlohy předpovědi více úrovní v rámci jedné proměnné třídy.)

    Tento typ regrese není v Azure Machine Learning podporován. Chcete-li předpovědět více proměnných, vytvořte samostatný seznam pro každý výstup, který chcete předpovědět.

Pro roky byly statistikou vyvinuty stále větší pokročilé metody pro regresi. To platí i pro lineární regresi. Tento modul podporuje dvě metody měření chyby a odpovídá regresní čáře: obyčejnému nejmenšímu čtverci a zaklesání gradientu.

- **Klesání gradientu** je metoda, která minimalizuje množství chyb v každém kroku procesu školení modelu. Existuje spousta variací pro klesání gradientu a jeho optimalizace pro různé problémy s učením byla značně vyzkoušena. Pokud zvolíte tuto možnost pro **metodu řešení**, můžete nastavit různé parametry pro řízení velikosti kroku, studijní frekvence a tak dále. Tato možnost také podporuje použití integrovaného úklidu parametrů.

- **Běžný nejmenší počet čtverců** je jedním z nejčastěji používaných technik v lineární regresi. Například nejméně čtverce je metoda, která se používá v analytickém doplňku pro aplikaci Microsoft Excel.

    Běžné nejmenší čtverce odkazují na funkci ztráty, která počítá chybu jako součet čtverce vzdálenosti od skutečné hodnoty až po předpokládaný řádek a odpovídá modelu minimalizací čtvercové chyby. Tato metoda předpokládá silný lineární vztah mezi vstupy a závislou proměnnou.

## <a name="configure-linear-regression"></a>Konfigurace lineární regrese

Tento modul podporuje dvě metody pro přizpůsobení regresního modelu s různými možnostmi:

+ [Přizpůsobit regresní model pomocí běžných minimálních čtverců](#create-a-regression-model-using-ordinary-least-squares)

    Pro malé datové sady je nejlepší vybrat běžné nejméně čtverce. To by mělo mít podobné výsledky do Excelu.
    
+ [Vytvoření regresního modelu pomocí online gradientu](#create-a-regression-model-using-online-gradient-descent)

    Klesání gradientu je lepší funkcí pro ztrátu modelů, které jsou složitější, nebo které mají příliš málo školicích dat pro daný počet proměnných.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Vytvoření regresního modelu pomocí běžných minimálních čtverců

1. Přidejte modul **lineární regrese** do kanálu v návrháři.

    Tento modul můžete najít v kategorii **Machine Learning** . Rozbalte položku **inicializovat model**, rozbalte položku **regrese** a poté přetáhněte modul **lineární regrese** do vašeho kanálu.

2. V podokně **vlastnosti** v rozevíracím seznamu **Metoda řešení** vyberte možnost **obyčejné nejméně čtverce**. Tato možnost určuje metodu výpočtu, která se používá k nalezení regresní čáry.

3. V části **váha pravidelného vyvážení** zadejte hodnotu, která se má použít jako váha pro pravidelnosti L2. Doporučujeme použít nenulovou hodnotu, abyste se vyhnuli nedodržení.

     Další informace o tom, jak pravidelná změna má vliv na model, najdete v tomto článku: [L1 a pro použití algoritmu L2 pro Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)

4. Vyberte možnost **včetně podmínky zachycení**, pokud chcete zobrazit termín pro Intercept.

    Tuto možnost zrušte, pokud nepotřebujete kontrolovat vzorec regrese.

5. V případě použití **náhodného čísla** můžete volitelně zadat hodnotu pro počáteční generátor náhodných čísel používaný modelem.

    Použití počáteční hodnoty je užitečné, pokud chcete zachovat stejné výsledky v různých spuštěních stejného kanálu. V opačném případě se ve výchozím nastavení používá hodnota ze systémových hodin.


7. Přidejte modul [vlakového modelu](./train-model.md) do vašeho kanálu a připojte datovou sadu s popiskem.

8. Odešlete kanál.

### <a name="results-for-ordinary-least-squares-model"></a>Výsledky pro model obyčejného minimálního čtverce

Po dokončení školení:


+ Chcete-li vytvořit předpovědi, připojte k modulu pro stanovení [skóre](./score-model.md) , proškolený model a datovou sadu nových hodnot. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Vytvoření regresního modelu pomocí online gradientu

1. Přidejte modul **lineární regrese** do kanálu v návrháři.

    Tento modul můžete najít v kategorii **Machine Learning** . Rozbalte možnost **inicializovat model**, rozbalte **regresi** a přetáhněte modul **lineární regrese** do vašeho kanálu.

2. V podokně **vlastnosti** v rozevíracím seznamu **Metoda řešení** vyberte možnost **online přechodové** prolomení jako metodu výpočtu použitou k nalezení regresní čáry.

3. V případě **režimu vytvoření Trainer** určete, zda chcete vytvořit výuku modelu pomocí předdefinované sady parametrů, nebo pokud chcete model optimalizovat pomocí parametru sweep.

    + **Jediný parametr**: Pokud víte, jak chcete nakonfigurovat síť lineární regrese, můžete zadat konkrétní sadu hodnot jako argumenty.
    
    + **Rozsah parametrů**: tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit mazání parametrů. Vyberte rozsah hodnot, na které se mají iterovat, a s [parametry modelu ladění](tune-model-hyperparameters.md) prochází všechny možné kombinace nastavení, které jste zadali, abyste určili, jaké parametry jsou výsledkem optimálních výsledků.  

   
4. Do pole **rychlost učení** zadejte počáteční rychlost učení pro Optimalizátor stochastického pro prorážku klesání.

5. Pro **počet epochs školení** zadejte hodnotu, která označuje, kolikrát by se měl algoritmus iterovat prostřednictvím příkladů. U datových sad s malým počtem příkladů by toto číslo mělo být velké, aby se dosáhlo konvergence.

6. **Funkce normalizace**: Pokud už máte normalizovaná číselná data, která se používají pro výuku modelu, můžete tuto možnost zrušit. Ve výchozím nastavení modul normalizuje všechny číselné vstupy na rozsah od 0 do 1.

    > [!NOTE]
    > 
    > Nezapomeňte použít stejnou metodu normalizace na nová data použitá pro vyhodnocování.

7. V části **váha pravidelného vyvážení** zadejte hodnotu, která se má použít jako váha pro pravidelnosti L2. Doporučujeme použít nenulovou hodnotu, abyste se vyhnuli nedodržení.

    Další informace o tom, jak pravidelná změna má vliv na model, najdete v tomto článku: [L1 a pro použití algoritmu L2 pro Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)


9. Tuto možnost **Vyberte, pokud** chcete, aby se studijní frekvence snížila v průběhu iterací.  

10. V případě použití **náhodného čísla** můžete volitelně zadat hodnotu pro počáteční generátor náhodných čísel používaný modelem. Použití počáteční hodnoty je užitečné, pokud chcete zachovat stejné výsledky v různých spuštěních stejného kanálu.


12. Výukové modely:

    + Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, připojíte tagované datové sady a modul [vlakového modelu](train-model.md) .  
  
    + Pokud nastavíte **režim vytvořit Trainer** na **rozsah parametrů**, připojíte s příznakovou datovou sadu a provedete model pomocí [předparametrů ladit model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Pokud předáte rozsah parametru pro [vlakový model](train-model.md), použije se jenom výchozí hodnota v seznamu jednoho parametru.  
    > 
    > Pokud předáte jednu sadu hodnot parametrů do modulu [předparametrů modelu ladění](tune-model-hyperparameters.md) , když očekává rozsah nastavení pro každý parametr, ignoruje hodnoty a použije výchozí hodnoty pro učit se.  
    > 
    > Pokud vyberete možnost **rozsahu parametrů** a zadáte jednu hodnotu pro libovolný parametr, tato jediná hodnota, kterou jste zadali, se použije v celém oblouku, a to i v případě, že se jiné parametry mění v rozsahu hodnot.

13. Odešlete kanál.

### <a name="results-for-online-gradient-descent"></a>Výsledky pro online gradient

Po dokončení školení:

+ Chcete-li vytvořit předpovědi, připojte k modulu určení [modelu hodnocení](./score-model.md) , a to společně s novými vstupními daty.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.