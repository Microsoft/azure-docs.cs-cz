---
title: 'Model detekce anomálií vlaku: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Model detekce vlakových anomálií vytvořit trénovaný model detekce anomálií.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502968"
---
# <a name="train-anomaly-detection-model"></a>Model detekce anomálií vlaku

Tento článek popisuje, jak použít modul **Model detekce vlakových anomálií** v návrháři Azure Machine Learning (preview) k vytvoření trénovaného modelu detekce anomálií.

Modul bere jako vstupní sadu parametrů modelu pro model detekce anomálií a neoznačenou datovou sadu. Vrátí model detekce trénovaných anomálií spolu se sadou popisků pro trénovací data.  

Další informace o algoritmech detekce anomálií, které jsou k dispozici v návrháři, naleznete v těchto tématech: 

+ [Detekce anomálií založených na PCA](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Jak nakonfigurovat model detekce anomálií vlaku 

1.  Přidejte modul **Model detekce vlakových anomálií** do kanálu v návrháři. Tento modul naleznete v kategorii **Detekce anomálií.**

2. Připojte jeden z modulů určených pro detekci anomálií, jako je [například detekce anomálií na základě PCA](pca-based-anomaly-detection.md)

    Jiné typy modelů nejsou podporovány; při spuštění kanálu se zobrazí chyba: Všechny modely musí mít stejný typ studenta.  

3.  Nakonfigurujte modul detekce anomálií výběrem sloupce popisku a nastavením dalších parametrů specifických pro algoritmus.  

4.  Připojte trénovací datovou sadu k pravému vstupu **modelu detekce anomálií vlaku**.  

5.  Odešlete potrubí.  

## <a name="results"></a>Výsledky

Po dokončení tréninku:

+ Chcete-li zobrazit parametry modelu, klepněte pravým tlačítkem myši na modul a vyberte možnost **Vizualizovat**. 

+ Chcete-li vytvořit předpovědi, použijte [model skóre](score-model.md) s novými vstupními daty.

+ Chcete-li uložit snímek trénovaného modelu, vyberte modul a klepněte na ikonu **Registrovat datovou sadu** v části **Výstupy+protokoly** na pravé straně.   

 
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

V části [Výjimky a kódy chyb pro návrháře (náhled)](designer-error-codes.md) seznam chyb specifických pro moduly návrháře.
'