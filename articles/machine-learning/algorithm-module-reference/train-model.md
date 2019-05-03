---
title: 'Trénování modelu: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití **trénování modelu** modulu ve službě Azure Machine Learning tak moct trénovat klasifikační nebo regresní model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29d0f698456b83c1520a92bc7df47b26540325f4
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028107"
---
# <a name="train-model-module"></a>Modul trénování modelu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k trénovat klasifikační nebo regresní model. Školení probíhá po definované modelu a nastavte jeho parametry a vyžaduje označených datových. Můžete také použít **Train Model** k přeučování existující model s novými daty. 

## <a name="how-the-training-process-works"></a>Jak funguje proces školení

V Azure Machine Learning je obvykle o třech krocích vytváření a používání modelu strojového učení. 

1. Konfigurace modelu, výběrem konkrétního typu algoritmu a definování jeho parametry nebo hyperparameters. Zvolte některou z následujících typů modelu: 

    + **Klasifikace** modely založené na neuronových sítí, rozhodovacích stromů a rozhodnutí doménové struktury a ostatní algoritmy.
    + **Regrese** modely, které obsahují standardní lineární regrese, nebo využívající jiné algoritmy, včetně neuronových sítí a regresní Bayesova.  

2. Zadejte datovou sadu, má popisek, který obsahuje data, které jsou kompatibilní s algoritmem. Připojení dat a modelu, který má **Train Model**.

    Jaké školení vytváří je konkrétní binární formát, iLearner, který zapouzdřuje statistické vzory zjistili díky spolupráci se data. Nelze přímo upravovat nebo si přečtěte tento formát; ostatní moduly však můžete použít tento trénovaného modelu. 
    
    Můžete také zobrazit vlastnosti modelu. Další informace najdete v části výsledků.

3. Po dokončení školení použít s jednou z trénovaného modelu [vyhodnocování moduly](./score-model.md), k vytvoření predikcí nová data.

## <a name="how-to-use-train-model"></a>Jak používat **Train Model**  
  
1.  Ve službě Azure Machine Learning nakonfigurujte model klasifikace nebo regresní model.
    
2. Přidat **Train Model** modulů na experiment.  Můžete najít v rámci tohoto modulu **Machine Learning** kategorie. Rozbalte **Train**a pak přetáhněte **Train Model** modulu do svého experimentu.
  
3.  Na levém vstup připojte Nezkušený režimu. Připojit trénovací datové sady na pravém vstup **Train Model**.

    Trénovací datovou sadu musí obsahovat sloupec popisek. Všechny řádky bez popisky jsou ignorovány.
  
4.  Pro **popisek sloupce**, klikněte na tlačítko **spustit selektor sloupců**a zvolte jeden sloupec, který obsahuje výsledky modelu lze použít k trénování.
  
    - Pro klasifikaci problémy sloupec popisek musí obsahovat buď **zařazené do kategorií** hodnoty nebo **diskrétní** hodnoty. Některé příklady může být hodnocení Ano/Ne, kód klasifikace stádiu nebo název nebo skupinu příjmů.  Pokud vyberete noncategorical sloupce, modul vrátí chybu během cvičení.
  
    -   Regrese problémů, musí obsahovat sloupec popisek **číselné** data, která představuje proměnnou odpovědi. V ideálním případě představuje číselná data průběžné škálování. 
    
    Příklady je možné kredit rizikovým skóre, předpokládané čas selhání pro pevný disk nebo předpokládaných počet volání do centra volání v daném dni nebo čas.  Pokud jste nezvolili číselný sloupec, může se objevit chyba.
  
    -   Pokud nezadáte popisek sloupce, Azure Machine Learning se pokusí odvodit, což je sloupec odpovídající popisku, na základě metadat datové sady. Pokud použila nesprávném sloupci, opravte ji pomocí voliče sloupců.
  
    > [!TIP] 
    > Pokud máte problémy s používáním selektor sloupců, najdete v článku [výběr sloupců v datové sadě](./select-columns-in-dataset.md) tipy. Popisuje některé běžné scénáře a tipy pro používání **pravidla s** a **podle názvu** možnosti.
  
5.  Spusťte experiment. Pokud máte velké množství dat, to může chvíli trvat.

## <a name="bkmk_results"></a> výsledky

Po model se trénuje:

+ Chcete-li zobrazit parametry modelu a váhy funkce, klikněte pravým tlačítkem na výstupu a vyberte **vizualizovat**.
+ K použití modelu v další pokusy, klikněte pravým tlačítkem na model a vyberte **uložit Model**. Zadejte název pro model. 

    Toto uloží modelu jako snímek, který není aktualizován opakovaných spuštění experimentu.
+ K použití modelu pro předpověď nové hodnoty, připojte ho k [Score Model](./score-model.md) modulu, společně s novou vstupní data.


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 