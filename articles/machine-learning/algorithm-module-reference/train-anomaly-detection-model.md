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
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502968"
---
# <a name="train-anomaly-detection-model"></a>Trénování modelu detekce anomálií

Tento článek popisuje, jak použít modul **detekce anomálií pro vlaky** v Návrháři Azure Machine Learning (Preview) k vytvoření výukového modelu pro detekci anomálií.

Modul přijímá jako vstup sadu parametrů modelu pro model detekce anomálií a datovou sadu bez popisku. Vrátí model detekce anomálií spolu se sadou štítků pro školicí data.  

Další informace o algoritmech detekce anomálií, které jsou k dispozici v návrháři, najdete v těchto tématech: 

+ [Detekce anomálií na základě PCA](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Jak nakonfigurovat model detekce anomálií vlaku 

1.  Přidejte modul **detekce anomálií vlaku** do kanálu v návrháři. Tento modul můžete najít v kategorii **detekce anomálií** .

2. Propojit jeden z modulů navržených pro detekci anomálií, například [detekci anomálií založenou na DPS](pca-based-anomaly-detection.md)

    Jiné typy modelů nejsou podporovány. Při spuštění kanálu se zobrazí chyba: všechny modely musí mít stejný typ informací.  

3.  Nakonfigurujte modul detekce anomálií tak, že vyberete sloupec popisek a nakonfigurujete další parametry specifické pro algoritmus.  

4.  Připojte školicí datovou sadu k pravému vstupu **modelu detekce anomálií vlaků**.  

5.  Odešlete kanál.  

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Chcete-li zobrazit parametry modelu, klikněte pravým tlačítkem na modul a vyberte **vizualizovat**. 

+ K vytvoření předpovědi použijte [model skóre](score-model.md) s novými vstupními daty.

+ Pokud chcete uložit snímek výukového modelu, vyberte modul a klikněte na ikonu **zaregistrovat datovou sadu** na kartě **výstupy + protokoly** na pravém panelu.   

 
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

Seznam chyb specifických pro moduly návrháře naleznete v tématu [výjimky a kódy chyb pro návrháře (Preview)](designer-error-codes.md) .
'