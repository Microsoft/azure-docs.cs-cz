---
title: 'Model vlaku: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak použít modul **Model vlaku** v Azure Machine Learning k trénování modelu klasifikace nebo regrese.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 0a9728e05aee27e74054a77e2c9be7dc08968207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455855"
---
# <a name="train-model-module"></a>Modul modelu vlaku

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k trénování klasifikačního nebo regresního modelu. Školení probíhá po definování modelu a nastavení jeho parametrů a vyžaduje označená data. Model **vlaku** můžete také použít k přetáčce existujícího modelu s novými daty. 

## <a name="how-the-training-process-works"></a>Jak funguje proces školení

V Azure Machine Learning je vytváření a používání modelu strojového učení obvykle třístupňový proces. 

1. Nakonfigurujete model výběrem konkrétního typu algoritmu a definováním jeho parametrů nebo hyperparametrů. Zvolte některý z následujících typů modelů: 

    + **Modely klasifikace** založené na neuronových sítích, rozhodovacích stromech a rozhodovacích doménových strukturách a dalších algoritmech.
    + **Regresní** modely, které mohou zahrnovat standardní lineární regrese, nebo které používají jiné algoritmy, včetně neuronových sítí a Bayesovy regrese.  

2. Zadejte datovou sadu, která je označena a má data kompatibilní s algoritmem. Připojte data i model k **modelu vlaku**.

    Co školení produkuje, je konkrétní binární formát, iLearner, který zapouzdřuje statistické vzory získané z dat. Tento formát nelze přímo změnit ani přečíst. ostatní moduly však můžete použít tento trénovaný model. 
    
    Můžete také zobrazit vlastnosti modelu. Další informace naleznete v části Výsledky.

3. Po dokončení trénování použijte trénovaný model s jedním z [modulů hodnocení](./score-model.md), abyste vytvořili předpovědi na nová data.

## <a name="how-to-use-train-model"></a>Jak používat vlak model 
  
1.  V Azure Machine Learning nakonfigurujte klasifikační model nebo regresní model.
    
2. Přidejte modul **Model vlaku** do kanálu.  Tento modul najdete v kategorii **Machine Learning.** Rozbalte **vlak**a přetáhněte modul **Model vlaku** do kanálu.
  
3.  Na levém vstupu připojte netrénovaný režim. Připojte trénovací datovou sadu k pravému vstupu **modelu vlaku**.

    Trénovací datová sada musí obsahovat sloupec popisku. Všechny řádky bez popisků jsou ignorovány.
  
4.  Ve **sloupci Popisek**klepněte na **tlačítko Upravit sloupec** v pravém panelu modulu a zvolte jeden sloupec, který obsahuje výsledky, které může model použít pro školení.
  
    - V případě problémů s klasifikací musí sloupec popisku obsahovat **buď kategorické** hodnoty, nebo **diskrétní** hodnoty. Některé příklady mohou být hodnocení ano/ne, kód nebo název klasifikace onemocnění nebo příjmová skupina.  Pokud vyberete nekategorický sloupec, modul vrátí chybu během tréninku.
  
    -   Pro regresní problémy musí sloupec popisku obsahovat **číselná** data, která představují proměnnou odpovědi. V ideálním případě číselná data představují souvislé měřítko. 
    
    Příkladem může být skóre úvěrového rizika, předpokládaný čas selhání pevného disku nebo předpokládaný počet volání do call centra v daný den nebo čas.  Pokud nezvolíte číselný sloupec, může se zobrazit chyba.
  
    -   Pokud nezadáte, který sloupec popisku použít, Azure Machine Learning se pokusí odvodit, který je příslušný sloupec popisku, pomocí metadat datové sady. Pokud vybere nesprávný sloupec, opravte jej pomocí voliče sloupců.
  
    > [!TIP] 
    > Pokud máte potíže s používáním voliče sloupců, přečtěte si tipy v článku [Výběr sloupců v datové sadě.](./select-columns-in-dataset.md) Popisuje některé běžné scénáře a tipy pro použití **možnosti WITH RULES** a **BY NAME.**
  
5.  Odešlete potrubí. Pokud máte hodně dat, může to chvíli trvat.

## <a name="results"></a><a name="bkmk_results"></a>Výsledky

Po trénince modelu:


+ Chcete-li model použít v jiných kanálech, vyberte modul a vyberte ikonu **Registrovat datovou sadu** na kartě **Výstupy** v pravém panelu. K uloženým modelům můžete přistupovat v paletě modulů v části **Datové sady**.

+ Chcete-li model použít při předpovídání nových hodnot, připojte jej k modulu [Model skóre](./score-model.md) spolu s novými vstupními daty.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 