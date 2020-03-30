---
title: 'Model skóre: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Model skóre v Azure Machine Learning generovat předpovědi pomocí trénované klasifikace nebo regresní model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 56d8cad05a42da8de680ade487dddee9a97aab3a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364187"
---
# <a name="score-model-module"></a>Modul Určení skóre modelu

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží ke generování předpovědi pomocí trénované klasifikace nebo regresní model.

## <a name="how-to-use"></a>Způsob použití

1. Přidejte modul **Model skóre** do kanálu.

2. Připojte trénovaný model a datovou sadu obsahující nová vstupní data. 

    Data by měla být ve formátu kompatibilním s typem trénovaného modelu, který používáte. Schéma vstupní datové sady by také obecně mělo odpovídat schématu dat použitých k trénování modelu.

3. Odešlete potrubí.

## <a name="results"></a>Výsledky

Poté, co jste vygenerovali sadu skóre pomocí [score model](./score-model.md):

+ Chcete-li generovat sadu metrik používaných pro hodnocení přesnosti (výkonu) modelu, můžete připojit datovou sadu s hodnocením k [vyhodnocení modelu](./evaluate-model.md), 
+ Klikněte pravým tlačítkem myši na modul a výběrem **možnosti Visualize** zobrazte ukázku výsledků.
<!-- + To Save the results to a dataset. -->

Skóre nebo předpokládaná hodnota může být v mnoha různých formátech v závislosti na modelu a vstupních datech:

- Pro klasifikační modely [model skóre](./score-model.md) výstupy předpovídané hodnoty pro třídu, jakož i pravděpodobnost předpovídané hodnoty.
- Pro regresní modely [score model](./score-model.md) generuje pouze předpokládanou číselnou hodnotu.


## <a name="publish-scores-as-a-web-service"></a>Publikování skóre jako webové služby

Běžné použití vyhodnocování je vrátit výstup jako součást prediktivní webové služby. Další informace najdete [v tomto kurzu](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) o tom, jak nasadit koncový bod v reálném čase na základě kanálu v návrháři Azure Machine Learning.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 