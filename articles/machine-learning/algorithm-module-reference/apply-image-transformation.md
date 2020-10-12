---
title: Použití transformace obrázku
titleSuffix: Azure Machine Learning
description: Naučte se používat modul transformace bitové kopie k použití transformace obrázku na adresář obrázků.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: a64d5cebfd8e70e2f54a66193a7041c47887c54a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898917"
---
# <a name="apply-image-transformation"></a>Použití transformace obrázku 

Tento článek popisuje, jak použít modul transformace obrázku v Návrháři Azure Machine Learning pro úpravu adresáře vstupní bitové kopie na základě dříve zadané transformace obrázku.  

Aby bylo možné zadat transformaci, je nutné připojit modul pro [transformaci inicializačních imagí](init-image-transformation.md) a následně tuto transformaci použít do složky vstupní bitové kopie v modulu použít transformaci obrázku.

## <a name="how-to-use-apply-image-transformation"></a>Použití transformace použít obrázek  

1. Přidejte do svého kanálu modul **použití transformace obrázku** . Tento modul můžete najít v kategorii *transformace dat počítačové zpracování obrazu nebo Image* . 

2. Připojte výstup **transformace init image** k levému vstupu **transformace použít obrázek**.

     > [!NOTE]
     > Do tohoto modulu se přijímají pouze transformace obrázku vygenerované modulem pro [transformaci init image](init-image-transformation.md) . Pro jiný typ transformace ho prosím připojte, abyste [použili transformaci](apply-transformation.md), jinak bude vyvolána výjimka "InvalidTransformationDirectoryError".


3. Připojte adresář imagí, který chcete transformovat.

4. V nastavení **režim**určete, jaký účel použijete transformaci vstupu: pro školení nebo pro odvození. 

   Vyberete-li možnost **pro školení**, bude použita veškerá transformace, kterou zadáte v nástroji init image Transform.

   Pokud vyberete **pro odvození**, transformaci, jako je vytváření nových vzorků náhodně, bude před použitím vyloučeno. Důvodem je to, že operace transformace pro vytvoření nových vzorků náhodně vypadající jako "náhodné horizontální překlopení" se používají pro rozšíření dat při výuce, které by se mělo odebrat při odvození, protože vzorky odvození je potřeba opravit pro přesné předpovědi a vyhodnocení.

   > [!NOTE]
   > Transformace, které budou vyloučeny v režimu **pro odvození** , jsou: náhodně změněná velikost oříznutí, náhodné oříznutí, náhodné horizontální překlopení, náhodné vertikální překlopení, náhodné otočení, náhodný spřažení, náhodné stupně šedé, náhodný pohled, náhodné mazání.

5. Chcete-li použít transformaci obrázku do nového adresáře obrázků, odešlete kanál.  

### <a name="module-parameters"></a>Parametry modulu

| Name | Rozsah | Typ | Výchozí                   | Description                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Mode | Všechny   | Mode | (Vyžadovat zadání uživatelem) | Pro jaký účel použijete transformaci vstupu. Měli byste vyloučit operace transformace Random v odvozeném objektu, ale zachovat jejich školení. |

### <a name="expected-inputs"></a>Očekávané vstupy  

| Název                       | Typ                    | Description                       |
| -------------------------- | ----------------------- | --------------------------------- |
| Transformace vstupního obrázku | TransformationDirectory | Transformace vstupního obrázku        |
| Adresář vstupní bitové kopie      | ImageDirectory          | Adresář obrázku, který se má transformovat |

### <a name="outputs"></a>Výstupy  

| Název                   | Typ           | Description            |
| ---------------------- | -------------- | ---------------------- |
| Adresář výstupních imagí | ImageDirectory | Adresář výstupních imagí |

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
