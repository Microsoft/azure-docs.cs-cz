---
title: ResNet
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet model klasifikace obrázků pomocí algoritmu ResNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: bd0431a8e503605c6137d948cf207c1bd2fa45b4
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442037"
---
# <a name="resnet"></a>ResNet

Tento článek popisuje, jak pomocí modulu **ResNet** v Návrháři Azure Machine Learning vytvořit model klasifikace obrázků pomocí algoritmu ResNet..  

Tento algoritmus klasifikace je metoda učení pod dohledem a vyžaduje datovou sadu s popiskem. 
> [!NOTE]
> Tento modul nepodporuje s popiskem datovou sadu generovanou z *popisků dat* v nástroji Studio, ale podporuje pouze označený adresář imagí vygenerovaný z části [převést na Image adresář](convert-to-image-directory.md) . 

Model můžete vytvořit tak, že poskytnete model a s popiskem adresář obrázků jako vstupy pro [výuku modelu Pytorch](train-pytorch-model.md). Školený model se pak dá použít k předpovědi hodnot pro nové vstupní příklady pomocí [modelu obrázku skóre](score-image-model.md).

### <a name="more-about-resnet"></a>Další informace o ResNet

Další podrobnosti o ResNet najdete v [tomto dokumentu](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d) .

## <a name="how-to-configure-resnet"></a>Jak nakonfigurovat ResNet

1.  Přidejte modul **ResNet** do kanálu v návrháři.  

2.  V části **název modelu**zadejte název určité struktury ResNet a můžete vybrat z podporovaných ResNet: ' resnet18 ', ' resnet34 ', ' resnet50 ', ' resnet101 ', ' resnet152 ', ' resnet152 ', ' resnext50 \_ 32x4d ', ' resnext101 32x8d ', ' \_ wide_resnet50 \_ 2 ', ' wide_resnet101 \_ 2 '.

3.  Pro **předvlaky**určete, jestli se má používat model předběžně vyškolený v ImageNet. Pokud je tato možnost vybraná, můžete model vyladit podle vybraného předem připraveného modelu. Pokud je tento výběr nevybraný, můžete vytvářet sestavy od začátku.

4.  Propojte výstup modulu **DenseNet** , školení a sady dat image ověřování v [modelu Pytorch pro analýzu](train-pytorch-model.md). 

5. Odešlete kanál.

## <a name="results"></a>Výsledky

Po dokončení běhu kanálu můžete použít model pro bodování, připojit [model Pytorch](train-pytorch-model.md) pro [vyhodnocení modelu obrázku](score-image-model.md)a předpovídat hodnoty pro nové vstupní příklady.

## <a name="technical-notes"></a>Technické poznámky  

###  <a name="module-parameters"></a>Parametry modulu  

| Název       | Rozsah | Typ    | Výchozí           | Popis                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Název modelu | Všechny   | Mode    | resnext101 \_ 32x8d | Název určité struktury ResNet       |
| Předvlakované | Všechny   | Logická hodnota | Ano              | Jestli se má používat model předučený na ImageNet |
|            |       |         |                   |                                          |

###  <a name="output"></a>Výstup  

| Název            | Typ                    | Popis                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Nevlakový model | UntrainedModelDirectory | Nevlakový model ResNet, který se dá připojit ke Pytorch modelu výuky. |

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 