---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet model klasifikace obrázků pomocí algoritmu densenet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 69c18c24ae9a8eb4c1fd54c1f8530e126a40b004
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898524"
---
# <a name="densenet"></a>DenseNet

Tento článek popisuje, jak pomocí modulu **DenseNet** v Návrháři Azure Machine Learning vytvořit model klasifikace obrázků pomocí algoritmu DenseNet.  

Tento algoritmus klasifikace je metoda učení pod dohledem a vyžaduje datovou sadu s popiskem. Další informace o tom, jak získat adresář obrázků s popiskem, najdete v tématu [převod na modul adresáře imagí](convert-to-image-directory.md) . Model můžete proškolit tak, že zadáte model a s popiskem adresář obrázků jako vstupy pro [výuku modelu Pytorch](train-pytorch-model.md). Školený model se pak dá použít k předpovědi hodnot pro nové vstupní příklady pomocí [modelu obrázku skóre](score-image-model.md).

### <a name="more-about-densenet"></a>Další informace o DenseNet

Další podrobnosti najdete v [hustě propojených konvoluční sítích](https://arxiv.org/abs/1608.06993) .

## <a name="how-to-configure-densenet"></a>Jak nakonfigurovat DenseNet

1.  Přidejte modul **DenseNet** do kanálu v návrháři.  

2.  Jako **název modelu**zadejte název určité struktury densenet a můžete vybrat z podporovaných densenet: ' densenet121 ', ' densenet161 ', ' densenet169 ', ' densenet201 '.

3.  Pro **předvlaky**určete, jestli se má používat model předběžně vyškolený v ImageNet. Pokud je tato možnost vybraná, můžete model vyladit podle vybraného předem připraveného modelu. Pokud je tento výběr nevybraný, můžete vytvářet sestavy od začátku.

4.  V případě **efektivní paměti**určete, jestli se má použít kontrolní bod, což je mnohem víc paměti, ale pomalejší. Další informace naleznete v tématu https://arxiv.org/pdf/1707.06990.pdf.

5.  Propojte výstup modulu **DenseNet** , školení a sady dat image ověřování v [modelu Pytorch pro analýzu](train-pytorch-model.md). 

6. Odešlete kanál.


## <a name="results"></a>Výsledky

Po dokončení běhu kanálu můžete použít model pro bodování, připojit [model Pytorch](train-pytorch-model.md) pro [vyhodnocení modelu obrázku](score-image-model.md)a předpovídat hodnoty pro nové vstupní příklady.

## <a name="technical-notes"></a>Technické poznámky  

###  <a name="module-parameters"></a>Parametry modulu  

| Name             | Rozsah | Typ    | Výchozí     | Description                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Název modelu       | Všechny   | Mode    | densenet201 | Název určité struktury densenet     |
| Předvlakované       | Všechny   | Logická hodnota | Ano        | Jestli se má používat model předučený na ImageNet |
| Efektivní paměť | Všechny   | Logická hodnota | Nepravda       | Bez ohledu na to, jestli se má použít kontrolní bod, což je mnohem víc paměti, ale pomalejší |

###  <a name="output"></a>Výstup  

| Název            | Typ                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Nevlakový model | UntrainedModelDirectory | Nevlakový model densenet, který se dá připojit ke Pytorch modelu výuky. |

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
