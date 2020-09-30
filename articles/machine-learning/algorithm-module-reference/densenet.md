---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet model klasifikace obrázků pomocí algoritmu DenseNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 2351012738f4cf5697fb29891c9459e4cc86cd3a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536626"
---
# <a name="densenet"></a>DenseNet

Tento článek popisuje, jak pomocí modulu **DenseNet** v Návrháři Azure Machine Learning vytvořit model klasifikace obrázků pomocí algoritmu DenseNet.  

Tento algoritmus klasifikace je metoda učení pod dohledem a vyžaduje adresář obrázků označený pomocí popisku. 

> [!NOTE]
> Tento modul nepodporuje s popiskem datovou sadu generovanou z *popisků dat* v nástroji Studio, ale podporuje pouze označený adresář imagí vygenerovaný z části [převést na Image adresář](convert-to-image-directory.md) . 

Model můžete proškolit tak, že zadáte model a s popiskem adresář obrázků jako vstupy pro [výuku modelu Pytorch](train-pytorch-model.md). Školený model se pak dá použít k předpovědi hodnot pro nové vstupní příklady pomocí [modelu obrázku skóre](score-image-model.md).

### <a name="more-about-densenet"></a>Další informace o DenseNet

Další informace o DenseNet najdete v článku o výzkumném dokumentu, [hustě propojených konvoluční sítích](https://arxiv.org/abs/1608.06993).

## <a name="how-to-configure-densenet"></a>Jak nakonfigurovat DenseNet

1.  Přidejte modul **DenseNet** do kanálu v návrháři.  

2.  Jako **název modelu**zadejte název určité struktury DenseNet a můžete vybrat z podporovaných DenseNet: ' densenet121 ', ' densenet161 ', ' densenet169 ', ' densenet201 '.

3.  Pro **předvlaky**určete, jestli se má používat model předběžně vyškolený v ImageNet. Pokud je tato možnost vybraná, můžete model vyladit podle vybraného předem připraveného modelu. Pokud je tento výběr nevybraný, můžete vytvářet sestavy od začátku.

4.  V případě **efektivní paměti**určete, jestli se má použít kontrolní bod, což je mnohem víc paměti, ale pomalejší. Další informace najdete v článku o výzkumném dokumentu, který je [efektivní pro paměť DenseNets](https://arxiv.org/pdf/1707.06990.pdf).

5.  Připojte výstup modulu **DenseNet** , školení a sady dat image ověřování v [modelu Pytorch pro analýzu](train-pytorch-model.md)obrazu. 

6. Odešlete kanál.


## <a name="results"></a>Výsledky

Po dokončení běhu kanálu můžete použít model pro bodování, připojit [model Pytorch](train-pytorch-model.md) pro [vyhodnocení modelu obrázku](score-image-model.md)a předpovídat hodnoty pro nové vstupní příklady.

## <a name="technical-notes"></a>Technické poznámky  

###  <a name="module-parameters"></a>Parametry modulu  

| Název             | Rozsah | Typ    | Výchozí     | Popis                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Název modelu       | Všechny   | Mode    | densenet201 | Název určité struktury DenseNet     |
| Předvlakované       | Všechny   | Logická hodnota | Ano        | Jestli se má používat model předučený na ImageNet |
| Efektivní paměť | Všechny   | Logická hodnota | Nepravda       | Bez ohledu na to, jestli se má použít kontrolní bod, což je mnohem víc paměti, ale pomalejší |

###  <a name="output"></a>Výstup  

| Název            | Typ                    | Popis                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Nevlakový model | UntrainedModelDirectory | Nevlakový model DenseNet, který se dá připojit ke Pytorch modelu výuky. |

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
