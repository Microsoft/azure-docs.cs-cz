---
title: 'Model detekce anomálií vlaku: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul detekce anomálií pro vlaky k vytvoření výukového modelu pro detekci anomálií.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: edf35fada4233fbe43bc7f859c2414bfb8130714
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905720"
---
# <a name="train-anomaly-detection-model-module"></a>Modul detekce anomálií pro vlaky

Tento článek popisuje, jak v Návrháři Azure Machine Learning použít modul detekce anomálií vlaků k vytvoření výukového modelu pro detekci anomálií.

Modul přijímá jako vstup sadu parametrů pro model detekce anomálií a datovou sadu bez popisku. Vrátí model detekce anomálií spolu se sadou štítků pro školicí data.  

Další informace o algoritmech detekce anomálií, které jsou k dispozici v návrháři, najdete v tématu [zjištění anomálií na základě DPS](pca-based-anomaly-detection.md).  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Jak nakonfigurovat model detekce anomálií vlaku 

1.  Přidejte modul **detekce anomálií vlaku** do kanálu v návrháři. Tento modul můžete najít v kategorii **detekce anomálií** .

2. Propojte jeden z modulů navržených pro detekci anomálií, například [detekci anomálií založenou na DPS](pca-based-anomaly-detection.md).

    Jiné typy modelů nejsou podporovány. Při spuštění kanálu se zobrazí chyba "všechny modely musí mít stejný typ informací".  

3.  Nakonfigurujte modul detekce anomálií tak, že vyberete sloupec popisek a nakonfigurujete další parametry specifické pro algoritmus.  

4.  Připojte školicí datovou sadu ke správnému vstupu **modelu detekce anomálií vlaků**na pravé straně.  

5.  Odešlete kanál.  

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Chcete-li zobrazit parametry modelu, klikněte pravým tlačítkem na modul a vyberte **vizualizovat**. 

+ K vytvoření předpovědi použijte modul [bodového modelu](score-model.md) s novými vstupními daty.

+ Pokud chcete uložit snímek výukového modelu, vyberte modul. Pak vyberte ikonu **zaregistrovat datovou sadu** na kartě **výstupy + protokoly** v pravém panelu.   

 
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

Seznam chyb specifických pro moduly návrháře naleznete v tématu [výjimky a kódy chyb pro návrháře](designer-error-codes.md) .
'